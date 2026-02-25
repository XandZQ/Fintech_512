# Week 2: Software Design 软件设计

> **Course**: Software Engineering
> **Coverage**: Slide 04-Design + "A Laboratory for Teaching OO Thinking" (Beck & Cunningham) + "A Simple Web Server" (500 Lines or Less)
> **Prerequisite knowledge**: [[Week1-Software-Engineering-Introduction]]

---

## Table of Contents

- [[#1. What is Software Design 什么是软件设计]]
- [[#2. Design Principles 设计原则]]
- [[#3. SOLID Principles SOLID原则]]
- [[#4. Coupling and Cohesion 耦合与内聚]]
- [[#5. Information Hiding 信息隐藏]]
- [[#6. Design Patterns 设计模式]]
- [[#7. UML Diagrams UML图]]
- [[#8. CRC Cards CRC卡片]]
- [[#9. Architecture Patterns 架构模式]]
- [[#10. Code Smells and Refactoring 代码异味与重构]]
- [[#11. Practical Example — A Simple Web Server 实例：简单Web服务器]]
- [[#Exam Quick Review 考试速览]]

---

## 1. What is Software Design 什么是软件设计

### Definition 定义

Software design is the process of **defining the architecture, components, interfaces, and other characteristics** of a system. It's the bridge between [[Week1-Software-Engineering-Introduction#5. Requirements Engineering 需求工程|Requirements]] (WHAT to build) and Implementation (the actual CODE).

```
Requirements (WHAT) → Design (HOW) → Implementation (CODE)
   需求（做什么）     设计（怎么做）      实现（写代码）
```

> 简单理解：需求告诉你**做什么**，设计告诉你**怎么做**，实现就是**写代码**。

### Why Design Matters 为什么设计重要

- A good design makes the system **easier to understand, maintain, and extend** 好的设计让系统更容易理解、维护和扩展
- A bad design leads to **spaghetti code** — tangled, hard to change 坏的设计导致"意大利面条代码"——纠缠不清，难以修改
- Design is about **managing complexity** 设计就是管理复杂性
- **Every design decision has trade-offs** 每个设计决策都有权衡

### Design Levels 设计层次

| Level | Scope | Example |
|-------|-------|---------|
| **Architecture Design** 架构设计 | High-level structure of the entire system | [[#9. Architecture Patterns 架构模式\|MVC, Client-Server, Layered]] |
| **Class/Module Design** 类/模块设计 | Individual classes and their relationships | [[#7. UML Diagrams UML图\|Class diagrams]], [[#8. CRC Cards CRC卡片\|CRC Cards]] |
| **Method/Function Design** 方法设计 | Internal logic of individual methods | Algorithms, data structures |

---

## 2. Design Principles 设计原则

These are **general guidelines** that help you make good design decisions. Think of them as "rules of thumb."

### 2.1 DRY — Don't Repeat Yourself 不要重复自己

> **"Every piece of knowledge must have a single, unambiguous representation within a system."**

- If you see the **same code in multiple places**, extract it into a shared function/class
- Duplication = maintenance nightmare. Change one copy, forget the other → bugs!
- 如果同样的代码出现在多个地方，提取到一个共享的函数/类中

**Bad example (违反DRY)**:
```python
# In file A:
def calculate_tax(price):
    return price * 0.13

# In file B (exact same logic!):
def get_tax(amount):
    return amount * 0.13
```

**Good example (遵循DRY)**:
```python
# In shared utility:
TAX_RATE = 0.13
def calculate_tax(price):
    return price * TAX_RATE

# Both A and B import and use calculate_tax()
```

### 2.2 KISS — Keep It Simple, Stupid 保持简单

- Don't over-engineer solutions 不要过度工程化
- Choose the **simplest approach** that works 选择最简单的可行方案
- Complex code is harder to debug, test, and maintain 复杂代码更难调试、测试和维护

> If you can solve it with a simple `if` statement, don't build a complex design pattern framework.

### 2.3 YAGNI — You Aren't Gonna Need It 你不会需要它

- Don't add functionality **until it's actually needed** 在真正需要之前不要添加功能
- Part of [[Week1-Software-Engineering-Introduction#4.5 Extreme Programming (XP) 极限编程|XP]]'s philosophy
- Building features "just in case" wastes time and adds complexity 为"以防万一"而构建功能是浪费

> **YAGNI + KISS + DRY** 是三个最基本的设计原则。考试经常会考它们的含义和区别。

### 2.4 Separation of Concerns (SoC) 关注点分离

- Each module/class should address a **single concern** 每个模块/类应该只关注一个问题
- Don't mix unrelated responsibilities 不要混合无关的职责
- Example: Don't put database logic inside your UI code 不要把数据库逻辑放在UI代码里

> SoC is closely related to [[#4.2 Cohesion 内聚|Cohesion]] and the [[#3.1 Single Responsibility Principle (SRP)|Single Responsibility Principle]].

### 2.5 Favor Composition over Inheritance 组合优于继承

- **Inheritance** ("is-a" 是一个): class B extends class A → B inherits everything from A
- **Composition** ("has-a" 有一个): class B contains an instance of class A → B uses A's functionality

**Why favor composition?**
- Inheritance creates **tight coupling** between parent and child 继承造成紧密耦合
- Changes to the parent can **break all children** 父类的变化可能破坏所有子类
- Composition is more **flexible** — you can swap components at runtime 组合更灵活——可以在运行时替换组件

```
Inheritance (tight coupling):     Composition (flexible):
    Animal                          Duck
    ├── Duck                          ├── has: FlyBehavior
    └── Penguin                       └── has: SwimBehavior

If Animal changes fly() →          Change FlyBehavior independently
  ALL subclasses affected            without affecting Duck
```

---

## 3. SOLID Principles SOLID原则

**SOLID** is a set of 5 design principles coined by **Robert C. Martin (Uncle Bob)** that are fundamental to object-oriented design. 这是面向对象设计中最重要的5个原则。

### 3.1 Single Responsibility Principle (SRP) 单一职责原则

> **"A class should have only ONE reason to change."**

每个类应该只有**一个职责**，只因**一个原因**而改变。

**Bad example** ❌:
```
class Employee:
    - calculate_pay()        # 薪资计算
    - save_to_database()     # 数据库操作
    - generate_report()      # 报告生成
```
This class has THREE reasons to change (pay logic, DB schema, report format).

**Good example** ✅:
```
class Employee:          # Only employee data
class PayCalculator:     # Only pay calculation
class EmployeeDB:        # Only database operations
class ReportGenerator:   # Only report generation
```

> **Exam tip**: SRP ≠ "one method per class." It means one **responsibility/reason to change**. 不是说一个类只能有一个方法，而是只能有一个变化的原因。

### 3.2 Open/Closed Principle (OCP) 开闭原则

> **"Software entities should be open for EXTENSION, but closed for MODIFICATION."**

对扩展开放，对修改关闭。你应该能**添加新功能**而**不修改现有代码**。

**Bad example** ❌:
```python
def calculate_area(shape):
    if shape.type == "circle":
        return 3.14 * shape.radius ** 2
    elif shape.type == "rectangle":
        return shape.width * shape.height
    # Adding triangle? Must MODIFY this function!
```

**Good example** ✅:
```python
class Shape:
    def area(self): pass

class Circle(Shape):
    def area(self): return 3.14 * self.radius ** 2

class Rectangle(Shape):
    def area(self): return self.width * self.height

class Triangle(Shape):    # NEW shape — no existing code modified!
    def area(self): return 0.5 * self.base * self.height
```

### 3.3 Liskov Substitution Principle (LSP) 里氏替换原则

> **"Subtypes must be substitutable for their base types."**

子类必须能够替换其父类而不破坏程序。如果 B 是 A 的子类，那么任何使用 A 的地方都能用 B 替换。

**Classic violation** ❌ — Square extends Rectangle:
```
class Rectangle:
    set_width(w)
    set_height(h)

class Square(Rectangle):   # Square IS-A Rectangle?
    set_width(w):  # must also set height = w
    set_height(h): # must also set width = h
```
Problem: Code expecting a Rectangle (where width and height are independent) breaks with a Square!

> **Key test**: If substituting a subclass causes unexpected behavior, you're violating LSP.

### 3.4 Interface Segregation Principle (ISP) 接口隔离原则

> **"No client should be forced to depend on methods it does not use."**

不应该强迫一个类实现它不需要的接口方法。

**Bad example** ❌:
```
interface Worker:
    work()
    eat()
    sleep()

class Robot implements Worker:
    work() { ... }
    eat()  { ??? }   # Robot doesn't eat!
    sleep(){ ??? }   # Robot doesn't sleep!
```

**Good example** ✅:
```
interface Workable:    work()
interface Eatable:     eat()
interface Sleepable:   sleep()

class Human implements Workable, Eatable, Sleepable
class Robot implements Workable     # Only what it needs!
```

> ISP: 把大接口拆成小接口，让每个类只实现自己需要的。

### 3.5 Dependency Inversion Principle (DIP) 依赖反转原则

> **"High-level modules should NOT depend on low-level modules. Both should depend on ABSTRACTIONS."**

高层模块不应该依赖低层模块。两者都应该依赖**抽象**（接口）。

```
Without DIP (bad) ❌:              With DIP (good) ✅:

OrderService                       OrderService
    ↓ depends on                       ↓ depends on
MySQLDatabase                      DatabaseInterface (abstract)
                                       ↑ implements
                                   MySQLDatabase / PostgresDB / MongoDB
```

Without DIP: switching from MySQL to PostgreSQL means **rewriting** OrderService.
With DIP: just create a new implementation of `DatabaseInterface`.

> **Exam tip**: DIP 的核心——依赖抽象，不依赖具体实现。这让你可以轻松替换底层组件。

### SOLID Summary Table

| Principle | Mnemonic | Core Idea | 核心思想 |
|-----------|----------|-----------|---------|
| **S**RP | Single Responsibility | One class, one reason to change | 一个类，一个变化原因 |
| **O**CP | Open/Closed | Extend without modifying | 扩展不修改 |
| **L**SP | Liskov Substitution | Subtypes replaceable for base types | 子类可替换父类 |
| **I**SP | Interface Segregation | Small, specific interfaces | 小而专的接口 |
| **D**IP | Dependency Inversion | Depend on abstractions | 依赖抽象，不依赖具体 |

---

## 4. Coupling and Cohesion 耦合与内聚

These two concepts are the **most important metrics** for evaluating software design quality. 这两个概念是评估设计质量的最重要指标。

> **Goal**: **Low Coupling（低耦合）+ High Cohesion（高内聚）**

### 4.1 Coupling 耦合

**Coupling** measures the **degree of dependency between modules**. How much does one module "know about" or "rely on" another?

#### Coupling Spectrum (from WORST to BEST):

| Level | Type | Description | 说明 |
|-------|------|-------------|------|
| 🔴 Worst | **Content Coupling** | Module A directly modifies internal data of Module B | A直接修改B的内部数据 |
| 🟠 | **Common Coupling** | Multiple modules share global variables | 多个模块共享全局变量 |
| 🟡 | **Control Coupling** | Module A passes a control flag to tell Module B what to do | A传递控制标志告诉B做什么 |
| 🟢 | **Stamp Coupling** | Modules share a data structure, but only use part of it | 模块共享数据结构但只用一部分 |
| 🟢 Best | **Data Coupling** | Modules communicate only through parameters (simple data) | 模块只通过参数（简单数据）通信 |

**Why low coupling is good** 为什么低耦合好：
- Changes in one module **don't ripple** to others 一个模块的变化不会波及其他模块
- Modules can be **tested independently** 模块可以独立测试
- Modules can be **reused** in different contexts 模块可以在不同场景中复用
- Easier to **understand** each module 更容易理解每个模块

### 4.2 Cohesion 内聚

**Cohesion** measures **how strongly related the elements within a single module are**. Do all the methods and data in a class belong together?

#### Cohesion Spectrum (from WORST to BEST):

| Level | Type | Description | 说明 |
|-------|------|-------------|------|
| 🔴 Worst | **Coincidental** | Elements are in the same class for no logical reason | 元素在一起没有逻辑原因 |
| 🟠 | **Logical** | Elements do similar things but are unrelated (e.g., all input handling) | 做类似但不相关的事 |
| 🟡 | **Temporal** | Elements are grouped because they run at the same time (e.g., initialization) | 因为同时执行而分组 |
| 🟡 | **Procedural** | Elements follow a sequence of steps | 按步骤顺序分组 |
| 🟢 | **Communicational** | Elements operate on the same data | 操作相同数据 |
| 🟢 | **Sequential** | Output of one element is input to the next | 一个元素的输出是下一个的输入 |
| 🟢 Best | **Functional** | All elements work together to perform a **single, well-defined function** | 所有元素共同完成一个明确定义的功能 |

> **Exam tip**: Remember — **Low Coupling + High Cohesion = Good Design**. 记住这个公式。能举例说明每种类型是加分项。

### The Relationship Between Coupling and Cohesion

```
High Cohesion → each module does ONE thing well → less need to interact with others → Low Coupling
                                                                                        ↓
                                                                            Good Design ✅

Low Cohesion → module does MANY unrelated things → must reach into other modules → High Coupling
                                                                                      ↓
                                                                            Bad Design ❌
```

> 高内聚自然导致低耦合。如果每个模块职责单一，它们之间的依赖就会减少。This also connects to [[#3.1 Single Responsibility Principle (SRP) 单一职责原则|SRP]].

---

## 5. Information Hiding 信息隐藏

### Concept 概念

Proposed by **David Parnas** (1972). The idea: each module should **hide its internal implementation details** behind a well-defined **interface**.

```
Module (Public Interface)
┌──────────────────────────┐
│  + publicMethod()        │  ← What others can see/use
│  + anotherPublicMethod() │
├──────────────────────────┤
│  - privateData           │  ← Hidden from outside
│  - internalHelper()      │     (implementation details)
│  - secretAlgorithm()     │
└──────────────────────────┘
```

### Why Information Hiding Matters

- **Reduces coupling**: Other modules depend only on the interface, not internal details 降低耦合
- **Enables independent development**: Internal changes don't affect others 内部变化不影响外部
- **Simplifies understanding**: You only need to know WHAT it does, not HOW 你只需知道它做什么，不需知道怎么做

### Implementation in OOP

| Access Modifier | Visibility | 说明 |
|----------------|------------|------|
| `public` | Everyone can access | 所有人可访问 |
| `private` | Only within the class | 仅类内部可访问 |
| `protected` | Class + subclasses | 类和子类可访问 |

> Information hiding is the **foundation** of [[#4.1 Coupling 耦合|low coupling]] — if you can't see internal details, you can't depend on them.

---

## 6. Design Patterns 设计模式 (Introduction — 完整详解见 [[Week4-DesignPatterns-Testing]])

Design patterns are **proven, reusable solutions** to common design problems. They were popularized by the **"Gang of Four" (GoF)** book (1994).

> 设计模式不是代码，是**解决常见问题的模板**。

### 6.1 Three Categories 三大分类

| Category | Purpose 目的 | Key Patterns (详见 Week 4) |
|----------|---------|------|
| **Creational** 创建型 | 如何创建对象 | [[Week4-DesignPatterns-Testing#5. Factory Patterns 工厂模式\|Factory]], [[Week4-DesignPatterns-Testing#6. Singleton Pattern 单例模式\|Singleton]] |
| **Structural** 结构型 | 如何组合类/对象 | [[Week4-DesignPatterns-Testing#4. Decorator Pattern 装饰者模式\|Decorator]], [[Week4-DesignPatterns-Testing#7. Adapter Pattern 适配器模式\|Adapter]], [[Week4-DesignPatterns-Testing#8. Facade Pattern 外观模式\|Facade]] |
| **Behavioral** 行为型 | 对象如何交互 | [[Week4-DesignPatterns-Testing#2. Strategy Pattern 策略模式\|Strategy]], [[Week4-DesignPatterns-Testing#3. Observer Pattern 观察者模式\|Observer]], [[Week4-DesignPatterns-Testing#9. Command Pattern 命令模式\|Command]] |

### 6.2 Core Principles Behind All Patterns 模式背后的原则

这些原则连接了 [[#3. SOLID Principles SOLID原则|SOLID]] 和具体 Design Patterns：

1. **Encapsulate what varies** 封装变化 → Strategy, State
2. **Program to an interface** 面向接口编程 → Factory, Observer
3. **Favor composition over inheritance** 组合优于继承 → Decorator, Strategy
4. **Strive for loosely coupled designs** 追求松耦合 → Observer, Facade

> 💡 所有 Design Patterns 都是 SOLID 和 [[#4. Coupling and Cohesion 耦合与内聚|Coupling/Cohesion]] 原则的**具体应用**。先学好原则，再学模式会容易得多。完整 12+ 模式详解在 [[Week4-DesignPatterns-Testing]]。

---

## 7. UML Diagrams UML图

**UML (Unified Modeling Language)** is a standardized visual language for modeling software systems. In design, the most important diagrams are:

### 7.1 Class Diagrams 类图

The most common UML diagram. Shows **classes, their attributes, methods, and relationships**.

#### Class Box Format:

```
┌────────────────────┐
│    ClassName        │  ← Name
├────────────────────┤
│ - privateField: int │  ← Attributes (fields)
│ + publicField: str  │     - = private, + = public, # = protected
├────────────────────┤
│ + publicMethod()    │  ← Methods (operations)
│ - privateHelper()   │
└────────────────────┘
```

#### Relationships 关系:

| Relationship | Symbol | Meaning | Example |
|-------------|--------|---------|---------|
| **Association** 关联 | ——→ | "uses" or "knows about" | Student → Course |
| **Aggregation** 聚合 | ◇——→ | "has-a" (weak ownership, can exist independently) | Department ◇→ Professor |
| **Composition** 组合 | ◆——→ | "has-a" (strong ownership, part dies with whole) | House ◆→ Room |
| **Inheritance** 继承 | △——→ | "is-a" (extends) | Dog △→ Animal |
| **Dependency** 依赖 | - - → | "uses temporarily" (e.g., method parameter) | Order --→ Product |
| **Implementation** 实现 | △- - → | "implements interface" | Circle △--→ Shape |

> **Aggregation vs Composition 聚合 vs 组合**:
> - Aggregation: Professor can exist **without** Department（教授可以独立于院系存在）
> - Composition: Room **cannot** exist without House（房间不能独立于房子存在）

#### Multiplicity 多重性:

| Notation | Meaning |
|----------|---------|
| `1` | Exactly one |
| `0..1` | Zero or one |
| `*` or `0..*` | Zero or more |
| `1..*` | One or more |
| `n..m` | Between n and m |

### 7.2 Sequence Diagrams 序列图

Shows the **interaction between objects over time**. Useful for understanding the flow of a [[Week1-Software-Engineering-Introduction#7. Use Cases 用例|Use Case]].

```
  User          LoginPage       AuthService       Database
   │                │                │                │
   │── enter creds→ │                │                │
   │                │── validate() → │                │
   │                │                │── query() ───→ │
   │                │                │  ←── result ───│
   │                │  ←── token ────│                │
   │  ←── success ──│                │                │
   │                │                │                │
   ▼ time          ▼               ▼                ▼
```

**Key elements**:
- **Lifelines** 生命线: vertical dashed lines (one per object)
- **Messages** 消息: horizontal arrows between lifelines
- **Activation bars** 激活条: rectangles on lifelines showing when object is active
- **Return messages** 返回消息: dashed arrows going back

---

## 8. CRC Cards CRC卡片

CRC Cards were introduced by **Kent Beck and Ward Cunningham** (1989) as a simple, hands-on technique for discovering classes in OO design.

> CRC = **Class – Responsibility – Collaboration**

### 8.1 What is a CRC Card?

A physical index card (or digital equivalent) with three sections:

```
┌─────────────────────────────────────────┐
│              Class Name                  │
├────────────────────┬────────────────────┤
│  Responsibilities  │  Collaborators     │
│                    │                    │
│ - What this class  │ - What other       │
│   knows            │   classes it       │
│ - What this class  │   works with       │
│   does             │                    │
│                    │                    │
└────────────────────┴────────────────────┘
```

| Section | Description | 说明 |
|---------|-------------|------|
| **Class Name** | The name of the class | 类的名称 |
| **Responsibilities** | What the class knows (data) and does (behavior) | 类知道什么（数据）和做什么（行为） |
| **Collaborators** | Other classes this class needs to work with | 需要合作的其他类 |

### 8.2 How to Use CRC Cards — Role Playing

The Beck & Cunningham paper describes a **role-playing exercise**:

1. **Identify candidate classes** from the problem description 从问题描述中识别候选类
2. **Write one CRC card per class** 每个类一张CRC卡
3. **Team members each hold cards** and "become" that object 团队成员每人拿一张卡，"扮演"那个对象
4. **Walk through scenarios** (use cases) — when your object receives a message, say what you do and who you collaborate with 走过场景——当你的对象收到消息时，说你做什么，和谁合作
5. **Discover missing classes, responsibilities, or collaborations** 发现缺失的类、职责或合作关系

### 8.3 CRC Card Example

**ATM System Scenario**: Customer withdraws cash

```
┌──────────────────────────────────┐
│            ATM                   │
├──────────────────┬───────────────┤
│ Responsibilities │ Collaborators │
│ - Read card      │ - Account     │
│ - Get PIN        │ - Transaction │
│ - Dispense cash  │ - Bank        │
│ - Print receipt  │               │
└──────────────────┴───────────────┘

┌──────────────────────────────────┐
│           Account                │
├──────────────────┬───────────────┤
│ Responsibilities │ Collaborators │
│ - Know balance   │ - Transaction │
│ - Verify PIN     │ - Bank        │
│ - Update balance │               │
└──────────────────┴───────────────┘

┌──────────────────────────────────┐
│         Transaction              │
├──────────────────┬───────────────┤
│ Responsibilities │ Collaborators │
│ - Know amount    │ - Account     │
│ - Know type      │               │
│ - Execute        │               │
└──────────────────┴───────────────┘
```

### 8.4 Benefits of CRC Cards 优点

- **Simple and tangible** — no special tools needed 简单且实体化
- **Encourages teamwork** — everyone participates 鼓励团队合作
- **Anthropomorphization** — thinking of objects as "alive" helps assign responsibilities naturally 把对象拟人化，更自然地分配职责
- **Quick iteration** — throw away and rewrite cards easily 快速迭代
- **Discovers design problems early** — missing classes, wrong responsibilities 早期发现设计问题

> CRC Cards 是从需求到设计的桥梁工具。它帮助你从 [[Week1-Software-Engineering-Introduction#6. User Stories 用户故事|User Stories]] / [[Week1-Software-Engineering-Introduction#7. Use Cases 用例|Use Cases]] 发现需要哪些类。

---

## 9. Architecture Patterns 架构模式

Architecture patterns define the **high-level structure** of an entire application. They are bigger than [[#6. Design Patterns 设计模式|design patterns]] — they organize the whole system.

### 9.1 Layered Architecture 分层架构

The most common architecture. The system is divided into **horizontal layers**, each with a specific role. Each layer can only communicate with the layer **directly below** it.

```
┌──────────────────────────┐
│   Presentation Layer     │  ← UI, user interaction
│   (表示层/界面层)          │
├──────────────────────────┤
│   Business Logic Layer   │  ← Core rules, computations
│   (业务逻辑层)            │
├──────────────────────────┤
│   Data Access Layer      │  ← Database operations
│   (数据访问层)            │
├──────────────────────────┤
│   Database               │  ← Actual data storage
│   (数据库)               │
└──────────────────────────┘
```

**Benefits**: [[#4. Coupling and Cohesion 耦合与内聚|Low coupling]] between layers; each layer can be changed independently.

### 9.2 MVC — Model-View-Controller

**The most important architecture pattern** for web applications. Separates an application into three components:

| Component | Role | 职责 |
|-----------|------|------|
| **Model** 模型 | Data + business logic. Does NOT know about the UI. | 数据和业务逻辑。不知道UI的存在 |
| **View** 视图 | Displays data to the user. Gets data FROM the Model. | 向用户展示数据 |
| **Controller** 控制器 | Handles user input. Updates the Model. Selects the View. | 处理用户输入，更新模型，选择视图 |

```
        User
         │
    ① Input (clicks, types)
         ↓
    ┌─────────┐      ② Updates      ┌─────────┐
    │Controller│ ──────────────────→ │  Model   │
    └─────────┘                      └─────────┘
         │                                │
    ③ Selects View                   ④ Notifies (data changed)
         ↓                                ↓
    ┌─────────┐      ⑤ Gets data    ┌─────────┐
    │  View   │ ←────────────────── │  Model   │
    └─────────┘                      └─────────┘
         │
    ⑥ Displays to User
```

**Why MVC matters**:
- **Separation of concerns**: UI logic ≠ business logic ≠ data logic
- Multiple views can use the **same model** 多个视图可以共用同一个模型
- Designers work on Views while developers work on Models/Controllers 设计师和开发者可以并行工作
- Easier to test: Model can be tested **without** any UI 更容易测试

> **Exam tip**: MVC 是最常考的架构模式。理解每个组件的职责和它们之间的交互方向。

### 9.3 Client-Server Architecture 客户端-服务器架构

```
┌────────┐         HTTP Request          ┌────────┐
│ Client │ ────────────────────────────→ │ Server │
│(Browser│ ←──────────────────────────── │        │
│ /App)  │         HTTP Response         │        │
└────────┘                               └────────┘
```

- **Client**: Makes requests, displays results 发出请求，显示结果
- **Server**: Processes requests, manages data 处理请求，管理数据
- Examples: Web applications, email (IMAP/SMTP), databases

### 9.4 Microservices Architecture 微服务架构

Instead of one big application (**monolith**), split it into **small, independent services** that communicate via APIs.

```
Monolith:                    Microservices:
┌─────────────┐             ┌──────────┐  ┌──────────┐
│ All code     │             │ User     │  │ Order    │
│ in one       │     →       │ Service  │  │ Service  │
│ application  │             └──────────┘  └──────────┘
└─────────────┘             ┌──────────┐  ┌──────────┐
                            │ Payment  │  │ Inventory│
                            │ Service  │  │ Service  │
                            └──────────┘  └──────────┘
```

**Benefits**: Each service can be developed, deployed, and scaled **independently**.
**Drawbacks**: Network complexity, harder to debug, data consistency challenges.

---

## 10. Code Smells and Refactoring 代码异味与重构（概述）

Code smells are **indicators of potential problems** — they don't cause bugs directly, but suggest the design could be improved. Refactoring changes **internal structure** without changing **external behavior**.

> 代码异味不是 bug，而是"something doesn't feel right"。重构 = 改结构不改行为。

**Quick Reference** (详细内容、完整分类和重构手法见 [[Week3-Reviews-OODesign-Refactoring#9. Code Smells 代码异味 (Detailed)]])：

| Category | Examples | 常用重构 |
|----------|---------|---------|
| **Bloaters** 膨胀类 | Long Method, Large Class, Long Parameter List | Extract Method, Extract Class |
| **OO Abusers** 面向对象滥用 | Switch Statements, Refused Bequest | Replace Conditional with Polymorphism |
| **Change Preventers** 阻碍变更 | Shotgun Surgery, Divergent Change | Move Method, Extract Class |
| **Dispensables** 可删除 | Duplicate Code, Dead Code, Comments（过多注释） | Delete, Extract Method |
| **Couplers** 耦合过度 | Feature Envy, Message Chains | Move Method, Hide Delegate |

> 💡 **考试提示**: 要能根据代码片段**识别 smell 名称**并说出**对应的重构手法**。详见 [[Week3-Reviews-OODesign-Refactoring#10. Refactoring Catalog 重构目录]]。

---

## 11. Practical Example — A Simple Web Server 实例：简单Web服务器

The "500 Lines or Less" reading demonstrates real-world design principles through building a web server.

### Key Design Lessons from the Web Server 设计教训

#### HTTP Protocol Basics:
```
Client Request:              Server Response:
GET /page.html HTTP/1.1      HTTP/1.1 200 OK
Host: example.com            Content-Type: text/html

                             <html>...</html>
```

#### Design Principle Application:

1. **Separation of Concerns** — The server separates:
   - **Request parsing** (understanding what the client wants)
   - **Request handling** (deciding how to respond)
   - **Response generation** (creating the HTTP response)

2. **Inheritance and Polymorphism** — Different types of requests (static file, CGI script) are handled by different handler classes that share a common interface.

```
BaseRequestHandler
    ├── StaticFileHandler    → serves static files (.html, .css)
    └── CGIHandler           → executes server-side scripts
```

3. **Strategy-like Pattern** — The server uses a **dispatch mechanism** to route requests to the appropriate handler, similar to the [[#Strategy Pattern 策略模式 (Behavioral)|Strategy Pattern]].

4. **[[#3.2 Open/Closed Principle (OCP) 开闭原则|Open/Closed Principle]]** in action — Adding new request types (e.g., a REST API handler) only requires creating a new handler class, not modifying the server core.

> 这个例子展示了设计原则如何在真实代码中应用。Web服务器通过分离关注点、使用继承和多态来保持代码的可扩展性。

---

## Exam Quick Review 考试速览

### Must-Know Concepts 必知概念

| Topic | Key Points |
|-------|------------|
| **Design Principles** | DRY, KISS, YAGNI, SoC, Composition > Inheritance |
| **SOLID** | SRP (one reason to change), OCP (extend not modify), LSP (subtypes replaceable), ISP (small interfaces), DIP (depend on abstractions) |
| **Coupling** | Low = good. Best: Data coupling. Worst: Content coupling |
| **Cohesion** | High = good. Best: Functional. Worst: Coincidental |
| **Information Hiding** | Hide internal details behind interfaces (Parnas) |
| **Design Patterns** | Strategy (swap algorithms), Observer (notify on change), Factory (create objects), Singleton (one instance) |
| **UML Class Diagram** | Classes, attributes, methods + relationships (association, aggregation, composition, inheritance) |
| **Sequence Diagram** | Objects on top, time flows down, messages are arrows |
| **CRC Cards** | Class-Responsibility-Collaboration; role-playing exercise (Beck & Cunningham) |
| **Architecture** | MVC (Model-View-Controller), Layered, Client-Server, Microservices |
| **Code Smells** | Long method, God class, duplicate code, feature envy... |
| **Refactoring** | Change structure, NOT behavior. Use with TDD for safety |

### Concept Relationship Map 概念关系图

```
Software Design
    │
    ├── Design Principles
    │       ├── DRY, KISS, YAGNI
    │       ├── Separation of Concerns → leads to → High Cohesion
    │       ├── Composition > Inheritance → leads to → Low Coupling
    │       └── SOLID
    │             ├── SRP ←→ High Cohesion
    │             ├── OCP ←→ Strategy Pattern, Factory Pattern
    │             ├── LSP ←→ Proper Inheritance
    │             ├── ISP ←→ Small Interfaces
    │             └── DIP ←→ Depend on Abstractions
    │
    ├── Quality Metrics
    │       ├── Coupling (LOW = good) ←→ Information Hiding
    │       └── Cohesion (HIGH = good) ←→ SRP
    │
    ├── Design Patterns (solutions to common problems)
    │       ├── Strategy ← uses OCP, DIP
    │       ├── Observer ← one-to-many notification
    │       ├── Factory ← encapsulate object creation
    │       └── Singleton ← exactly one instance (⚠️ anti-pattern risk)
    │
    ├── Modeling Tools
    │       ├── UML Class Diagrams (static structure)
    │       ├── UML Sequence Diagrams (dynamic interaction)
    │       └── CRC Cards (discovery & role-playing)
    │
    ├── Architecture Patterns (system-level)
    │       ├── MVC (most important for web)
    │       ├── Layered
    │       ├── Client-Server
    │       └── Microservices
    │
    └── Code Quality
            ├── Code Smells (warning signs)
            └── Refactoring (improve structure, keep behavior)
                    └── Enabled by TDD (Week 1)
```

### Week 1 ↔ Week 2 Connections

| Week 1 Concept | Week 2 Connection |
|----------------|-------------------|
| [[Week1-Software-Engineering-Introduction#5. Requirements Engineering 需求工程\|Requirements]] | Design transforms requirements into structure |
| [[Week1-Software-Engineering-Introduction#6. User Stories 用户故事\|User Stories]] | CRC Cards help discover classes FROM user stories |
| [[Week1-Software-Engineering-Introduction#7. Use Cases 用例\|Use Cases]] | Sequence diagrams visualize use case flows |
| [[Week1-Software-Engineering-Introduction#11. Test-Driven Development (TDD) 测试驱动开发\|TDD]] | Refactoring is the "Refactor" step in Red-Green-Refactor |
| [[Week1-Software-Engineering-Introduction#4.5 Extreme Programming (XP) 极限编程\|XP]] | YAGNI, refactoring, simple design are XP practices |

---

> **Study Strategy 学习建议**:
> 1. SOLID 五个原则要能**默写 + 举例**，这是设计部分最核心的内容
> 2. Coupling 和 Cohesion 的等级从好到坏要能排序
> 3. MVC 三个组件的职责和交互方向要画出来
> 4. CRC Cards 的使用流程要理解（从场景到发现类）
> 5. 代码异味和对应的重构方法要能配对

---

*Notes generated from: 04 Design.pdf, "A Laboratory for Teaching Object-Oriented Thinking" (Beck & Cunningham), "A Simple Web Server" (500 Lines or Less)*
