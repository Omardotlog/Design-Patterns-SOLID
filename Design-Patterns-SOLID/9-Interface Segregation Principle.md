# Interface Segregation Principle

> A SOLID design principle that says clients should not be forced to depend on methods they do not use.

---

## Table of Contents

- [Note](#note)
- [Historical Note on SOLID](#historical-note-on-solid)
- [Definition](#1-definition)
- [Problem](#2-problem)
- [Solution](#3-solution)
- [Structure](#4-structure)
- [Applicability](#5-applicability)
- [How to Implement](#6-how-to-implement)
- [Pros and Cons](#7-pros-and-cons)
- [Example in Test Automation](#8-example-in-test-automation)
- [Interface Segregation Principle vs Single Responsibility Principle](#9-interface-segregation-principle-vs-single-responsibility-principle)
- [Interface Segregation Principle vs Liskov Substitution Principle](#10-interface-segregation-principle-vs-liskov-substitution-principle)
- [Common ISP Violations](#11-common-isp-violations)
- [Summary](#summary)
- [References](#references)

---

## Note

The **Interface Segregation Principle (ISP)** is the **I** in **SOLID**.

It is **not a GoF design pattern**. It is an object-oriented design principle that helps keep interfaces small, focused, and client-specific.

In simple words:

> Do not make a class or client depend on methods it does not need.

This is especially important in Java, C#, and other statically typed languages, because depending on a large interface can force unnecessary recompilation, redeployment, and maintenance when unrelated methods change.

---

## Historical Note on SOLID

The **SOLID principles** are most commonly associated with **Robert C. Martin**, also known as **Uncle Bob**, because he collected, explained, and popularized these object-oriented design principles.

However, it is more accurate to say:

- **Robert C. Martin** collected, explained, and popularized the five principles as a practical object-oriented design set.
- **Michael Feathers** is commonly credited with arranging/coining the acronym **SOLID**.
- **Bertrand Meyer** is credited with the original **Open/Closed Principle**.
- **Barbara Liskov** is credited with the original substitution idea behind **Liskov Substitution Principle**.
- **Robert C. Martin** is credited with the **Interface Segregation Principle**, commonly connected to his Xerox printer-system example and his 1996 C++ Report article.

So, avoid saying:

> Robert C. Martin invented SOLID.

A better sentence is:

> The SOLID principles are commonly associated with Robert C. Martin, who collected and popularized them, while the acronym SOLID is commonly credited to Michael Feathers.

---

## 1. Definition

The **Interface Segregation Principle** states that:

> **Clients should not be forced to depend on interfaces they do not use.**

In simple words:

> Do not create one large interface that forces classes to implement methods they do not need.

A better design uses smaller, focused interfaces based on client needs or capabilities.

---

## 2. Problem

A class violates ISP when it is forced to implement methods that do not belong to it.

### Bad Example

```java
public interface Worker {
    void work();
    void eat();
    void sleep();
}
```

```java
public class HumanWorker implements Worker {

    @Override
    public void work() {
        System.out.println("Human is working");
    }

    @Override
    public void eat() {
        System.out.println("Human is eating");
    }

    @Override
    public void sleep() {
        System.out.println("Human is sleeping");
    }
}
```

```java
public class RobotWorker implements Worker {

    @Override
    public void work() {
        System.out.println("Robot is working");
    }

    @Override
    public void eat() {
        throw new UnsupportedOperationException("Robot does not eat");
    }

    @Override
    public void sleep() {
        throw new UnsupportedOperationException("Robot does not sleep");
    }
}
```

### What Is Wrong Here?

The `RobotWorker` class is forced to implement `eat()` and `sleep()`, even though robots do not need those behaviors.

This creates several problems:

- The interface is too large.
- Classes depend on methods they do not use.
- Some methods throw unsupported exceptions.
- The design becomes harder to maintain.
- Changes to one part of the interface can affect unrelated classes.
- The interface mixes unrelated capabilities into one contract.

This is a classic ISP violation.

---

## 3. Solution

The solution is to split large interfaces into smaller, more focused interfaces.

### Better Design

```java
public interface Workable {
    void work();
}
```

```java
public interface Eatable {
    void eat();
}
```

```java
public interface Sleepable {
    void sleep();
}
```

```java
public class HumanWorker implements Workable, Eatable, Sleepable {

    @Override
    public void work() {
        System.out.println("Human is working");
    }

    @Override
    public void eat() {
        System.out.println("Human is eating");
    }

    @Override
    public void sleep() {
        System.out.println("Human is sleeping");
    }
}
```

```java
public class RobotWorker implements Workable {

    @Override
    public void work() {
        System.out.println("Robot is working");
    }
}
```

Now each class implements only the interfaces it actually needs.

| Class | Interfaces Implemented |
|---|---|
| `HumanWorker` | `Workable`, `Eatable`, `Sleepable` |
| `RobotWorker` | `Workable` |

This follows ISP because the client or class depends only on the behavior it actually uses.

---

## 4. Structure

The Interface Segregation Principle usually leads to this structure:

### Large Interface

A large interface contains too many unrelated methods.

Example:

```java
Worker
```

### Small Focused Interfaces

Smaller interfaces represent specific capabilities.

Examples:

```java
Workable
Eatable
Sleepable
```

### Implementing Classes

Each class implements only the interfaces that match its behavior.

Examples:

```java
HumanWorker implements Workable, Eatable, Sleepable
RobotWorker implements Workable
```

### Client Code

Client code depends on the smallest interface it needs.

Example:

```java
WorkScheduler depends on Workable
MealService depends on Eatable
```

---

## Bad Design Diagram

```mermaid
classDiagram
    class Worker {
        <<interface>>
        +work()
        +eat()
        +sleep()
    }

    class HumanWorker {
        +work()
        +eat()
        +sleep()
    }

    class RobotWorker {
        +work()
        +eat()
        +sleep()
    }

    Worker <|.. HumanWorker
    Worker <|.. RobotWorker
```

### Why This Is Bad

```mermaid
flowchart TD
    A[Worker Interface] --> B[work()]
    A --> C[eat()]
    A --> D[sleep()]
    E[RobotWorker] --> B
    E --> C
    E --> D
    C --> F[Unsupported Method]
    D --> G[Unsupported Method]
    F --> H[ISP Violation]
    G --> H
```

---

## Good Design Diagram

```mermaid
classDiagram
    class Workable {
        <<interface>>
        +work()
    }

    class Eatable {
        <<interface>>
        +eat()
    }

    class Sleepable {
        <<interface>>
        +sleep()
    }

    class HumanWorker {
        +work()
        +eat()
        +sleep()
    }

    class RobotWorker {
        +work()
    }

    Workable <|.. HumanWorker
    Eatable <|.. HumanWorker
    Sleepable <|.. HumanWorker
    Workable <|.. RobotWorker
```

---

## Dependency Flow Diagram

```mermaid
flowchart LR
    A[Client Needs Work Behavior] --> B[Depends on Workable Only]
    C[Client Needs Eating Behavior] --> D[Depends on Eatable Only]
    E[Client Needs Sleeping Behavior] --> F[Depends on Sleepable Only]
    B --> G[No Unused Dependencies]
    D --> G
    F --> G
```

---

## 5. Applicability

Use the Interface Segregation Principle when:

- An interface has too many methods.
- Classes implement methods they do not use.
- Some implementations throw `UnsupportedOperationException`.
- Some methods have empty bodies because they are not supported.
- One interface is used by many unrelated clients.
- Changes to one method affect many classes unnecessarily.
- A class depends on behavior it does not need.
- You want cleaner, more focused abstractions.
- You want to avoid “fat interfaces.”

ISP is especially useful in large systems because interfaces often grow over time as new features are added.

---

## 6. How to Implement

To implement the Interface Segregation Principle:

1. Review interfaces that contain many methods.
2. Identify which clients use which methods.
3. Group related methods together.
4. Split the large interface into smaller focused interfaces.
5. Let each class implement only the interfaces it needs.
6. Update client code to depend on the smallest suitable interface.
7. Avoid forcing classes to provide empty methods.
8. Avoid forcing classes to throw unsupported exceptions.
9. Keep interface names clear and capability-based.
10. Do not over-split interfaces when the methods naturally belong together.

A useful check is:

> “Does every class that implements this interface actually need every method?”

If the answer is no, the interface may need to be split.

---

## Implementation Steps Diagram

```mermaid
flowchart TD
    A[Start with Large Interface] --> B[List All Methods]
    B --> C[Identify Different Client Needs]
    C --> D[Group Related Methods]
    D --> E[Create Smaller Interfaces]
    E --> F[Update Classes to Implement Needed Interfaces Only]
    F --> G[Clients Depend on Focused Interfaces]
    G --> H[ISP Followed]
```

---

## 7. Pros and Cons

### ✅ Pros

- Reduces unnecessary dependencies.
- Makes interfaces easier to understand.
- Prevents classes from implementing unused methods.
- Improves maintainability.
- Reduces side effects when interfaces change.
- Supports cleaner architecture.
- Helps classes follow the Single Responsibility Principle.
- Makes testing easier because clients depend on smaller contracts.
- Reduces unsupported or empty method implementations.

### ❌ Cons

- Can increase the number of interfaces.
- Can make the design feel more complex at first.
- Poorly named small interfaces can confuse developers.
- Too much splitting can create unnecessary abstraction.
- Requires careful understanding of client needs.
- Existing code may need refactoring when a large interface is split.

ISP does not mean every method needs its own interface.

The goal is useful separation based on real client needs, not unnecessary complexity.

---

## 8. Example in Test Automation

ISP is very useful in Selenium automation frameworks, especially when different pages or components support different actions.

### Bad Example

```java
public interface PageActions {
    void open();
    void login(String email, String password);
    void logout();
    void addToCart();
    void checkout();
    void uploadFile(String filePath);
}
```

This interface is too broad.

A `LoginPage` may need `open()` and `login()`, but not `addToCart()` or `checkout()`.

A `ProductPage` may need `open()` and `addToCart()`, but not `uploadFile()`.

A `ProfilePage` may need `open()` and `uploadFile()`, but not `checkout()`.

This design pushes unrelated behavior into one interface.

---

### Better Design

```java
public interface OpenablePage {
    void open();
}
```

```java
public interface LoginCapable {
    void login(String email, String password);
}
```

```java
public interface CartCapable {
    void addToCart();
}
```

```java
public interface CheckoutCapable {
    void checkout();
}
```

```java
public interface FileUploadCapable {
    void uploadFile(String filePath);
}
```

```java
public class LoginPage implements OpenablePage, LoginCapable {

    private WebDriver driver;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    @Override
    public void open() {
        driver.get("https://automationexercise.com/login");
    }

    @Override
    public void login(String email, String password) {
        System.out.println("Login with email and password");
    }
}
```

```java
public class ProductPage implements OpenablePage, CartCapable {

    private WebDriver driver;

    public ProductPage(WebDriver driver) {
        this.driver = driver;
    }

    @Override
    public void open() {
        driver.get("https://automationexercise.com/products");
    }

    @Override
    public void addToCart() {
        System.out.println("Add product to cart");
    }
}
```

```java
public class ProfilePage implements OpenablePage, FileUploadCapable {

    private WebDriver driver;

    public ProfilePage(WebDriver driver) {
        this.driver = driver;
    }

    @Override
    public void open() {
        driver.get("https://automationexercise.com/profile");
    }

    @Override
    public void uploadFile(String filePath) {
        System.out.println("Upload profile image: " + filePath);
    }
}
```

Now each page class implements only the behavior it supports.

---

### TestNG Example

```java
import org.testng.annotations.Test;

public class LoginTest extends BaseTest {

    @Test
    public void userCanLogin() {
        LoginPage loginPage = new LoginPage(driver);

        loginPage.open();
        loginPage.login("user@test.com", "123456");
    }
}
```

```java
import org.testng.annotations.Test;

public class ProductTest extends BaseTest {

    @Test
    public void userCanAddProductToCart() {
        ProductPage productPage = new ProductPage(driver);

        productPage.open();
        productPage.addToCart();
    }
}
```

This is cleaner because the test only sees the behavior that belongs to that page.

---

## 9. Interface Segregation Principle vs Single Responsibility Principle

| Point | Interface Segregation Principle | Single Responsibility Principle |
|---|---|---|
| SOLID Letter | I | S |
| Main focus | Interfaces | Classes/modules |
| Main idea | Do not force clients to depend on unused methods | A class should have one reason to change |
| Common problem | Fat interfaces | God classes |
| Common solution | Split interfaces by client need | Split classes by responsibility |
| Example | `Workable`, `Eatable`, `Sleepable` | `Employee`, `PayrollService`, `EmployeeRepository` |

---

## 10. Interface Segregation Principle vs Liskov Substitution Principle

| Point | Interface Segregation Principle | Liskov Substitution Principle |
|---|---|---|
| Main focus | Interface size and client needs | Correct substitution of subtypes |
| Main question | “Does this client need every method?” | “Can this subtype replace the parent safely?” |
| Common symptom | Empty or unsupported methods | Subtype breaks expected behavior |
| Common fix | Split interfaces | Use better abstraction or composition |
| Example | Robot should not implement `eat()` | Read-only page should not inherit `clickSave()` |

ISP and LSP often support each other.

When an interface is too large, classes may be forced to implement unsupported behavior. That can also create LSP violations because clients expect behavior that some implementations cannot honestly provide.

---

## 11. Common ISP Violations

### 1. Fat Interface

```java
public interface Machine {
    void print();
    void scan();
    void fax();
    void staple();
}
```

A simple printer may only support `print()`, so it should not be forced to implement `scan()`, `fax()`, or `staple()`.

---

### 2. Empty Method Implementation

```java
public class SimplePrinter implements Machine {

    @Override
    public void print() {
        System.out.println("Printing");
    }

    @Override
    public void scan() {
        // Not supported
    }

    @Override
    public void fax() {
        // Not supported
    }

    @Override
    public void staple() {
        // Not supported
    }
}
```

---

### 3. Unsupported Exception

```java
@Override
public void fax() {
    throw new UnsupportedOperationException("Fax not supported");
}
```

This is a strong sign that the interface is too broad.

---

## Better Printer Design

```java
public interface Printable {
    void print();
}
```

```java
public interface Scannable {
    void scan();
}
```

```java
public interface Faxable {
    void fax();
}
```

```java
public class SimplePrinter implements Printable {

    @Override
    public void print() {
        System.out.println("Printing document");
    }
}
```

```java
public class MultiFunctionPrinter implements Printable, Scannable, Faxable {

    @Override
    public void print() {
        System.out.println("Printing document");
    }

    @Override
    public void scan() {
        System.out.println("Scanning document");
    }

    @Override
    public void fax() {
        System.out.println("Faxing document");
    }
}
```

This design is cleaner because each device implements only the capabilities it actually supports.

---

## Summary

The **Interface Segregation Principle** says that clients should not be forced to depend on methods they do not use.

Instead of creating large general-purpose interfaces, create smaller, focused interfaces based on real client needs.

ISP reduces unnecessary dependencies, avoids unsupported methods, and makes software easier to maintain and extend. In Selenium automation frameworks, it helps page objects and components expose only the actions they truly support.

---

## References

| Source | Link |
|---|---|
| Microsoft Learn — SOLID principles and application-layer design | https://learn.microsoft.com/en-us/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/microservice-application-layer-web-api-design |
| Robert C. Martin — SOLID Relevance | https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html |
| Robert C. Martin — Design Principles and Design Patterns | https://www.fil.univ-lille.fr/~routier/enseignement/licence/coo/cours/Principles_and_Patterns.pdf |
| Robert C. Martin — Principles of Object-Oriented Design | https://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod |
| InfoQ Podcast — Uncle Bob Martin on Origins of SOLID | https://www.infoq.com/podcasts/uncle-bob-solid-ddd/ |
| Baeldung — Interface Segregation Principle in Java | https://www.baeldung.com/java-interface-segregation |
