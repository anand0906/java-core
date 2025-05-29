**What is Lambda Expression?**

* It is an anonymous function which doesn't have any name, return type and access modifiers.
* It was introduced in Java 1.8 version and its main theme is to bring the benefits of functional oriented programming in Java.

**What is Functional Interface?**

* It is an interface which contains only single abstract method.
* Basically, it is used as a reference to the lambda expressions. In order to invoke (execute/call) lambda functions we need functional interfaces.
* It can have any number of static and default methods in addition to a single abstract method.
* It was introduced in Java 1.8 version and its main theme is to bring the benefits of functional oriented programming in Java.
* Built-in functional interfaces: Runnable, Comparable

**What is @FunctionalInterface Annotation?**

* It is used to indicate that the given interface is a functional interface.
* It enforces the given interface to have a single abstract method only. Any violations will throw a compile time error.

**What are the Different Types of Functional Interfaces Introduced in Java 1.8?**
There are four main types of functional interfaces introduced in Java 1.8:

1. **Predicate**

   * Takes a single input and returns a boolean value.
   * Method: `boolean test(T t)`

2. **Function\<T, R>**

   * Takes a single input (T) and returns an output (R).
   * Method: `R apply(T t)`

3. **Consumer**

   * Takes a single input and returns nothing.
   * Method: `void accept(T t)`

4. **Supplier**

   * Takes no input and returns a result.
   * Method: `R get()`

**What are Default Methods in Interfaces?**

* Introduced in Java 1.8.
* Allows interfaces to have concrete implementations.
* So that, if we want to add new functionality to existing interfaces, we don't need to force all its implementation classes to implement new functions.
 Simply we can define default method in interface with default functionality, all implementation classes can use it like normal method, if any class wants
 it can override this default method.
* Default methods can be overridden by implementation classes.

**What are Static Methods in Interfaces?**

* It is like a utility functions, if we want any function common to all implementation classes, which shouldn' be overridden, we can use static method in
 interfaces.
* They can't be overridden by implementation classes.
* Must be invoked using the interface name.

**What is Stream?**

* Used to process a group of objects in a collection.
* If we want to store group of objects we can use collection, if we want to process group of object we can use Stream.
* By using Stream, we can process objects in pipelined manner.

**Commonly Used Stream Methods:**

* `filter()` – Filters the given objects based on condition.
* `map()` – Modifies each element.
* `collect()` – Converts stream to a collection.
* `count()` – Counts the elements in the stream.
* `sorted()` – Sorts the elements.
* `min()`, `max()` – Finds the minimum and maximum elements.
* `forEach()` – Iterates over each element.
* `toArray()` – Converts stream to array.
* `flatMap()` – Flattens the elements and returns stream of multiple values.
* `takeWhile()` – Takes elements until the condition is false.
* `dropWhile()` – Drops elements until the condition is false.
* `Stream.iterate()` – Used for loop-like iteration.
* `Stream.ofNullable()` – Avoids `NullPointerException`.

**Difference Between map() and flatMap():**

* `map()` produces one output value for each input.
* `flatMap()` produces zero or more values for each input (flattens nested structures).

**What are Private Methods in Interfaces?**

* Introduced in Java 1.9.
* When we have multiple defualt/static methods with common code inside interfaces, code will look ugly and code duplication will
 be there, so inorder to solve this problem, we can have private methods inside interface, which can be called by static/default methods inside interface to
 have good code reusablity..
* Can only be accessed within the interface by default/static methods.

**Additional Key Features Introduced in Java 1.8:**

* **Optional Class**: Used to avoid null checks and handle optional values gracefully.
* **Date and Time API (java.time package)**: Improved and immutable API for date, time, duration, period, etc.
* **Nashorn JavaScript Engine**: Allows Java code to run JavaScript code.
* **Default and Static Methods in Interfaces**: Already covered above.
