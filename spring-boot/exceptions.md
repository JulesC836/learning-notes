
## 🏗️ The Evolution of Exception Handling

There are three main ways to handle exceptions, ranging from local to global scope.

### 1. The Local Approach: `@ExceptionHandler`

You can define a method inside a specific `@RestController` to catch exceptions thrown only by that controller.

* **Pros:** Quick to implement; specific logic for specific endpoints.
* **Cons:** Not reusable; leads to code duplication if you have many controllers.

```java
@RestController
public class UserController {

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable String id) {
        if (id.equals("0")) throw new UserNotFoundException("User not found");
        return new User(id, "John Doe");
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}

```

### 2. The Global Approach: `@RestControllerAdvice` (Recommended)

This is the "modern" way. It acts as an interceptor for exceptions thrown across **all** controllers in your application. It centralizes your error logic in one class.

* **Pros:** Clean separation of concerns; consistent error format across the whole API.
* **Cons:** None, really. This is the industry standard.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorDetails> handleUserNotFound(UserNotFoundException ex, WebRequest request) {
        ErrorDetails error = new ErrorDetails(LocalDateTime.now(), ex.getMessage(), request.getDescription(false));
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    // Catch-all for any other unhandled exceptions
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGlobalException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("An unexpected error occurred");
    }
}

```

### 3. The Simple Approach: `ResponseStatusException`

Introduced in Spring 5, this allows you to throw an exception directly in your business logic without creating custom exception classes or handlers.

```java
throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "Invalid input data");

```

---

## 🔄 The Request-Error Flow

Understanding how Spring intercepts these errors is vital for debugging.

1. **Request** enters the `DispatcherServlet`.
2. **Controller** executes and throws an exception.
3. The **Interceptor** checks for a `@RestControllerAdvice`.
4. If a match is found, the **Exception Handler** method is executed.
5. If no match is found, it falls back to the default WhiteLabel Error Page (BasicErrorController).

---

## 📊 Comparison Table

| Feature | `@ExceptionHandler` | `@RestControllerAdvice` | `ResponseStatusException` |
| --- | --- | --- | --- |
| **Scope** | Controller-level | Application-level (Global) | Local (Inline) |
| **Reusability** | Low | High | Medium |
| **Complexity** | Simple | Medium | Very Simple |
| **Best For** | One-off specific logic | Standardized APIs | Prototyping/Quick fixes |

---

## 🛠️ Handling Validation Errors

When using `jakarta.validation` (like `@NotBlank` or `@Min`), Spring throws a `MethodArgumentNotValidException`. To return a clean list of validation errors to the client, you should override it in your advice:

```java
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<Map<String, String>> handleValidationExceptions(MethodArgumentNotValidException ex) {
    Map<String, String> errors = new HashMap<>();
    ex.getBindingResult().getFieldErrors().forEach(error -> 
        errors.put(error.getField(), error.getDefaultMessage()));
    return ResponseEntity.badRequest().body(errors);
}

```
