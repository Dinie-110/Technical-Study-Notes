# Java Programming — Beginner to Advanced Study Notes

A practical, GitHub-ready Java study guide covering syntax, object-oriented programming, collections, modern Java, concurrency, testing, and JVM fundamentals.

> **Version note:** Examples target Java 17+ unless noted. Java 21+ features are labeled. Compile snippets individually; each `public` class belongs in a matching `.java` file.

## Table of Contents

1. [Getting Started](#1-getting-started)
2. [Syntax, Variables, and Types](#2-syntax-variables-and-types)
3. [Operators and Control Flow](#3-operators-and-control-flow)
4. [Methods and Scope](#4-methods-and-scope)
5. [Arrays and Strings](#5-arrays-and-strings)
6. [Object-Oriented Programming](#6-object-oriented-programming)
7. [Exceptions and Resource Management](#7-exceptions-and-resource-management)
8. [Generics and Collections](#8-generics-and-collections)
9. [Functional Java and Streams](#9-functional-java-and-streams)
10. [Files, Dates, and Regular Expressions](#10-files-dates-and-regular-expressions)
11. [Concurrency](#11-concurrency)
12. [Modern Java Features](#12-modern-java-features)
13. [JVM, Memory, and Performance](#13-jvm-memory-and-performance)
14. [Testing, Build Tools, and Debugging](#14-testing-build-tools-and-debugging)
15. [JDBC and Secure Coding](#15-jdbc-and-secure-coding)
16. [Data Structures and Algorithms](#16-data-structures-and-algorithms)
17. [Common Interview Questions](#17-common-interview-questions)
18. [Practice Projects and Roadmap](#18-practice-projects-and-roadmap)
19. [Quick Reference](#19-quick-reference)

---

## 1. Getting Started

Java is a statically typed, object-oriented programming language. The JDK compiles `.java` source into JVM bytecode (`.class`), which the Java Virtual Machine executes.

- **JDK:** Development kit, including `javac` and `java`.
- **JRE:** Runtime components needed to run Java programs (modern distributions may not ship a separate JRE).
- **JVM:** Executes bytecode and manages memory.

Check your installation:

```bash
java -version
javac -version
```

Create `HelloWorld.java`:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

Run:

```bash
javac HelloWorld.java
java HelloWorld
```

Java is case-sensitive. A `public` top-level class name must match its filename. `main` is the traditional entry point; `String[] args` contains command-line arguments.

### Packages and imports

```java
package com.example.app;

import java.util.ArrayList;

public class Main {
    public static void main(String[] args) {
        ArrayList<String> names = new ArrayList<>();
        names.add("Aina");
        System.out.println(names);
    }
}
```

Save at `com/example/app/Main.java`, then from the source root:

```bash
javac com/example/app/Main.java
java com.example.app.Main
```

## 2. Syntax, Variables, and Types

```java
int age = 24;
double price = 19.99;
boolean active = true;
char grade = 'A';
String name = "Aina";
final double TAX_RATE = 0.06;
```

### Primitive types

| Type | Typical size | Example | Notes |
| --- | --- | --- | --- |
| `byte` | 8 bits | `byte b = 10;` | Signed integer |
| `short` | 16 bits | `short s = 100;` | Signed integer |
| `int` | 32 bits | `int n = 42;` | Common integer |
| `long` | 64 bits | `long n = 42L;` | Large integer |
| `float` | 32 bits | `float f = 1.5f;` | Approximate decimal |
| `double` | 64 bits | `double d = 1.5;` | Approximate decimal |
| `char` | 16 bits | `char c = 'J';` | UTF-16 code unit |
| `boolean` | JVM-dependent | `boolean ok = true;` | `true` or `false` |

Reference types include `String`, arrays, objects, and interfaces. A reference may be `null`.

```java
int count = 10;
double widened = count;        // implicit widening
int truncated = (int) 9.8;     // explicit narrowing: 9
var message = "Hello";        // local type inference (Java 10+)
```

`var` is for initialized local variables, not fields or method parameters. For currency, prefer `BigDecimal` over binary floating-point:

```java
import java.math.BigDecimal;

BigDecimal total = new BigDecimal("0.10").add(new BigDecimal("0.20"));
System.out.println(total); // 0.30
```

## 3. Operators and Control Flow

| Category | Operators |
| --- | --- |
| Arithmetic | `+ - * / %` |
| Comparison | `== != > < >= <=` |
| Logical | `&& || !` |
| Assignment | `= += -= *= /=` |
| Increment | `++ --` |
| Conditional | `condition ? a : b` |

Integer division truncates toward zero: `7 / 2` is `3`. `&&` and `||` short-circuit.

### `if`, `else if`, `else`

```java
int score = 78;
if (score >= 90) {
    System.out.println("A");
} else if (score >= 70) {
    System.out.println("B");
} else {
    System.out.println("Keep practicing");
}
```

### Switch expression (Java 14+)

```java
String day = "MON";
String category = switch (day) {
    case "SAT", "SUN" -> "Weekend";
    case "MON", "TUE", "WED", "THU", "FRI" -> "Weekday";
    default -> "Unknown";
};
```

### Loops

```java
for (int i = 1; i <= 3; i++) {
    System.out.println(i);
}

int n = 3;
while (n > 0) {
    System.out.println(n--);
}

do {
    System.out.println("Runs at least once");
} while (false);

for (String fruit : new String[] {"Apple", "Mango"}) {
    System.out.println(fruit);
}
```

- `break` exits a loop or switch.
- `continue` skips to the next loop iteration.
- Use `while` when repetition depends on a condition; use `for` when iteration is naturally counted.

## 4. Methods and Scope

```java
public class Calculator {
    static int add(int a, int b) {
        return a + b;
    }

    static int add(int a, int b, int c) { // overloading
        return a + b + c;
    }

    static int sum(int... values) { // varargs
        int total = 0;
        for (int value : values) total += value;
        return total;
    }

    public static void main(String[] args) {
        System.out.println(add(2, 3));
        System.out.println(sum(1, 2, 3));
    }
}
```

- **Parameters** are declared in a method; **arguments** are supplied when calling it.
- **Local variables** exist within their scope; **fields** belong to a class or instance.
- Java always passes arguments **by value**. For objects, the copied value is a reference; methods can mutate the referenced object but cannot reassign the caller's variable.
- **Recursion** requires a base case:

```java
static long factorial(int n) {
    if (n < 0) throw new IllegalArgumentException("n must be nonnegative");
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

This implementation overflows `long` for sufficiently large inputs.

## 5. Arrays and Strings

### Arrays

```java
int[] scores = {80, 90, 75};
System.out.println(scores.length); // 3
System.out.println(scores[0]);     // 80

int[][] matrix = {{1, 2}, {3, 4}};
System.out.println(matrix[1][0]); // 3
```

Arrays have fixed length. Invalid indexes throw `ArrayIndexOutOfBoundsException`.

### Strings

```java
String text = " Java Programming ";
System.out.println(text.trim());
System.out.println(text.toUpperCase());
System.out.println(text.contains("Java"));
System.out.println(text.substring(1, 5));

String a = new String("hello");
String b = "hello";
System.out.println(a.equals(b)); // true: content
System.out.println(a == b);      // false here: identity
```

`String` is immutable. Use `StringBuilder` for repeated concatenation:

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 3; i++) sb.append(i);
System.out.println(sb); // 012
```

## 6. Object-Oriented Programming

**Four common OOP principles:** encapsulation, inheritance, polymorphism, and abstraction.

### Classes, objects, constructors, encapsulation

```java
class BankAccount {
    private final String owner;
    private double balance;

    BankAccount(String owner, double openingBalance) {
        if (openingBalance < 0) throw new IllegalArgumentException();
        this.owner = owner;
        this.balance = openingBalance;
    }

    public void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException();
        balance += amount;
    }

    public double getBalance() { return balance; }
    public String getOwner() { return owner; }
}
```

For production financial software, represent money with `BigDecimal` or integer minor units rather than `double`.

### Inheritance and overriding

```java
class Animal {
    void speak() { System.out.println("Some sound"); }
}

class Dog extends Animal {
    @Override
    void speak() { System.out.println("Woof"); }
}

class Demo {
    public static void main(String[] args) {
        Animal pet = new Dog(); // polymorphism
        pet.speak();            // Woof
    }
}
```

### Abstract classes and interfaces

```java
interface Payable {
    double calculatePay();
}

abstract class Employee implements Payable {
    protected final String name;
    protected Employee(String name) { this.name = name; }
}

class SalariedEmployee extends Employee {
    private final double salary;
    SalariedEmployee(String name, double salary) {
        super(name);
        this.salary = salary;
    }
    @Override public double calculatePay() { return salary; }
}
```

| Concept | Meaning |
| --- | --- |
| Encapsulation | Hide internal state behind methods |
| Inheritance | `extends` reuses/specializes a superclass |
| Polymorphism | Supertype reference invokes subtype behavior |
| Abstraction | Expose essential contract, hide implementation |
| Overloading | Same method name, different parameter lists |
| Overriding | Subclass supplies a new inherited method implementation |

**Access modifiers:** `private` (class), package-private/no modifier (package), `protected` (package plus qualifying subclasses), `public` (accessible wherever type/module rules allow).

**Other keywords:** `static` belongs to class; `final` prevents reassignment/overriding/inheritance depending on context; `this` refers to current object; `super` refers to superclass behavior.

### `equals`, `hashCode`, and `toString`

Override `equals` and `hashCode` together when instances are compared by logical value. Equal objects must have equal hash codes. Prefer records for simple immutable data carriers.

### Composition over inheritance

A `Car` **has an** `Engine` (composition); a `Dog` **is an** `Animal` (inheritance). Composition often yields more flexible designs.

## 7. Exceptions and Resource Management

```java
try {
    int result = Integer.parseInt("42");
    System.out.println(result);
} catch (NumberFormatException e) {
    System.err.println("Invalid number: " + e.getMessage());
} finally {
    System.out.println("Finished");
}
```

- **Checked exceptions:** Must be caught or declared, e.g. `IOException`.
- **Unchecked exceptions:** Subclasses of `RuntimeException`, e.g. `NullPointerException`.
- **Errors:** Serious JVM-level problems; generally not handled as application exceptions.

### Custom exception

```java
class InvalidAgeException extends RuntimeException {
    InvalidAgeException(String message) { super(message); }
}
```

### Try-with-resources

```java
import java.io.BufferedReader;
import java.nio.file.Files;
import java.nio.file.Path;

try (BufferedReader reader = Files.newBufferedReader(Path.of("notes.txt"))) {
    System.out.println(reader.readLine());
} catch (java.io.IOException e) {
    System.err.println("Could not read file: " + e.getMessage());
}
```

Resources implementing `AutoCloseable` are closed automatically. Catch specific exceptions; avoid swallowing failures silently.

## 8. Generics and Collections

### Collections overview

| Interface / Type | Behavior | Typical implementation |
| --- | --- | --- |
| `List` | Ordered, duplicates allowed | `ArrayList`, `LinkedList` |
| `Set` | Unique elements | `HashSet`, `TreeSet` |
| `Queue` | Processing order | `ArrayDeque`, `PriorityQueue` |
| `Deque` | Both ends | `ArrayDeque` |
| `Map` | Key/value pairs, unique keys | `HashMap`, `TreeMap` |

```java
import java.util.*;

List<String> names = new ArrayList<>();
names.add("Aina");
names.add("Ali");

Set<String> unique = new HashSet<>(names);
Map<String, Integer> ages = new HashMap<>();
ages.put("Aina", 24);

for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

### Sorting and comparators

```java
List<Integer> nums = new ArrayList<>(List.of(4, 1, 3));
nums.sort(Comparator.naturalOrder());
nums.sort(Comparator.reverseOrder());
```

### Generics

```java
class Box<T> {
    private T value;
    void set(T value) { this.value = value; }
    T get() { return value; }
}

Box<String> box = new Box<>();
box.set("Java");
System.out.println(box.get());
```

Generics provide compile-time type safety. Due to **type erasure**, most generic type parameters are not available at runtime.

### Wildcards: PECS

```java
static double total(List<? extends Number> numbers) { // producer
    double sum = 0;
    for (Number n : numbers) sum += n.doubleValue();
    return sum;
}

static void addDefaults(List<? super Integer> values) { // consumer
    values.add(0);
    values.add(1);
}
```

**PECS:** Producer `extends`, Consumer `super`. `List<Integer>` is **not** a subtype of `List<Number>`.

### Complexity intuition

- `ArrayList.get(index)`: O(1); insert/remove in middle: O(n).
- `HashMap` lookup: O(1) average, potentially worse in pathological cases.
- `TreeMap` lookup: O(log n).
- `HashSet` membership: O(1) average.

## 9. Functional Java and Streams

### Lambda expressions and functional interfaces

```java
import java.util.function.Predicate;

Predicate<Integer> isEven = n -> n % 2 == 0;
System.out.println(isEven.test(8)); // true
```

Common interfaces: `Predicate<T>` (boolean test), `Function<T,R>` (transform), `Consumer<T>` (action), `Supplier<T>` (provide value).

### Stream pipeline

```java
import java.util.List;

List<String> names = List.of("Aina", "Ali", "Amir", "Sara");
List<String> result = names.stream()
        .filter(name -> name.startsWith("A"))
        .map(String::toUpperCase)
        .sorted()
        .toList(); // Java 16+
System.out.println(result);
```

- **Intermediate:** `filter`, `map`, `flatMap`, `sorted`, `distinct` (lazy).
- **Terminal:** `toList`, `collect`, `count`, `reduce`, `forEach`.
- A stream cannot be reused after a terminal operation.

### Grouping

```java
import java.util.*;
import java.util.stream.Collectors;

List<String> words = List.of("cat", "dog", "bird", "ant");
Map<Integer, List<String>> byLength = words.stream()
        .collect(Collectors.groupingBy(String::length));
System.out.println(byLength);
```

### Optional

```java
import java.util.Optional;

Optional<String> nickname = Optional.ofNullable(null);
System.out.println(nickname.orElse("Guest"));
```

`Optional` represents a potentially absent result; avoid calling `get()` without checking presence.

## 10. Files, Dates, and Regular Expressions

### Read/write UTF-8 text

```java
import java.nio.file.*;
import java.nio.charset.StandardCharsets;
import java.io.IOException;

class FileExample {
    public static void main(String[] args) throws IOException {
        Path path = Path.of("notes.txt");
        Files.writeString(path, "Study Java", StandardCharsets.UTF_8);
        String text = Files.readString(path, StandardCharsets.UTF_8);
        System.out.println(text);
    }
}
```

### Modern date/time API

```java
import java.time.*;
import java.time.format.DateTimeFormatter;

LocalDate today = LocalDate.now();
LocalDate nextWeek = today.plusWeeks(1);
ZonedDateTime kl = ZonedDateTime.now(ZoneId.of("Asia/Kuala_Lumpur"));
System.out.println(nextWeek.format(DateTimeFormatter.ISO_LOCAL_DATE));
System.out.println(kl);
```

Use `Instant` for timestamps, `LocalDate` for calendar dates, `Duration` for elapsed time, and `Period` for calendar-based differences.

### Regular expressions

```java
import java.util.regex.Pattern;

boolean valid = Pattern.matches("[A-Za-z]+\\d{2}", "Java21");
System.out.println(valid); // true
```

Regex in Java strings needs doubled backslashes (`\\d`).

## 11. Concurrency

### Threads and executors

```java
import java.util.concurrent.*;

class ConcurrentDemo {
    public static void main(String[] args) throws Exception {
        ExecutorService pool = Executors.newFixedThreadPool(2);
        try {
            Future<Integer> future = pool.submit(() -> 21 * 2);
            System.out.println(future.get()); // waits for result
        } finally {
            pool.shutdown();
        }
    }
}
```

### Shared mutable state

```java
import java.util.concurrent.atomic.AtomicInteger;

AtomicInteger counter = new AtomicInteger();
counter.incrementAndGet(); // atomic increment
```

- **Race condition:** Result depends on unsynchronized interleavings.
- **`synchronized`:** Mutual exclusion and visibility guarantees.
- **`volatile`:** Visibility/order guarantees for a field, but does **not** make `count++` atomic.
- **Atomic classes:** Efficient atomic operations on individual variables.
- **`ConcurrentHashMap`:** Concurrent map access.
- **Deadlock:** Threads wait indefinitely for locks held by one another; consistent lock ordering helps avoid it.

### CompletableFuture

```java
import java.util.concurrent.CompletableFuture;

CompletableFuture<Integer> answer = CompletableFuture
        .supplyAsync(() -> 20 + 22)
        .thenApply(n -> n * 2);
System.out.println(answer.join()); // 84
```

### Virtual threads (Java 21+)

```java
import java.util.concurrent.Executors;

try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    var task = executor.submit(() -> "Hello from a virtual thread");
    System.out.println(task.get());
}
```

Virtual threads are useful for high-concurrency blocking I/O workloads; they do not make CPU-bound computation inherently faster.

## 12. Modern Java Features

### Records (Java 16+)

```java
record Person(String name, int age) {}

Person person = new Person("Aina", 24);
System.out.println(person.name());
```

Records automatically supply a constructor, accessors, `equals`, `hashCode`, and `toString`. Their components are final, but referenced objects can still be mutable.

### Text blocks (Java 15+)

```java
String json = """
        {
          "language": "Java",
          "level": "beginner"
        }
        """;
```

### Sealed classes (Java 17+)

```java
sealed interface Shape permits Circle, Rectangle {}
record Circle(double radius) implements Shape {}
record Rectangle(double width, double height) implements Shape {}
```

### Pattern matching for `instanceof` (Java 16+)

```java
Object value = "Java";
if (value instanceof String text) {
    System.out.println(text.length());
}
```

### Pattern matching in switch (Java 21+)

```java
static String describe(Object value) {
    return switch (value) {
        case null -> "null";
        case Integer n -> "integer " + n;
        case String s -> "text " + s;
        default -> "other";
    };
}
```

### Modules (Java 9+)

A `module-info.java` file declares module dependencies and exported packages:

```java
module com.example.app {
    requires java.sql;
    exports com.example.app.api;
}
```

## 13. JVM, Memory, and Performance

Java source is compiled into bytecode. The JVM interprets and JIT-compiles frequently executed code.

- **Heap:** Objects and arrays; managed by garbage collection.
- **Thread stacks:** Stack frames, local variables, method invocation state.
- **Metaspace:** Class metadata (HotSpot JVM).
- **Garbage collector:** Reclaims objects that are no longer reachable.
- **Memory leak:** References unintentionally retain objects, preventing collection.

### Useful commands

```bash
javac Main.java
java Main
java -Xms256m -Xmx1g Main
jps
jstack <PID>
```

The last two commands are available with suitable JDK tooling; replace `<PID>` with an actual process ID.

### Performance guidance

1. Measure first using a profiler or Java Flight Recorder (JFR).
2. Choose appropriate data structures and algorithms.
3. Avoid unnecessary allocations and expensive I/O in hot loops.
4. Prefer clear code until profiling demonstrates a bottleneck.
5. Benchmark with JMH rather than naive `System.nanoTime()` loops for reliable microbenchmarks.

## 14. Testing, Build Tools, and Debugging

### Unit testing with JUnit 5

```java
import static org.junit.jupiter.api.Assertions.assertEquals;
import org.junit.jupiter.api.Test;

class CalculatorTest {
    @Test
    void addsTwoNumbers() {
        assertEquals(5, 2 + 3);
    }
}
```

JUnit must be added as a test dependency through Maven/Gradle or your IDE.

### Maven structure

```text
my-app/
├── pom.xml
└── src/
    ├── main/java/com/example/App.java
    └── test/java/com/example/AppTest.java
```

Typical Maven commands:

```bash
mvn test
mvn package
mvn clean verify
```

Typical Gradle commands (when the project includes a wrapper):

```bash
./gradlew test
./gradlew build
```

### Debugging checklist

- Read the **first relevant exception** and stack-trace location.
- Reproduce with the smallest possible input.
- Set breakpoints and inspect variable values.
- Add targeted logging rather than printing sensitive data.
- Write a regression test after fixing the bug.

### Clean code and SOLID

- **S:** Single Responsibility Principle.
- **O:** Open/Closed Principle.
- **L:** Liskov Substitution Principle.
- **I:** Interface Segregation Principle.
- **D:** Dependency Inversion Principle.

Prefer descriptive names, short focused methods, immutable state where practical, and dependency injection instead of hard-coded dependencies.

## 15. JDBC and Secure Coding

JDBC provides database access using drivers and SQL.

```java
import java.sql.*;

class DatabaseExample {
    static void printUser(Connection connection, int userId) throws SQLException {
        String sql = "SELECT name FROM users WHERE id = ?";
        try (PreparedStatement stmt = connection.prepareStatement(sql)) {
            stmt.setInt(1, userId);
            try (ResultSet rs = stmt.executeQuery()) {
                while (rs.next()) {
                    System.out.println(rs.getString("name"));
                }
            }
        }
    }
}
```

`Connection` must be supplied by configured application/database infrastructure. `PreparedStatement` parameters protect values against SQL injection; never concatenate untrusted input into SQL. For dynamic identifiers such as column names, use a strict allowlist.

### Security basics

- Validate input and enforce authorization server-side.
- Never commit passwords, tokens, or API keys to Git.
- Use environment variables or a secret manager for credentials.
- Hash passwords with a suitable password-hashing algorithm (e.g. Argon2id, bcrypt), not plain SHA-256.
- Keep libraries and the JDK patched.
- Avoid deserializing untrusted Java serialized objects.
- Apply least privilege to databases and cloud services.

## 16. Data Structures and Algorithms

### Big-O cheat sheet

| Complexity | Example |
| --- | --- |
| O(1) | Array indexed access |
| O(log n) | Binary search on sorted data |
| O(n) | Linear scan |
| O(n log n) | Efficient comparison sorting |
| O(n²) | Nested pair comparisons |
| O(2ⁿ) | Exhaustive subset enumeration |

### Binary search

```java
static int binarySearch(int[] a, int target) {
    int left = 0, right = a.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (a[mid] == target) return mid;
        if (a[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

**Precondition:** The array must be sorted in ascending order.

### Stack and queue

```java
import java.util.ArrayDeque;
import java.util.Deque;

Deque<Integer> stack = new ArrayDeque<>();
stack.push(10);
System.out.println(stack.pop());

Deque<Integer> queue = new ArrayDeque<>();
queue.addLast(10);
System.out.println(queue.removeFirst());
```

### Common algorithm topics

- Two pointers and sliding windows.
- Hash-based counting and lookup.
- Recursion and backtracking.
- Trees: DFS, BFS, binary search trees.
- Graphs: adjacency lists, BFS, DFS, shortest paths.
- Dynamic programming: overlapping subproblems and memoization.
- Sorting, binary search, heaps, and priority queues.

## 17. Common Interview Questions

| Question | Short answer |
| --- | --- |
| JDK vs JVM? | JDK builds/runs programs; JVM executes bytecode. |
| `==` vs `.equals()`? | Identity for object references vs logical equality (if implemented). |
| Overload vs override? | Different parameters vs subclass implementation. |
| Abstract class vs interface? | Shared base implementation/state vs contract; interfaces can also have default/static methods. |
| `ArrayList` vs `LinkedList`? | Array-backed random access vs linked nodes; `ArrayList` is often the default choice. |
| `HashMap` vs `TreeMap`? | Hash-based lookup vs sorted keys. |
| Checked vs unchecked exceptions? | Compile-time catch/declare requirement vs no such requirement. |
| `String` vs `StringBuilder`? | Immutable vs mutable text builder. |
| `final` vs `finally`? | Language modifier vs exception-handling block. |
| `volatile` vs `synchronized`? | Visibility/order vs locking plus visibility. |
| Heap vs stack? | Objects/arrays vs per-thread call frames. |
| What is garbage collection? | Automatic reclamation of unreachable objects. |
| What is dependency injection? | Supplying dependencies from outside rather than constructing them internally. |
| Why use generics? | Type safety and reusable abstractions. |
| What is a stream? | Declarative pipeline of operations over data. |

**Interview exercise:** Explain the output of this code:

```java
String x = "abc";
String y = new String("abc");
System.out.println(x == y);      // false
System.out.println(x.equals(y)); // true
```

## 18. Practice Projects and Roadmap

### Beginner

1. **Calculator CLI:** Methods, input validation, `switch`.
2. **Grade tracker:** Arrays, loops, averages, conditions.
3. **To-do list:** `ArrayList`, CRUD operations, file persistence.

### Intermediate

4. **Library management system:** OOP, inheritance/interfaces, collections, exceptions.
5. **Expense tracker:** `BigDecimal`, dates, CSV files, JUnit tests.
6. **Student management system:** JDBC, prepared statements, relational data.

### Advanced

7. **REST API with Spring Boot:** Controllers, services, persistence, validation, tests. Spring Boot is an external framework, not part of the JDK.
8. **Concurrent task processor:** Executors, queues, timeouts, thread safety.
9. **URL shortener:** API design, caching, database constraints, observability, security.

### Suggested study sequence

| Stage | Focus | Goal |
| --- | --- | --- |
| Week 1 | Syntax, types, loops, methods | Write simple CLI programs |
| Week 2 | Arrays, strings, OOP | Model real-world entities |
| Week 3 | Exceptions, collections, generics | Build a to-do or library app |
| Week 4 | Files, streams, dates, testing | Persist and test data |
| Week 5 | JDBC, algorithms, Maven/Gradle | Build a database-backed CLI |
| Week 6+ | Concurrency, JVM, Spring Boot | Build and deploy a tested API |

## 19. Quick Reference

```java
// Print
System.out.println("Hello");

// Conditional
if (true) { System.out.println("Yes"); }

// Loop
for (int i = 0; i < 5; i++) { System.out.println(i); }

// List
java.util.List<String> list = new java.util.ArrayList<>();
list.add("Java");

// Map
java.util.Map<String, Integer> map = new java.util.HashMap<>();
map.put("score", 100);

// Parse
int n = Integer.parseInt("123");

// String comparison
boolean same = "java".equalsIgnoreCase("JAVA");

// Null-safe equality
boolean equal = java.util.Objects.equals(null, null);
```

### Helpful official references

- [dev.java — Learn Java](https://dev.java/learn/)
- [Java SE API Documentation](https://docs.oracle.com/en/java/javase/21/docs/api/)
- [OpenJDK](https://openjdk.org/)
- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)
- [Apache Maven](https://maven.apache.org/guides/)
- [Gradle](https://docs.gradle.org/current/userguide/userguide.html)

---

**How to use this README:** Work through the sections in order, type and run examples yourself, and turn each practice project into its own GitHub repository. Many snippets are fragments intended to be placed inside a class or method; examples with full class definitions can be compiled separately.

**License:** Add a license file if you want to explicitly permit reuse or contributions.
