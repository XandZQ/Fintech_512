# Week 6: Test Doubles, Methodologies, Estimation & UI Design 测试替身、开发方法论、估算与UI设计

> 📅 Week 6 | 🎯 Midterm Review Notes
> 📖 Sources: `11 Test Doubles.pdf` ⭐, `12a Methodologies.pdf` ⭐, `11 Estimation.pdf`, `12 User Interface.pdf`
> ⭐ = Class Slides (最重要 Most Important)

---

## Table of Contents 目录

1. [[#1. Test Doubles Overview 测试替身概览]]
2. [[#2. Five Types of Test Doubles 五种测试替身]]
3. [[#3. Python unittest.mock 模块]]
4. [[#4. patch() Decorator 补丁装饰器]]
5. [[#5. Mock Assertions 模拟断言]]
6. [[#6. Dependency Injection 依赖注入]]
7. [[#7. SDLC Overview 软件开发生命周期概览]]
8. [[#8. Plan-Driven Models 计划驱动模型]]
9. [[#9. Agile Manifesto 敏捷宣言]]
10. [[#10. Scrum Framework 敏捷Scrum框架]]
11. [[#11. Kanban 看板方法]]
12. [[#12. Extreme Programming XP 极限编程]]
13. [[#13. Lean & DevOps 精益与运维开发]]
14. [[#14. Estimation Techniques 估算技术]]
15. [[#15. Function Point Analysis 功能点分析]]
16. [[#16. Story Points & Planning Poker]]
17. [[#17. COCOMO & PERT Models]]
18. [[#18. UI vs UX Design]]
19. [[#19. Nielsen's 10 Usability Heuristics 尼尔森十大可用性原则]]
20. [[#20. Shneiderman's 8 Golden Rules]]
21. [[#21. UI Design Process & Prototyping]]
22. [[#22. Accessibility 无障碍设计]]
23. [[#23. UI Evaluation Methods]]
24. [[#24. Exam Quick Review 考试速览]]
25. [[#25. Concept Relationship Map 概念关系图]]

---

## 1. Test Doubles Overview 测试替身概览

### What is a Test Double? 什么是测试替身？

Test Double 是测试中用来**代替真实对象**的替代品 (A test double is a generic term for any object used in place of a real object for testing purposes).

> 💡 **Key Idea**: 就像电影中的替身演员 (stunt double) 代替真实演员一样，Test Double 代替真实依赖来让测试更简单、更快、更可控。

### Why Use Test Doubles? 为什么需要？

| Reason 原因 | Explanation 说明 |
|:---|:---|
| **Isolation** 隔离 | Test one unit without depending on others 只测试一个单元，不依赖其他部分 |
| **Speed** 速度 | Avoid slow operations (DB, network, file I/O) 避免慢操作 |
| **Determinism** 确定性 | Remove randomness and external factors 消除随机性和外部因素 |
| **Unavailable Dependencies** 不可用依赖 | Test when real objects don't exist yet 当真实对象还不存在时也能测试 |
| **Edge Cases** 边界情况 | Simulate errors, exceptions, rare conditions 模拟错误、异常、罕见情况 |

> 📚 **术语来源**: Gerard Meszaros 在 *xUnit Test Patterns* 一书中定义了这套术语体系

---

## 2. Five Types of Test Doubles 五种测试替身

这是 Week 6 最核心的知识点之一 ⭐

### Comparison Table 对比表

| Type 类型 | Purpose 用途 | Responds to calls? | Records calls? | Has logic? |
|:---|:---|:---:|:---:|:---:|
| **Dummy** | Fills parameter lists, never actually used 填充参数列表 | ❌ | ❌ | ❌ |
| **Stub** | Provides canned/pre-set answers 提供预设的固定回答 | ✅ | ❌ | ❌ |
| **Spy** | Like Stub + records how it was called 像Stub但还记录调用信息 | ✅ | ✅ | ❌ |
| **Mock** | Pre-programmed with expectations, verified 预编程期望值并验证 | ✅ | ✅ | ❌ |
| **Fake** | Has working but simplified implementation 有真实但简化的实现 | ✅ | ❌ | ✅ |

### Detailed Explanation 详细解释

#### 🔹 Dummy 虚拟对象

```python
# Dummy: 只是占位，从不真正使用
def test_something():
    dummy_logger = None  # or any placeholder object
    service = MyService(logger=dummy_logger)  # logger not used in this test
    assert service.calculate(5) == 25
```

就是一个占位符，传进去但不会被调用 (Just a placeholder - passed around but never actually used).

#### 🔹 Stub 存根

```python
# Stub: 返回预设的固定值
class StubWeatherService:
    def get_temperature(self, city):
        return 25  # Always returns 25, regardless of city

def test_with_stub():
    stub = StubWeatherService()
    planner = TripPlanner(weather_service=stub)
    assert planner.should_bring_jacket("Sydney") == False
```

对特定调用返回预设值，不关心调用方式 (Returns pre-programmed responses to calls made during the test).

#### 🔹 Spy 间谍

```python
# Spy: 像 Stub，但还记录被调用的信息
class SpyEmailService:
    def __init__(self):
        self.sent_emails = []  # Records all calls

    def send(self, to, subject, body):
        self.sent_emails.append({"to": to, "subject": subject})
        return True  # Also provides canned response like a stub

def test_with_spy():
    spy = SpyEmailService()
    notifier = OrderNotifier(email_service=spy)
    notifier.notify_shipped("order123")
    assert len(spy.sent_emails) == 1
    assert spy.sent_emails[0]["subject"] == "Order Shipped"
```

不仅返回值，还记录调用了什么、参数是什么 (Records information about how it was called).

#### 🔹 Mock 模拟对象

```python
# Mock: 预先设定期望，测试后验证
from unittest.mock import Mock

def test_with_mock():
    mock_repo = Mock()
    mock_repo.save.return_value = True

    service = UserService(repository=mock_repo)
    service.register("Alice", "alice@email.com")

    # Verify: was save() called with correct args?
    mock_repo.save.assert_called_once_with("Alice", "alice@email.com")
```

> ⚠️ **Mock vs Spy 区别**: Mock 在测试**之前**定义期望值，测试**之后**验证；Spy 是在测试之后检查记录的调用信息。Mock focuses on **behavior verification**（行为验证），Spy focuses on **state verification**（状态验证）.

#### 🔹 Fake 假对象

```python
# Fake: 有真实的简化实现
class FakeDatabase:
    def __init__(self):
        self.data = {}  # In-memory storage instead of real DB

    def save(self, key, value):
        self.data[key] = value

    def get(self, key):
        return self.data.get(key)

def test_with_fake():
    fake_db = FakeDatabase()
    service = UserService(database=fake_db)
    service.create_user("Alice")
    assert fake_db.get("Alice") is not None
```

有简化但可工作的实现，比如用内存字典代替真实数据库 (Has a working implementation but takes shortcuts - e.g., in-memory DB instead of real DB).

> 🔑 **记忆口诀**: **D**ummy=空壳, **S**tub=固定回答, **S**py=Stub+记录, **M**ock=期望+验证, **F**ake=简化实现

---

## 3. Python unittest.mock 模块

### Mock() Object

```python
from unittest.mock import Mock

# 创建 Mock 对象
mock = Mock()

# Mock 接受任何属性访问和方法调用 - 不会报错
mock.some_method()          # OK, returns another Mock
mock.foo.bar.baz()          # OK, chaining works too
mock.anything_at_all        # OK, returns a Mock

# 设定返回值
mock.get_price.return_value = 9.99
result = mock.get_price()   # returns 9.99
```

> 💡 Mock 对象非常灵活：访问任何属性或调用任何方法都不会出错，默认返回另一个 Mock 对象

### MagicMock() Object

MagicMock 是 Mock 的子类，自动支持 **magic methods** (dunder methods):

```python
from unittest.mock import MagicMock

magic = MagicMock()

# MagicMock 支持 Python 的魔术方法
len(magic)          # Works! Returns 0 by default
str(magic)          # Works!
magic[0]            # Works! (__getitem__)
iter(magic)         # Works! (__iter__)

# 普通 Mock 不支持这些
regular = Mock()
len(regular)        # ❌ TypeError!
```

| Feature | Mock | MagicMock |
|:---|:---:|:---:|
| Attribute access | ✅ | ✅ |
| Method calls | ✅ | ✅ |
| Magic methods (`__len__`, `__str__`, etc.) | ❌ | ✅ |
| Default for `patch()` | — | ✅ (default) |

> 🎯 **考试提示**: 大多数情况用 `MagicMock`，因为它支持更多 Python 特性。`patch()` 默认创建 `MagicMock`.

### side_effect 属性

`side_effect` 可以让 Mock 做更复杂的事:

```python
from unittest.mock import Mock

mock = Mock()

# 1. 抛出异常
mock.side_effect = ValueError("Invalid input!")
# mock()  # Raises ValueError

# 2. 返回不同值（按顺序）
mock.side_effect = [1, 2, 3]
mock()   # returns 1
mock()   # returns 2
mock()   # returns 3

# 3. 自定义函数
def custom_logic(x):
    if x > 0:
        return x * 2
    raise ValueError("Must be positive")

mock.side_effect = custom_logic
mock(5)   # returns 10
mock(-1)  # raises ValueError
```

> ⚠️ `side_effect` 优先级高于 `return_value`！如果同时设了两个，`side_effect` 生效.

---

## 4. patch() Decorator 补丁装饰器

`patch()` 临时替换一个对象（通常是导入的模块或类），测试结束后自动恢复:

### 用法一: @patch Decorator

```python
from unittest.mock import patch

# 假设 my_module.py 里 import 了 requests
# my_module.py:
#   import requests
#   def fetch_data(url):
#       response = requests.get(url)
#       return response.json()

@patch('my_module.requests.get')  # 替换 my_module 中的 requests.get
def test_fetch_data(mock_get):    # mock 作为参数传入
    # 设置 mock 返回值
    mock_response = Mock()
    mock_response.json.return_value = {"name": "Alice"}
    mock_get.return_value = mock_response

    result = fetch_data("http://example.com/api")

    assert result == {"name": "Alice"}
    mock_get.assert_called_once_with("http://example.com/api")
```

### 用法二: Context Manager

```python
def test_fetch_data():
    with patch('my_module.requests.get') as mock_get:
        mock_response = Mock()
        mock_response.json.return_value = {"data": 42}
        mock_get.return_value = mock_response

        result = fetch_data("http://api.example.com")
        assert result == {"data": 42}
    # patch 在 with 块结束后自动恢复原对象
```

### 多个 patch

```python
@patch('my_module.database')
@patch('my_module.email_service')
def test_something(mock_email, mock_db):  # ⚠️ 注意：参数顺序是反的！
    # mock_email corresponds to email_service (bottom decorator)
    # mock_db corresponds to database (top decorator)
    pass
```

> ⚠️ **重要陷阱**: 多个 `@patch` 装饰器时，参数顺序是**从下往上** (bottom-up)！最靠近函数的装饰器对应第一个参数.

### patch 的目标路径

```python
# ❌ Wrong: patching where it's defined
@patch('requests.get')

# ✅ Correct: patching where it's USED (imported)
@patch('my_module.requests.get')
```

> 🔑 **Key Rule**: Always patch where the object is **looked up**, not where it's **defined** (在对象被使用/查找的地方 patch，而不是在定义的地方).

---

## 5. Mock Assertions 模拟断言

### Assertion Methods 断言方法

| Method 方法 | What it checks 检查什么 |
|:---|:---|
| `mock.assert_called()` | Was the mock called at all? 是否被调用过？ |
| `mock.assert_called_once()` | Was it called exactly once? 是否恰好被调用一次？ |
| `mock.assert_called_with(args)` | Was the **last** call with these args? 最后一次调用的参数？ |
| `mock.assert_called_once_with(args)` | Called once AND with these args? 调用一次且参数正确？ |
| `mock.assert_not_called()` | Was it never called? 是否从未被调用？ |
| `mock.assert_any_call(args)` | Was it called with these args at any point? 任何时候是否用过这些参数？ |

### Inspection Properties 检查属性

```python
mock = Mock()
mock("hello")
mock("world")
mock("hello", key="value")

mock.call_count          # 3 (被调用了3次)
mock.called              # True (是否被调用过)
mock.call_args           # call('hello', key='value') - 最后一次调用
mock.call_args_list      # [call('hello'), call('world'), call('hello', key='value')]
```

> 🎯 **考试提示**: `assert_called_with()` 只检查**最后一次**调用！要检查所有调用用 `call_args_list` 或 `assert_any_call()`.

---

## 6. Dependency Injection 依赖注入

Dependency Injection (DI) 是一种设计模式：不让对象自己创建依赖，而是从外部**注入**依赖 (Instead of a class creating its own dependencies, they are provided from outside).

### Why DI Matters for Testing 为什么DI对测试重要

```python
# ❌ Bad: Hard to test (紧耦合, 难以测试)
class OrderService:
    def __init__(self):
        self.db = RealDatabase()           # Creates its own dependency!
        self.mailer = RealEmailService()   # Can't replace for testing!

    def place_order(self, order):
        self.db.save(order)
        self.mailer.send_confirmation(order)

# ✅ Good: Easy to test with DI (松耦合, 容易测试)
class OrderService:
    def __init__(self, db, mailer):        # Dependencies injected!
        self.db = db
        self.mailer = mailer

    def place_order(self, order):
        self.db.save(order)
        self.mailer.send_confirmation(order)

# Testing:
def test_place_order():
    mock_db = Mock()
    mock_mailer = Mock()
    service = OrderService(db=mock_db, mailer=mock_mailer)

    service.place_order({"item": "laptop"})

    mock_db.save.assert_called_once()
    mock_mailer.send_confirmation.assert_called_once()
```

### DI Types 注入类型

| Type 类型 | How 方式 | Example |
|:---|:---|:---|
| **Constructor Injection** 构造函数注入 | Pass via `__init__()` | `Service(db=mock_db)` |
| **Method Injection** 方法注入 | Pass via method parameter | `service.process(db=mock_db)` |
| **Setter Injection** 设值注入 | Set via property/setter | `service.db = mock_db` |

> 🔑 **Constructor Injection 最常用**，因为确保对象创建时就有所有依赖

---

## 7. SDLC Overview 软件开发生命周期概览

### Software Development Life Cycle (SDLC) 软件开发生命周期

SDLC 是软件从概念到退役的完整过程，包含以下阶段:

```
Requirements → Design → Implementation → Testing → Deployment → Maintenance
   需求分析       设计       实现/编码        测试       部署        维护
```

### Two Main Approaches 两大方法论

| Aspect | Plan-Driven 计划驱动 | Agile 敏捷 |
|:---|:---|:---|
| **Planning** | Extensive upfront 大量前期计划 | Minimal upfront, evolving 少量前期，持续演进 |
| **Requirements** | Fixed early 早期固定 | Can change anytime 随时可变 |
| **Delivery** | All at once 一次性交付 | Incremental 增量交付 |
| **Customer involvement** | Mainly at start/end 主要在开头和结尾 | Continuous 持续参与 |
| **Documentation** | Heavy 大量文档 | Lightweight 轻量文档 |
| **Best for** | Stable requirements, safety-critical 需求稳定、安全关键 | Changing requirements, fast delivery 需求多变、快速交付 |

---

## 8. Plan-Driven Models 计划驱动模型

### 🔹 Waterfall Model 瀑布模型

最经典的模型，各阶段严格按顺序执行:

```
Requirements ──→ Design ──→ Implementation ──→ Verification ──→ Maintenance
    需求            设计         实现              验证            维护
```

| Pros 优点 | Cons 缺点 |
|:---|:---|
| Simple, easy to understand 简单易懂 | No going back easily 难以回退 |
| Clear milestones 清晰的里程碑 | Late discovery of problems 问题发现太晚 |
| Good documentation 文档完善 | Customer sees product very late 客户很晚才看到产品 |
| Works for stable requirements 适合需求稳定项目 | Not flexible 不灵活 |

### 🔹 V-Model V模型

瀑布的扩展，每个开发阶段都对应一个**测试阶段** (Each development phase has a corresponding testing phase):

```
Requirements ─────────────────────── Acceptance Testing
     Design ────────────────── System Testing
          Architecture ──── Integration Testing
               Coding ── Unit Testing
```

> 💡 V-Model 强调 **Verification & Validation**: 左边是开发 (Verification: "Are we building it right?")，右边是测试 (Validation: "Are we building the right thing?")

### 🔹 Incremental Development 增量开发

把系统分成多个部分，每次交付一部分 (Build the system in pieces, delivering one piece at a time):

```
[Increment 1] → [Increment 2] → [Increment 3] → ... → [Complete System]
  基础功能         添加功能A       添加功能B              完整系统
```

### 🔹 Iterative Development 迭代开发

每次迭代都产出一个完整但不断精化的版本 (Each iteration produces a complete but refined version):

```
[Version 0.1] → [Version 0.5] → [Version 0.8] → [Version 1.0]
  粗糙原型        改进版本         接近完成         最终版本
```

> ⚠️ **Incremental vs Iterative**: Incremental 每次加新功能块；Iterative 每次改进整体。实际中常结合使用.

### 🔹 Spiral Model 螺旋模型 (Boehm)

Risk-driven model，以风险为核心，每个循环有4个象限:

```
    1. Planning (目标确定)
         ↓
    2. Risk Analysis (风险分析) ← 核心特点！
         ↓
    3. Engineering (开发与测试)
         ↓
    4. Evaluation (客户评估)
         ↓
    → Next spiral (更大的螺旋)
```

> 🔑 Spiral Model 的独特之处是**每次迭代都进行风险分析**，适合大型、高风险项目

### 🔹 Rational Unified Process (RUP)

把项目分为4个阶段，每个阶段可以有多次迭代:

| Phase 阶段 | Goal 目标 | Key Milestone |
|:---|:---|:---|
| **Inception** 初始 | Define scope, feasibility 确定范围和可行性 | Lifecycle Objective |
| **Elaboration** 细化 | Architecture, reduce risks 建立架构，降低风险 | Lifecycle Architecture |
| **Construction** 构建 | Build the system 构建系统 | Initial Operational Capability |
| **Transition** 转换 | Deploy to users 部署给用户 | Product Release |

---

## 9. Agile Manifesto 敏捷宣言

### 4 Core Values 四大核心价值观 ⭐

| We Value More 更重视 | Over 胜于 |
|:---|:---|
| **Individuals and interactions** 个体和互动 | Processes and tools 流程和工具 |
| **Working software** 可工作的软件 | Comprehensive documentation 详尽的文档 |
| **Customer collaboration** 客户合作 | Contract negotiation 合同谈判 |
| **Responding to change** 响应变化 | Following a plan 遵循计划 |

> ⚠️ "That is, while there is value in the items on the right, we value the items on the left more." 右边也有价值，但我们更重视左边.

### 12 Principles 十二原则 (Key ones for exam)

1. **Highest priority**: satisfy the customer through early and continuous delivery 最高优先级是通过早期和持续交付满足客户
2. **Welcome changing requirements**, even late in development 欢迎变化的需求
3. **Deliver working software frequently** (weeks to months) 频繁交付可工作的软件
4. **Business people and developers** work together daily 业务人员和开发人员每天一起工作
5. Build projects around **motivated individuals** 围绕有积极性的个人构建项目
6. **Face-to-face conversation** is the most efficient communication 面对面交流最高效
7. **Working software** is the primary measure of progress 可工作的软件是进度的主要度量标准
8. Promote **sustainable development** pace 提倡可持续的开发节奏
9. Continuous attention to **technical excellence** 持续关注技术卓越
10. **Simplicity** — maximizing the amount of work not done 简单性——尽量减少不必要的工作
11. Best architectures emerge from **self-organizing teams** 最好的架构来自自组织团队
12. **Reflect and adjust** at regular intervals 定期反思和调整

---

## 10. Scrum Framework 敏捷Scrum框架

Scrum 是最流行的 Agile 框架 ⭐ 考试重点！

### Three Roles 三个角色

| Role 角色 | Responsibility 职责 |
|:---|:---|
| **Product Owner** 产品负责人 | Defines WHAT to build; manages Product Backlog; represents stakeholders 决定做什么；管理产品待办列表；代表利益相关者 |
| **Scrum Master** | Facilitates Scrum process; removes impediments; NOT a project manager 促进Scrum流程；移除障碍；不是项目经理 |
| **Development Team** 开发团队 | Self-organizing, cross-functional; decides HOW to build; typically 3-9 people 自组织、跨功能；决定怎么做；通常3-9人 |

### Five Events 五个事件

| Event 事件 | When/Duration | Purpose 目的 |
|:---|:---|:---|
| **Sprint** | 1-4 weeks (fixed) | Timeboxed iteration to produce an Increment 固定时间框的迭代 |
| **Sprint Planning** | Start of Sprint | Select items from Product Backlog → Sprint Backlog 从待办中选择本次Sprint要做的 |
| **Daily Scrum / Standup** | Every day, ≤15 min | Sync: What did I do? What will I do? Any blockers? 同步进度和障碍 |
| **Sprint Review** | End of Sprint | Demo the Increment to stakeholders; get feedback 展示成果，获取反馈 |
| **Sprint Retrospective** | After Sprint Review | Team reflects: What went well? What to improve? 团队反思改进 |

### Three Artifacts 三个工件

| Artifact 工件 | Description 描述 |
|:---|:---|
| **Product Backlog** 产品待办列表 | Ordered list of ALL work needed; owned by Product Owner 所有待完成工作的有序列表 |
| **Sprint Backlog** Sprint待办列表 | Subset selected for current Sprint + plan to deliver 当前Sprint选中的子集+交付计划 |
| **Increment** 增量 | The sum of all completed items — must meet Definition of Done 所有完成项目的总和 |

### Definition of Done (DoD) 完成的定义

DoD 是团队对"完成"的共同标准 (Shared understanding of what "Done" means):

- Code written and reviewed 代码已编写并审查
- Tests passed 测试通过
- Documentation updated 文档已更新
- Meets acceptance criteria 满足验收标准

> 🔑 **Sprint 规则**: Sprint 期间不能改变目标 (no changes that endanger the Sprint Goal)；Sprint 长度固定不变.

---

## 11. Kanban 看板方法

### Core Principles 核心原则

| Principle 原则 | Explanation 解释 |
|:---|:---|
| **Visualize the workflow** 可视化工作流 | Use a board with columns 使用看板可视化 |
| **Limit WIP** 限制在制品 | Set max items per column 每列设置最大数量限制 |
| **Manage flow** 管理流动 | Monitor and optimize item movement 监控和优化项目流动 |
| **Make policies explicit** 明确政策 | Write down rules for each column 写明每列的规则 |
| **Implement feedback loops** 实施反馈循环 | Regular reviews 定期检查 |
| **Improve collaboratively** 协作改进 | Evolve through experiments 通过实验持续改进 |

### Kanban Board 看板

```
| To Do        | In Progress (WIP: 3) | Code Review (WIP: 2) | Done    |
|:-------------|:---------------------|:----------------------|:--------|
| Feature E    | Feature B            | Feature A             | Feature X|
| Feature F    | Feature C            |                       | Feature Y|
|              | Feature D            |                       |          |
```

> 💡 **WIP Limit** 是 Kanban 的核心！限制同时进行的工作数量，避免多任务切换，提高完成速度.

### Scrum vs Kanban 对比

| Aspect | Scrum | Kanban |
|:---|:---|:---|
| **Iterations** | Fixed Sprints | Continuous flow 持续流动 |
| **Roles** | PO, SM, Team | No prescribed roles 无规定角色 |
| **Planning** | Sprint Planning | On-demand 按需 |
| **Change** | Not during Sprint | Anytime 随时 |
| **Metric** | Velocity | Lead time / Cycle time |
| **Board** | Reset each Sprint | Persistent 持续使用 |

---

## 12. Extreme Programming XP 极限编程

### 5 Values 五大价值观

**C**ommunication, **S**implicity, **F**eedback, **C**ourage, **R**espect
沟通、简单、反馈、勇气、尊重

### Key Practices 关键实践

| Practice 实践 | Description 描述 |
|:---|:---|
| **Pair Programming** 结对编程 | Two developers at one computer, one writes (Driver), one reviews (Navigator) 两人一台电脑 |
| **Test-Driven Development (TDD)** 测试驱动开发 | Write test FIRST → Write code → Refactor (Red-Green-Refactor) 先写测试再写代码 |
| **Continuous Integration (CI)** 持续集成 | Integrate and test code multiple times a day 每天多次集成和测试 |
| **Refactoring** 重构 | Continuously improve code structure without changing behavior 持续改进代码结构 |
| **Small Releases** 小版本发布 | Release frequently in small increments 频繁小规模发布 |
| **Simple Design** 简单设计 | Do the simplest thing that works 做最简单能工作的设计 |
| **Collective Code Ownership** 集体代码所有权 | Anyone can change any code 任何人可以修改任何代码 |
| **40-Hour Week** 每周40小时 | Sustainable pace, no overtime 可持续节奏 |
| **On-site Customer** 现场客户 | Customer available full-time 客户全时可用 |
| **Coding Standards** 编码标准 | Agreed-upon coding conventions 统一编码规范 |

> 🔑 **TDD Cycle**: Red (写失败的测试) → Green (写最少代码通过测试) → Refactor (改进代码)

---

## 13. Lean & DevOps 精益与运维开发

### Lean Software Development 精益软件开发

Seven Principles 七原则:

1. **Eliminate Waste** 消除浪费 — Remove anything that doesn't add value
2. **Amplify Learning** 增强学习 — Short iteration cycles, feedback
3. **Decide as Late as Possible** 尽量晚做决定 — Keep options open
4. **Deliver as Fast as Possible** 尽快交付 — Speed = competitive advantage
5. **Empower the Team** 授权团队 — Trust developers to make decisions
6. **Build Integrity In** 内建完整性 — Quality from the start
7. **Optimize the Whole** 整体优化 — Not just individual parts

### DevOps

DevOps = **Dev**elopment + **Op**eration**s** — 打破开发与运维之间的壁垒

Key practices: CI/CD (Continuous Integration / Continuous Deployment), Infrastructure as Code, Monitoring, Automation

---

## 14. Estimation Techniques 估算技术

### Why Estimation is Hard 为什么估算很难

**Cone of Uncertainty** 不确定性锥: 项目初期估算偏差可达 4x，随项目进行逐渐缩小

```
Project Start    →→→→→→→→→→→→→    Project End
   4x                                   1x
   ↕ (huge uncertainty)                  ↕ (accurate)
   0.25x                                1x
```

### Estimation Approaches Overview 估算方法概览

| Technique 技术 | Type 类型 | Description 描述 |
|:---|:---|:---|
| **Expert Judgment** 专家判断 | Top-down | Experts estimate based on experience 专家凭经验估算 |
| **Delphi Method** 德尔菲法 | Top-down | Anonymous expert consensus 匿名专家共识 |
| **Analogy** 类比法 | Top-down | Compare to similar past projects 与相似的历史项目比较 |
| **Decomposition** 分解法 | Bottom-up | Break into small tasks, estimate each 分解为小任务逐个估算 |
| **Function Points** 功能点 | Bottom-up | Count functional components 计算功能组件数量 |
| **Story Points** 故事点 | Relative | Relative sizing using Fibonacci 用斐波那契数列相对估算 |
| **Planning Poker** | Relative | Team consensus game 团队共识游戏 |
| **T-shirt Sizing** T恤尺码 | Relative | XS, S, M, L, XL, XXL |
| **COCOMO** | Algorithmic | Mathematical model based on KLOC 基于代码行数的数学模型 |
| **Three-point / PERT** | Statistical | Optimistic + Most Likely + Pessimistic 乐观+最可能+悲观 |

---

## 15. Function Point Analysis 功能点分析

Function Point Analysis (FPA) 通过计算系统功能来估算项目规模 (Measures software size by the amount of functionality):

### Five Function Types 五种功能类型

| Type 类型 | Description 描述 | Direction |
|:---|:---|:---|
| **EI** (External Input) 外部输入 | Data entering the system 进入系统的数据 | In → |
| **EO** (External Output) 外部输出 | Data/reports leaving the system 离开系统的数据/报告 | → Out |
| **EQ** (External Inquiry) 外部查询 | Input triggers immediate output 输入触发即时输出 | In → Out |
| **ILF** (Internal Logical File) 内部逻辑文件 | Data maintained by the system 系统维护的数据 | Internal |
| **EIF** (External Interface File) 外部接口文件 | Data referenced but maintained elsewhere 引用但由其他系统维护的数据 | External ref |

### Complexity Weights 复杂度权重

Each function type is rated as Low / Average / High complexity, then weighted:

| Type | Low | Average | High |
|:---|:---:|:---:|:---:|
| EI | 3 | 4 | 6 |
| EO | 4 | 5 | 7 |
| EQ | 3 | 4 | 6 |
| ILF | 7 | 10 | 15 |
| EIF | 5 | 7 | 10 |

> **Unadjusted FP** = Σ (count × weight) for all function types

---

## 16. Story Points & Planning Poker

### Story Points 故事点

Story Points 是**相对**估算，不是绝对时间 (Relative measure of effort, not hours):

- 使用 **Fibonacci sequence**: 1, 2, 3, 5, 8, 13, 21, 34...
- 选一个 baseline story (比如简单的登录功能 = 3 points)
- 其他 story 相对它来估算

### Planning Poker 估算扑克 ⭐

Step-by-step process:

1. Product Owner 描述一个 User Story
2. 团队讨论并提问
3. 每人**同时**亮出自己的估算牌 (Fibonacci numbers)
4. 如果估算差异大 → 讨论为什么 (最高和最低解释)
5. 重新投票直到**达成共识**
6. 重复下一个 Story

> 💡 同时亮牌是为了避免 **anchoring bias** (锚定效应) — 不被别人的估算影响

### Velocity 速度

**Velocity** = team 每个 Sprint 完成的 Story Points 总和

```
Sprint 1: completed 20 points
Sprint 2: completed 25 points
Sprint 3: completed 22 points
Average Velocity = (20 + 25 + 22) / 3 ≈ 22 points/sprint

项目剩余 110 points → 预计还需 110/22 = 5 Sprints
```

### T-shirt Sizing T恤尺码法

快速粗略估算: **XS, S, M, L, XL, XXL**

适合早期 Backlog 整理 (Backlog grooming)，后期再转换为 Story Points.

---

## 17. COCOMO & PERT Models

### COCOMO (Constructive Cost Model)

Basic COCOMO 用代码行数 (KLOC = Kilo Lines of Code) 估算:

**Effort = a × (KLOC)^b** (person-months)

| Project Type | a | b | Description |
|:---|:---:|:---:|:---|
| **Organic** 有机型 | 2.4 | 1.05 | Small team, familiar with project 小团队，熟悉项目 |
| **Semi-Detached** 半独立型 | 3.0 | 1.12 | Medium team, mixed experience 中等团队，经验混合 |
| **Embedded** 嵌入型 | 3.6 | 1.20 | Complex, tight constraints 复杂，严格约束 |

Example: 50 KLOC organic project → Effort = 2.4 × 50^1.05 ≈ 2.4 × 57.0 ≈ **137 person-months**

### Three-Point Estimation 三点估算

使用三个估算值:
- **O** = Optimistic (最乐观)
- **M** = Most Likely (最可能)
- **P** = Pessimistic (最悲观)

### PERT Formula

$$E = \frac{O + 4M + P}{6}$$

Standard Deviation: $σ = \frac{P - O}{6}$

> **Example**: O=5 days, M=10 days, P=21 days
> E = (5 + 4×10 + 21) / 6 = (5 + 40 + 21) / 6 = **11 days**
> σ = (21 - 5) / 6 ≈ **2.67 days**

> 🎯 **考试提示**: 记住 PERT 公式 = **(O + 4M + P) / 6**，4M 权重最大因为最可能的值最有参考价值

---

## 18. UI vs UX Design

### Definitions 定义

| Term | Full Name | Focus 关注点 |
|:---|:---|:---|
| **UI** | User Interface | How it LOOKS 外观设计 — buttons, colors, layout |
| **UX** | User Experience | How it FEELS 体验感受 — ease of use, satisfaction, journey |

> 💡 UI 是产品的"外表"，UX 是用户使用产品的"整体体验"。好的 UI 不一定有好的 UX，反之亦然.

### User-Centered Design (UCD) Process 以用户为中心的设计

```
Understand users → Define requirements → Design solutions → Evaluate
  理解用户           定义需求              设计方案           评估
       ↑                                                     |
       └─────────────── iterate (迭代) ────────────────────┘
```

### Usability 可用性 (5 Components by Nielsen)

| Component 组成 | Meaning 含义 |
|:---|:---|
| **Learnability** 可学习性 | How easy to accomplish tasks first time? 第一次使用时多容易完成任务？ |
| **Efficiency** 效率 | How quickly can experienced users perform tasks? 熟练用户能多快完成任务？ |
| **Memorability** 可记忆性 | How easy to re-establish proficiency after time away? 一段时间不用后重新上手有多容易？ |
| **Errors** 错误 | How many errors do users make? How severe? How easy to recover? 用户犯多少错？多严重？多容易恢复？ |
| **Satisfaction** 满意度 | How pleasant to use? 使用起来愉悦吗？ |

---

## 19. Nielsen's 10 Usability Heuristics 尼尔森十大可用性原则

这是 UI 设计最经典的评估准则 ⭐

| # | Heuristic 原则 | Explanation 解释 | Example 例子 |
|:---:|:---|:---|:---|
| 1 | **Visibility of System Status** 系统状态可见性 | Always keep users informed 让用户知道发生了什么 | Loading spinner, progress bar |
| 2 | **Match Between System & Real World** 系统与现实世界匹配 | Use familiar language & concepts 使用用户熟悉的语言和概念 | Shopping "cart" icon 🛒 |
| 3 | **User Control & Freedom** 用户控制与自由 | Provide undo/redo, clear exits 提供撤销/重做，清晰的退出方式 | Undo button, "Back" link |
| 4 | **Consistency & Standards** 一致性与标准 | Same words/actions mean same thing 相同的词和操作意味着相同的事 | All "Save" buttons work the same way |
| 5 | **Error Prevention** 错误预防 | Design to prevent errors before they occur 设计防止错误发生 | Confirmation dialog before delete |
| 6 | **Recognition Rather Than Recall** 识别而非回忆 | Make options visible; minimize memory load 让选项可见，减少记忆负担 | Dropdown menus, autocomplete |
| 7 | **Flexibility & Efficiency of Use** 灵活性与效率 | Provide shortcuts for experienced users 为熟练用户提供快捷方式 | Keyboard shortcuts, bookmarks |
| 8 | **Aesthetic & Minimalist Design** 美观与简约设计 | Don't include irrelevant information 不包含无关信息 | Clean Google homepage |
| 9 | **Help Users Recognize, Diagnose, & Recover from Errors** 帮助用户识别、诊断和恢复错误 | Error messages in plain language with solutions 用通俗语言展示错误和解决方案 | "Password must be 8+ characters" |
| 10 | **Help & Documentation** 帮助与文档 | Provide searchable help when needed 需要时提供可搜索的帮助 | FAQ, tooltips, search in help |

> 🎯 **考试记忆法**: **V**isibility, **M**atch, **C**ontrol, **C**onsistency, **P**revention, **R**ecognition, **F**lexibility, **A**esthetic, **E**rror recovery, **H**elp = "**VM CC PR FA EH**"

---

## 20. Shneiderman's 8 Golden Rules

| # | Rule 规则 | Key Idea 关键思想 |
|:---:|:---|:---|
| 1 | **Strive for Consistency** 追求一致性 | Consistent actions, terminology, layout 一致的操作、术语、布局 |
| 2 | **Seek Universal Usability** 追求通用可用性 | Design for novice to expert, consider accessibility 从新手到专家，考虑无障碍 |
| 3 | **Offer Informative Feedback** 提供信息反馈 | Every action should have feedback 每个操作都应有反馈 |
| 4 | **Design Dialogs to Yield Closure** 设计对话框以产生闭合感 | Group actions with beginning, middle, end 操作有清晰的开始、中间和结束 |
| 5 | **Prevent Errors** 防止错误 | Design so errors cannot occur 设计使错误不可能发生 |
| 6 | **Permit Easy Reversal of Actions** 允许轻松撤销操作 | Users feel safe to explore 用户可以放心探索 |
| 7 | **Keep Users in Control** 保持用户控制感 | Users are initiators, not responders 用户是主动者，不是被动者 |
| 8 | **Reduce Short-term Memory Load** 减少短期记忆负担 | Don't require memorization 不要求用户记忆信息 |

---

## 21. UI Design Process & Prototyping

### Design Process 设计流程

```
Requirements → Wireframe → Mockup → Prototype → User Testing → Final Design
  需求分析       线框图      视觉稿     原型         用户测试      最终设计
```

### Wireframe vs Mockup vs Prototype 对比

| Type 类型 | Fidelity 保真度 | Interactive? | Purpose 目的 |
|:---|:---|:---:|:---|
| **Wireframe** 线框图 | Low 低 — sketch/boxes | ❌ | Layout and structure 布局和结构 |
| **Mockup** 视觉稿 | Medium-High 中高 — visual design | ❌ | Look and feel 外观和感觉 |
| **Prototype** 原型 | Varies | ✅ | Simulate user interaction 模拟用户交互 |

### Low-fi vs High-fi Prototypes

| Aspect | Low-fidelity 低保真 | High-fidelity 高保真 |
|:---|:---|:---|
| **Tools** | Paper, whiteboard 纸和白板 | Figma, Adobe XD, code |
| **Speed** | Very fast 非常快 | Slow 慢 |
| **Cost** | Very cheap 便宜 | Expensive 昂贵 |
| **Feedback type** | Conceptual 概念性 | Detailed 细节性 |
| **Best for** | Early exploration 早期探索 | Final validation 最终验证 |

### Layout Principles 布局原则 (C.A.R.P.)

| Principle 原则 | Meaning 含义 |
|:---|:---|
| **C**ontrast 对比 | Make different things look different 让不同的东西看起来不同 |
| **A**lignment 对齐 | Every element should be visually connected 每个元素应视觉上有联系 |
| **R**epetition 重复 | Repeat visual elements for consistency 重复视觉元素保持一致 |
| **P**roximity 亲密性 | Group related items together 把相关的项目放在一起 |

---

## 22. Accessibility 无障碍设计

### POUR Principles (WCAG) 四大原则

| Principle 原则 | Meaning 含义 | Example 例子 |
|:---|:---|:---|
| **P**erceivable 可感知 | Users can perceive the content 用户能感知内容 | Alt text for images, captions for video |
| **O**perable 可操作 | Users can interact with all controls 用户能操作所有控件 | Keyboard navigation, sufficient time |
| **U**nderstandable 可理解 | Users can understand content and UI 用户能理解内容和界面 | Clear labels, consistent navigation |
| **R**obust 健壮性 | Works with various technologies 兼容各种技术 | Valid HTML, ARIA attributes |

### Key Accessibility Practices 关键实践

- **Alt text** for all images 为图片添加替代文本
- **Color contrast** ratio ≥ 4.5:1 for normal text 颜色对比度
- **Keyboard navigation** — all interactive elements accessible 键盘导航
- Don't rely on **color alone** to convey information 不仅靠颜色传达信息
- **Semantic HTML** (`<nav>`, `<main>`, `<header>`, `<button>`) 语义化HTML
- **ARIA attributes** for complex widgets 复杂控件使用ARIA属性

---

## 23. UI Evaluation Methods

### Overview 概览

| Method 方法 | Who | When | Description |
|:---|:---|:---|:---|
| **Heuristic Evaluation** 启发式评估 | Experts (3-5) | Any stage | Evaluate against heuristics (e.g., Nielsen's 10) 专家用原则检查 |
| **Cognitive Walkthrough** 认知走查 | Experts | Early | Walk through tasks as a new user would 模拟新用户完成任务 |
| **Usability Testing** 可用性测试 | Real users (5-8) | Prototype/Product | Observe users performing tasks 观察真实用户执行任务 |
| **Think-Aloud Protocol** 出声思考法 | Real users | During usability test | Users verbalize their thoughts while using 用户边用边说出想法 |
| **A/B Testing** | Real users (large N) | Live product | Compare two versions statistically 统计比较两个版本 |

### Heuristic Evaluation Process 启发式评估流程

1. 每个评估者**独立**检查界面 (Each evaluator inspects independently)
2. 对照 heuristics 列出问题 (Compare against heuristics)
3. 给每个问题评**严重性** (Rate severity: 0=cosmetic → 4=catastrophic)
4. 合并所有评估者的发现 (Aggregate findings)

> 💡 **Nielsen's research**: 5 evaluators can find ~75% of usability problems. 5个评估者能发现约75%的可用性问题.

### Usability Testing Tips

- **5-8 users** is usually sufficient to find most problems 5-8个用户通常足够
- Give **realistic tasks**, not instructions 给真实任务，不是操作指令
- **Don't help** the user during the test 测试中不要帮助用户
- **Record** the session (screen + audio) 录制会话
- Focus on **behavior**, not opinions 关注行为，而非意见

---

## 24. Exam Quick Review 考试速览

### 🔴 Test Doubles (Must Know)

- **5 types**: Dummy (placeholder), Stub (canned answers), Spy (stub + records), Mock (expectations + verify), Fake (simplified implementation)
- `Mock()` vs `MagicMock()`: MagicMock supports magic methods
- `patch()`: patch where it's USED, not where it's DEFINED
- `side_effect` > `return_value` in priority
- Multiple `@patch`: parameter order is **bottom-up**
- `assert_called_with()` checks **last** call only

### 🔴 Methodologies (Must Know)

- **Waterfall**: sequential, rigid, good for stable requirements
- **V-Model**: Waterfall + testing phases mirror development phases
- **Spiral**: risk-driven, 4 quadrants per cycle
- **Agile Values**: Individuals > process, Working software > docs, Customer collaboration > contracts, Responding to change > plan
- **Scrum**: 3 Roles (PO, SM, Team) + 5 Events (Sprint, Planning, Daily, Review, Retro) + 3 Artifacts (Product Backlog, Sprint Backlog, Increment)
- **Kanban**: Visualize, Limit WIP, Manage flow, no fixed iterations
- **XP**: Pair programming, TDD, CI, Small releases
- **Scrum vs Kanban**: Scrum has fixed sprints; Kanban is continuous flow

### 🟡 Estimation (Supplementary)

- **Cone of Uncertainty**: early estimates can be off by 4x
- **PERT** = (O + 4M + P) / 6
- **COCOMO**: Effort = a × (KLOC)^b
- **Story Points**: relative, Fibonacci, measured by Velocity
- **Planning Poker**: simultaneous reveal, discuss outliers, re-vote

### 🟡 UI/UX (Supplementary)

- **Nielsen's 10 Heuristics** — especially Visibility, Consistency, Error Prevention, Recognition > Recall
- **POUR** (WCAG accessibility): Perceivable, Operable, Understandable, Robust
- **Wireframe** (low-fi) → **Mockup** (visual) → **Prototype** (interactive)
- **5 evaluators** find ~75% of usability problems
- **5-8 users** sufficient for usability testing

---

## 25. Concept Relationship Map 概念关系图

```
                    Week 6: Testing & Methodologies
                    ┌──────────────────────────────┐
                    │                              │
          ┌─────────┴──────────┐         ┌─────────┴─────────┐
          │   Test Doubles     │         │   Methodologies   │
          │  [[#2. Five Types  │         │   [[#7. SDLC      │
          │  of Test Doubles]] │         │   Overview]]      │
          └────────┬───────────┘         └────────┬──────────┘
                   │                              │
    ┌──────┬───────┼──────┬──────┐     ┌──────────┼──────────┐
    │      │       │      │      │     │          │          │
  Dummy  Stub    Spy   Mock   Fake  Plan-Driven  Agile    Hybrid
    │      │       │      │      │     │          │
    │      └──→ records   │      │  Waterfall  ┌──┴──┐
    │           calls     │      │  V-Model   Scrum Kanban
    │                     │      │  Spiral      │     │
    └─ placeholder        │      │  RUP         │     └→ WIP Limits
                          │      │              │        Continuous
                    ┌─────┘      │          ┌───┴───┐
              expectations    simplified   Roles  Events  Artifacts
              + verify       implementation  │      │        │
                    │                       PO    Sprint   Product
              ┌─────┘                       SM    Planning Backlog
          unittest.mock                    Team   Daily    Sprint
              │                                   Review   Backlog
        ┌─────┼─────┐                            Retro    Increment
      Mock  MagicMock patch()
        │              │
   return_value    patch where
   side_effect     it's USED
   assert_*        (not defined)

  ┌──────────────────────┐      ┌──────────────────────┐
  │    Estimation        │      │    UI/UX Design      │
  │  [[#14. Estimation   │      │  [[#18. UI vs UX]]   │
  │  Techniques]]        │      │                      │
  └─────────┬────────────┘      └─────────┬────────────┘
            │                             │
    ┌───────┼───────┐            ┌────────┼────────┐
  Expert  Relative  Model     Heuristics  Design   Accessibility
  Judgment Points   Based     Nielsen's   Process  POUR/WCAG
  Delphi  Planning  COCOMO   10 Rules    Wire→Mock
  Analogy Poker     PERT     Shneiderman →Proto
          T-shirt            8 Rules    →Test
```

### Cross-Week Links 跨周链接

- [[Week4-DesignPatterns-Testing]] → Unit Testing fundamentals → now Test Doubles to isolate units
- [[Week3-Reviews-OODesign-Refactoring]] → Refactoring → XP practice
- [[Week5-Databases-APIs]] → Database testing → use Fake (in-memory DB)
- [[Week2-Software-Design]] → Design Patterns → Dependency Injection for testability
- [[Week1-Software-Engineering-Introduction]] → SDLC introduced → now detailed methodology comparison

---

> 📝 **Study Tips**:
> - 🔴 红色标记 = Class Slides 内容 (Test Doubles + Methodologies)，考试最重要
> - 🟡 黄色标记 = Supplementary 内容 (Estimation + UI/UX)，了解即可
> - 重点记忆：5种 Test Doubles 的区别、Scrum 框架的角色/事件/工件、Agile 4 Values
> - 代码类题目：`patch()` 的用法、Mock assertions、`side_effect`
