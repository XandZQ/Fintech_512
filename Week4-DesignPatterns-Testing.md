# Week 4: Design Patterns & Software Testing 设计模式与软件测试

> **Course**: Software Engineering
> **Coverage**: Slides 07 (Design Patterns), 08 (Testing) + UML Package Diagrams + C4 Model + Test Code Examples
> **Prerequisite knowledge**: [[Week2-Software-Design]] (SOLID, Coupling/Cohesion) → [[Week3-Reviews-OODesign-Refactoring]] (OO Design, GRASP, Code Smells)

---

## Table of Contents

- [[#1. Design Patterns Overview 设计模式概述]]
- [[#2. Strategy Pattern 策略模式]]
- [[#3. Observer Pattern 观察者模式]]
- [[#4. Decorator Pattern 装饰者模式]]
- [[#5. Factory Patterns 工厂模式]]
- [[#6. Singleton Pattern 单例模式]]
- [[#7. Adapter Pattern 适配器模式]]
- [[#8. Facade Pattern 外观模式]]
- [[#9. Command Pattern 命令模式]]
- [[#10. Template Method Pattern 模板方法模式]]
- [[#11. Iterator Pattern 迭代器模式]]
- [[#12. Composite Pattern 组合模式]]
- [[#13. State Pattern 状态模式]]
- [[#14. MVC as Compound Pattern MVC复合模式]]
- [[#15. Anti-Patterns 反模式]]
- [[#16. UML Package Diagrams 包图]]
- [[#17. Component Diagrams & C4 Model 组件图与C4模型]]
- [[#18. Software Testing Overview 软件测试概述]]
- [[#19. V-Model & Testing Levels V模型与测试层级]]
- [[#20. Unit Testing with JUnit 单元测试]]
- [[#21. Test Doubles 测试替身]]
- [[#22. Test Coverage Metrics 测试覆盖率]]
- [[#23. Control Flow Graphs 控制流图]]
- [[#24. Black-Box Testing Techniques 黑盒测试技术]]
- [[#25. Mutation Testing 变异测试]]
- [[#26. Property-Based Testing 属性测试]]
- [[#27. Test Smells 测试异味]]
- [[#28. BDD & Cucumber 行为驱动开发]]
- [[#29. Integration & System Testing 集成与系统测试]]
- [[#Exam Quick Review 考试速览]]

---

## 1. Design Patterns Overview 设计模式概述

### What are Design Patterns? 什么是设计模式？

Design Pattern 是**经过验证的、可复用的解决方案模板**，用来解决面向对象设计中反复出现的问题。它们不是代码，而是**解决问题的通用方案描述**。

> 💡 **Key insight**: Design Patterns 来自于 GoF (Gang of Four) — Gamma, Helm, Johnson, Vlissides 在 1994 年出版的经典书籍 "Design Patterns: Elements of Reusable Object-Oriented Software"。

### Three Categories of GoF Patterns 三大分类

| Category 分类 | Purpose 目的 | Patterns 包含的模式 |
|---|---|---|
| **Creational** 创建型 | 控制对象创建方式 | Factory Method, Abstract Factory, Singleton, Builder, Prototype |
| **Structural** 结构型 | 处理类和对象的组合 | Adapter, Decorator, Facade, Composite, Proxy, Bridge, Flyweight |
| **Behavioral** 行为型 | 对象之间的职责分配和通信 | Strategy, Observer, Command, Template Method, Iterator, State, Chain of Responsibility, Mediator, Visitor |

### Core OO Design Principles Behind Patterns 模式背后的核心原则

这些原则在 [[Week2-Software-Design]] 和 [[Week3-Reviews-OODesign-Refactoring]] 中已介绍，Design Patterns 正是这些原则的具体应用：

1. **Encapsulate what varies** 封装变化 — 把会变的部分隔离出来
2. **Program to an interface, not an implementation** 面向接口编程 — 依赖抽象，不依赖具体类
3. **Favor composition over inheritance** 组合优于继承 — 用 HAS-A 替代 IS-A 获得灵活性
4. **Strive for loosely coupled designs** 追求松耦合 — 减少对象间的依赖（参见 [[Week2-Software-Design#Coupling]])
5. **Open-Closed Principle** 开闭原则 — 对扩展开放，对修改关闭（参见 [[Week3-Reviews-OODesign-Refactoring#SOLID]])

---

## 2. Strategy Pattern 策略模式

> **Category**: Behavioral 行为型
> **Intent**: 定义一组算法，把每个算法封装起来，并使它们可互换。让算法的变化独立于使用算法的客户。

### Problem 问题场景

假设你在设计 Duck 模拟器 🦆。不同的 Duck 有不同的 fly() 和 quack() 行为：
- MallardDuck: 能飞，能叫
- RubberDuck: 不能飞，squeaks
- DecoyDuck: 不能飞，不能叫

如果用**继承**把 fly() 放在 Duck 基类里，RubberDuck 和 DecoyDuck 就会继承到不该有的行为！

### Solution 解决方案

**Encapsulate what varies** — 把 fly 和 quack 行为抽取成**接口**：

```
          <<interface>>                    <<interface>>
          FlyBehavior                      QuackBehavior
         +fly()                           +quack()
            △                                △
           / \                              / \
   FlyWithWings  FlyNoWay          Quack  Squeak  MuteQuack
```

```java
// Strategy Interface
public interface FlyBehavior {
    void fly();
}

// Concrete strategies
public class FlyWithWings implements FlyBehavior {
    public void fly() { System.out.println("I'm flying!"); }
}

public class FlyNoWay implements FlyBehavior {
    public void fly() { System.out.println("I can't fly"); }
}

// Context class
public abstract class Duck {
    FlyBehavior flyBehavior;      // HAS-A (composition)
    QuackBehavior quackBehavior;  // HAS-A

    public void performFly() {
        flyBehavior.fly();  // delegates to strategy
    }

    public void setFlyBehavior(FlyBehavior fb) {
        this.flyBehavior = fb;  // can change at RUNTIME!
    }
}
```

### Key Points 考试重点

- **Composition over Inheritance** — Duck HAS-A FlyBehavior, not IS-A
- **Runtime flexibility** — 可以在运行时通过 setter 改变行为
- **Encapsulates what varies** — fly/quack 行为被封装成独立的 strategy 对象
- Relates to [[Week3-Reviews-OODesign-Refactoring#GRASP Patterns]] — **Polymorphism** pattern

### UML Structure

```
   Context ◆────────> <<interface>> Strategy
   - strategy           + algorithm()
   + setStrategy()            △
   + doSomething()           / \
                    ConcreteStrategyA  ConcreteStrategyB
```

---

## 3. Observer Pattern 观察者模式

> **Category**: Behavioral 行为型
> **Intent**: 定义一对多的依赖关系，当一个对象（Subject）状态改变时，所有依赖者（Observers）都会被自动通知并更新。

### Problem 问题场景

Weather Station 气象站：有一个 WeatherData 对象，每当气象数据更新时，多个显示面板（当前状态、统计、预报）都需要更新。

### Solution 解决方案

```java
// Subject (Observable)
public interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

// Observer
public interface Observer {
    void update(float temp, float humidity, float pressure);
}

// Concrete Subject
public class WeatherData implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private float temperature, humidity, pressure;

    public void registerObserver(Observer o) { observers.add(o); }
    public void removeObserver(Observer o) { observers.remove(o); }

    public void notifyObservers() {
        for (Observer o : observers) {
            o.update(temperature, humidity, pressure); // PUSH model
        }
    }

    public void measurementsChanged() {
        notifyObservers();
    }
}
```

### Push vs Pull Model 推 vs 拉

| Aspect | Push 推模型 | Pull 拉模型 |
|--------|-----------|-----------|
| **Data flow** | Subject 把数据**推送**给 Observer | Observer 主动**拉取**需要的数据 |
| **Coupling** | Observer 被迫接收所有数据 | Observer 选择性获取数据 |
| **Flexibility** | 不够灵活，Subject 决定发什么 | 更灵活，Observer 按需取 |
| **Implementation** | `update(temp, humidity, pressure)` | `update(Subject s)` 然后 `s.getTemp()` |

### Key Design Principle 关键原则

> **Loose Coupling** 松耦合: Subject 只知道 Observer 实现了某个接口，不知道具体类是什么。这样可以随时添加新的 Observer 而不用修改 Subject。

---

## 4. Decorator Pattern 装饰者模式

> **Category**: Structural 结构型
> **Intent**: 动态地给对象添加额外的职责（功能），比继承更灵活的替代方案。

### Problem 问题场景

Starbuzz Coffee ☕：有各种饮料 (DarkRoast, Espresso, HouseBlend)，可以加各种配料 (Mocha, Whip, Soy, Milk)。如果用继承组合所有可能，类的数量会**爆炸** (class explosion)！

### Solution 解决方案

```
         Beverage (abstract component)
         + getDescription()
         + cost()
              △
             / \
     HouseBlend   CondimentDecorator (abstract)
     DarkRoast     + getDescription()
     Espresso           △
                       / \
                   Mocha  Whip  Soy
```

```java
// Abstract Component
public abstract class Beverage {
    String description = "Unknown Beverage";
    public String getDescription() { return description; }
    public abstract double cost();
}

// Concrete Component
public class DarkRoast extends Beverage {
    public DarkRoast() { description = "Dark Roast Coffee"; }
    public double cost() { return 0.99; }
}

// Abstract Decorator — IS-A Beverage, also HAS-A Beverage
public abstract class CondimentDecorator extends Beverage {
    Beverage beverage;  // wraps a beverage
    public abstract String getDescription();
}

// Concrete Decorator
public class Mocha extends CondimentDecorator {
    public Mocha(Beverage beverage) { this.beverage = beverage; }
    public String getDescription() { return beverage.getDescription() + ", Mocha"; }
    public double cost() { return 0.20 + beverage.cost(); }
}

// Usage — wrapping objects layer by layer
Beverage b = new DarkRoast();           // $0.99
b = new Mocha(b);                       // $0.99 + $0.20
b = new Mocha(b);                       // $1.19 + $0.20
b = new Whip(b);                        // $1.39 + $0.10
System.out.println(b.cost());           // $1.49
```

### Key Points 考试重点

- **Open-Closed Principle** (OCP): 不修改现有代码就能添加新配料 → 参见 [[Week2-Software-Design#SOLID]]
- Decorator IS-A Component (继承保证类型兼容), 也 HAS-A Component (组合实现包装)
- 可以**无限嵌套**装饰层
- Java I/O stream 就是经典的 Decorator Pattern 例子 (BufferedInputStream wraps FileInputStream)

---

## 5. Factory Patterns 工厂模式

### 5.1 Factory Method Pattern 工厂方法模式

> **Category**: Creational 创建型
> **Intent**: 定义一个创建对象的接口，但让子类决定实例化哪个类。将对象的实例化推迟到子类。

```java
// Creator (abstract)
public abstract class PizzaStore {
    public Pizza orderPizza(String type) {
        Pizza pizza = createPizza(type);  // factory method
        pizza.prepare();
        pizza.bake();
        pizza.cut();
        pizza.box();
        return pizza;
    }

    // Factory method — subclasses decide WHAT to create
    protected abstract Pizza createPizza(String type);
}

// Concrete Creator
public class NYPizzaStore extends PizzaStore {
    protected Pizza createPizza(String type) {
        if (type.equals("cheese")) return new NYStyleCheesePizza();
        if (type.equals("veggie")) return new NYStyleVeggiePizza();
        return null;
    }
}
```

**Key**: Creator 类中的 `orderPizza()` 不知道具体创建哪种 Pizza — 由子类决定。这就是 **Hollywood Principle** "Don't call us, we'll call you" 的体现。

### 5.2 Abstract Factory Pattern 抽象工厂模式

> **Intent**: 提供一个接口来创建**一系列相关的对象**（product family），而不需要指定它们的具体类。

```java
// Abstract Factory
public interface PizzaIngredientFactory {
    Dough createDough();
    Sauce createSauce();
    Cheese createCheese();
    Clams createClams();
}

// Concrete Factory — NY style ingredients
public class NYPizzaIngredientFactory implements PizzaIngredientFactory {
    public Dough createDough() { return new ThinCrustDough(); }
    public Sauce createSauce() { return new MarinaraSauce(); }
    public Cheese createCheese() { return new ReggianoCheese(); }
    public Clams createClams() { return new FreshClams(); }
}
```

### Factory Method vs Abstract Factory 对比

| Aspect | Factory Method | Abstract Factory |
|--------|---------------|-----------------|
| **Creates** | 一个产品 | 一族相关产品 |
| **Method** | 通过继承（子类重写 factory method） | 通过组合（将 factory 作为参数传入） |
| **Class count** | 每种产品对应一个 Creator 子类 | 一个 factory 对应一族产品 |
| **Flexibility** | 创建单个对象 | 创建一整套相关对象 |

---

## 6. Singleton Pattern 单例模式

> **Category**: Creational 创建型
> **Intent**: 确保一个类只有一个实例，并提供全局访问点。

### Basic Implementation

```java
public class Singleton {
    private static Singleton uniqueInstance;

    private Singleton() {}  // private constructor!

    public static Singleton getInstance() {
        if (uniqueInstance == null) {
            uniqueInstance = new Singleton();
        }
        return uniqueInstance;
    }
}
```

⚠️ **Problem**: 上面的代码在**多线程**环境下不安全！两个线程可能同时进入 `if` 判断。

### Thread-Safe Implementations 线程安全实现

| Approach | Code | Pros | Cons |
|----------|------|------|------|
| **Synchronized method** | `synchronized getInstance()` | Simple | Performance hit on every call |
| **Eager initialization** | `static Singleton instance = new Singleton()` | Simple, thread-safe | Instance created even if never used |
| **Double-checked locking** | Check null → synchronized → check null again | Only syncs on first creation | Needs `volatile` keyword |
| **Enum Singleton** | `enum Singleton { INSTANCE; }` | Best! Thread-safe, serialization-safe | Looks unusual |

```java
// Double-checked locking
public class Singleton {
    private volatile static Singleton instance;

    public static Singleton getInstance() {
        if (instance == null) {              // First check (no locking)
            synchronized (Singleton.class) {
                if (instance == null) {      // Second check (with lock)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}

// Enum approach (recommended)
public enum Singleton {
    INSTANCE;
    // add methods here
}
```

---

## 7. Adapter Pattern 适配器模式

> **Category**: Structural 结构型
> **Intent**: 将一个类的接口转换成客户期望的另一个接口。让原本不兼容的类可以合作。

### Example: Turkey acting as a Duck

```java
// Target interface (what the client expects)
public interface Duck {
    void quack();
    void fly();
}

// Adaptee (what we have)
public class WildTurkey implements Turkey {
    public void gobble() { System.out.println("Gobble gobble"); }
    public void fly() { System.out.println("I'm flying a short distance"); }
}

// Adapter — wraps Turkey, makes it look like Duck
public class TurkeyAdapter implements Duck {
    Turkey turkey;

    public TurkeyAdapter(Turkey turkey) { this.turkey = turkey; }

    public void quack() { turkey.gobble(); }  // translate quack → gobble
    public void fly() {
        for (int i = 0; i < 5; i++) turkey.fly();  // turkeys fly short
    }
}
```

### Object Adapter vs Class Adapter

| Type | Mechanism | Language Requirement |
|------|-----------|---------------------|
| **Object Adapter** 对象适配器 | 组合 (composition) — wraps adaptee | Any OO language |
| **Class Adapter** 类适配器 | 多重继承 — extends both target and adaptee | Needs multiple inheritance (C++) |

> Java 只支持 Object Adapter，因为 Java 不支持多重继承（类）。

---

## 8. Facade Pattern 外观模式

> **Category**: Structural 结构型
> **Intent**: 提供一个统一的简单接口来访问子系统中的一组接口。Facade 定义了一个更高层次的接口，使子系统更容易使用。

### Example: Home Theater System 🎬

没有 Facade 时，看一场电影需要：

```java
amplifier.on();
amplifier.setDvd(dvd);
amplifier.setSurroundSound();
amplifier.setVolume(5);
dvd.on();
dvd.play(movie);
projector.on();
projector.wideScreenMode();
lights.dim(10);
screen.down();
popper.on();
popper.pop();
// ... 太复杂了！
```

有了 Facade：

```java
public class HomeTheaterFacade {
    // references to all subsystem components
    Amplifier amp; DvdPlayer dvd; Projector proj; ...

    public void watchMovie(String movie) {
        // one simple method does everything
        lights.dim(10);
        screen.down();
        projector.on();
        amp.on();
        dvd.on();
        dvd.play(movie);
    }

    public void endMovie() { ... }
}

// Client code — simple!
homeTheater.watchMovie("Raiders of the Lost Ark");
```

### Key Points

- Facade **不封装**子系统 — 子系统的类仍然可以直接使用
- Facade 遵循 **Principle of Least Knowledge** (Law of Demeter) — 参见 [[Week3-Reviews-OODesign-Refactoring#Law of Demeter]]
- 一个子系统可以有多个 Facade

---

## 9. Command Pattern 命令模式

> **Category**: Behavioral 行为型
> **Intent**: 将请求封装成对象，从而支持参数化、队列化、日志化、以及撤销操作 (undo)。

### Example: Remote Control 遥控器

```java
// Command interface
public interface Command {
    void execute();
    void undo();    // supports undo!
}

// Concrete Command
public class LightOnCommand implements Command {
    Light light;

    public LightOnCommand(Light light) { this.light = light; }
    public void execute() { light.on(); }
    public void undo() { light.off(); }  // reverse of execute
}

// Invoker
public class RemoteControl {
    Command[] onCommands;
    Command lastCommand;  // for undo

    public void onButtonPressed(int slot) {
        onCommands[slot].execute();
        lastCommand = onCommands[slot];
    }

    public void undoButtonPressed() {
        lastCommand.undo();
    }
}

// Receiver — the actual object that does the work
public class Light {
    public void on() { ... }
    public void off() { ... }
}
```

### Roles in Command Pattern

```
Client → creates ConcreteCommand, sets its Receiver
Invoker → holds Command, calls execute() when needed
Command → interface with execute() and undo()
Receiver → knows how to perform the actual work
```

### Key Points

- **Decouples** invoker from receiver — 遥控器不需要知道灯的细节
- Supports **undo/redo** — 保存 lastCommand 来支持撤销
- Supports **macro commands** — 一个命令可以包含多个子命令
- Supports **queuing** and **logging** — 命令可以排队执行、记录日志

---

## 10. Template Method Pattern 模板方法模式

> **Category**: Behavioral 行为型
> **Intent**: 在基类中定义算法的骨架，将某些步骤的实现推迟到子类。子类可以重新定义算法的某些步骤而不改变其结构。

### Example: Caffeine Beverages ☕🍵

Coffee 和 Tea 的制作过程很相似：

```java
public abstract class CaffeineBeverage {
    // Template Method — defines the algorithm skeleton
    final void prepareRecipe() {  // final! 子类不能改变流程
        boilWater();
        brew();              // abstract — subclass must implement
        pourInCup();
        addCondiments();     // abstract — subclass must implement
    }

    abstract void brew();           // 子类实现
    abstract void addCondiments();  // 子类实现

    void boilWater() { System.out.println("Boiling water"); }
    void pourInCup() { System.out.println("Pouring into cup"); }
}

public class Coffee extends CaffeineBeverage {
    void brew() { System.out.println("Dripping coffee through filter"); }
    void addCondiments() { System.out.println("Adding sugar and milk"); }
}

public class Tea extends CaffeineBeverage {
    void brew() { System.out.println("Steeping the tea"); }
    void addCondiments() { System.out.println("Adding lemon"); }
}
```

### Hook Methods 钩子方法

Hook 是基类中有**默认实现**（通常什么都不做）的方法，子类**可以选择性**重写：

```java
public abstract class CaffeineBeverage {
    final void prepareRecipe() {
        boilWater();
        brew();
        pourInCup();
        if (customerWantsCondiments()) {  // hook!
            addCondiments();
        }
    }

    boolean customerWantsCondiments() {
        return true;  // default — hook method
    }
}
```

### Hollywood Principle 好莱坞原则

> "Don't call us, we'll call you" — 高层组件（基类）调用低层组件（子类），而不是反过来。

Template Method 就是 Hollywood Principle 的典型体现：基类控制算法流程，在需要时"call" 子类的方法。

---

## 11. Iterator Pattern 迭代器模式

> **Category**: Behavioral 行为型
> **Intent**: 提供一种方法来顺序访问聚合对象中的各个元素，而不暴露其内部表示。

### Problem

两个餐厅的菜单用了不同的数据结构：一个用 ArrayList，一个用 Array。客户代码必须知道两种遍历方式。

### Solution

```java
public interface Iterator {
    boolean hasNext();
    Object next();
}

// Now both menus provide an iterator
public class DinerMenuIterator implements Iterator {
    MenuItem[] items;
    int position = 0;

    public boolean hasNext() { return position < items.length && items[position] != null; }
    public Object next() { return items[position++]; }
}
```

### Key Point

- **Single Responsibility** — 把遍历的职责从集合类中分离出来
- Java 已内置 `java.util.Iterator` 接口

---

## 12. Composite Pattern 组合模式

> **Category**: Structural 结构型
> **Intent**: 将对象组合成树形结构以表示"部分-整体"的层次结构。Composite 让客户可以一致地对待单个对象和组合对象。

### Example: Menu System 菜单系统

```
         MenuComponent (abstract)
         + print()
         + add(MenuComponent)
         + remove(MenuComponent)
              △
             / \
      MenuItem   Menu
      (leaf)     (composite — contains MenuComponents)
```

```java
public abstract class MenuComponent {
    public void add(MenuComponent c) { throw new UnsupportedOperationException(); }
    public void remove(MenuComponent c) { throw new UnsupportedOperationException(); }
    public void print() { throw new UnsupportedOperationException(); }
}

public class MenuItem extends MenuComponent {
    String name; double price;
    public void print() { System.out.println(name + " $" + price); }
}

public class Menu extends MenuComponent {
    List<MenuComponent> components = new ArrayList<>();
    String name;

    public void add(MenuComponent c) { components.add(c); }

    public void print() {
        System.out.println(name);
        for (MenuComponent c : components) {
            c.print();  // recursive! Works for both MenuItem and sub-Menu
        }
    }
}
```

### Key Points

- **Uniformity** — 叶子节点 (MenuItem) 和组合节点 (Menu) 对客户代码看起来一样
- 支持**递归**结构 — Menu 可以包含 Menu
- 违反了 Single Responsibility (一个接口同时管理叶子和组合行为)，但这是有意的 trade-off

---

## 13. State Pattern 状态模式

> **Category**: Behavioral 行为型
> **Intent**: 允许对象在内部状态改变时改变其行为。对象看起来好像修改了它的类。

### Example: Gumball Machine 🎱

状态机有多个状态（No Quarter, Has Quarter, Sold, Sold Out），每个状态对操作的响应不同：

```java
// State interface
public interface State {
    void insertQuarter();
    void ejectQuarter();
    void turnCrank();
    void dispense();
}

// Concrete State
public class HasQuarterState implements State {
    GumballMachine machine;

    public void insertQuarter() {
        System.out.println("You already inserted a quarter");
    }

    public void ejectQuarter() {
        System.out.println("Quarter returned");
        machine.setState(machine.getNoQuarterState()); // transition!
    }

    public void turnCrank() {
        System.out.println("You turned...");
        machine.setState(machine.getSoldState()); // transition!
    }

    public void dispense() {
        System.out.println("No gumball dispensed");
    }
}

// Context
public class GumballMachine {
    State noQuarterState, hasQuarterState, soldState, soldOutState;
    State currentState;

    public void insertQuarter() {
        currentState.insertQuarter();  // delegate to current state
    }
}
```

### State vs Strategy Pattern 对比

| Aspect | State | Strategy |
|--------|-------|----------|
| **Intent** | 根据内部状态改变行为 | 选择算法 |
| **State transitions** | States 之间会转换 | 通常固定一个 strategy |
| **Who decides** | State 对象自己触发转换 | Client 选择 strategy |
| **Awareness** | State 知道其他 states | Strategy 不知道其他 strategies |

---

## 14. MVC as Compound Pattern MVC复合模式

MVC (Model-View-Controller) 是多个设计模式的组合应用：

```
        User Action
            ↓
    ┌─── Controller ───┐
    │   (Strategy)      │
    ↓                   ↓
  Model ──────────→ View
  (Observable)      (Observer + Composite)
  notifyObservers() update()
```

| MVC Component | Design Pattern | Role |
|---------------|---------------|------|
| **Model** | Observer (Subject) | 数据和业务逻辑，状态变化时通知 View |
| **View** | Observer + Composite | 观察 Model 的变化，UI 用 Composite 组织 |
| **Controller** | Strategy | View 的行为策略，可以替换不同的 Controller |

### How they connect

1. **User** 操作 View (点击按钮)
2. **View** 把操作转给 **Controller** (Strategy pattern)
3. **Controller** 更新 **Model**
4. **Model** 通知所有 **Views** (Observer pattern)
5. **View** 从 Model 获取新数据并更新显示

---

## 15. Anti-Patterns 反模式

Anti-patterns 是常见的**糟糕做法**，看起来像是解决方案，但实际上会造成更多问题。与 [[Week3-Reviews-OODesign-Refactoring#Code Smells]] 相关但层次更高。

| Anti-Pattern | 描述 | Problem |
|---|---|---|
| **God Class** 上帝类 | 一个类做了太多事情，知道太多信息 | 违反 SRP, 难以维护和测试 |
| **Spaghetti Code** 意面代码 | 代码结构混乱，控制流纠缠不清 | 难以理解和修改 |
| **Golden Hammer** 金锤子 | 对所有问题都用同一个熟悉的技术/模式 | 不适合的方案导致复杂性 |
| **Lava Flow** 熔岩流 | 没人敢删的遗留代码，因为不确定有没有用 | 代码膨胀，维护成本增加 |
| **Copy-Paste Programming** 复制粘贴 | 复制代码而不是抽象重用 | 违反 DRY, bug 要改多处 |

---

## 16. UML Package Diagrams 包图

Package Diagram 用来组织和展示系统的**模块化结构**。

### Notation 表示法

```
┌─────────────────┐
│ <<stereotype>>  │
│ PackageName     │
├─────────────────┤
│  Class1         │
│  Class2         │
│  Interface1     │
└─────────────────┘
```

### Dependency Stereotypes 依赖关系

| Stereotype | Meaning | 说明 |
|---|---|---|
| `<<import>>` | Public import | 被导入包的 public 元素可以不加限定名使用 |
| `<<access>>` | Private import | 被访问包的元素可用但不再导出 |
| `<<merge>>` | Package merge | 合并两个包的内容 |

### Dependency Arrow 依赖箭头

```
  Package A ----> Package B    (A depends on B)
              <<import>>
```

虚线箭头从**依赖者**指向**被依赖者**。

---

## 17. Component Diagrams & C4 Model 组件图与C4模型

### Component Diagrams 组件图

Component 是系统中**可替换的模块化部分**，通过接口与外界交互：

```
  ┌──────────────┐
  │ <<component>> │
  │   OrderService │──○ IOrderService    (provided interface 提供接口)
  │               │──◐ IPayment         (required interface 需要接口)
  └──────────────┘
```

- **Provided interface** ○── (lollipop): 组件**提供**给外界使用的接口
- **Required interface** ◐── (socket): 组件**需要**从外部获得的接口

### C4 Model — 4 Levels of Abstraction

C4 Model 由 Simon Brown 提出，用 4 个层次来描述软件架构：

| Level | Name | 描述 | 类比 |
|-------|------|------|------|
| **Level 1** | System Context | 系统在**环境**中的位置，显示外部用户和系统 | 地图上标注城市 |
| **Level 2** | Container | 系统内部的**容器**（web app, database, API等） | 城市里的建筑 |
| **Level 3** | Component | 每个容器内部的**组件** | 建筑里的楼层 |
| **Level 4** | Code | 每个组件的**代码级别**（UML类图） | 楼层里的房间 |

```
Level 1: [User] → [My System] → [External System]
Level 2: [Web App] → [API] → [Database]
Level 3: [Controller] → [Service] → [Repository]
Level 4: UML Class Diagrams
```

> 💡 不是每个系统都需要到 Level 4 — 通常 Level 1-3 足够交流架构决策。

---

## 18. Software Testing Overview 软件测试概述

### Why Testing? 为什么要测试？

Testing 的目标不是证明软件没有 bug，而是**发现缺陷**（finding defects）。

> **Dijkstra**: "Testing can show the presence of bugs, but never their absence."

### Testing Terminology 术语

| Term | 定义 |
|------|------|
| **Error/Mistake** | 人类犯的错误（思维上的） |
| **Fault/Bug/Defect** | 代码中的缺陷（Error 的结果） |
| **Failure** | 程序运行时表现出的错误行为（Fault 被触发后） |
| **Test Case** | 输入 + 预期输出 |
| **Test Suite** | 一组 test cases |
| **SUT** | System Under Test 被测系统 |

---

## 19. V-Model & Testing Levels V模型与测试层级

### V-Model

V-Model 把**开发阶段**和**测试阶段**对应起来：

```
Requirements ──────────────────── Acceptance Testing
     ↓                                    ↑
  System Design ──────────── System Testing
       ↓                          ↑
    Architecture Design ── Integration Testing
         ↓                    ↑
      Detailed Design ── Unit Testing
              ↓          ↑
            Implementation
```

左边是开发（从上到下），右边是测试（从下到上）。每个开发阶段都有对应的测试层级。

### Testing Levels 测试层级

| Level | 测试什么 | 谁做 | Test Basis |
|-------|---------|------|-----------|
| **Unit Testing** 单元测试 | 单个方法/类 | Developer | Detailed Design |
| **Integration Testing** 集成测试 | 模块之间的交互 | Developer/Tester | Architecture Design |
| **System Testing** 系统测试 | 整个系统的功能和非功能需求 | Tester | System Design |
| **Acceptance Testing** 验收测试 | 用户需求是否满足 | User/Customer | Requirements |

---

## 20. Unit Testing with JUnit 单元测试

### JUnit Basics

```java
import org.junit.Test;
import org.junit.Before;
import org.junit.After;
import static org.junit.Assert.*;

public class CalculatorTest {
    Calculator calc;

    @Before  // runs BEFORE each test method
    public void setUp() {
        calc = new Calculator();
    }

    @After  // runs AFTER each test method
    public void tearDown() {
        calc = null;
    }

    @Test
    public void testAdd() {
        assertEquals(5, calc.add(2, 3));
    }

    @Test(expected = ArithmeticException.class)
    public void testDivideByZero() {
        calc.divide(1, 0);
    }
}
```

### JUnit Annotations

| Annotation | 说明 | 执行频率 |
|---|---|---|
| `@Test` | 标记测试方法 | 每个 test method |
| `@Before` | 每个测试前执行 (setup) | 每个 test method 前 |
| `@After` | 每个测试后执行 (teardown) | 每个 test method 后 |
| `@BeforeClass` | 所有测试前执行一次 (static) | 整个 class 一次 |
| `@AfterClass` | 所有测试后执行一次 (static) | 整个 class 一次 |
| `@Test(expected=X.class)` | 期望抛出异常 | — |
| `@Test(timeout=1000)` | 设置超时 (ms) | — |
| `@Ignore` | 跳过测试 | — |

### Common Assertions

```java
assertEquals(expected, actual);      // 相等
assertNotEquals(a, b);               // 不相等
assertTrue(condition);               // 为 true
assertFalse(condition);              // 为 false
assertNull(object);                  // 为 null
assertNotNull(object);               // 不为 null
assertSame(obj1, obj2);              // 同一个对象引用
assertArrayEquals(arr1, arr2);       // 数组相等
```

### Test Structure: AAA Pattern

```
Arrange → 准备测试数据和对象
Act     → 执行被测方法
Assert  → 验证结果
```

---

## 21. Test Doubles 测试替身

当被测对象依赖于外部系统（数据库、网络、API）时，我们用 **Test Doubles** 来替代真实的依赖，使测试更快、更可控。

### Five Types of Test Doubles 五种测试替身

| Type | 中文 | Purpose | 描述 |
|------|------|---------|------|
| **Dummy** | 哑对象 | 填充参数 | 传入但**从不使用**，只为了满足方法签名 |
| **Stub** | 桩 | 提供预设返回值 | 对特定调用返回**硬编码**的结果 |
| **Spy** | 间谍 | 记录调用信息 | 像 Stub 一样工作，但还**记录**被调用了什么 |
| **Mock** | 模拟对象 | 验证交互 | 预设**期望**的调用，验证调用是否发生 |
| **Fake** | 伪对象 | 简化实现 | 有实际的工作实现，但更简单（如内存数据库） |

### Complexity Spectrum 复杂度谱

```
Dummy → Stub → Spy → Mock → Fake
简单                              复杂
(no logic)                    (real implementation)
```

### Example with Stock Quote App (from test_code)

课程提供的 `refactored/` 目录展示了 Test Doubles 的实际应用：

```python
# stockquote.py — SUT (System Under Test)
class StockQuote:
    def __init__(self, service):    # Dependency Injection!
        self.service = service

    def get_price(self, symbol):
        return self.service.fetch_price(symbol)

# test_stock.py — using a Stub
class StubService:
    def fetch_price(self, symbol):
        return 150.0  # hardcoded response — this is a STUB

def test_get_price():
    stub = StubService()
    sq = StockQuote(stub)
    assert sq.get_price("AAPL") == 150.0
```

> 💡 注意这里用了 **Dependency Injection** (参见 [[Week3-Reviews-OODesign-Refactoring#Dependency Injection]]) 来注入 test double。

### Mockito Framework (Java)

```java
import static org.mockito.Mockito.*;

// Create a mock
List<String> mockList = mock(List.class);

// Stubbing — define behavior
when(mockList.get(0)).thenReturn("first");
when(mockList.size()).thenReturn(10);

// Use the mock
System.out.println(mockList.get(0));  // "first"

// Verify interactions — did the method get called?
verify(mockList).get(0);              // passes — get(0) was called
verify(mockList, times(1)).get(0);    // passes — called exactly once
verify(mockList, never()).clear();     // passes — clear was never called

// Argument matchers
when(mockList.get(anyInt())).thenReturn("element");
verify(mockList).get(eq(0));
```

### Mockito Key Methods

| Method | Purpose |
|--------|---------|
| `mock(Class)` | 创建 mock 对象 |
| `when(x).thenReturn(y)` | 设置 stub 行为 |
| `when(x).thenThrow(e)` | 设置抛出异常 |
| `verify(mock).method()` | 验证方法被调用 |
| `verify(mock, times(n))` | 验证调用次数 |
| `verify(mock, never())` | 验证从未调用 |
| `anyInt()`, `anyString()` | 参数匹配器 |

---

## 22. Test Coverage Metrics 测试覆盖率

Coverage 衡量的是测试对代码的**执行程度**。

### Coverage Types 覆盖率类型

| Metric | 中文 | 定义 | 公式 |
|--------|------|------|------|
| **Statement Coverage** | 语句覆盖 | 执行了多少语句 | `executed statements / total statements` |
| **Branch/Decision Coverage** | 分支覆盖 | 覆盖了多少分支 | `executed branches / total branches` |
| **Condition Coverage** | 条件覆盖 | 每个布尔子条件的 true/false | 每个条件至少 true 和 false 各一次 |
| **Path Coverage** | 路径覆盖 | 覆盖了多少执行路径 | `executed paths / total paths` |
| **MC/DC** | 修改条件/判定覆盖 | 每个条件独立影响判定结果 | 用于航空等高安全领域 |

### Coverage Hierarchy 层级关系

```
Path Coverage (最强)
    ⊃ MC/DC
        ⊃ Branch Coverage
            ⊃ Statement Coverage (最弱)
```

> 100% Statement Coverage ≠ 100% Branch Coverage。一个 `if` 语句可能只走了 true 分支。

### Example with factorial.py

```python
def factorial(x):       # 1
    result = 1           # 2
    while x > 1:         # 3 (branch: true/false)
        result = result * x  # 4
        x = x - 1        # 5
    return result         # 6
```

- `factorial(3)`: 执行路径 1→2→3(T)→4→5→3(T)→4→5→3(F)→6 ✅ covers both branches of while
- `factorial(0)`: 执行路径 1→2→3(F)→6 — while 条件直接 false
- 要达到 100% branch coverage: 至少需要一个 x > 1 和一个 x ≤ 1 的测试用例

### Example with price.py

```python
def calculate_price(stock_price, num_items, discount):
    quantity_discount = 0.0
    result = 0.0

    if num_items >= 10:           # Branch 1
        quantity_discount = 15
    elif num_items >= 5:          # Branch 2
        quantity_discount = 10
    else:                         # Branch 3
        quantity_discount = 0

    if discount > quantity_discount:  # Branch 4
        quantity_discount = discount

    result = stock_price/100.0 * (100-quantity_discount) * num_items
    return result
```

要达到 100% branch coverage，需要覆盖所有分支：
- `num_items >= 10` → Branch 1
- `5 <= num_items < 10` → Branch 2
- `num_items < 5` → Branch 3
- `discount > quantity_discount` → Branch 4 true
- `discount <= quantity_discount` → Branch 4 false

---

## 23. Control Flow Graphs 控制流图

CFG 是测试覆盖分析的基础工具，把代码结构可视化为图。

### How to Build a CFG 如何构建

1. **Node** = 一条或一组顺序执行的语句 (basic block)
2. **Edge** = 控制流转移（条件分支、循环等）
3. **Decision node** = 有多个出边的节点（if, while, switch）

### Example: cfg.py

```python
def f(b, x, y):
    a = x + y           # Node 1
    z = 0               # Node 1 (same basic block)
    if (b < 4):          # Decision Node 2
        z = f(a, b, x)  # Node 3 (true branch)
    q = a + z            # Node 4 (merge point)
    if a > 3:            # Decision Node 5
        q = q - 2        # Node 6 (true branch)
    else:
        q = q + 3        # Node 7 (false branch)
    return q             # Node 8
```

```
    [1: a=x+y, z=0]
           ↓
    [2: b<4?]
       ↙    ↘
  [3: z=f()]  │
       ↘    ↙
    [4: q=a+z]
         ↓
    [5: a>3?]
       ↙    ↘
  [6: q=q-2]  [7: q=q+3]
       ↘    ↙
    [8: return q]
```

### Test case: `f(4, 0, 0)`

- b=4, x=0, y=0
- a = 0+0 = 0, z = 0
- b<4? → 4<4 is **False** → skip Node 3
- q = 0+0 = 0
- a>3? → 0>3 is **False** → Node 7: q = 0+3 = 3
- Path: 1 → 2(F) → 4 → 5(F) → 7 → 8
- Return value: 3

---

## 24. Black-Box Testing Techniques 黑盒测试技术

Black-box testing 只关注**输入和输出**，不看内部代码。

### 24.1 Equivalence Partitioning 等价类划分

将输入域划分为若干**等价类**，假设同一类中的任何值行为相同。

对于 `calculate_price(stock_price, num_items, discount)`:

**num_items 的等价类**:
| Class | Range | Expected quantity_discount |
|-------|-------|--------------------------|
| EP1 | num_items < 5 | 0 |
| EP2 | 5 ≤ num_items < 10 | 10 |
| EP3 | num_items ≥ 10 | 15 |

每个等价类**只需测试一个代表值**。

### 24.2 Boundary Value Analysis (BVA) 边界值分析

在等价类的**边界处**测试，因为 bug 最常出现在边界。

对于 `num_items`:
```
     EP1      |     EP2      |     EP3
  num < 5     | 5 ≤ num < 10 | num ≥ 10
              ↑              ↑
    Boundaries: 4,5          9,10
```

测试边界值: **4, 5, 9, 10**

> 💡 **BVA 原则**: Test at the boundary, one below, and one above.

### Combining EP and BVA

| Test Case | stock_price | num_items | discount | Expected Behavior |
|-----------|------------|-----------|----------|-------------------|
| TC1 | 100 | 3 | 0 | EP1: no quantity discount |
| TC2 | 100 | 4 | 0 | BVA: boundary of EP1 |
| TC3 | 100 | 5 | 0 | BVA: boundary of EP1/EP2 |
| TC4 | 100 | 7 | 0 | EP2: 10% discount |
| TC5 | 100 | 9 | 0 | BVA: boundary of EP2/EP3 |
| TC6 | 100 | 10 | 0 | BVA: boundary of EP3 |
| TC7 | 100 | 15 | 0 | EP3: 15% discount |
| TC8 | 100 | 5 | 20 | discount > quantity_discount |

---

## 25. Mutation Testing 变异测试

### Concept 概念

Mutation testing 通过在代码中引入**小的修改**（mutants 变异体）来评估测试质量。如果测试能检测到这些修改（杀死变异体），说明测试质量高。

### Process 流程

```
Original Code → Apply Mutation Operators → Mutants
                                              ↓
                                   Run Test Suite
                                     ↙        ↘
                              Mutant KILLED    Mutant SURVIVED
                              (test detected)  (test missed it!)
```

### Mutation Operators 变异算子

| Operator | Example | 描述 |
|----------|---------|------|
| **AOR** (Arithmetic) | `+` → `-` | 替换算术运算符 |
| **ROR** (Relational) | `>` → `>=` | 替换关系运算符 |
| **COR** (Conditional) | `&&` → `\|\|` | 替换逻辑运算符 |
| **SBR** (Statement Block) | Delete a statement | 删除语句 |
| **ASR** (Assignment) | `=` → `+=` | 替换赋值运算符 |

### Mutation Score 变异得分

```
Mutation Score = Killed Mutants / Total Mutants × 100%
```

- **高 Mutation Score** → 测试质量高
- **Survived Mutant** → 测试可能遗漏了某些场景
- **Equivalent Mutant** — 修改后行为没变，算"无效变异"，应从分母中排除

---

## 26. Property-Based Testing 属性测试

### Concept 概念

传统测试：给定**具体输入**，检查**具体输出**。
Property-based testing：定义**通用属性**，让框架**自动生成**大量随机输入来验证。

### Example

```python
# Traditional unit test
def test_sort():
    assert sort([3, 1, 2]) == [1, 2, 3]

# Property-based test
@given(lists(integers()))
def test_sort_properties(lst):
    result = sort(lst)
    assert len(result) == len(lst)           # Property 1: same length
    assert all(result[i] <= result[i+1]      # Property 2: sorted
               for i in range(len(result)-1))
    assert set(result) == set(lst)           # Property 3: same elements
```

### Key Concepts

- **Generator** — 生成随机输入数据的工具
- **Shrinking** — 当测试失败时，自动找到**最小失败输入**
- **QuickCheck** — 最早的 property-based testing 框架（Haskell），Python 有 Hypothesis

---

## 27. Test Smells 测试异味

Test Smells 是测试代码中的**不良实践**，类似于 [[Week3-Reviews-OODesign-Refactoring#Code Smells]]，但针对测试代码。

| Test Smell | 中文 | 描述 | 问题 |
|---|---|---|---|
| **Mystery Guest** | 神秘客人 | 测试依赖外部文件、数据库等隐式资源 | 环境变化导致测试不稳定 |
| **Resource Optimism** | 资源乐观 | 假设外部资源总是可用的 | 网络断开等情况下失败 |
| **Test Run War** | 测试竞争 | 多个测试竞争同一资源 | 并行执行时相互干扰 |
| **General Fixture** | 通用 Fixture | setUp() 准备了过多不必要的数据 | 难以理解每个测试需要什么 |
| **Eager Test** | 急切测试 | 一个测试方法测试太多功能 | 失败时难以定位问题 |
| **Lazy Test** | 懒惰测试 | 多个测试方法测试同一个功能 | 冗余和浪费 |
| **Assertion Roulette** | 断言轮盘 | 一个测试中有多个无消息的断言 | 失败时不知道是哪个断言 |
| **Indirect Testing** | 间接测试 | 测试通过其他类间接测试目标类 | 不聚焦，依赖链 |
| **Sensitive Equality** | 敏感相等 | 用 toString() 等做比较 | 格式变化就会破坏测试 |
| **Test Code Duplication** | 测试代码重复 | 测试之间复制粘贴代码 | 违反 DRY |

---

## 28. BDD & Cucumber 行为驱动开发

### BDD (Behavior-Driven Development)

BDD 是 [[Week1-Software-Engineering-Introduction#TDD]] 的扩展，用**自然语言**描述期望行为。

### Given-When-Then 三段式

```gherkin
Feature: User Login

  Scenario: Successful login with valid credentials
    Given the user is on the login page
    When the user enters "admin" and "password123"
    And clicks the login button
    Then the user should see the dashboard
    And a welcome message "Hello, admin" should be displayed
```

### Cucumber Framework

Cucumber 把 `.feature` 文件中的 Gherkin 语法与 **step definitions** (实际的测试代码) 关联：

```java
// Step definitions
@Given("the user is on the login page")
public void userOnLoginPage() {
    browser.navigateTo("/login");
}

@When("the user enters {string} and {string}")
public void userEntersCredentials(String username, String password) {
    browser.type("#username", username);
    browser.type("#password", password);
}

@Then("the user should see the dashboard")
public void userSeesDashboard() {
    assertTrue(browser.isOnPage("/dashboard"));
}
```

### Benefits of BDD

- **Stakeholder collaboration** — 非技术人员也能读懂测试
- **Living documentation** — 测试本身就是需求文档
- **Acceptance test automation** — 自动化验收测试

---

## 29. Integration & System Testing 集成与系统测试

### Integration Testing Strategies 集成策略

| Strategy | 中文 | 描述 | Pros | Cons |
|----------|------|------|------|------|
| **Big Bang** | 大爆炸 | 所有模块一起集成测试 | 简单，不需要 stubs/drivers | 难以定位错误 |
| **Top-Down** | 自顶向下 | 从顶层模块开始，逐步集成下层 | 早期验证架构 | 需要 **Stubs** 模拟未完成的底层 |
| **Bottom-Up** | 自底向上 | 从底层模块开始，逐步集成上层 | 无需 stubs | 需要 **Drivers** 来调用底层，晚才看到完整系统 |
| **Sandwich** | 三明治 | Top-down + Bottom-up 结合 | 结合两者优点 | 复杂 |

```
Top-Down:    A (real)
            / \
        Stub   Stub     ← 用 stub 代替未完成的模块

Bottom-Up:  Driver      ← 用 driver 调用底层模块
            / \
           B    C (real)
```

### System Testing 系统测试

测试**整个系统**的功能和非功能需求：

- **Functional testing** — 系统是否满足功能需求
- **Performance testing** — 响应时间、吞吐量
- **Security testing** — 安全漏洞
- **Usability testing** — 用户体验
- **Reliability testing** — 系统稳定性

### Acceptance Testing 验收测试

由**用户/客户**确认系统是否满足其需求：

- **Alpha testing** — 开发环境中由内部用户测试
- **Beta testing** — 真实环境中由外部用户测试
- **UAT (User Acceptance Testing)** — 正式的用户验收流程

---

## Exam Quick Review 考试速览

### Design Patterns 速记卡

| Pattern | 一句话总结 | 关键词 |
|---------|----------|--------|
| **Strategy** | 封装可互换的算法 | Composition, runtime behavior change |
| **Observer** | 一对多通知 | Subject-Observer, loose coupling |
| **Decorator** | 动态包装添加功能 | Wrapping, OCP |
| **Factory Method** | 子类决定创建什么 | Subclass instantiation |
| **Abstract Factory** | 创建一族相关产品 | Product families |
| **Singleton** | 确保唯一实例 | Private constructor, getInstance() |
| **Adapter** | 接口转换 | Incompatible → compatible |
| **Facade** | 简化复杂子系统接口 | Unified simple interface |
| **Command** | 封装请求为对象 | Undo, queue, logging |
| **Template Method** | 算法骨架在基类，步骤延迟到子类 | Hollywood Principle, final method |
| **Iterator** | 统一遍历接口 | hasNext()/next() |
| **Composite** | 部分-整体树结构 | Uniform treatment, recursion |
| **State** | 内部状态改变行为 | State transitions, delegation |

### Testing 速记卡

| Concept | Key Points |
|---------|-----------|
| **V-Model** | 开发↔测试一一对应 |
| **Test Doubles** | Dummy → Stub → Spy → Mock → Fake (复杂度递增) |
| **Statement Coverage** | 最弱覆盖，执行了多少行 |
| **Branch Coverage** | 每个 if/else 的 true 和 false 都覆盖 |
| **MC/DC** | 每个条件独立影响结果，航空标准 |
| **Equivalence Partitioning** | 划分等价类，每类测一个 |
| **Boundary Value Analysis** | 在边界处测试 |
| **Mutation Testing** | 修改代码看测试能否发现 |
| **Property-Based Testing** | 定义属性 + 自动生成输入 |

### Concept Relationship Map 概念关系图

```
[[Week2-Software-Design#SOLID]] ──────→ Design Patterns (具体实现)
                                           │
    OCP ──→ Decorator, Strategy            │
    SRP ──→ Iterator (separates traversal)  │
    DIP ──→ Factory Method, Abstract Factory│
    LSP ──→ All patterns use polymorphism   │
                                           ↓
[[Week3-Reviews-OODesign-Refactoring]]     Testing
    GRASP Polymorphism ──→ Strategy       │
    Code Smells ──→ Anti-Patterns         │
    Dependency Injection ──→ Test Doubles  │
                                          │
[[Week1-Software-Engineering-Introduction]]│
    TDD ──→ Unit Testing ──→ JUnit        │
    Agile ──→ BDD / Cucumber              │
    User Stories ──→ Acceptance Testing    ↓
```

### Pattern Categories Quick Reference

**When to use which pattern?**

| Situation | Pattern |
|-----------|---------|
| 需要在运行时切换算法 | **Strategy** |
| 对象状态变化需要通知多个依赖者 | **Observer** |
| 需要动态添加功能而不改变类 | **Decorator** |
| 想把对象创建和使用分离 | **Factory Method / Abstract Factory** |
| 全局只需要一个实例 | **Singleton** |
| 需要把不兼容的接口对接 | **Adapter** |
| 想简化复杂子系统的使用 | **Facade** |
| 需要支持撤销操作 | **Command** |
| 算法步骤固定但某些步骤可变 | **Template Method** |
| 需要统一遍历不同集合 | **Iterator** |
| 处理树形层次结构 | **Composite** |
| 对象行为随内部状态改变 | **State** |
