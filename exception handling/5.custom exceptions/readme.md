# Customized Exceptions (User-Defined Exceptions) in Java

Sometimes, we need to create our own exceptions to fulfill specific requirements in our programs. These are known as **customized exceptions** or **user-defined exceptions**.

### Examples of Customized Exceptions:

1. `InSufficientFundsException`
2. `TooYoungException`
3. `TooOldException`

---

### Program: CustomizedExceptionDemo

```java
class TooYoungException extends RuntimeException {
    TooYoungException(String s) {
        super(s);
    }
}

class TooOldException extends RuntimeException {
    TooOldException(String s) {
        super(s);
    }
}

public class CustomizedExceptionDemo {
    public static void main(String[] args) {
        int age = Integer.parseInt(args[0]);

        if (age > 60) {
            throw new TooYoungException("Please wait some more time... you will get the best match.");
        } else if (age < 18) {
            throw new TooOldException("Your age has already crossed the limit... no chance of getting married.");
        } else {
            System.out.println("You will get match details soon by e-mail.");
        }
    }
}
```

### Sample Output:

```
1) E:\scjp> java CustomizedExceptionDemo 61
Exception in thread "main" TooYoungException: Please wait some more time... you will get the best match.
    at CustomizedExceptionDemo.main(CustomizedExceptionDemo.java:13)

2) E:\scjp> java CustomizedExceptionDemo 27
You will get match details soon by e-mail

3) E:\scjp> java CustomizedExceptionDemo 9
Exception in thread "main" TooOldException: Your age has already crossed the limit... no chance of getting married.
    at CustomizedExceptionDemo.main(CustomizedExceptionDemo.java:17)
```

---

### Key Points:

* User-defined exceptions should extend `Exception` (for checked exceptions) or `RuntimeException` (for unchecked exceptions).
* They allow developers to express meaningful and domain-specific error conditions.
* Including a constructor that passes a custom message to the superclass constructor (`super`) enhances clarity during debugging.
* ✅ It is **highly recommended** to maintain customized exceptions as **unchecked exceptions** by extending `RuntimeException`.
* ✅ We can catch any `Throwable` type, including **Errors**.
