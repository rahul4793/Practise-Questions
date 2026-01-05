## Q1. Migrating a monolithic application to microservices using Spring Boot

Spring Boot’s auto-configuration significantly simplifies bean creation when compared to traditional XML-based configuration. In legacy Spring applications, developers had to explicitly define every bean, its dependencies, and wiring logic inside XML files, which led to verbose, hard-to-maintain configurations. Spring Boot eliminates this overhead by automatically configuring beans based on the libraries available on the classpath, the selected starter dependencies, and application properties. For example, when spring-boot-starter-data-jpa is added, Spring Boot automatically configures DataSource, EntityManagerFactory, and transaction management without requiring explicit XML definitions. This allows developers to focus on business logic rather than infrastructure setup.

There are scenarios where excluding an auto-configuration class becomes necessary. This is typically done when the default configuration provided by Spring Boot does not fit application requirements or when a custom implementation is needed. For instance, if an application uses a custom DataSource configuration or an external connection pool, the default DataSourceAutoConfiguration can be excluded to prevent conflicts. Exclusion can be done using the exclude attribute of @SpringBootApplication. This ensures full control over how specific beans are created and managed.

Profile-specific beans are activated using Spring profiles, which allow different configurations for different environments such as development, testing, and production. Beans can be restricted to a profile using the @Profile annotation, and profiles are activated via the spring.profiles.active property. This is especially useful in microservices, where each environment may require different database credentials, logging levels, or external service endpoints.
```
@Profile("dev")
@Bean
public DataSource devDataSource() {
    return new HikariDataSource();
}
```
## Q2. Core concepts of Dependency Injection and annotations in Spring Boot

Dependency Injection (DI) is a design principle where objects receive their dependencies from an external source rather than creating them internally. In Spring applications, DI reduces tight coupling by allowing components to depend on interfaces instead of concrete implementations. This makes the application more modular, testable, and easier to maintain. When dependencies change, the consuming class does not need modification, which aligns with the principle of loose coupling.

Spring provides several stereotype annotations to define components. @Component is a generic annotation used for any Spring-managed bean. @Service is used in the service layer to represent business logic, while @Repository is used in the persistence layer and provides exception translation for database operations. @Controller and @RestController are used in the presentation layer, with @RestController being a specialization that automatically serializes responses into JSON or XML. Component scanning automatically detects these annotations and registers them as beans when the application starts.

Constructor injection and @Autowired are both used to inject dependencies, but constructor injection is generally preferred. Constructor injection ensures that all required dependencies are provided at object creation time, making the object immutable and easier to test. Field injection using @Autowired hides dependencies and makes unit testing harder. Constructor injection also prevents the creation of partially initialized objects.

When multiple beans of the same type exist, ambiguity can occur during injection. @Qualifier is used to explicitly specify which bean should be injected, while @Primary marks a default bean that should be preferred when no qualifier is specified. These annotations help resolve conflicts cleanly without changing business logic.
```
@Service
public class PaymentService {
    private final PaymentGateway gateway;

    public PaymentService(@Qualifier("stripeGateway") PaymentGateway gateway) {
        this.gateway = gateway;
    }
}
```
## Q3. Externalizing and rotating secrets without redeployment

Spring Boot supports externalized configuration through application.yml and profile-specific configuration files such as application-dev.yml or application-prod.yml. These files work together by allowing a base configuration to be shared across environments while overriding specific values per profile. This ensures consistent behavior while enabling environment-specific customization.

For secure secret management, sensitive values such as database passwords and API keys should never be stored directly in configuration files. A secure approach involves using environment variables, external configuration servers, or secret management tools like HashiCorp Vault or AWS Secrets Manager. Spring Boot integrates well with these tools, allowing secrets to be injected securely at runtime and rotated without redeploying the application.

In multi-region deployments, configurations are typically structured using a combination of profiles and external configuration sources. Each region can have its own profile or configuration namespace, ensuring region-specific endpoints, credentials, and scaling policies while maintaining a common application codebase.

## Q4. Scheduling and graceful shutdown of batch jobs

Scheduling in Spring Boot is enabled using the @EnableScheduling annotation along with @Scheduled methods. To restrict scheduled jobs to specific environments, the @Profile annotation can be applied, ensuring that jobs only run in intended environments such as production. This prevents unnecessary execution in development or testing setups.

Graceful shutdown during scale-down is handled using lifecycle hooks such as @PreDestroy or implementing the DisposableBean interface. These hooks allow the application to complete ongoing tasks and release resources before shutting down. This is critical in distributed systems where abrupt termination can cause data inconsistency or partial processing.

Spring Boot Actuator helps monitor scheduled jobs by exposing health, metrics, and custom endpoints. Metrics related to task execution time, failure counts, and system health can be monitored using Actuator endpoints, making it easier to observe and troubleshoot batch jobs in production environments.
```
@Scheduled(fixedRate = 10000)
@Profile("prod")
public void runBatchJob() {
    // job logic
}
```
