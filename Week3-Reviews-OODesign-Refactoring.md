# Week 3: Code Reviews, OO Design & Refactoring 代码审查、面向对象设计与重构

> **Course**: Software Engineering
> **Coverage**: Slides 05a (Reviews), 05b (OO Design), 05c (Design Principles), 06 (Refactoring) + Parnas "On the criteria to be used in decomposing systems into modules"
> **Prerequisite knowledge**: [[Week1-Software-Engineering-Introduction]] → [[Week2-Software-Design]]

---

## Table of Contents

- [[#1. Code Reviews 代码审查]]
- [[#2. Static Analysis 静态分析]]
- [[#3. OO Design Fundamentals 面向对象设计基础]]
- [[#4. GRASP Patterns GRASP模式]]
- [[#5. Design Principles Deep Dive 设计原则深入]]
- [[#6. Design by Contract 契约式设计]]
- [[#7. Module Decomposition 模块分解 (Parnas)]]
- [[#8. Refactoring 重构]]
- [[#9. Code Smells 代码异味 (Detailed)]]
- [[#10. Refactoring Catalog 重构目录]]
- [[#Exam Quick Review 考试速览]]

---

## 1. Code Reviews 代码审查

### What is Code Review? 什么是代码审查？

Code Review 是指**其他开发者在代码合并前检查你的代码**的过程。这是一种质量保证（QA）手段，不是为了"挑毛病"，而是为了：

- **找 Bug 和潜在问题** — 在 production 之前发现问题
- **确保代码质量** — readability, maintainability
- **知识共享** — team members 互相学习
- **保持一致性** — coding standards, architectural consistency

> 💡 **Key insight**: Code review is one of the most cost-effective ways to find defects. Studies show it catches **60-90% of defects** before testing.

### Types of Code Reviews 审查类型

| Type | 描述 | Formality | 效果 |
|------|------|-----------|------|
| **Formal Inspection** 正式审查 | Structured process with defined roles (moderator, reader, reviewer, author) | 最高 | Very effective but expensive |
| **Walkthrough** 走查 | Author guides reviewers through the code | 中等 | Good for complex logic |
| **Lightweight Review** 轻量审查 | Informal, usually tool-assisted (e.g., GitHub PR review) | 低 | Most common in industry |
| **Pair Programming** 结对编程 | Two developers work together in real-time | 实时 | Continuous review |

### Formal Inspection Process (Fagan Inspection)

```
Planning → Overview → Preparation → Inspection Meeting → Rework → Follow-up
   ↓          ↓           ↓              ↓                ↓          ↓
选材料    作者讲解    各自阅读代码    集中讨论缺陷      修复       确认修复
```

**Roles in Formal Inspection**:
- **Moderator** 主持人: 管理流程，确保效率
- **Author** 作者: 写代码的人，回答问题但不辩护
- **Reader** 朗读者: 逐行解释代码逻辑
- **Reviewer/Inspector** 审查者: 找缺陷
- **Recorder** 记录者: 记录发现的问题

### Pull Request (PR) Based Review — 最常用的方式

PR Review 是现代软件开发中最常见的 code review 方式，结合了 [[Week1-Software-Engineering-Introduction#10. Git Version Control 版本控制|Git]] workflow:

```
Developer creates branch → Makes changes → Opens PR →
Reviewers comment → Author addresses feedback →
Approved → Merged into main branch
```

**Good PR Practices**:
- **Small PRs** — 越小越好，easier to review (ideally < 400 lines)
- **Clear description** — 说明 what and why
- **Self-review first** — 提交前自己先 review 一遍
- **Respond to all comments** — 即使是 disagree 也要回复
- **Automate what you can** — linting, formatting, tests should run automatically

### What to Look for in Code Review 审查时关注什么

1. **Correctness 正确性** — Does the code do what it's supposed to?
2. **Design 设计** — Is it well-structured? Does it follow [[Week2-Software-Design#3. SOLID Principles SOLID原则|SOLID principles]]?
3. **Readability 可读性** — Can you understand the code easily?
4. **Test coverage 测试覆盖** — Are there sufficient tests? (See [[Week1-Software-Engineering-Introduction#11. Test-Driven Development (TDD) 测试驱动开发|TDD]])
5. **Performance 性能** — Any obvious performance issues?
6. **Security 安全性** — Any security vulnerabilities?
7. **Edge cases 边界条件** — Are edge cases handled?

### Pair Programming vs Code Review 结对编程 vs 代码审查

| Aspect | Pair Programming | Code Review |
|--------|-----------------|-------------|
| **Timing** | Real-time, during coding | After coding, before merge |
| **Knowledge sharing** | Immediate, bidirectional | Asynchronous |
| **Cost** | 2 developers full-time | Reviewer's time only |
| **Defect finding** | Continuous | Batch |
| **Best for** | Complex/critical code | All code changes |

> 💡 它们不是互斥的！很多团队两种都用：pair programming 写复杂功能，code review 确保所有代码质量。

---

## 2. Static Analysis 静态分析

### What is Static Analysis? 什么是静态分析？

Static Analysis 是**不运行代码**就分析代码的技术。与 [[Week1-Software-Engineering-Introduction#11. Test-Driven Development (TDD) 测试驱动开发|testing]]（dynamic analysis）不同，static analysis 只看代码结构。

```
Static Analysis = 分析源代码，不执行
Dynamic Analysis = 运行代码，观察行为（如 testing）
```

### Tools 常见工具

| Language | Tools |
|----------|-------|
| Java | Checkstyle, SpotBugs, PMD |
| Python | Pylint, Flake8, mypy |
| JavaScript | ESLint, JSHint |
| General | SonarQube |

### What Static Analysis Can Find

- **Style violations** — naming conventions, formatting
- **Potential bugs** — null pointer dereferences, array out of bounds
- **Code smells** — See [[#9. Code Smells 代码异味 (Detailed)]]
- **Security vulnerabilities** — SQL injection, XSS
- **Dead code** — unreachable code, unused variables
- **Complexity issues** — methods that are too complex

### Limitations 局限性

Static analysis **cannot** find:
- Logic errors（逻辑错误）
- Requirements errors（需求错误）
- Runtime-specific issues（运行时问题）
- Concurrency bugs（大部分并发问题）

> 💡 Static analysis 和 code review 是互补的：tool 找 pattern-based 问题，human 找 logic/design 问题。

---

## 3. OO Design Fundamentals 面向对象设计基础

### Core OO Concepts 核心概念回顾

在 [[Week2-Software-Design]] 中我们学了基本设计原则。现在我们深入 OO 设计。

#### Four Pillars of OOP 面向对象四大支柱

| Pillar | 中文 | Description |
|--------|------|-------------|
| **Encapsulation** | 封装 | 把数据和操作数据的方法bundled在一起，隐藏内部细节 |
| **Abstraction** | 抽象 | 只暴露essential信息，隐藏implementation details |
| **Inheritance** | 继承 | 子类继承父类的属性和方法，实现代码复用 |
| **Polymorphism** | 多态 | 同一个接口，不同的实现，"many forms" |

#### Inheritance 继承

```java
// 父类 (Superclass / Base class)
class Animal {
    String name;
    void eat() { System.out.println("eating..."); }
}

// 子类 (Subclass / Derived class)
class Dog extends Animal {
    void bark() { System.out.println("Woof!"); }
}

// Dog 继承了 Animal 的 name 和 eat()，还有自己的 bark()
Dog d = new Dog();
d.eat();   // ✅ 从 Animal 继承
d.bark();  // ✅ Dog 自己的方法
```

**Inheritance 的问题** — 为什么要谨慎使用：
- **Tight coupling** — 子类和父类紧密耦合（参见 [[Week2-Software-Design#4. Coupling and Cohesion 耦合与内聚]]）
- **Fragile base class problem** — 修改父类可能破坏子类
- **Diamond problem** — 多重继承的歧义（Java 不允许多重继承 class）

> 💡 **Favor composition over inheritance**（优先组合而非继承）— 这是现代 OO 设计的重要原则！

#### Abstract Classes vs Interfaces 抽象类 vs 接口

```java
// Abstract class — 可以有实现，也可以有抽象方法
abstract class Shape {
    String color;                          // 有状态
    abstract double area();                // 无实现，子类必须实现
    void printColor() {                    // 有实现
        System.out.println(color);
    }
}

// Interface — 只定义契约（Java 8+ 可以有 default methods）
interface Drawable {
    void draw();                           // 抽象方法
    default void reset() { /* ... */ }     // default method
}

// 一个类可以 implement 多个 interfaces
class Circle extends Shape implements Drawable {
    double radius;
    double area() { return Math.PI * radius * radius; }
    void draw() { /* draw circle */ }
}
```

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| 可以有构造器 | ✅ | ❌ |
| 可以有状态（fields） | ✅ | ❌ (only constants) |
| 可以有实现的方法 | ✅ | ✅ (default methods) |
| 多重继承 | ❌ (只能 extends 一个) | ✅ (可以 implements 多个) |
| **用途** | "is-a" + shared code | "can-do" capability |

#### Polymorphism 多态

```java
// 同一个方法调用，不同的对象有不同的行为
Shape s1 = new Circle(5);
Shape s2 = new Rectangle(3, 4);

// 虽然都是 Shape 类型，但调用的是各自子类的 area()
s1.area(); // → 78.54 (Circle's area)
s2.area(); // → 12.0  (Rectangle's area)
```

**Polymorphism 的好处**：
- 可以用统一接口处理不同类型 → 符合 [[Week2-Software-Design#3. SOLID Principles SOLID原则|OCP (Open-Closed Principle)]]
- 新增类型不需要修改现有代码
- 减少 if-else / switch 判断

---

## 4. GRASP Patterns GRASP模式

### What is GRASP? 什么是 GRASP?

**GRASP** = **G**eneral **R**esponsibility **A**ssignment **S**oftware **P**atterns

GRASP 是一组**分配职责（responsibility）的指导原则**，帮你决定：哪个类应该负责什么？

> 💡 GRASP 不是 [[Week2-Software-Design#6. Design Patterns 设计模式|GoF Design Patterns]]（Factory, Strategy 等），而是更基础的**职责分配原则**。

### The 9 GRASP Patterns

#### 4.1 Information Expert 信息专家

> **Principle**: Assign responsibility to the class that has the **information needed** to fulfill it.
> **原则**：把职责分配给**拥有完成该职责所需信息**的类。

```java
// ❌ Bad — Order 不该自己算总价吗？
class PriceCalculator {
    double calculateTotal(Order order) {
        double total = 0;
        for (OrderItem item : order.getItems()) {
            total += item.getPrice() * item.getQuantity();
        }
        return total;
    }
}

// ✅ Good — Order 拥有 items 信息，所以它是 Information Expert
class Order {
    List<OrderItem> items;

    double calculateTotal() {
        double total = 0;
        for (OrderItem item : items) {
            total += item.getSubtotal(); // item 知道自己的小计
        }
        return total;
    }
}

class OrderItem {
    double price;
    int quantity;

    double getSubtotal() {
        return price * quantity; // OrderItem 是 subtotal 的 expert
    }
}
```

#### 4.2 Creator 创建者

> **Principle**: Class B should create instances of class A if B **contains/aggregates** A, **records** A, **closely uses** A, or **has the data to initialize** A.

```java
// Order contains OrderItems → Order is the Creator of OrderItem
class Order {
    List<OrderItem> items = new ArrayList<>();

    void addItem(Product product, int quantity) {
        OrderItem item = new OrderItem(product, quantity); // ✅ Order creates OrderItem
        items.add(item);
    }
}
```

#### 4.3 Controller 控制器

> **Principle**: Assign the responsibility of handling a **system event** to a class that represents the overall system (facade controller) or a use case scenario (use case controller).

```java
// Controller 不是 UI！它是 UI 和 domain 之间的中介
// Facade Controller — 代表整个系统
class StoreSystem {
    void processPayment(PaymentInfo info) { /* ... */ }
    void addToCart(Product p, int qty) { /* ... */ }
}

// Use Case Controller — 代表一个具体用例
class CheckoutController {
    void processCheckout(Cart cart, PaymentInfo info) { /* ... */ }
}
```

> 💡 Controller 类似 [[Week2-Software-Design#9. Architecture Patterns 架构模式|MVC]] 中的 Controller 层，是 UI 和 business logic 的桥梁。

#### 4.4 Low Coupling 低耦合

> **Principle**: Assign responsibilities so that **coupling stays low**. Reduce the impact of change.
> 参见 [[Week2-Software-Design#4. Coupling and Cohesion 耦合与内聚|Week 2 Coupling]]

**目标**：减少类之间的依赖。修改一个类时，尽量少影响其他类。

```java
// ❌ High Coupling — Payment 直接依赖具体的 StripeGateway
class Payment {
    StripeGateway gateway = new StripeGateway();
    void process() { gateway.charge(); }
}

// ✅ Low Coupling — 通过接口解耦
class Payment {
    PaymentGateway gateway;  // 依赖抽象接口
    Payment(PaymentGateway gw) { this.gateway = gw; }
    void process() { gateway.charge(); }
}
```

#### 4.5 High Cohesion 高内聚

> **Principle**: Keep classes **focused and manageable**. A class should do one set of related things.
> 参见 [[Week2-Software-Design#4. Coupling and Cohesion 耦合与内聚|Week 2 Cohesion]] 和 [[Week2-Software-Design#3. SOLID Principles SOLID原则|SRP]]

```java
// ❌ Low Cohesion — Employee 做了太多不相关的事
class Employee {
    void calculatePay() { }
    void saveToDatabase() { }
    void generateReport() { }
    void sendEmail() { }
}

// ✅ High Cohesion — 每个类只做相关的事
class Employee { void calculatePay() { } }
class EmployeeRepository { void save(Employee e) { } }
class ReportGenerator { void generate(Employee e) { } }
class EmailService { void send(String to, String msg) { } }
```

#### 4.6 Indirection 间接性

> **Principle**: Assign responsibility to an **intermediate object** to mediate between other components, reducing direct coupling.

```java
// TaxCalculator 作为中间层，解耦 Order 和各种税务规则
class Order {
    TaxCalculator taxCalc;
    double getFinalPrice() {
        return getSubtotal() + taxCalc.calculateTax(this);
    }
}
```

#### 4.7 Polymorphism 多态

> **Principle**: When behavior varies by type, assign responsibility using **polymorphic operations** instead of conditionals.

```java
// ❌ Using conditionals
double calculateShipping(String type) {
    if (type.equals("standard")) return 5.0;
    else if (type.equals("express")) return 15.0;
    else if (type.equals("overnight")) return 25.0;
    // 每加一种类型就要改这个方法 → 违反 OCP
}

// ✅ Using Polymorphism
interface ShippingStrategy {
    double calculateCost();
}
class StandardShipping implements ShippingStrategy {
    public double calculateCost() { return 5.0; }
}
class ExpressShipping implements ShippingStrategy {
    public double calculateCost() { return 15.0; }
}
// 新增类型只需新建一个类，不需改现有代码！
```

#### 4.8 Pure Fabrication 纯虚构

> **Principle**: When no existing domain class is suitable, **create an artificial class** that doesn't represent a domain concept but achieves low coupling and high cohesion.

```java
// "DatabaseAccess" 不是一个领域概念（domain concept）
// 但它帮助我们把持久化逻辑从 domain 类中分离出来
class PersistenceService {  // Pure Fabrication
    void save(Order order) { /* DB logic */ }
    Order load(int id) { /* DB logic */ }
}
```

> 💡 Pure Fabrication 经常出现在 Service 层、Repository 层、Utility 类中。

#### 4.9 Protected Variations 受保护的变化

> **Principle**: Identify points of predicted variation and create a **stable interface** around them.
> 与 [[Week2-Software-Design#5. Information Hiding 信息隐藏|Information Hiding]] 和 [[Week2-Software-Design#3. SOLID Principles SOLID原则|OCP]] 密切相关。

```java
// 支付方式可能变化 → 用接口保护
interface PaymentProcessor {
    void process(double amount);
}
// 即使以后换支付方式，使用 PaymentProcessor 的代码不需要改变
```

### GRASP Summary Table

| Pattern | 核心问题 | 简记 |
|---------|---------|------|
| **Information Expert** | 谁该负责？ | 谁有数据谁负责 |
| **Creator** | 谁来创建？ | 谁包含/使用就谁创建 |
| **Controller** | 谁处理系统事件？ | 中介层处理 |
| **Low Coupling** | 如何减少依赖？ | 依赖抽象，减少关联 |
| **High Cohesion** | 如何保持专注？ | 一个类只做相关的事 |
| **Indirection** | 如何解耦？ | 加中间层 |
| **Polymorphism** | 如何处理变化行为？ | 用多态替代条件判断 |
| **Pure Fabrication** | 没有合适的类？ | 创造一个辅助类 |
| **Protected Variations** | 如何应对变化？ | 用接口隔离变化点 |

---

## 5. Design Principles Deep Dive 设计原则深入

### SOLID Recap + Deeper Examples

在 [[Week2-Software-Design#3. SOLID Principles SOLID原则]] 中学了基础。这里更深入地讨论。

#### SRP — Single Responsibility Principle 单一职责

```java
// ❌ Violation — Book 类同时负责内容和持久化
class Book {
    String title, content;
    void printBook() { /* printing logic */ }
    void saveToFile() { /* file I/O logic */ }
}

// ✅ Fixed — 分离职责
class Book {
    String title, content;
}
class BookPrinter {
    void print(Book b) { /* printing logic */ }
}
class BookRepository {
    void save(Book b) { /* file I/O logic */ }
}
```

#### OCP — Open-Closed Principle 开闭原则

```java
// ❌ 每次加新形状就要修改这个方法
class AreaCalculator {
    double calculate(Object shape) {
        if (shape instanceof Circle) { /* ... */ }
        else if (shape instanceof Rectangle) { /* ... */ }
        // 新增 Triangle 就要改这里...
    }
}

// ✅ Open for extension, closed for modification
abstract class Shape {
    abstract double area();
}
class Triangle extends Shape {
    double area() { return 0.5 * base * height; } // 扩展，不修改
}
```

#### LSP — Liskov Substitution Principle 里氏替换原则

> 子类必须能**完全替代**父类使用，不改变程序的正确性。

```java
// ❌ Classic Violation — Square 不是 Rectangle 的合法子类！
class Rectangle {
    int width, height;
    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
    int area() { return width * height; }
}

class Square extends Rectangle {
    // Square 的 width 和 height 必须相等
    void setWidth(int w) { width = w; height = w; } // 改了 height!
    void setHeight(int h) { height = h; width = h; } // 改了 width!
}

// 使用时出问题：
Rectangle r = new Square();
r.setWidth(5);
r.setHeight(3);
r.area(); // 期望 15，实际得到 9！违反 LSP
```

#### ISP — Interface Segregation Principle 接口隔离原则

```java
// ❌ Fat Interface — 不是所有 Worker 都需要 eat()
interface Worker {
    void work();
    void eat();
    void sleep();
}

// ✅ Segregated Interfaces
interface Workable { void work(); }
interface Feedable { void eat(); }
interface Sleepable { void sleep(); }

class Robot implements Workable {
    void work() { /* ... */ } // Robot 不需要 eat 和 sleep
}
class Human implements Workable, Feedable, Sleepable {
    void work() { /* ... */ }
    void eat() { /* ... */ }
    void sleep() { /* ... */ }
}
```

#### DIP — Dependency Inversion Principle 依赖倒置原则

```java
// ❌ 高层模块依赖低层模块
class OrderService {
    MySQLDatabase db = new MySQLDatabase(); // 直接依赖具体实现
    void save(Order o) { db.insert(o); }
}

// ✅ 两者都依赖抽象
interface Database { void insert(Object obj); }
class MySQLDatabase implements Database { /* ... */ }
class OrderService {
    Database db; // 依赖抽象
    OrderService(Database db) { this.db = db; } // 依赖注入
}
```

### Law of Demeter (LoD) 迪米特法则 / Principle of Least Knowledge

> **Rule**: A method should only talk to its **immediate friends**. Don't talk to strangers!
> **规则**：一个方法只应该调用以下对象的方法：

1. 对象自身 (`this`)
2. 方法的参数
3. 方法内创建的对象
4. 对象的直接组件（fields）

```java
// ❌ Violates LoD — "train wreck" 链式调用
customer.getWallet().getMoney().pay(amount);
// customer 不应该知道 wallet 的内部结构

// ✅ Follows LoD — 只跟直接朋友对话
customer.pay(amount);
// Customer 内部自己决定怎么 pay
```

> 💡 **简记**："Only talk to your friends, not to strangers"（只和朋友说话，不跟陌生人说话）

### Tell, Don't Ask 命令，不要询问

> **Principle**: Tell objects what to do, **don't ask** them for their state and then make decisions for them.

```java
// ❌ Ask — 获取状态然后外部做决定
if (account.getBalance() >= amount) {
    account.setBalance(account.getBalance() - amount);
}

// ✅ Tell — 让对象自己处理
account.withdraw(amount);
// Account 自己知道怎么检查余额和扣款
```

> 💡 Tell Don't Ask 与 [[#4.1 Information Expert 信息专家|Information Expert]] 紧密相关：拥有数据的类应该拥有行为。

### Dependency Injection (DI) 依赖注入

DI 是实现 [[Week2-Software-Design#3. SOLID Principles SOLID原则|DIP]] 的主要方式。

**Three Types of DI**:

```java
// 1. Constructor Injection（最推荐）
class OrderService {
    private final Database db;
    OrderService(Database db) { this.db = db; } // 通过构造器注入
}

// 2. Setter Injection
class OrderService {
    private Database db;
    void setDatabase(Database db) { this.db = db; } // 通过 setter 注入
}

// 3. Interface Injection
interface DatabaseAware {
    void injectDatabase(Database db);
}
class OrderService implements DatabaseAware {
    void injectDatabase(Database db) { this.db = db; }
}
```

> 💡 Constructor Injection 最好，因为它保证对象创建时就有所有依赖，而且依赖是 immutable 的。

---

## 6. Design by Contract 契约式设计

### Core Idea 核心思想

**Design by Contract (DbC)** 就像一份**合同**：方法和调用者之间有明确的约定。

```
Caller (Client)                    Method (Supplier)
    |                                    |
    |--- "I promise preconditions" ----->|
    |                                    |
    |<--- "I promise postconditions" ----|
    |                                    |
    |     [Class invariants always hold] |
```

### Three Components 三要素

| Component | 中文 | Description | Responsibility |
|-----------|------|-------------|---------------|
| **Precondition** | 前置条件 | What must be true BEFORE the method runs | **Caller** must ensure |
| **Postcondition** | 后置条件 | What must be true AFTER the method runs | **Method** must ensure |
| **Class Invariant** | 类不变量 | What must ALWAYS be true for the object | **Class** must maintain |

```java
class BankAccount {
    private double balance; // invariant: balance >= 0

    /**
     * @pre amount > 0 && amount <= balance  (前置条件：调用者保证)
     * @post balance == old(balance) - amount (后置条件：方法保证)
     * @invariant balance >= 0               (不变量：始终成立)
     */
    void withdraw(double amount) {
        assert amount > 0 : "Precondition: amount must be positive";
        assert amount <= balance : "Precondition: insufficient funds";

        double oldBalance = balance;
        balance -= amount;

        assert balance == oldBalance - amount : "Postcondition failed";
        assert balance >= 0 : "Invariant violated";
    }
}
```

### DbC vs Defensive Programming

| Approach | 谁负责检查？ | 处理方式 |
|----------|-------------|---------|
| **Design by Contract** | Caller 确保前置条件 | 如果 caller 违反，是 caller 的 bug |
| **Defensive Programming** | Method 自己检查一切 | 用 if/exception 处理错误输入 |

> 💡 DbC 更清晰：每个参与者知道自己的责任。Defensive programming 更安全但代码更多。

---

## 7. Module Decomposition 模块分解 (Parnas)

### Parnas 1972 — 经典论文

David Parnas 的论文 "On the criteria to be used in decomposing systems into modules" 是 [[Week2-Software-Design#5. Information Hiding 信息隐藏|Information Hiding]] 的起源。

### Two Decomposition Approaches

Parnas 用一个 **KWIC (Key Word in Context)** 系统来比较两种分解方式：

#### Decomposition 1: Flowchart-based 基于流程的分解

```
Module 1: Input → Module 2: Circular Shift → Module 3: Sort → Module 4: Output
```

每个 module 对应处理流程中的一个步骤。

**问题**：
- Modules 之间共享数据结构 → **high coupling**
- 改变数据格式会影响所有 modules
- 每个模块都需要知道数据的具体存储方式

#### Decomposition 2: Information Hiding 基于信息隐藏的分解

```
Module 1: Line Storage (隐藏数据存储方式)
Module 2: Input (隐藏输入格式)
Module 3: Circular Shifter (隐藏 shift 算法)
Module 4: Alphabetizer (隐藏排序算法)
Module 5: Output (隐藏输出格式)
```

每个 module **隐藏一个设计决策**。

**优势**：
- **Low coupling** — 模块通过接口通信，不共享内部数据
- **Changeability** — 改变一个决策只影响一个模块
- **Independent development** — 可以并行开发

### Key Takeaway 关键结论

> **"每个模块应该隐藏一个设计决策"** — Parnas
>
> 分解标准不应该是处理步骤（flowchart），而应该是**需要隐藏的设计决策**（design decisions that might change）。

这个思想直接影响了：
- [[Week2-Software-Design#5. Information Hiding 信息隐藏|Information Hiding]]
- [[Week2-Software-Design#3. SOLID Principles SOLID原则|SRP]] — 每个模块一个"变化的理由"
- [[#4.9 Protected Variations 受保护的变化|Protected Variations]] — 隔离变化点
- [[Week2-Software-Design#4. Coupling and Cohesion 耦合与内聚|Low Coupling / High Cohesion]]

---

## 8. Refactoring 重构

### What is Refactoring? 什么是重构？

> **Refactoring** = changing the **internal structure** of code without changing its **external behavior**.
> **重构** = 改变代码的**内部结构**，但不改变其**外部行为**。

在 [[Week2-Software-Design#10. Code Smells and Refactoring 代码异味与重构]] 中简单介绍过，这里深入学习。

### Why Refactor? 为什么重构？

- **Improve readability** 提高可读性 — 让代码更容易理解
- **Reduce complexity** 降低复杂度 — 简化逻辑
- **Enable new features** 支持新功能 — 先重构再加功能
- **Eliminate [[#9. Code Smells 代码异味 (Detailed)|code smells]]** 消除代码异味
- **Reduce technical debt** 减少技术债务

### When to Refactor? 什么时候重构？

**The Rule of Three**:
1. First time → just do it
2. Second time (similar thing) → wince at the duplication, but do it anyway
3. Third time → **refactor!**

```
Also refactor when:
├── Before adding a new feature (先重构再加功能)
├── During code review (review 时发现问题)
├── When fixing a bug (修 bug 时顺便重构)
└── When code is hard to understand (代码难以理解时)
```

### When NOT to Refactor 什么时候不该重构？

- 代码完全不工作 → 需要 rewrite，不是 refactor
- Deadline 前 → 别在 deadline 前开始大重构
- 没有 tests → 没有测试保护的重构很危险！

> ⚠️ **Critical**: Refactoring **requires tests**! 没有 [[Week1-Software-Engineering-Introduction#11. Test-Driven Development (TDD) 测试驱动开发|tests]]，你怎么知道重构没有破坏功能？

### Refactoring Process 重构流程

```
1. 确保有 tests 覆盖要重构的代码 ✅
2. 做一个小的改动（single refactoring step）
3. Run tests → 全通过？
   ├── Yes → commit，继续下一步
   └── No → revert（撤销），找问题
4. Repeat
```

> 💡 重构的关键是**小步前进**：每次只做一个小改动，每次改动后都跑测试。

---

## 9. Code Smells 代码异味 (Detailed)

### What are Code Smells? 什么是代码异味？

Code smells 是代码中的**表面症状**，暗示可能存在更深层的设计问题。It's not a bug — the code works — but it indicates **poor design**.

> 💡 就像你闻到了坏味道 🦨 → 代码能跑，但结构不好。

### Smell Catalog 异味目录

#### Naming & Data Issues 命名与数据问题 ⭐ 历年考题常见

| Smell | 中文 | Description | Fix |
|-------|------|-------------|-----|
| **Mysterious Name** | 神秘命名 | 变量/方法/类名不清楚，无法表达意图。如 `x`, `d`, `temp`, `doStuff()` | Rename Variable / Change Function Declaration |
| **Global Data** | 全局数据 | 使用全局变量，任何代码都能修改它，难以追踪谁改了它 | Encapsulate Variable (wrap in class/function) |

> 💡 **Mysterious Name** 是最常见也最容易修复的 smell — 好的名字是代码可读性的基础。`d` → `elapsedDays`, `calc()` → `calculateMonthlyPayment()`
> 💡 **Global Data** 危险因为任何地方都能改它，debug 时很难找到问题源头。用 class 封装 + getter/setter 限制访问。

#### Bloaters 膨胀类 — 代码或类太大

| Smell | 中文 | Description | Fix |
|-------|------|-------------|-----|
| **Long Method** | 过长方法 | 方法太长，做太多事 | [[#10.1 Extract Method 提取方法]] |
| **Large Class** | 过大的类 | 类有太多职责 | Extract Class |
| **Long Parameter List** | 过长参数列表 | 方法参数太多 | Introduce Parameter Object |
| **Primitive Obsession** | 基本类型偏执 | 用原始类型代替小对象 | [[#10.2 Replace Primitive with Object]] |
| **Data Clumps** | 数据泥团 | 相同的一组数据总是一起出现 | Extract Class |

#### Object-Orientation Abusers 面向对象滥用

| Smell | 中文 | Description | Fix |
|-------|------|-------------|-----|
| **Switch Statements** | Switch 语句 | 大量 switch/if-else 根据类型分支 | Replace with Polymorphism |
| **Refused Bequest** | 被拒绝的遗赠 | 子类不用父类的方法 | Replace Inheritance with Delegation |
| **Parallel Inheritance** | 平行继承 | 每次新建一个子类，另一个层级也要新建 | Merge hierarchies |

#### Change Preventers 变更阻碍

| Smell | 中文 | Description | Fix |
|-------|------|-------------|-----|
| **Divergent Change** | 发散式变化 | 一个类因多种不同原因而修改 | Extract Class (每个变化原因一个类) |
| **Shotgun Surgery** | 霰弹式修改 | 一个改动需要修改很多不同的类 | Move Method / Inline Class |

> 💡 **Divergent Change vs Shotgun Surgery** — 经常考！
> - **Divergent Change**: 1个类，N种修改原因 → 拆分这个类
> - **Shotgun Surgery**: 1个改动，N个类需要修改 → 合并到一个类

#### Dispensables 可有可无的

| Smell | 中文 | Description | Fix |
|-------|------|-------------|-----|
| **Dead Code** | 死代码 | 没被使用的代码 | Delete it |
| **Duplicate Code** | 重复代码 | 相同或类似的代码出现多次 | Extract Method |
| **Speculative Generality** | 过度设计 | 为"可能"需要的功能写的代码 | Remove it (YAGNI!) |
| **Comments** | 过多注释 | 注释掩盖了代码不清晰的问题 | Refactor code to be self-explanatory |
| **Lazy Class** | 懒类 | 类做的事太少，不值得单独存在 | Inline Class |

> ⚠️ Comments 作为 smell 并不意味着"永远不写注释"！意思是：**如果你需要注释来解释代码在做什么，也许代码本身应该更清晰**。好的注释解释 **WHY**，不是 **WHAT**。

#### Couplers 耦合类

| Smell | 中文 | Description | Fix |
|-------|------|-------------|-----|
| **Feature Envy** | 特性依恋 | 方法更多地使用另一个类的数据 | Move Method |
| **Inappropriate Intimacy** | 不当亲密 | 两个类过多地访问彼此的私有部分 | Move Method, Extract Class |
| **Message Chains** | 消息链 | a.getB().getC().getD() | Hide Delegate (Law of Demeter!) |
| **Middle Man** | 中间人 | 类只是转发调用给另一个类 | Remove Middle Man |

---

## 10. Refactoring Catalog 重构目录

### 10.1 Extract Method 提取方法

**最常用的重构！** 把一段代码提取成独立的方法。

```java
// ❌ Before — 方法太长，逻辑混杂
void printReport(Order order) {
    // print header
    System.out.println("=== Order Report ===");
    System.out.println("Date: " + order.getDate());
    System.out.println("Customer: " + order.getCustomer());

    // calculate total
    double total = 0;
    for (Item item : order.getItems()) {
        total += item.getPrice() * item.getQuantity();
    }

    // print footer
    System.out.println("Total: $" + total);
    System.out.println("===================");
}

// ✅ After — 每个方法做一件事
void printReport(Order order) {
    printHeader(order);
    double total = calculateTotal(order);
    printFooter(total);
}

void printHeader(Order order) {
    System.out.println("=== Order Report ===");
    System.out.println("Date: " + order.getDate());
    System.out.println("Customer: " + order.getCustomer());
}

double calculateTotal(Order order) {
    double total = 0;
    for (Item item : order.getItems()) {
        total += item.getPrice() * item.getQuantity();
    }
    return total;
}

void printFooter(double total) {
    System.out.println("Total: $" + total);
    System.out.println("===================");
}
```

### 10.2 Replace Primitive with Object 用对象替代基本类型

```java
// ❌ Before — 用 String 表示 phone number
class Customer {
    String phoneNumber; // 没有验证，没有格式化
}

// ✅ After — 用专门的类
class PhoneNumber {
    private String number;

    PhoneNumber(String number) {
        if (!isValid(number)) throw new IllegalArgumentException();
        this.number = number;
    }

    String getAreaCode() { return number.substring(0, 3); }
    boolean isValid(String num) { /* validation logic */ }
    String formatted() { return "(" + getAreaCode() + ") " + /*...*/; }
}

class Customer {
    PhoneNumber phoneNumber; // 有验证、有行为
}
```

### 10.3 Replace Magic Number/Value 替换魔法值

```java
// ❌ Before — 什么是 9.81？什么是 0.1？
double force = mass * 9.81;
if (score > 0.1) { /* ... */ }

// ✅ After — 用有意义的常量
static final double GRAVITATIONAL_ACCELERATION = 9.81;
static final double PASSING_THRESHOLD = 0.1;

double force = mass * GRAVITATIONAL_ACCELERATION;
if (score > PASSING_THRESHOLD) { /* ... */ }
```

### 10.4 Extract Variable 提取变量

```java
// ❌ Before — 复杂表达式难以理解
if (order.getTotal() > 100 && customer.getLoyaltyYears() > 2
    && !order.hasHazardousItems()) {
    applyDiscount(order);
}

// ✅ After — 给子表达式命名
boolean isLargeOrder = order.getTotal() > 100;
boolean isLoyalCustomer = customer.getLoyaltyYears() > 2;
boolean isEligibleForDiscount = isLargeOrder && isLoyalCustomer
    && !order.hasHazardousItems();

if (isEligibleForDiscount) {
    applyDiscount(order);
}
```

### 10.5 Move Method / Move Field 移动方法/字段

```java
// ❌ Feature Envy — method 更多地使用另一个类的数据
class Order {
    Customer customer;

    String getCustomerFullAddress() {
        return customer.getStreet() + ", " +
               customer.getCity() + ", " +
               customer.getState();
    }
}

// ✅ 移到数据所在的类 (Information Expert!)
class Customer {
    String getFullAddress() {
        return street + ", " + city + ", " + state;
    }
}
```

### 10.6 Replace Conditional with Polymorphism 用多态替代条件

```java
// ❌ Before — switch on type
class Bird {
    String type;
    double getSpeed() {
        switch (type) {
            case "EUROPEAN": return getBaseSpeed();
            case "AFRICAN": return getBaseSpeed() - getLoadFactor();
            case "NORWEGIAN_BLUE": return isNailed ? 0 : getBaseSpeed();
        }
    }
}

// ✅ After — polymorphism
abstract class Bird {
    abstract double getSpeed();
}
class European extends Bird {
    double getSpeed() { return getBaseSpeed(); }
}
class African extends Bird {
    double getSpeed() { return getBaseSpeed() - getLoadFactor(); }
}
class NorwegianBlue extends Bird {
    double getSpeed() { return isNailed ? 0 : getBaseSpeed(); }
}
```

### Taxonomy of Refactorings 重构分类总结

```
Refactoring Techniques
├── Composing Methods (组织方法)
│   ├── Extract Method
│   ├── Inline Method
│   ├── Extract Variable
│   └── Replace Temp with Query
├── Moving Features (移动特性)
│   ├── Move Method
│   ├── Move Field
│   └── Extract/Inline Class
├── Organizing Data (组织数据)
│   ├── Replace Primitive with Object
│   ├── Replace Magic Number with Constant
│   └── Encapsulate Field
├── Simplifying Conditionals (简化条件)
│   ├── Replace Conditional with Polymorphism
│   ├── Consolidate Conditional
│   └── Replace Nested Conditional with Guard Clauses
└── Dealing with Generalization (处理泛化)
    ├── Pull Up Method / Field
    ├── Push Down Method / Field
    └── Replace Inheritance with Delegation
```

---

## Exam Quick Review 考试速览

### 🔑 Must-Know Concepts 必考概念

**Code Reviews**:
- Types: Formal Inspection, Walkthrough, Lightweight (PR), Pair Programming
- PR review 是最常用的，要求 small PRs, clear description
- Static analysis = 不运行代码就找问题（tool-based）
- Code review 找逻辑/设计问题，static analysis 找 pattern-based 问题

**OO Design**:
- Four pillars: Encapsulation, Abstraction, Inheritance, Polymorphism
- **Favor composition over inheritance!**
- Abstract class vs Interface 区别（see table above）

**GRASP Patterns (9个)**:
- **Information Expert** → 谁有数据谁负责
- **Creator** → 谁包含谁创建
- **Controller** → 处理系统事件的中介
- **Low Coupling** → 减少依赖
- **High Cohesion** → 保持专注
- **Indirection** → 加中间层解耦
- **Polymorphism** → 用多态替代条件
- **Pure Fabrication** → 创造辅助类
- **Protected Variations** → 用接口隔离变化

**Design Principles**:
- SOLID (详见 [[Week2-Software-Design#3. SOLID Principles SOLID原则]])
- **Law of Demeter** → "只跟朋友说话" → 避免 `a.getB().getC()`
- **Tell Don't Ask** → 命令对象做事，别问状态自己做
- **Dependency Injection** → Constructor Injection 最好

**Design by Contract**:
- **Precondition** = caller 的责任
- **Postcondition** = method 的责任
- **Invariant** = 类始终保持的条件

**Parnas Module Decomposition**:
- Flowchart-based = 按步骤分 → high coupling, bad
- Information Hiding-based = 按设计决策分 → low coupling, good ✅
- **"每个模块隐藏一个设计决策"**

**Refactoring**:
- = 改内部结构，不改外部行为
- **需要 tests！** 没有 tests 不要重构
- 小步前进，每步都跑测试

**Code Smells (key ones)**:
- **Divergent Change** vs **Shotgun Surgery** — 必考！
- **Feature Envy** → 方法更多用别的类的数据 → Move Method
- **Message Chains** → `a.b().c().d()` → 违反 Law of Demeter
- **Long Method / Large Class** → Extract Method / Extract Class

**Refactoring Techniques (key ones)**:
- **Extract Method** → 最常用
- **Replace Conditional with Polymorphism** → switch → 继承 + 多态
- **Replace Primitive with Object** → String phone → PhoneNumber class
- **Move Method** → 移到 Information Expert

### 📊 Concept Relationship Map 概念关系图

```
                    ┌─────────────────────────┐
                    │    Parnas (1972)         │
                    │  Module Decomposition    │
                    └────────┬────────────────┘
                             │ originated
                             ▼
┌──────────────────────────────────────────────────────┐
│              Information Hiding                       │
│    (每个模块隐藏一个设计决策)                            │
└────┬──────────────┬────────────────┬─────────────────┘
     │              │                │
     ▼              ▼                ▼
   SRP          OCP/DIP       Protected Variations
   (SOLID)      (SOLID)         (GRASP)
     │              │                │
     └──────────┬───┘                │
                ▼                    │
          Low Coupling ◄─────────────┘
          High Cohesion
           (GRASP)
                │
     ┌──────────┼──────────┐
     ▼          ▼          ▼
 Law of    Tell Don't   Design by
 Demeter     Ask        Contract
     │          │          │
     └──────────┼──────────┘
                ▼
         Code Smells
         (symptoms)
                │
                ▼
         Refactoring
         (treatment)
                │
         ┌──────┼──────┐
         ▼      ▼      ▼
      Extract  Move  Replace
      Method  Method  Conditional
                      w/ Polymorphism
```

### 🔗 Cross-Week Links 跨周链接

- [[Week1-Software-Engineering-Introduction#11. Test-Driven Development (TDD) 测试驱动开发|Week 1: TDD]] → Refactoring 需要 tests 保护
- [[Week1-Software-Engineering-Introduction#10. Git Version Control 版本控制|Week 1: Git]] → PR-based code review 依赖 Git workflow
- [[Week2-Software-Design#3. SOLID Principles SOLID原则|Week 2: SOLID]] → Week 3 深入理解 + 更多例子
- [[Week2-Software-Design#4. Coupling and Cohesion 耦合与内聚|Week 2: Coupling/Cohesion]] → GRASP Low Coupling / High Cohesion 是同一概念
- [[Week2-Software-Design#5. Information Hiding 信息隐藏|Week 2: Information Hiding]] → Parnas 论文是其理论起源
- [[Week2-Software-Design#6. Design Patterns 设计模式|Week 2: Design Patterns]] → GRASP 是比 GoF 更基础的职责分配原则
- [[Week2-Software-Design#10. Code Smells and Refactoring 代码异味与重构|Week 2: Code Smells intro]] → Week 3 完整的 smell catalog + refactoring catalog
