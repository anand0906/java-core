# Complete Guide to Java Optional

## What is Optional?

Optional is a container class in Java that may or may not contain a value. It was introduced in Java 8 to help avoid `NullPointerException` and make code more readable.

Think of Optional like a box that might be empty or might contain something valuable.

## Why Use Optional?

### The Problem with null
```java
// Traditional approach - risky!
public String getUserName(int userId) {
    User user = findUser(userId);
    return user.getName(); // What if user is null? NullPointerException!
}
```

### The Solution with Optional
```java
// Optional approach - safer!
public Optional<String> getUserName(int userId) {
    Optional<User> user = findUser(userId);
    return user.map(User::getName);
}
```

## Creating Optional Objects

### 1. Optional.empty()
Creates an empty Optional (no value inside).

```java
Optional<String> emptyOptional = Optional.empty();
System.out.println(emptyOptional); // Optional.empty
```

### 2. Optional.of()
Creates Optional with a non-null value. Throws exception if value is null.

```java
Optional<String> nameOptional = Optional.of("John");
System.out.println(nameOptional); // Optional[John]

// This will throw NullPointerException
Optional<String> nullOptional = Optional.of(null); // DON'T DO THIS!
```

### 3. Optional.ofNullable()
Creates Optional that can handle null values safely.

```java
String name = null;
Optional<String> safeOptional = Optional.ofNullable(name);
System.out.println(safeOptional); // Optional.empty

String realName = "Alice";
Optional<String> nameOptional = Optional.ofNullable(realName);
System.out.println(nameOptional); // Optional[Alice]
```

## Checking if Optional has Value

### 1. isPresent()
Returns true if Optional contains a value.

```java
Optional<String> name = Optional.of("Bob");
if (name.isPresent()) {
    System.out.println("Name exists: " + name.get());
}
```

### 2. isEmpty() (Java 11+)
Returns true if Optional is empty.

```java
Optional<String> emptyName = Optional.empty();
if (emptyName.isEmpty()) {
    System.out.println("No name found");
}
```

## Getting Values from Optional

### 1. get()
Returns the value if present, throws exception if empty.

```java
Optional<String> name = Optional.of("Charlie");
String actualName = name.get(); // Returns "Charlie"

Optional<String> emptyName = Optional.empty();
String noName = emptyName.get(); // Throws NoSuchElementException!
```

**Warning**: Only use `get()` when you're 100% sure the Optional has a value.

### 2. orElse()
Returns the value if present, otherwise returns a default value.

```java
Optional<String> name = Optional.empty();
String result = name.orElse("Default Name");
System.out.println(result); // "Default Name"

Optional<String> actualName = Optional.of("David");
String result2 = actualName.orElse("Default Name");
System.out.println(result2); // "David"
```

### 3. orElseGet()
Returns the value if present, otherwise calls a supplier function.

```java
Optional<String> name = Optional.empty();
String result = name.orElseGet(() -> "Generated Default");
System.out.println(result); // "Generated Default"

// More complex example
Optional<String> name2 = Optional.empty();
String result2 = name2.orElseGet(() -> {
    System.out.println("Generating default name...");
    return "Auto-Generated-" + System.currentTimeMillis();
});
```

### 4. orElseThrow()
Returns the value if present, otherwise throws an exception.

```java
Optional<String> name = Optional.empty();

// Throws NoSuchElementException
String result = name.orElseThrow();

// Throws custom exception
String result2 = name.orElseThrow(() -> 
    new IllegalArgumentException("Name not found"));
```

## Transforming Optional Values

### 1. map()
Transforms the value if present, returns Optional of the result.

```java
Optional<String> name = Optional.of("john");
Optional<String> upperName = name.map(String::toUpperCase);
System.out.println(upperName); // Optional[JOHN]

Optional<String> emptyName = Optional.empty();
Optional<String> result = emptyName.map(String::toUpperCase);
System.out.println(result); // Optional.empty
```

### 2. flatMap()
Similar to map(), but used when the mapping function returns an Optional.

```java
public Optional<String> getEmailDomain(String email) {
    if (email.contains("@")) {
        return Optional.of(email.substring(email.indexOf("@") + 1));
    }
    return Optional.empty();
}

Optional<String> email = Optional.of("user@example.com");
Optional<String> domain = email.flatMap(this::getEmailDomain);
System.out.println(domain); // Optional[example.com]
```

### 3. filter()
Keeps the value only if it matches a condition.

