## Q1. Designing a Product Service with DTOs instead of JPA Entities

In RESTful API design, Data Transfer Objects (DTOs) are preferred over exposing JPA entities directly because entities represent the internal persistence model, which should remain decoupled from external API contracts. Entities often contain lazy-loaded relationships, persistence annotations, and internal fields that are not meant to be exposed to clients. DTOs allow precise control over what data is sent or received, improve security by hiding sensitive fields, and make APIs stable even when database schemas evolve. Mapping between entities and DTOs can be done manually using constructor-based mapping or setter methods, or automatically using libraries like MapStruct, which generate type-safe and maintainable mapping code.

Validation should ideally occur at the controller boundary using DTOs because invalid data should be rejected as early as possible before entering business logic. This keeps the service layer clean and focused on domain rules rather than request validation. However, critical business validations that depend on database state or complex rules should still reside in the service layer. This layered validation approach ensures both data integrity and clean separation of concerns.

Keeping controllers thin and services fat is achieved by limiting controllers to request parsing, validation triggering, and response construction, while moving business logic, transformations, and transactional work into services. Controllers should delegate almost immediately to a service method. This design improves testability, reusability, and readability, and prevents controllers from becoming tightly coupled to business rules.

For clean API design, annotations such as @RestController are essential to mark the class as a REST endpoint provider and automatically serialize responses to JSON. The @RequestMapping annotation (and its specialized variants like @GetMapping and @PostMapping) defines clear and readable endpoint paths. The @RequestParam annotation is useful for handling query parameters in a controlled and explicit manner, making APIs self-descriptive and predictable.
```
@RestController
@RequestMapping("/api/products")
public class ProductController {

    private final ProductService productService;

    public ProductController(ProductService productService) {
        this.productService = productService;
    }

    @PostMapping
    public ResponseEntity<ProductDTO> create(@Valid @RequestBody ProductDTO dto) {
        return ResponseEntity.ok(productService.createProduct(dto));
    }
}
```
## Q2. Consistent Error Handling and API Stability

Global error handling in Spring Boot is implemented using @ControllerAdvice, which acts as a centralized interceptor for exceptions thrown across controllers. Within this class, methods annotated with @ExceptionHandler catch specific exceptions and convert them into structured HTTP responses. This approach eliminates repetitive try-catch blocks in controllers and ensures consistent error formatting across the entire application.

A standard error payload should include a timestamp to help trace issues chronologically, the request path to identify the failing endpoint, an HTTP status code to indicate the nature of the failure, and a human-readable message for debugging or client display. Including optional fields such as a correlation ID further improves traceability in distributed systems.

API versioning is crucial for backward compatibility and can be achieved through URL versioning, such as /api/v1/products, or through request headers. Deprecation should be communicated clearly using response headers or documentation, while keeping older versions functional for a defined transition period. This strategy allows clients to migrate safely without breaking changes.

Custom HTTP status codes are returned using ResponseEntity, which provides full control over the response body, headers, and status. This enables APIs to communicate precise outcomes, such as returning 409 Conflict for duplicate resources or 422 Unprocessable Entity for validation failures.
```
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiError> handleValidation(MethodArgumentNotValidException ex) {
        ApiError error = new ApiError(
                LocalDateTime.now(),
                "Validation failed",
                HttpStatus.BAD_REQUEST.value()
        );
        return ResponseEntity.badRequest().body(error);
    }
}
```
## Q3. Idempotency, Resilience, and Distributed Failure Handling

Idempotency ensures that repeated requests produce the same outcome, which is critical for create operations in unreliable networks. This is commonly implemented using an idempotency key sent by the client in request headers. The server stores this key along with the processed response and returns the cached result if the same key is received again, preventing duplicate resource creation during retries.

Resilience against downstream delays is achieved using Resilience4j by configuring timeouts, retries, and circuit breakers. Timeouts prevent threads from being blocked indefinitely, retries handle transient failures, and circuit breakers stop cascading failures by temporarily blocking calls to unhealthy services. This combination improves system stability under load or partial outages.

For long-running operations, returning 202 Accepted is a best practice. The server acknowledges the request and processes it asynchronously, while clients poll a status endpoint or receive updates via webhooks. This pattern improves responsiveness and avoids blocking HTTP connections.

Partial failures in distributed systems should be handled gracefully by using compensating transactions, fallback logic, and clear error reporting. Instead of rolling back everything, systems should aim for eventual consistency and provide clients with accurate status information so corrective actions can be taken.

## Q4. Backend Validations for POST APIs

Validations are critical for API security and data integrity because they prevent malformed, malicious, or incomplete data from entering the system. Without proper validation, applications are vulnerable to data corruption, unexpected runtime errors, and even security exploits such as injection attacks or resource exhaustion.

Spring Boot supports declarative validation using @Valid in controllers and constraint annotations like @NotBlank, @Size, and @Email directly on DTO fields. This ensures that request payloads are automatically validated before reaching business logic, enforcing rules consistently and transparently.

When validation fails, Spring throws a MethodArgumentNotValidException, which can be intercepted globally using @ControllerAdvice. Validation errors should be propagated to clients in a structured format that clearly explains which fields failed and why, enabling clients to correct requests efficiently.

Customizing validation error responses improves API usability by replacing generic error messages with domain-specific explanations. This customization ensures that clients receive predictable and meaningful feedback instead of ambiguous failure messages.
```
public class UserDTO {

    @NotBlank
    private String name;

    @Size(min = 8, max = 20)
    private String password;

    // getters and setters
}
```
