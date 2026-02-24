# Midterm Exam Preparation 期中考试备考指南

> 📅 FinTech 512 Midterm | Based on Spring 2023 & 2024 Past Exams
> 🎯 This guide maps every exam topic to your study notes with direct links
> ⏱️ Exam Format: ~100 points, 4-5 questions, mix of short answer + coding + design

---

## Table of Contents

1. [[#Exam Structure Analysis 考试结构分析]]
2. [[#Topic 1 — Requirements (FR vs NFR & User Stories)]]
3. [[#Topic 2 — Database Normalization (1NF → 2NF → 3NF)]]
4. [[#Topic 3 — SQL DDL (CREATE TABLE)]]
5. [[#Topic 4 — Code Coverage & Test Case Design]]
6. [[#Topic 5 — Code Smells Identification]]
7. [[#Topic 6 — Refactoring Techniques]]
8. [[#Topic 7 — UML Class Diagrams]]
9. [[#Topic 8 — Dependency Injection & Testability]]
10. [[#Priority Study Plan 优先学习计划]]
11. [[#Practice Questions 模拟练习]]

---

## Exam Structure Analysis 考试结构分析

### 2023 vs 2024 Comparison 两年对比

| Question Topic | 2023 Points | 2024 Points | Appeared Both Years? |
|:---|:---:|:---:|:---:|
| **Requirements (FR/NFR/User Stories)** | 20 | 20 | ✅ 必考! |
| **Database (Normalization + SQL DDL)** | 30 | 30 | ✅ 必考! |
| **Code Coverage & Testing** | 25 | 15 | ✅ 必考! |
| **Code Smells & Refactoring** | 25 | 18 | ✅ 必考! |
| **UML Class Diagrams** | — | 17 | 2024 only |

> 🔴 **核心发现**: Requirements, Database, Testing, Refactoring 四大主题**每年必考**，占分 90-100%！

### Point Distribution 分值分布

```
Database (Norm + SQL)  ████████████████████  30 pts (最多!)
Requirements (FR/NFR)  ████████████████      20 pts
Testing (Coverage)     ██████████████        15-25 pts
Refactoring (Smells)   ████████████          18-25 pts
UML Class Diagrams     ██████████            17 pts (2024)
─────────────────────────────────────────────────────
Total                                        ~100 pts
```

---

## Topic 1 — Requirements (FR vs NFR & User Stories)

### 📖 Study Links 学习链接

- [[Week1-Software-Engineering-Introduction#5. Requirements Engineering 需求工程]] — FR vs NFR definitions
- [[Week1-Software-Engineering-Introduction#6. User Stories 用户故事]] — User Story format + INVEST

### What the Exam Tests 考什么

**Part A: Classify FR vs NFR (10 pts)**

给你一个项目描述 (e.g., online bookshop, personal playlist app)，然后列出 8-10 个需求，你要判断每个是 **Functional** 还是 **Non-functional**.

**How to classify 判断方法**:
- **FR (Functional)** = "系统应该**做什么**" → 涉及具体功能、操作、数据处理
  - Keywords: search, add, remove, display, create, update, delete, notify, login, register
- **NFR (Non-functional)** = "系统应该**如何表现**" → 涉及质量属性、约束
  - Keywords: fast, secure, reliable, available, easy to use, scalable, maintainable

> ⚠️ **考试陷阱**: "The system should encrypt user passwords" — 这是 NFR (Security)，不是 FR！虽然有动词 "encrypt"，但它描述的是安全**质量**要求。

**Part A-2: Identify Quality Attributes (5 pts)**

对于每个 NFR，还要写出它属于哪种 Quality Attribute:

| Attribute | 识别关键词 |
|:---|:---|
| **Performance** | speed, response time, throughput, latency |
| **Efficiency** | resource usage, memory, CPU |
| **Reliability** | failure rate, crash, MTBF, consistent |
| **Availability** | uptime, 24/7, accessible, downtime |
| **Security** | encrypt, authenticate, authorize, protect |
| **Usability** | easy to learn, user-friendly, intuitive |
| **Modifiability** | easy to change, extend, adapt |
| **Maintainability** | easy to fix, update, debug |
| **Portability** | cross-platform, different OS/browsers |
| **Testability** | easy to test, verify, validate |
| **Reusability** | reuse components, modular |
| **Functionality** | feature-complete, correct behavior |

**Part B: Write User Stories (5 pts)**

Format: **"As a [role], I would like to [action/feature], so that [benefit/value]."**

> 🎯 **关键**: 一定要写出 **so that** 部分！很多同学忘了写 benefit，这是扣分点。

Example from 2023 exam:
- ✅ "As a customer, I would like to search for books by title or author, so that I can quickly find the book I want to purchase."
- ❌ "As a customer, I would like to search for books." (缺少 so that!)

---

## Topic 2 — Database Normalization (1NF → 2NF → 3NF)

### 📖 Study Links 学习链接

- [[Week5-Databases-APIs#Database Normalization 数据库规范化 ⭐ 历年必考]] — Full normalization guide
- [[Week5-Databases-APIs#2. Relational Database Model 关系数据库模型]] — Keys, relationships

### What the Exam Tests 考什么 (15 pts)

给你一个 **unnormalized table** (有重复数据、冗余)，要求你一步步 normalize 到 3NF。

**Step-by-step approach 解题步骤**:

**Step 1: Identify the raw data and find repeating groups**
```
原始表可能有多值属性 (如 "Book1, Book2, Book3" 在一个格子里)
```

**Step 2: Convert to 1NF**
- 确保每个格子只有一个值 (atomic values)
- 消除 repeating groups → 每行一条记录
- 确定 Primary Key (可能是 composite key)

**Step 3: Convert to 2NF**
- 检查是否有 **partial dependency** (部分依赖)
- 只在 composite PK 时才需要检查！
- 如果非键属性只依赖 PK 的一部分 → 拆分成新表

**Step 4: Convert to 3NF**
- 检查是否有 **transitive dependency** (传递依赖)
- 如果 A → B → C，且 B 不是键 → 把 B → C 拆成新表
- 每个非键属性必须**直接**依赖于主键

**Step 5: Write the final table schemas**
- 列出每个表的 columns
- 标注 PK 和 FK

> 🔑 **记忆**: "The key (1NF), the whole key (2NF), and nothing but the key (3NF)"

### 2023 Exam Example Pattern

2023 给了一个 bookshop 的数据（有 customers, orders, books, authors 信息混在一个表里），要求 normalize 到 3NF。

2024 给了一个 playlist 应用的数据，要求同样的 normalization。

**Common decomposition pattern**:
```
One big table → 1NF (atomic) → 2NF (split partial deps) → 3NF (split transitive deps)
Usually ends up with 3-5 separate tables with FK relationships
```

---

## Topic 3 — SQL DDL (CREATE TABLE)

### 📖 Study Links 学习链接

- [[Week5-Databases-APIs#3. SQL Fundamentals SQL基础]] — DDL syntax
- [[Week5-Databases-APIs#The Chinook Database 示例数据库]] — Schema reference

### What the Exam Tests 考什么 (15 pts)

把 normalize 后的表写成 **CREATE TABLE** SQL statements。

**Template 模板**:

```sql
CREATE TABLE TableName (
    column1  DATATYPE  PRIMARY KEY,
    column2  DATATYPE  NOT NULL,
    column3  DATATYPE,
    FOREIGN KEY (column3) REFERENCES OtherTable(otherColumn)
);
```

**Key Syntax Points 关键语法**:

| Element | Syntax | 用途 |
|:---|:---|:---|
| **PRIMARY KEY** | `column INT PRIMARY KEY` or `PRIMARY KEY (col1, col2)` | 主键 |
| **FOREIGN KEY** | `FOREIGN KEY (col) REFERENCES Table(col)` | 外键引用 |
| **NOT NULL** | `column VARCHAR(100) NOT NULL` | 不允许空值 |
| **Composite PK** | `PRIMARY KEY (col1, col2)` at end of CREATE | 复合主键 |
| **AUTO INCREMENT** | `column INTEGER PRIMARY KEY AUTOINCREMENT` | 自增主键 |

**Common Data Types**:

| Type | Usage |
|:---|:---|
| `INTEGER` / `INT` | Whole numbers, IDs |
| `VARCHAR(n)` | Variable-length strings (name, email) |
| `TEXT` | Long text |
| `DECIMAL(p,s)` | Money, precise numbers |
| `DATE` | Dates |
| `BOOLEAN` | True/False |

> 🎯 **考试技巧**: 每个 normalize 出来的表都需要一个 CREATE TABLE。FK 一定要写 REFERENCES！

### 2024 Exam Also Tests: Modifying Existing Schema

2024 考了基于 Chinook database 的修改 — 给现有 schema 添加新表或修改表结构来支持新功能 (e.g., adding personal playlists feature to Chinook).

---

## Topic 4 — Code Coverage & Test Case Design

### 📖 Study Links 学习链接

- [[Week4-DesignPatterns-Testing#22. Code Coverage 代码覆盖率]] — Coverage types, calculations
- [[Week6-TestDoubles-Methodologies#5. Mock Assertions 模拟断言]] — Test assertions

### What the Exam Tests 考什么 (15-25 pts)

**Part A: Given code → Identify coverage type (5-10 pts)**

给你一段 Python 代码 (通常有 if/else, loops) 和几个 test cases，问:
- What is the **statement coverage**? (执行了多少行)
- What is the **branch/condition coverage**? (覆盖了多少分支)
- What is the **path coverage**? (覆盖了多少路径)

**Coverage Formulas 公式**:

| Type | Formula | 什么意思 |
|:---|:---|:---|
| **Statement** | executed statements / total statements × 100% | 执行了多少行代码 |
| **Branch** | executed branches / total branches × 100% | 覆盖了多少 if/else 分支 |
| **Path** | executed paths / total paths × 100% | 覆盖了多少完整执行路径 |

**How to analyze 分析方法**:

1. **Draw the Control Flow Graph (CFG)** — 画出控制流图
2. **Trace each test case** through the code 追踪每个测试用例的执行路径
3. **Mark which lines/branches/paths** are covered 标记被覆盖的行/分支/路径
4. **Calculate the percentage** 计算百分比

**Part B: Design test cases to achieve coverage (10-15 pts)**

要求你设计 test cases 来达到特定覆盖率：

- For **100% statement coverage**: 确保每一行代码至少执行一次
- For **100% condition coverage**: 确保每个 if condition 的 True 和 False 都被测到
- For **100% path coverage**: 确保每条可能的执行路径都被覆盖

> ⚠️ **Exam trap**: 100% statement coverage ≠ 100% branch coverage! 即使所有行都执行了，可能有些 else 分支没进入。

**Example approach**:
```python
def categorize(score, is_vip):
    if score >= 90:           # Branch 1: T/F
        grade = "A"
    else:
        grade = "B"
    if is_vip:                # Branch 2: T/F
        discount = 0.2
    else:
        discount = 0
    return grade, discount
```

- **Statement coverage 100%**: Need 2 tests (one hitting each branch)
  - `categorize(95, True)` → "A", 0.2
  - `categorize(80, False)` → "B", 0
- **Path coverage 100%**: Need 4 tests (2 branches × 2 branches = 4 paths)
  - `categorize(95, True)`, `categorize(95, False)`, `categorize(80, True)`, `categorize(80, False)`

---

## Topic 5 — Code Smells Identification

### 📖 Study Links 学习链接

- [[Week3-Reviews-OODesign-Refactoring#9. Code Smells 代码异味 (Detailed)]] — Full smell catalog
- [[Week3-Reviews-OODesign-Refactoring#Naming & Data Issues 命名与数据问题 ⭐ 历年考题常见]] — Mysterious Name, Global Data

### What the Exam Tests 考什么 (8-12 pts)

给你一段代码，要求你**识别所有 code smells** 并说出对应的 **refactoring technique** 来修复。

**Top Exam Smells 考试高频 Smells** (both 2023 & 2024):

| Smell | How to Spot 如何识别 | Fix |
|:---|:---|:---|
| **Mysterious Name** | `x`, `d`, `temp`, `calc()`, meaningless names | Rename Variable / Change Function Declaration |
| **Divergent Change** | One class changed for many different reasons | Extract Class |
| **Primitive Obsession** | Using strings/ints for domain concepts (zip code as string, money as float) | Replace Primitive with Object |
| **Long Function** | Function > 20 lines, does too many things | Extract Function |
| **Global Data** | Global variables modified from many places | Encapsulate Variable |
| **Duplicated Code** | Same/similar code appears in multiple places | Extract Function |
| **Comments** | Excessive comments explaining WHAT code does (not WHY) | Refactor to self-explanatory code |
| **Data Clumps** | Same group of variables always appear together | Extract Class / Introduce Parameter Object |
| **Feature Envy** | Method uses another class's data more than its own | Move Method |
| **Long Parameter List** | Function with 4+ parameters | Introduce Parameter Object |

> 🎯 **答题格式**: "This code exhibits **[Smell Name]** because [reason]. It can be fixed by applying **[Refactoring Technique]**."

---

## Topic 6 — Refactoring Techniques

### 📖 Study Links 学习链接

- [[Week3-Reviews-OODesign-Refactoring#10. Refactoring Catalog 重构目录]] — All techniques with examples
- [[Week3-Reviews-OODesign-Refactoring#10.1 Extract Method 提取方法]] — Most important refactoring

### What the Exam Tests 考什么 (8-13 pts)

给你一段有 code smells 的代码，要求你**实际重构**它 — 写出重构后的代码。

**Must-Know Refactorings 必须掌握的重构**:

| Technique | What it does | When to use |
|:---|:---|:---|
| **Extract Function** | Pull code block into its own function | Long Function, Duplicated Code |
| **Extract Variable** | Replace expression with named variable | Complex expressions |
| **Extract Class** | Move related fields/methods to new class | Large Class, Data Clumps |
| **Change Function Declaration** | Rename function or change parameters | Mysterious Name |
| **Rename Variable** | Give variable a meaningful name | Mysterious Name |
| **Replace Primitive with Object** | Wrap primitive in a class | Primitive Obsession |
| **Introduce Parameter Object** | Group related parameters into object | Long Parameter List, Data Clumps |

**Example refactoring pattern** (常见考题模式):

```python
# BEFORE (smells: Long Function, Mysterious Name, Duplicated Code)
def calc(d, r, t):
    a = d * (1 + r) ** t
    if a > 10000:
        print("High: " + str(a))
        # ... 20 more lines of formatting
    else:
        print("Low: " + str(a))
        # ... 20 similar lines of formatting

# AFTER (refactored)
def calculate_compound_interest(deposit, rate, years):
    final_amount = deposit * (1 + rate) ** years
    category = "High" if final_amount > 10000 else "Low"
    display_result(category, final_amount)

def display_result(category, amount):
    print(f"{category}: {amount}")
```

---

## Topic 7 — UML Class Diagrams

### 📖 Study Links 学习链接

- [[Week2-Software-Design#7. UML Diagrams]] — Class diagram notation
- [[Week3-Reviews-OODesign-Refactoring#4. OO Design Principles 面向对象设计原则]] — OO concepts

### What the Exam Tests 考什么 (17 pts — 2024)

给你一段 **requirements description**，要求你画出 UML Class Diagram:

**What to include**:

| Element | Notation | Example |
|:---|:---|:---|
| **Class box** | Name / Attributes / Methods | `User` / `- name: String` / `+ login()` |
| **Visibility** | `+` public, `-` private, `#` protected | `- password: String` |
| **Association** | Solid line with label | User — Order ("places") |
| **Multiplicity** | Numbers at ends | `1` — `*` (one to many) |
| **Composition** | Filled diamond ◆ | Order ◆—— OrderItem (items can't exist without order) |
| **Aggregation** | Empty diamond ◇ | Team ◇—— Player (players can exist independently) |
| **Inheritance** | Triangle arrow △ | SavingsAccount △—— Account |

**Multiplicity Quick Reference**:

| Symbol | Meaning |
|:---|:---|
| `1` | Exactly one |
| `0..1` | Zero or one (optional) |
| `*` or `0..*` | Zero or more |
| `1..*` | One or more |
| `2..5` | Between 2 and 5 |

> 🎯 **考试技巧**: 从需求描述中找出 **nouns** (→ classes), **verbs** (→ methods), **adjectives** (→ attributes), **relationships** (→ associations)

---

## Topic 8 — Dependency Injection & Testability

### 📖 Study Links 学习链接

- [[Week6-TestDoubles-Methodologies#6. Dependency Injection 依赖注入]] — DI patterns
- [[Week6-TestDoubles-Methodologies#2. Five Types of Test Doubles 五种测试替身]] — Using test doubles
- [[Week6-TestDoubles-Methodologies#3. Python unittest.mock 模块]] — Mock objects

### What the Exam Tests 考什么

2023 exam (Q3 Part c) 问: "What makes this code difficult to test?" — Answer: tightly coupled dependencies, no DI.

**Key Points to Know**:

1. **Tight coupling** = class creates its own dependencies inside `__init__` → can't replace with mocks
2. **Loose coupling** via DI = dependencies passed in from outside → easy to mock
3. **Three DI types**: Constructor injection (most common), Method injection, Setter injection
4. **Link to test doubles**: DI allows you to inject Mocks/Stubs/Fakes instead of real objects

---

## Priority Study Plan 优先学习计划

### 🔴 Must Master (出现在每年考试 — 高分值)

| Priority | Topic | Points | Study Time | Notes Link |
|:---:|:---|:---:|:---:|:---|
| 1 | **Database Normalization + SQL DDL** | 30 pts | 2-3 hours | [[Week5-Databases-APIs#Database Normalization 数据库规范化 ⭐ 历年必考]] |
| 2 | **Requirements: FR vs NFR + Quality Attributes** | 20 pts | 1-2 hours | [[Week1-Software-Engineering-Introduction#5. Requirements Engineering 需求工程]] |
| 3 | **Code Coverage Analysis** | 15-25 pts | 2 hours | [[Week4-DesignPatterns-Testing#22. Code Coverage 代码覆盖率]] |
| 4 | **Code Smells + Refactoring** | 18-25 pts | 2 hours | [[Week3-Reviews-OODesign-Refactoring#9. Code Smells 代码异味 (Detailed)]] |

### 🟡 Should Know (出现在 2024)

| Priority | Topic | Points | Study Time | Notes Link |
|:---:|:---|:---:|:---:|:---|
| 5 | **UML Class Diagrams** | 17 pts | 1-2 hours | [[Week2-Software-Design#7. UML Diagrams]] |
| 6 | **User Stories (INVEST, format)** | 5 pts | 30 min | [[Week1-Software-Engineering-Introduction#6. User Stories 用户故事]] |
| 7 | **Dependency Injection & Test Doubles** | 5-10 pts | 1 hour | [[Week6-TestDoubles-Methodologies#6. Dependency Injection 依赖注入]] |

### 🟢 Good to Know (可能出现的扩展题)

| Priority | Topic | Notes Link |
|:---:|:---|:---|
| 8 | Agile/Scrum/Kanban | [[Week6-TestDoubles-Methodologies#10. Scrum Framework 敏捷Scrum框架]] |
| 9 | Design Patterns (Observer, Strategy, Singleton) | [[Week4-DesignPatterns-Testing#Design Patterns]] |
| 10 | unittest.mock (patch, Mock, MagicMock) | [[Week6-TestDoubles-Methodologies#3. Python unittest.mock 模块]] |
| 11 | REST APIs | [[Week5-Databases-APIs#12. Web APIs & REST]] |
| 12 | Estimation (PERT, Story Points) | [[Week6-TestDoubles-Methodologies#14. Estimation Techniques 估算技术]] |

---

## Practice Questions 模拟练习

Based on the patterns from 2023 & 2024 exams, here are practice questions you should be able to answer:

### Practice 1: FR vs NFR Classification

> A music streaming app has these requirements. Classify each as FR or NFR, and for NFR identify the quality attribute:
>
> a) Users can create and name playlists → **FR**
> b) The app must load search results within 2 seconds → **NFR (Performance)**
> c) Users can add songs to their playlists → **FR**
> d) The system must handle 10,000 concurrent users → **NFR (Performance/Availability)**
> e) User passwords must be hashed using bcrypt → **NFR (Security)**
> f) The app should work on iOS and Android → **NFR (Portability)**
> g) Users can share playlists with friends → **FR**
> h) The system should be easy to add new music genres to → **NFR (Modifiability)**

### Practice 2: Normalization

> Given this unnormalized table, normalize to 3NF:
>
> | StudentID | Name | Courses | Professors | DeptID | DeptName |
> |---|---|---|---|---|---|
> | 1 | Alice | CS101, CS201 | Smith, Jones | CS | Computer Science |
>
> **Answer Approach**:
> 1. **1NF**: Eliminate multi-valued columns → one row per student-course
> 2. **2NF**: PK = (StudentID, Course) → Name, DeptID, DeptName depend only on StudentID (partial dep) → split
> 3. **3NF**: DeptName depends on DeptID (transitive) → split departments into own table
> Final: Students(StudentID, Name, DeptID), Departments(DeptID, DeptName), Enrollments(StudentID, CourseID), Courses(CourseID, CourseName, ProfessorID)

### Practice 3: Code Smell Identification

```python
# Identify all code smells in this code:
g_total = 0  # global variable

def p(a, b, c, d, e, f):
    global g_total
    # Calculate the thing
    x = a * b
    # Add tax
    x = x + (x * c)
    # Apply discount
    if d > 100:
        x = x - (x * 0.1)
    # Same calculation elsewhere
    y = a * b
    y = y + (y * c)
    g_total = g_total + x
    return x
```

> **Answers**:
> - **Mysterious Name**: `p`, `a`, `b`, `c`, `d`, `e`, `f`, `x`, `y`, `g_total` — none are meaningful → Rename
> - **Global Data**: `g_total` is a global variable → Encapsulate Variable
> - **Long Parameter List**: 6 parameters → Introduce Parameter Object
> - **Duplicated Code**: Lines calculating `x` and `y` are identical → Extract Function
> - **Comments**: Comments explain what code does, indicating code isn't self-explanatory → Rename + Extract Function
> - **Long Function** (if this were longer): Extract Function to break it up

### Practice 4: Write CREATE TABLE

> After normalizing, write SQL DDL for a Users and Orders table:

```sql
CREATE TABLE Users (
    UserID      INTEGER PRIMARY KEY AUTOINCREMENT,
    Username    VARCHAR(100) NOT NULL,
    Email       VARCHAR(255) NOT NULL,
    CreatedAt   DATE
);

CREATE TABLE Orders (
    OrderID     INTEGER PRIMARY KEY AUTOINCREMENT,
    UserID      INTEGER NOT NULL,
    OrderDate   DATE NOT NULL,
    TotalAmount DECIMAL(10, 2),
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);
```

### Practice 5: Code Coverage

```python
def check(x, y):
    if x > 0:          # Branch A
        result = "positive"
    else:
        result = "non-positive"
    if y == 0:          # Branch B
        result += " zero-y"
    return result
```

> **Q: Design test cases for 100% branch coverage.**
> Need to cover: Branch A True + False, Branch B True + False
> - Test 1: `check(1, 0)` → A=True, B=True → "positive zero-y"
> - Test 2: `check(-1, 5)` → A=False, B=False → "non-positive"
> → 2 tests achieve 100% branch coverage!
>
> **Q: How many tests for 100% path coverage?**
> Paths: (A-True, B-True), (A-True, B-False), (A-False, B-True), (A-False, B-False) = **4 paths**

---

## Quick Reference Cheat Sheet 速查表

### Normalization Rules

| | Rule | Key Question |
|:---|:---|:---|
| **1NF** | Atomic values only | "Is there a list/array in any cell?" |
| **2NF** | No partial dependencies | "Does every non-key depend on the WHOLE PK?" |
| **3NF** | No transitive dependencies | "Does any non-key depend on another non-key?" |

### Code Smell → Refactoring Mapping

| Smell → | Refactoring |
|:---|:---|
| Mysterious Name → | Rename Variable / Change Function Declaration |
| Long Function → | Extract Function |
| Duplicated Code → | Extract Function |
| Long Parameter List → | Introduce Parameter Object |
| Primitive Obsession → | Replace Primitive with Object |
| Global Data → | Encapsulate Variable |
| Feature Envy → | Move Method |
| Divergent Change → | Extract Class |
| Data Clumps → | Extract Class / Introduce Parameter Object |
| Comments (excessive) → | Refactor code to be self-explanatory |

### Coverage Hierarchy

```
Path Coverage (strongest) ⊃ Branch Coverage ⊃ Statement Coverage (weakest)
100% Path → guarantees 100% Branch → guarantees 100% Statement
But NOT the reverse!
```

### User Story Template

```
As a [ROLE],
I would like to [ACTION/FEATURE],
so that [BENEFIT/VALUE].
```

### CREATE TABLE Template

```sql
CREATE TABLE TableName (
    id       INTEGER PRIMARY KEY,
    name     VARCHAR(100) NOT NULL,
    other_id INTEGER,
    FOREIGN KEY (other_id) REFERENCES OtherTable(id)
);
```

---

## All Notes Index 全部笔记索引

| Week | File | Key Exam Topics |
|:---:|:---|:---|
| 1 | [[Week1-Software-Engineering-Introduction]] | FR/NFR, Quality Attributes, User Stories, INVEST |
| 2 | [[Week2-Software-Design]] | UML Class Diagrams, Design Principles |
| 3 | [[Week3-Reviews-OODesign-Refactoring]] | Code Smells, Refactoring Techniques, Code Reviews |
| 4 | [[Week4-DesignPatterns-Testing]] | Code Coverage, Testing, Design Patterns |
| 5 | [[Week5-Databases-APIs]] | Normalization (1NF-3NF), SQL DDL, Chinook, REST APIs |
| 6 | [[Week6-TestDoubles-Methodologies]] | Test Doubles, DI, Scrum/Kanban/Agile, Estimation |

---

> 📝 **Final Study Tips 最终学习建议**:
> 1. 🔴 **先做 Database Normalization 练习** — 30分最多，多练几遍从 UNF → 3NF 的过程
> 2. 🔴 **背 Code Smells 和对应 Refactoring** — 用上面的映射表，能识别就能拿分
> 3. 🔴 **练习 Code Coverage 追踪** — 画 CFG，追踪每个测试用例的路径
> 4. 🟡 **练习 User Story 写作** — 记住格式，特别是 "so that" 部分
> 5. 🟡 **练习 UML Class Diagram** — 从需求找 nouns=类, verbs=方法, adjectives=属性
> 6. 考前把这份文件的 **Practice Questions** 再做一遍！