```java
Optional<String> name = Optional.of("John");
Optional<String> longName = name.filter(n -> n.length() > 5);
System.out.println(longName); // Optional.empty (John has 4 letters)

Optional<String> name2 = Optional.of("Alexander");
Optional<String> longName2 = name2.filter(n -> n.length() > 5);
System.out.println(longName2); // Optional[Alexander]
```

## Performing Actions with Optional

### 1. ifPresent()
Executes an action if value is present.

```java
Optional<String> name = Optional.of("Emma");
name.ifPresent(n -> System.out.println("Hello, " + n));
// Prints: Hello, Emma

Optional<String> emptyName = Optional.empty();
emptyName.ifPresent(n -> System.out.println("Hello, " + n));
// Prints nothing
```

### 2. ifPresentOrElse() (Java 9+)
Executes one action if present, another if empty.

```java
Optional<String> name = Optional.of("Frank");
name.ifPresentOrElse(
    n -> System.out.println("Hello, " + n),
    () -> System.out.println("No name provided")
);
// Prints: Hello, Frank

Optional<String> emptyName = Optional.empty();
emptyName.ifPresentOrElse(
    n -> System.out.println("Hello, " + n),
    () -> System.out.println("No name provided")
);
// Prints: No name provided
```

## Real-World Examples

### Example 1: User Service
```java
public class UserService {
    private Map<Integer, User> users = new HashMap<>();
    
    public Optional<User> findUserById(int id) {
        return Optional.ofNullable(users.get(id));
    }
    
    public String getUserDisplayName(int id) {
        return findUserById(id)
            .map(User::getName)
            .filter(name -> !name.isEmpty())
            .orElse("Unknown User");
    }
}
```

### Example 2: Configuration Service
```java
public class ConfigService {
    private Properties properties = new Properties();
    
    public Optional<String> getProperty(String key) {
        return Optional.ofNullable(properties.getProperty(key));
    }
    
    public int getIntProperty(String key, int defaultValue) {
        return getProperty(key)
            .map(Integer::parseInt)
            .orElse(defaultValue);
    }
}
```

### Example 3: Shopping Cart
```java
public class ShoppingCart {
    private List<Item> items = new ArrayList<>();
    
    public Optional<Item> getMostExpensiveItem() {
        return items.stream()
            .max(Comparator.comparing(Item::getPrice));
    }
    
    public void printMostExpensiveItem() {
        getMostExpensiveItem()
            .ifPresentOrElse(
                item -> System.out.println("Most expensive: " + item.getName()),
                () -> System.out.println("Cart is empty")
            );
    }
}
```

## Best Practices

### DO:
1. **Use Optional for return types** when a method might not return a value
```java
public Optional<User> findUser(String username) {
    // Implementation
}
```

2. **Chain operations** for clean code
```java
user.map(User::getAddress)
    .map(Address::getCity)
    .orElse("Unknown City");
```

3. **Use orElse() for simple defaults**
```java
String name = optionalName.orElse("Anonymous");
```

4. **Use orElseGet() for expensive operations**
```java
String result = optional.orElseGet(() -> expensiveOperation());
```

### DON'T:
1. **Don't use Optional for fields**
```java
// BAD
public class User {
    private Optional<String> name; // Don't do this
}

// GOOD
public class User {
    private String name; // Can be null
}
```

2. **Don't use Optional for method parameters**
```java
// BAD
public void processUser(Optional<User> user) { }

// GOOD
public void processUser(User user) { }
```

3. **Don't use get() without checking**
```java
// BAD
String name = optional.get(); // Might throw exception

// GOOD
String name = optional.orElse("default");
```

## Common Patterns

### 1. Null-Safe Method Chaining
```java
// Before Optional
String city = null;
if (user != null && user.getAddress() != null) {
    city = user.getAddress().getCity();
}

// With Optional
String city = Optional.ofNullable(user)
    .map(User::getAddress)
    .map(Address::getCity)
    .orElse("Unknown");
```

### 2. Conditional Processing
```java
Optional<String> result = Optional.of("hello")
    .filter(s -> s.length() > 3)
    .map(String::toUpperCase);
```

### 3. Exception Handling
```java
public Optional<Integer> parseInteger(String str) {
    try {
        return Optional.of(Integer.parseInt(str));
    } catch (NumberFormatException e) {
        return Optional.empty();
    }
}
```

## Summary

Optional is a powerful tool that helps you:
- Avoid NullPointerException
- Make your code more readable
- Express your intent clearly (method might not return a value)
- Chain operations safely
- Handle absence of values gracefully

Remember: Optional is not a replacement for null, but a way to handle potentially absent values more safely and expressively.
