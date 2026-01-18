## 1. Why do we need Mockito when we already have JUnit?

Answer:

JUnit is a testing framework that helps us write and run tests, but it does not provide mocking capabilities. Mockito is used to create mock objects so that we can test a class in isolation by controlling the behavior of its dependencies.

JUnit = test runner & assertions
Mockito = mock dependencies

What happens if you don’t mock repository layer?

Test hits the real database

Test becomes slow

Test becomes flaky

Not a unit test anymore → becomes integration test

Fails if DB is unavailable

Which layers should be mocked?

In service layer tests:

Repository layer

External APIs

Kafka producers

REST clients

Do NOT mock:

The class under test

Simple DTOs / entities

## 2. Explain @Mock and @InjectMocks
@Mock

Creates a fake object of a dependency.

@Mock
private UserRepository userRepository;

@InjectMocks

Creates the real object of the class under test and injects mocked dependencies into it.

@InjectMocks
private UserService userService;

How do they work together?

Mockito creates mock objects for all @Mock

Mockito creates real instance of class with @InjectMocks

Injects mocks via:

Constructor

Setter

Field injection

What happens if @InjectMocks is missing?

userService is null

Test fails with NullPointerException

Mocks are never injected

How are mocks injected internally?

Order:

Constructor injection

Setter injection

Field injection (reflection)

 ## 3. How do you write a unit test for a service layer method?
Steps:

Mock dependencies

Define behavior using when

Call service method

Assert result

Verify interactions

Example:
@Test
void shouldReturnUserWhenIdExists() {
    User user = new User(1L, "Rahul");

    when(userRepository.findById(1L))
        .thenReturn(Optional.of(user));

    User result = userService.getUserById(1L);

    assertEquals("Rahul", result.getName());
    verify(userRepository).findById(1L);
}

Should service unit tests hit the database?

❌ No

Unit tests must be isolated

DB tests belong to integration tests

What exactly should be asserted?

Returned value

State changes

Exception thrown

Side effects (like method calls)

❌ Don’t assert internal implementation details

🔹 4. How do you mock and verify method calls?
Mocking behavior
when(repo.save(any(User.class)))
    .thenReturn(savedUser);

Verify method called
verify(repo).save(user);

Verify method never called
verify(repo, never()).delete(any());

Verify number of calls
verify(repo, times(2)).findAll();

Verify method arguments
verify(repo).findById(eq(1L));


OR

verify(repo).save(argThat(u -> u.getName().equals("Rahul")));

🔹 5. How do you test exception scenarios?
Assert exception using JUnit
@Test
void shouldThrowExceptionWhenUserNotFound() {
    when(userRepository.findById(1L))
        .thenReturn(Optional.empty());

    Exception ex = assertThrows(
        UserNotFoundException.class,
        () -> userService.getUserById(1L)
    );

    assertEquals("User not found", ex.getMessage());
}

How do you assert exception message?
assertEquals("User not found", ex.getMessage());

How do you test try–catch blocks?

👉 Best practice:
Test the observable outcome, not the catch block.

Example:

@Test
void shouldHandleDbExceptionGracefully() {
    when(repo.save(any()))
        .thenThrow(new RuntimeException("DB error"));

    Response result = service.saveUser(user);

    assertEquals("FAILED", result.getStatus());
}
