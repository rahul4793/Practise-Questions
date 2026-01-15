## Integrating MongoDB with a Spring Boot Application

MongoDB is integrated with a Spring Boot application using Spring Data MongoDB, which provides abstraction and repository support similar to JPA but tailored for a document-based database. To begin with, the required dependency is added to the project, typically spring-boot-starter-data-mongodb. This starter automatically configures MongoDB support, including the MongoTemplate and repository infrastructure.

Configuration is usually done in application.yml or application.properties, where the MongoDB connection URI, database name, and credentials are specified. Spring Boot uses auto-configuration to create the MongoDB client and connect to the database without manual setup. If MongoDB is running locally, minimal configuration is needed, whereas cloud-based MongoDB (like Atlas) requires a connection string.

The major difference between MongoDB and JPA/Hibernate lies in the data model and persistence approach. MongoDB is a NoSQL document database, storing data as flexible JSON-like documents without enforcing a fixed schema. JPA/Hibernate, on the other hand, is designed for relational databases, relying on tables, rows, joins, and strict schemas. MongoDB avoids joins by embedding related data where appropriate, while JPA relies heavily on relationships and joins. This makes MongoDB more suitable for rapidly evolving schemas and high-read, distributed systems.

## Difference Between MongoRepository and CrudRepository

CrudRepository is a generic Spring Data interface that provides basic CRUD operations such as save, findById, findAll, and delete. It is database-agnostic and works across multiple persistence technologies, including relational databases and MongoDB.

MongoRepository extends CrudRepository and adds MongoDB-specific features such as pagination, sorting, and more advanced query capabilities. It is tailored specifically for MongoDB and provides better integration with MongoDB’s query model. You would prefer MongoRepository when working exclusively with MongoDB and when you need features like paging, sorting, or MongoDB-specific query behavior without writing custom queries.

In real applications, MongoRepository is generally preferred over CrudRepository for MongoDB projects because it offers richer functionality while still remaining simple to use.

## Mapping a MongoDB Document to a Java Class in Spring Boot

In Spring Boot, a MongoDB document is mapped to a Java class using annotations provided by Spring Data MongoDB. The @Document annotation is used to indicate that a class represents a MongoDB document and to specify the collection name. Each document has a unique identifier, which is mapped using the @Id annotation.

Fields in the Java class are automatically mapped to document fields in MongoDB. If the field name in Java differs from the field name in MongoDB, the @Field annotation can be used to customize the mapping. Nested objects and lists are supported naturally, which aligns well with MongoDB’s document structure.
```
@Document(collection = "users")
public class User {

    @Id
    private String id;

    private String name;

    @Field("email_address")
    private String email;
}
```

This mapping removes the need for manual serialization or conversion logic. Spring Data automatically converts Java objects to BSON documents and vice versa.

Indexing and Performance Optimization in MongoDB with Spring Boot

Indexing is critical for performance optimization in MongoDB, especially for large collections and frequent queries. In Spring Boot, indexes can be defined directly in the entity class using annotations such as @Indexed and @CompoundIndex. This ensures that indexes are created automatically when the application starts, reducing the risk of missing performance optimizations.

Single-field indexes improve query performance for filters and sorts, while compound indexes are useful when queries frequently involve multiple fields. Choosing the correct fields to index is important because excessive indexing increases write overhead and memory usage.
```
@Document(collection = "products")
@CompoundIndex(name = "category_price_idx", def = "{'category': 1, 'price': 1}")
public class Product {

    @Id
    private String id;

    @Indexed
    private String category;

    private double price;
}
```

Beyond indexing, performance can be improved by designing documents carefully. Embedding related data instead of referencing reduces the need for multiple queries. Pagination should be used for large result sets, and projections can limit returned fields to only what is required. Monitoring query performance using MongoDB’s explain plans and logs helps identify slow queries and missing indexes.
