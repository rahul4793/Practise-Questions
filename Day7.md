## Q1. Modeling Orders, OrderItems, and Users in a Spring Boot Application

When modeling an e-commerce domain, the relationship between User, Order, and OrderItem naturally follows real-world ownership. A User can place multiple orders, so a One-to-Many relationship is used from User to Order, while each Order belongs to exactly one user, making it a Many-to-One relationship from Order to User. Similarly, an Order contains multiple OrderItem entries, so Order to OrderItem is modeled as One-to-Many, and each OrderItem points back to its parent Order using Many-to-One. This structure keeps the data normalized and avoids duplication.

Cascading is important for maintaining consistency between parent and child entities. For Order and OrderItem, cascading with CascadeType.ALL ensures that when an order is saved, updated, or deleted, its items are automatically handled without additional repository calls. Orphan removal is enabled using orphanRemoval = true so that if an OrderItem is removed from the order’s collection, it is also deleted from the database. This prevents stale or unused rows from remaining in the order_items table.

Enforcing order status transitions at the entity level helps maintain business correctness. This can be achieved by defining an enum for order status and controlling transitions through domain methods instead of public setters. For example, an order should not move directly from PENDING to SHIPPED without passing through PAID. By validating transitions inside entity methods, invalid state changes are prevented regardless of where the entity is modified.

Essential JPA annotations include @Entity to mark the class as a persistent entity, @Table to map it to a specific database table, and @JoinColumn to define foreign key relationships explicitly. These annotations make the database schema clear, readable, and predictable.
```
@Entity
@Table(name = "orders")
public class Order {

    @Id
    @GeneratedValue
    private Long id;

    @ManyToOne
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<OrderItem> items = new ArrayList<>();

    @Enumerated(EnumType.STRING)
    private OrderStatus status;

    public void markPaid() {
        if (status != OrderStatus.PENDING) {
            throw new IllegalStateException("Order must be PENDING to be PAID");
        }
        this.status = OrderStatus.PAID;
    }
}
```
## Q2. Handling Concurrent Purchases and Transactions

Transaction boundaries define where a database operation starts and ends, and in Spring Boot they are typically managed using the @Transactional annotation at the service layer. This ensures that all database operations related to a purchase, such as checking stock, reserving inventory, and creating an order, are executed atomically. If any step fails, the entire transaction is rolled back, preserving data consistency.

Choosing the correct isolation level depends on the trade-off between consistency and performance. READ COMMITTED prevents dirty reads and is usually sufficient for most applications, offering good performance. However, when two users attempt to purchase the last remaining item simultaneously, stronger guarantees may be required. SERIALIZABLE provides the highest level of isolation by ensuring transactions execute as if they were sequential, but it significantly reduces concurrency. In high-traffic systems, READ COMMITTED combined with proper locking is often preferred.

To handle concurrent updates safely, either optimistic or pessimistic locking can be used. Optimistic locking assumes conflicts are rare and uses a version field to detect concurrent modifications. If two transactions update the same row, the second one fails and can be retried. Pessimistic locking, on the other hand, locks the database row immediately, preventing other transactions from reading or writing it until the lock is released. In inventory management scenarios where conflicts are likely, pessimistic locking is often safer.

Deadlocks and retries should be handled gracefully at the service layer. When a deadlock occurs, the database throws an exception, which can be caught and retried using Spring Retry or custom retry logic with exponential backoff. This ensures temporary contention does not result in user-visible failures.
```
@Entity
public class Product {

    @Id
    private Long id;

    private int stock;

    @Version
    private Long version;
}
```
## Q3. Auditing with createdBy and updatedBy Fields

Auditing allows automatic tracking of who created or modified an entity and when, which is essential for traceability and compliance. Spring Data JPA provides built-in support using annotations such as @CreatedBy, @LastModifiedBy, @CreatedDate, and @LastModifiedDate. These fields are populated automatically through entity listeners during persistence events.

To integrate auditing with Spring Security, an implementation of AuditorAware is used. This component extracts the currently authenticated user from the Spring Security context and supplies it to the auditing framework. This way, the username or user ID is automatically stored whenever an entity is created or updated, without manual intervention in business logic.

One limitation of auditing arises with detached entities. If an entity is modified outside the persistence context and later merged, auditing callbacks may not always behave as expected, especially if changes bypass the normal repository save flow. Additionally, auditing only triggers on entity lifecycle events, so bulk updates executed directly via queries will not update auditing fields automatically.

JPA auditing is enabled globally in a Spring Boot project using the @EnableJpaAuditing annotation in a configuration class. This activates auditing across all entities that use the relevant annotations, ensuring consistent behavior throughout the application.
```
@Configuration
@EnableJpaAuditing
public class JpaAuditConfig {

    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> Optional.ofNullable(
            SecurityContextHolder.getContext().getAuthentication()
        ).map(auth -> auth.getName());
    }
}
```
