# Week 5: Databases, SQLAlchemy & Web APIs 数据库与网络API

> 📅 Week 5 | 🎯 Midterm Review Notes
> 📖 Sources: `09 Databases.pdf` ⭐, `10a Databases, Take 2.pdf` ⭐, `10. Accessing Web APIs.pdf`, `11. AJAX Requests.pdf`, Jupyter Notebooks
> ⭐ = Class Slides (最重要 Most Important)

---

## Table of Contents 目录

1. [[#1. Why Databases 为什么需要数据库]]
2. [[#2. Relational Database Model 关系数据库模型]]
3. [[#3. SQL Fundamentals SQL基础]]
4. [[#4. SELECT Queries 查询语句]]
5. [[#5. Aggregate Functions 聚合函数]]
6. [[#6. JOINs 表连接]]
7. [[#7. Data Manipulation INSERT UPDATE DELETE]]
8. [[#8. SQLAlchemy & Python Database Access]]
9. [[#9. Transactions 事务]]
10. [[#10. Parameterized Queries 参数化查询]]
11. [[#11. SQLAlchemy Results to Pandas]]
12. [[#12. Web APIs & REST]]
13. [[#13. AJAX & Fetch API]]
14. [[#14. Big Data & NoSQL 大数据与NoSQL]]
15. [[#15. Exam Quick Review 考试速览]]
16. [[#16. Concept Relationship Map 概念关系图]]

---

## 1. Why Databases 为什么需要数据库

### File System Problems 文件系统的问题

传统文件系统存储数据有很多限制 (Traditional file systems have many limitations):

| Problem 问题 | Description 描述 |
|:---|:---|
| **Data Redundancy** 数据冗余 | Same data stored in multiple files 同样的数据存在多个文件中 |
| **Data Inconsistency** 数据不一致 | Copies of data may not agree 不同副本的数据可能不一致 |
| **Difficult Data Access** 访问困难 | Need different programs for different queries 每个查询都需要不同的程序 |
| **Data Isolation** 数据孤岛 | Data scattered in various files/formats 数据分散在各种文件和格式中 |
| **Integrity Problems** 完整性问题 | Hard to enforce data constraints 难以强制执行数据约束 |
| **Atomicity Problems** 原子性问题 | Partial updates can leave data in bad state 部分更新可能导致数据状态异常 |
| **Concurrent Access** 并发访问 | Multiple users accessing same data 多用户同时访问同一数据 |
| **Security** 安全性 | Hard to control who sees what data 难以控制谁可以看到什么数据 |

### Database Advantages 数据库优势

- **Data Independence** 数据独立性: Programs don't need to know how data is stored 程序不需要知道数据如何存储
- **Efficient Access** 高效访问: DBMS uses sophisticated techniques to store/retrieve data
- **Data Integrity & Security** 数据完整性与安全: Centralized control of data
- **Reduced Redundancy** 减少冗余: Single source of truth
- **Concurrent Access** 并发控制: Multiple users can safely access simultaneously

> 💡 **关键概念**: Database = organized collection of structured information stored electronically 数据库是以电子方式存储的有组织的结构化信息的集合

---

## 2. Relational Database Model 关系数据库模型

### Core Concepts 核心概念

关系数据库把数据组织成**表 (Tables/Relations)**:

| Term 术语 | Meaning 含义 | Example 例子 |
|:---|:---|:---|
| **Relation / Table** 关系/表 | A collection of related data 一组相关数据 | `customers` table |
| **Attribute / Column** 属性/列 | A characteristic of the entity 实体的特征 | `firstName`, `email` |
| **Tuple / Row / Record** 元组/行/记录 | A single data entry 一条数据记录 | One customer's info |
| **Domain** 域 | Set of permitted values for an attribute 属性允许的值的集合 | Age: 0-150 |
| **Schema** 模式 | Structure/blueprint of the database 数据库的结构/蓝图 | Table definitions |

### Keys 键

Keys are crucial for identifying records and establishing relationships 键对于标识记录和建立关系至关重要:

| Key Type 键类型 | Purpose 用途 | Example 例子 |
|:---|:---|:---|
| **Primary Key (PK)** 主键 | Uniquely identifies each row 唯一标识每一行 | `customerID` |
| **Foreign Key (FK)** 外键 | References PK of another table 引用另一个表的主键 | `customer_id` in `orders` |
| **Composite Key** 复合键 | PK made of multiple columns 由多列组成的主键 | `(stock_symbol, closing_date)` |
| **Candidate Key** 候选键 | Could serve as PK (unique) 可以作为主键的列 | `email` (if unique) |

> 🔗 **FK creates relationships between tables**: `orders.customer_id` → `customers.customerID`
> 外键在表之间创建关系

### Database Normalization 数据库规范化 ⭐ 历年必考

> 🎯 **2023 & 2024 midterm 都考了 Normalization to 3NF**！这是数据库设计最核心的考点。

Normalization 是将数据库设计得**没有冗余**的过程 (Process of organizing data to reduce redundancy and dependency).

#### Unnormalized Form (UNF) 未规范化

原始数据可能有重复组 (repeating groups) 和冗余：

```
| OrderID | Customer | Items              | Prices       |
|---------|----------|--------------------|-------------|
| 1       | Alice    | Book, Pen, Pencil  | 20, 5, 3    |  ← repeating groups!
| 2       | Bob      | Book, Eraser       | 20, 2       |
```

#### First Normal Form (1NF) 第一范式

**Rule**: 每个单元格只包含**一个原子值** (Each cell contains only ONE atomic value — no repeating groups, no arrays)

```
| OrderID | Customer | Item    | Price |
|---------|----------|---------|-------|
| 1       | Alice    | Book    | 20    |  ← each cell = one value
| 1       | Alice    | Pen     | 5     |
| 1       | Alice    | Pencil  | 3     |
| 2       | Bob      | Book    | 20    |
| 2       | Bob      | Eraser  | 2     |
```

> ✅ **1NF Check**: No repeating groups? Each cell atomic? → Yes = 1NF

#### Second Normal Form (2NF) 第二范式

**Rule**: 满足 1NF + 每个**非键属性**完全依赖于**整个主键** (No partial dependency — every non-key attribute must depend on the WHOLE primary key)

> ⚠️ 2NF 只在有**复合主键**时才需要检查！如果主键是单列，1NF 就已经是 2NF。

Example problem: If PK = (OrderID, Item), then `Customer` only depends on `OrderID` (partial dependency!).

**Fix**: 拆分表 (Decompose):

```
Orders Table:                    OrderItems Table:
| OrderID | Customer |           | OrderID | Item    | Price |
|---------|----------|           |---------|---------|-------|
| 1       | Alice    |           | 1       | Book    | 20    |
| 2       | Bob      |           | 1       | Pen     | 5     |
                                 | 1       | Pencil  | 3     |
                                 | 2       | Book    | 20    |
                                 | 2       | Eraser  | 2     |
```

> ✅ **2NF Check**: Is it 1NF? No partial dependencies on composite PK? → Yes = 2NF

#### Third Normal Form (3NF) 第三范式

**Rule**: 满足 2NF + 没有**传递依赖** (No transitive dependency — non-key attributes must NOT depend on other non-key attributes)

Example problem:

```
| StudentID | StudentName | DeptID | DeptName    |
|-----------|-------------|--------|-------------|
| 1         | Alice       | CS     | Comp Sci    |
| 2         | Bob         | CS     | Comp Sci    |  ← DeptName depends on DeptID, not StudentID!
```

`DeptName` depends on `DeptID` (transitive: StudentID → DeptID → DeptName).

**Fix**: 拆分:

```
Students Table:                  Departments Table:
| StudentID | StudentName | DeptID |  | DeptID | DeptName    |
|-----------|-------------|--------|  |--------|-------------|
| 1         | Alice       | CS     |  | CS     | Comp Sci    |
| 2         | Bob         | CS     |  | EE     | Electrical  |
```

> ✅ **3NF Check**: Is it 2NF? No transitive dependencies? → Yes = 3NF

#### Normalization Quick Summary 规范化速查

| Normal Form | Rule 规则 | 关键词 |
|:---|:---|:---|
| **1NF** | Atomic values, no repeating groups 原子值，无重复组 | "每格一个值" |
| **2NF** | 1NF + No partial dependencies 无部分依赖 | "完全依赖整个主键" |
| **3NF** | 2NF + No transitive dependencies 无传递依赖 | "非键只依赖键" |

> 🔑 **记忆口诀**: "The key (**1NF**), the whole key (**2NF**), and nothing but the key (**3NF**), so help me Codd!"

#### Exam-Style Normalization Steps 考试做法

1. Identify the **primary key** (or composite key)
2. List all **functional dependencies** (A → B means A determines B)
3. Check **1NF**: Are all values atomic?
4. Check **2NF**: Any partial dependencies on composite PK? If yes → decompose
5. Check **3NF**: Any transitive dependencies (non-key → non-key)? If yes → decompose
6. Write **CREATE TABLE** statements for the final normalized tables

### The Chinook Database 示例数据库

课程使用 **chinook** 数据库作为例子 — 一个音乐商店数据库:
- `artists` → `albums` → `tracks`
- `customers` → `invoices` → `invoice_items`
- `employees` (with self-referencing FK for `ReportsTo`)
- `genres`, `media_types`, `playlists`

---

## 3. SQL Fundamentals SQL基础

SQL = **Structured Query Language** 结构化查询语言

SQL is divided into sub-languages SQL分为几个子语言:

| Sub-language 子语言 | Commands 命令 | Purpose 用途 |
|:---|:---|:---|
| **DDL** (Data Definition Language) | `CREATE`, `ALTER`, `DROP` | Define/modify database structure 定义/修改数据库结构 |
| **DML** (Data Manipulation Language) | `SELECT`, `INSERT`, `UPDATE`, `DELETE` | Query and modify data 查询和修改数据 |
| **DCL** (Data Control Language) | `GRANT`, `REVOKE` | Control access permissions 控制访问权限 |
| **TCL** (Transaction Control Language) | `COMMIT`, `ROLLBACK` | Manage transactions 管理事务 |

### DDL Examples DDL示例

```sql
-- CREATE: 创建新表
CREATE TABLE stock_data (
   stock_symbol      VARCHAR(5)    NOT NULL,
   closing_date      DATE          NOT NULL,
   open_price        DECIMAL(16,6) NOT NULL,
   close_price       DECIMAL(16,6) NOT NULL,
   volume            BIGINT        NOT NULL,
   PRIMARY KEY (stock_symbol, closing_date)
);

-- ALTER: 修改表结构
ALTER TABLE customers ADD COLUMN phone VARCHAR(20);

-- DROP: 删除表 ⚠️ 不可恢复!
DROP TABLE old_data;
```

### Common SQL Data Types 常见数据类型

| Type 类型 | Description 描述 |
|:---|:---|
| `INTEGER` / `INT` | Whole numbers 整数 |
| `VARCHAR(n)` | Variable-length string up to n chars 可变长度字符串 |
| `DECIMAL(p,s)` | Exact numeric: p total digits, s decimal places 精确数字 |
| `DATE` | Date value 日期 |
| `BIGINT` | Large integers 大整数 |
| `TEXT` | Unlimited length text 无限长度文本 |
| `BOOLEAN` | True/False 布尔值 |

---

## 4. SELECT Queries 查询语句

### Basic SELECT 基本查询

```sql
-- 查询所有列
SELECT * FROM customers;

-- 查询特定列
SELECT firstName, lastName, email FROM customers;

-- 使用别名 Aliases
SELECT firstName AS "First Name", lastName AS "Last Name" FROM customers;
```

### WHERE Clause 条件过滤

```sql
-- 等于
SELECT * FROM customers WHERE country = 'USA';

-- 比较运算符: =, <>, <, >, <=, >=
SELECT * FROM tracks WHERE milliseconds > 300000;

-- AND / OR
SELECT * FROM customers WHERE country = 'USA' AND state = 'CA';

-- IN: 在列表中
SELECT * FROM customers WHERE country IN ('USA', 'Canada', 'Brazil');

-- BETWEEN: 范围
SELECT * FROM invoices WHERE total BETWEEN 5 AND 15;

-- LIKE: 模式匹配
SELECT * FROM customers WHERE lastName LIKE 'S%';    -- starts with S
SELECT * FROM customers WHERE email LIKE '%gmail%';   -- contains gmail
-- % = any characters, _ = single character

-- IS NULL / IS NOT NULL: 空值检查
SELECT * FROM customers WHERE company IS NULL;
```

### ORDER BY 排序

```sql
-- 升序 (default) 默认升序
SELECT * FROM customers ORDER BY lastName;

-- 降序
SELECT * FROM customers ORDER BY lastName DESC;

-- 多列排序
SELECT * FROM customers ORDER BY country, lastName, firstName;
```

### DISTINCT 去重

```sql
-- 获取所有不重复的国家
SELECT DISTINCT country FROM customers;

-- 组合去重
SELECT DISTINCT country, city FROM customers;
```

### LIMIT 限制结果数量

```sql
-- 只返回前10条记录
SELECT * FROM tracks ORDER BY milliseconds DESC LIMIT 10;
```

---

## 5. Aggregate Functions 聚合函数

聚合函数对一组值执行计算并返回单个值:

| Function 函数 | Purpose 用途 | Example 例子 |
|:---|:---|:---|
| `COUNT(*)` | Count rows 计数行数 | `SELECT COUNT(*) FROM customers` |
| `COUNT(col)` | Count non-NULL values 计数非空值 | `SELECT COUNT(company) FROM customers` |
| `SUM(col)` | Total sum 总和 | `SELECT SUM(total) FROM invoices` |
| `AVG(col)` | Average value 平均值 | `SELECT AVG(total) FROM invoices` |
| `MIN(col)` | Minimum value 最小值 | `SELECT MIN(total) FROM invoices` |
| `MAX(col)` | Maximum value 最大值 | `SELECT MAX(total) FROM invoices` |

### GROUP BY 分组

`GROUP BY` groups rows with same values, then aggregate functions calculate per group.
将具有相同值的行分组，然后聚合函数按组计算:

```sql
-- 每个国家有多少客户?
SELECT country, COUNT(*) AS customer_count
FROM customers
GROUP BY country
ORDER BY customer_count DESC;

-- 每个股票的最高收盘价
SELECT stock_symbol, COUNT(*) num_records, MAX(adj_close_price)
FROM stock_data
GROUP BY stock_symbol;
-- Result:
-- AAPL:     60  $142.95
-- GME:    253  $147.98
-- SPY:   7037  $379.10
-- TSLA:  2689  $883.09
```

### HAVING 分组过滤

`HAVING` filters groups AFTER aggregation (`WHERE` filters BEFORE).
`HAVING` 在聚合之后过滤分组，`WHERE` 在聚合之前过滤:

```sql
-- 只显示客户数 > 5 的国家
SELECT country, COUNT(*) AS cnt
FROM customers
GROUP BY country
HAVING cnt > 5
ORDER BY cnt DESC;
```

> ⚠️ **WHERE vs HAVING 区别**:
> - `WHERE`: Filters individual rows BEFORE grouping 在分组前过滤单个行
> - `HAVING`: Filters groups AFTER aggregation 在聚合后过滤分组
> - `WHERE` 不能用聚合函数, `HAVING` 可以

### SQL Query Execution Order 查询执行顺序

这个顺序很重要! The execution order is different from the writing order:

```
Writing Order 书写顺序:    Execution Order 执行顺序:
1. SELECT               1. FROM / JOIN
2. FROM                 2. WHERE
3. WHERE                3. GROUP BY
4. GROUP BY             4. HAVING
5. HAVING               5. SELECT
6. ORDER BY             6. ORDER BY
7. LIMIT                7. LIMIT
```

---

## 6. JOINs 表连接

JOINs combine rows from two or more tables based on related columns.
JOIN将两个或多个表中的行基于相关列组合在一起:

### Types of JOINs JOIN类型

#### INNER JOIN 内连接
只返回两个表中都匹配的行 (Returns only matching rows from both tables):

```sql
SELECT artists.name, albums.title
FROM artists
INNER JOIN albums ON artists.artistid = albums.artistid;

-- 也可以用旧式语法 (Old-style syntax):
SELECT artists.name, albums.title
FROM artists, albums
WHERE artists.artistid = albums.artistid;
```

#### LEFT (OUTER) JOIN 左外连接
返回左表所有行 + 右表匹配的行 (All rows from left table + matching from right):

```sql
SELECT artists.name, albums.title
FROM artists
LEFT JOIN albums ON artists.artistid = albums.artistid;
-- Artists without albums will show NULL for album title
```

#### RIGHT (OUTER) JOIN 右外连接
返回右表所有行 + 左表匹配的行 (All rows from right table + matching from left):

```sql
SELECT artists.name, albums.title
FROM artists
RIGHT JOIN albums ON artists.artistid = albums.artistid;
```

#### FULL OUTER JOIN 全外连接
返回两个表的所有行 (All rows from both tables):

```sql
SELECT artists.name, albums.title
FROM artists
FULL OUTER JOIN albums ON artists.artistid = albums.artistid;
```

#### CROSS JOIN 交叉连接
笛卡尔积 — 每一行与另一表的每一行组合 (Cartesian product):

```sql
SELECT * FROM artists CROSS JOIN albums;
-- If artists has 275 rows and albums has 347 rows → 275 × 347 = 95,425 rows!
```

### JOIN Visualization JOIN可视化

```
INNER JOIN:        LEFT JOIN:         RIGHT JOIN:        FULL OUTER JOIN:
  ┌───┐             ┌───┐               ┌───┐            ┌───┐
 ╱█████╲           ╱█████╲             ╱█████╲          ╱█████╲
│███████│         │███████│           │███████│        │███████│
│██A∩B██│         │█A█████│           │█████B█│        │█A█∩█B█│
│███████│         │███████│           │███████│        │███████│
 ╲█████╱           ╲█████╱             ╲█████╱          ╲█████╱
  └───┘             └───┘               └───┘            └───┘
Only matched      All A +             All B +           All A +
rows              matched B           matched A          All B
```

### Self JOIN 自连接

```sql
-- Employees who report to other employees
SELECT e.firstName, e.lastName, m.firstName AS managerFirst
FROM employees e
JOIN employees m ON e.ReportsTo = m.EmployeeId;
```

### Multi-table JOINs 多表连接

```sql
-- Tracks with album and artist info (3 tables)
SELECT ar.name AS artist, al.title AS album, t.name AS track
FROM artists ar
JOIN albums al ON ar.artistid = al.artistid
JOIN tracks t ON al.albumid = t.albumid;
```

---

## 7. Data Manipulation INSERT UPDATE DELETE

### INSERT 插入数据

```sql
-- 插入单行
INSERT INTO artists (artistid, name) VALUES (99999, 'Fintech');

-- 插入多行
INSERT INTO artists (artistid, name) VALUES
  (99998, 'RockStar'),
  (99999, 'Fintech');
```

### UPDATE 更新数据

```sql
-- 更新特定行 ⚠️ 一定要加 WHERE!
UPDATE customers SET email = 'new@email.com' WHERE customerid = 1;

-- 更新多列
UPDATE tracks SET unitprice = 1.29, name = 'New Name' WHERE trackid = 100;
```

### DELETE 删除数据

```sql
-- 删除特定行 ⚠️ 一定要加 WHERE!
DELETE FROM artists WHERE artistid = 99999;

-- 删除满足条件的多行
DELETE FROM artists WHERE artistid > 99990;
```

> ⚠️ **WARNING 警告**: `UPDATE` and `DELETE` without `WHERE` affect ALL rows!
> 没有 `WHERE` 的 `UPDATE` 和 `DELETE` 会影响所有行！

---

## 8. SQLAlchemy & Python Database Access

### Architecture 架构

SQLAlchemy is a popular Python database library with layered architecture:
SQLAlchemy 是一个流行的Python数据库库，有分层架构:

```
┌─────────────────────────────┐
│         ORM Layer           │  ← Object Relational Mapper
│   (Classes → Tables)        │     把Python类映射到数据库表
├─────────────────────────────┤
│     SQLAlchemy Core         │  ← SQL Expression Language
│   (Python-oriented SQL)     │     Python风格的SQL
├─────────────────────────────┤
│        Engine               │  ← Connection management
│   (Connection Pool)         │     连接管理和连接池
├─────────────────────────────┤
│     DBAPI (PEP 249)         │  ← Python's standard DB interface
│   (sqlite3, psycopg2...)    │     Python标准数据库接口
└─────────────────────────────┘
```

### Connection String 连接字符串

格式: `dialect+driver://user:password@host:port/dbname`

| Component 组件 | Description 描述 | Example 例子 |
|:---|:---|:---|
| dialect | Database type 数据库类型 | `sqlite`, `postgresql`, `mysql` |
| driver | Specific driver (optional) 驱动 | `psycopg2`, `pymysql` |
| user:password | Authentication 认证 | `admin:secret123` |
| host:port | Server location 服务器位置 | `localhost:5432` |
| dbname | Database name 数据库名 | `chinook` |

```python
from sqlalchemy import create_engine, text

# SQLite (file-based) 基于文件
engine = create_engine("sqlite:///data/chinook.db")

# SQLite (in-memory) 内存数据库
engine = create_engine("sqlite:///:memory:")

# PostgreSQL
engine = create_engine("postgresql://user:pass@localhost:5432/mydb")
```

### Creating a Connection 创建连接

```python
# create_engine() 只是创建一个工厂，不会立即连接
engine = create_engine("sqlite:///data/chinook.db")

# connect() 才会真正连接到数据库
connection = engine.connect()
```

### Executing Queries 执行查询

```python
from sqlalchemy import text

# 必须用 text() 包装SQL字符串
stmt = text('SELECT firstName, lastName FROM customers ORDER BY lastName')
result = connection.execute(stmt)

# 获取第一条记录 (关闭result)
first_record = result.first()
print(first_record[0])          # By position 按位置: 'Roberto'
print(first_record.FirstName)   # By column name 按列名 (case sensitive!)
print(first_record._mapping)    # As dictionary 字典: {'FirstName': 'Roberto', 'LastName': 'Almeida'}

# 遍历所有记录
result = connection.execute(stmt)
for first, last in result:
    print(f"{last}, {first}")

# 获取所有记录为列表
result = connection.execute(stmt)
all_records = result.all()       # Returns list of row objects
all_records[1]._mapping          # Dictionary interface for 2nd row
```

### Context Manager 上下文管理器

```python
# 推荐使用 with 语句，会自动关闭连接
with engine.connect() as connection:
    result = connection.execute(text("SELECT * FROM artists"))
    all_rows = result.all()
    print(len(all_rows))
# connection is automatically closed here 连接自动关闭
```

> 💡 **Best Practice 最佳实践**: Always use context manager (`with`) to ensure connections are properly closed 始终使用上下文管理器确保连接被正确关闭

---

## 9. Transactions 事务

### What is a Transaction? 什么是事务?

A transaction is a group of operations that must ALL succeed or ALL fail.
事务是一组必须全部成功或全部失败的操作。

### ACID Properties ACID属性

| Property 属性 | Meaning 含义 | Example 例子 |
|:---|:---|:---|
| **A**tomicity 原子性 | All or nothing 全部成功或全部失败 | Transfer money: debit AND credit must both happen 转账：扣款和到账必须同时发生 |
| **C**onsistency 一致性 | DB moves from one valid state to another 数据库从一个有效状态转到另一个有效状态 | Total balance unchanged after transfer 转账后总余额不变 |
| **I**solation 隔离性 | Concurrent transactions don't interfere 并发事务互不干扰 | Two transfers at same time don't conflict 两笔同时的转账不冲突 |
| **D**urability 持久性 | Committed changes survive crashes 提交的更改在崩溃后仍然存在 | After commit, data is safe 提交后数据安全 |

### SQLAlchemy Transaction Modes SQLAlchemy事务模式

#### Default: Manual Commit 默认：手动提交

```python
# ⚠️ Without commit, changes are LOST!
with engine.connect() as connection:
    connection.execute(text("INSERT INTO artists VALUES(99999,'Fintech')"))
    # Transaction NOT committed → changes are rolled back!
    # 没有 commit → 更改会被回滚！

# ✅ With commit
with engine.connect() as connection:
    connection.execute(text("INSERT INTO artists VALUES(99999,'Fintech')"))
    connection.commit()  # 提交更改，数据才会保存
```

#### Auto-commit with begin() 使用begin()自动提交

```python
# begin() 在 with 结束时自动提交
with engine.begin() as connection:
    connection.execute(text("INSERT INTO artists VALUES(99999,'Fintech')"))
    # Transaction auto-committed when context manager exits
    # 上下文管理器退出时自动提交
```

#### Execution Options: AUTOCOMMIT 执行选项：自动提交

```python
with engine.connect() as connection:
    connection = connection.execution_options(isolation_level="AUTOCOMMIT")
    connection.execute(text("INSERT INTO artists VALUES(99999,'Fintech')"))
    # Each statement auto-committed 每条语句自动提交
```

### Transaction Control 事务控制

```python
with engine.connect() as connection:
    connection.execute(text("INSERT INTO artists VALUES(99999,'Fintech')"))
    connection.commit()    # ✅ 保存更改 Save changes

    connection.execute(text("INSERT INTO artists VALUES(99998,'Bad Data')"))
    connection.rollback()  # ❌ 撤销更改 Undo changes
```

### Three Transaction Patterns 三种事务模式对比

| Pattern 模式 | Code 代码 | Behavior 行为 |
|:---|:---|:---|
| Manual commit | `engine.connect()` + `connection.commit()` | Must explicitly commit 必须显式提交 |
| Auto-commit (begin) | `engine.begin()` | Commits when `with` exits 退出with时提交 |
| AUTOCOMMIT mode | `execution_options(isolation_level="AUTOCOMMIT")` | Each statement commits 每条语句都提交 |

---

## 10. Parameterized Queries 参数化查询

### Why Parameterized? 为什么要参数化?

1. **Prevent SQL Injection** 防止SQL注入攻击 🛡️
2. **Handle special characters** 处理特殊字符
3. **Better performance** 更好的性能 (query plan is pre-compiled 查询计划预编译)

### SQL Injection Example SQL注入示例

```python
# ❌ DANGEROUS! 危险！直接拼接字符串
user_input = "'; DROP TABLE customers; --"
query = f"SELECT * FROM customers WHERE name = '{user_input}'"
# This would DELETE the entire customers table! 这会删除整个客户表！

# ✅ SAFE! 安全！使用参数化查询
query = text("SELECT * FROM customers WHERE name = :name")
result = connection.execute(query, {"name": user_input})
# Input is treated as DATA, not SQL code 输入被当作数据，不是SQL代码
```

### SQLAlchemy Parameterized Queries SQLAlchemy参数化查询

```python
# 使用 :variableName 作为占位符
# Single parameter 单个参数
with engine.connect() as connection:
    query = text("SELECT artistID, name FROM artists WHERE artistid = :id")
    result = connection.execute(query, {"id": 1})
    print(result.first())  # (1, 'AC/DC')

# Multiple parameters 多个参数
query = text("SELECT * FROM tracks WHERE genreid = :genre AND milliseconds > :duration")
result = connection.execute(query, {"genre": 1, "duration": 300000})
```

### Executemany: Batch Operations 批量操作

```python
# 一次插入多条记录
with engine.connect() as connection:
    query = text("INSERT INTO artists (artistid, name) VALUES(:id, :name)")
    values = [
        {"id": 99999, "name": "Fintech"},
        {"id": 99998, "name": "RockStar"}
    ]
    result = connection.execute(query, values)
    print(result.rowcount, "record(s) inserted")  # 2 record(s) inserted
    connection.commit()
```

---

## 11. SQLAlchemy Results to Pandas

将SQLAlchemy查询结果转换为Pandas DataFrame非常实用:

```python
import pandas as pd
from sqlalchemy import create_engine, text

engine = create_engine("sqlite:///data/chinook.db")

with engine.connect() as connection:
    query = text("""
        SELECT c.country, i.invoicedate, i.total
        FROM customers c, invoices i
        WHERE c.customerid = i.customerid
    """)
    result = connection.execute(query)
    df = pd.DataFrame(result.all())
    df.columns = result.keys()  # 设置列名
    print(df)

#   Country          InvoiceDate  Total
# 0  Brazil  2010-03-11 00:00:00   3.98
# 1  Brazil  2010-06-13 00:00:00   3.96
# ...
```

### Stock Data Example 股票数据示例

```python
# Create in-memory database and load CSV stock data
engine = create_engine("sqlite:///:memory:")

# Create table with composite primary key
create_table_query = """
CREATE TABLE stock_data (
   stock_symbol      VARCHAR(5)    NOT NULL,
   closing_date      DATE          NOT NULL,
   open_price        DECIMAL(16,6) NOT NULL,
   close_price       DECIMAL(16,6) NOT NULL,
   volume            BIGINT        NOT NULL,
   PRIMARY KEY (stock_symbol, closing_date)
)"""

# Load CSV files into database using parameterized queries
def load_stock_file(symbol, fileName):
    with engine.connect() as connection:
        with open(fileName) as csv_file:
            csv_reader = csv.DictReader(csv_file)
            for row in csv_reader:
                row["ss"] = symbol
                connection.execute(text(insert_query), row)
        connection.commit()

# Query and convert to DataFrame
with engine.connect() as connection:
    query = text("SELECT * FROM stock_data WHERE stock_symbol IN ('GME','AAPL')")
    result = connection.execute(query)
    df = pd.DataFrame(result.all())
    df.columns = result.keys()
```

---

## 12. Web APIs & REST

### What is an API? 什么是API?

**API** = Application Programming Interface 应用程序编程接口
- A set of rules for how software components communicate 一套软件组件间通信的规则
- **Web API**: API accessed over HTTP/HTTPS 通过HTTP访问的API

### REST Principles REST原则

**REST** = **RE**presentational **S**tate **T**ransfer

| Principle 原则 | Meaning 含义 |
|:---|:---|
| **Client-Server** | Separation of concerns 关注点分离 |
| **Stateless** 无状态 | Each request contains all needed info 每个请求包含所有需要的信息 |
| **Uniform Interface** 统一接口 | Consistent URL patterns 一致的URL模式 |
| **Resource-Based** 基于资源 | Everything is a resource with a URL 一切都是有URL的资源 |

### HTTP Methods HTTP方法

| Method 方法 | Purpose 用途 | [[#7. Data Manipulation INSERT UPDATE DELETE\|SQL Equivalent]] |
|:---|:---|:---|
| **GET** | Read/retrieve data 读取数据 | `SELECT` |
| **POST** | Create new data 创建数据 | `INSERT` |
| **PUT** | Update/replace data 更新数据 | `UPDATE` |
| **DELETE** | Remove data 删除数据 | `DELETE` |
| **PATCH** | Partial update 部分更新 | `UPDATE` (partial) |

### HTTP Status Codes HTTP状态码

| Code 状态码 | Meaning 含义 |
|:---|:---|
| **200 OK** | Request succeeded 请求成功 |
| **201 Created** | Resource created 资源已创建 |
| **400 Bad Request** | Client error 客户端错误 |
| **401 Unauthorized** | Authentication required 需要认证 |
| **403 Forbidden** | No permission 没有权限 |
| **404 Not Found** | Resource doesn't exist 资源不存在 |
| **500 Internal Server Error** | Server error 服务器错误 |

> 💡 **记忆技巧**: 2xx = Success ✅, 4xx = Client error 🙅, 5xx = Server error 💥

### Python `requests` Library Python requests库

```python
import requests

# GET request
response = requests.get("https://api.example.com/data")
print(response.status_code)   # 200
print(response.json())        # Parse JSON response 解析JSON响应
print(response.text)          # Raw text 原始文本

# GET with parameters 带参数
params = {"symbol": "AAPL", "interval": "daily"}
response = requests.get("https://api.example.com/stock", params=params)

# POST request (create)
data = {"name": "New Item", "price": 9.99}
response = requests.post("https://api.example.com/items", json=data)

# With API key authentication
headers = {"Authorization": "Bearer YOUR_API_KEY"}
response = requests.get("https://api.example.com/data", headers=headers)
```

### JSON Data Format JSON数据格式

JSON (JavaScript Object Notation) is the standard format for API data.
JSON是API数据交换的标准格式:

```json
{
    "symbol": "AAPL",
    "price": 142.95,
    "volume": 86712000,
    "history": [
        {"date": "2021-01-04", "close": 129.41},
        {"date": "2021-01-05", "close": 131.01}
    ]
}
```

```python
import json

# Python dict → JSON string
json_string = json.dumps(my_dict)

# JSON string → Python dict
my_dict = json.loads(json_string)

# From API response
data = response.json()  # Automatically parses JSON
```

### API Authentication API认证方式

| Method 方式 | How 如何使用 |
|:---|:---|
| **API Key** | Pass key in header or query param 在header或参数中传递密钥 |
| **OAuth 2.0** | Token-based flow for user authorization 基于令牌的用户授权流程 |
| **Bearer Token** | JWT or session token in Authorization header 在Authorization头中传递令牌 |

### Rate Limiting 速率限制

APIs often limit how many requests you can make 很多API限制请求频率:
- Example: 5 requests per minute
- Check `X-RateLimit-Remaining` header
- Handle 429 (Too Many Requests) response

---

## 13. AJAX & Fetch API

### What is AJAX? 什么是AJAX?

**AJAX** = **A**synchronous **J**avaScript **A**nd **X**ML

AJAX allows web pages to update content WITHOUT reloading the entire page.
AJAX允许网页在不重新加载整个页面的情况下更新内容。

### The Fetch API

Modern JavaScript uses `fetch()` for AJAX requests (replaces older `XMLHttpRequest`):

```javascript
// Basic GET request
fetch('https://api.example.com/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('Error:', error));

// Using async/await (推荐 recommended)
async function getData() {
    try {
        const response = await fetch('https://api.example.com/data');
        const data = await response.json();
        console.log(data);
    } catch (error) {
        console.error('Error:', error);
    }
}

// POST request
async function createItem(item) {
    const response = await fetch('https://api.example.com/items', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(item)
    });
    return await response.json();
}
```

### Promises 异步承诺

A Promise represents a value that may be available now, later, or never.
Promise代表一个可能现在、以后或永远不可用的值:

```javascript
// Promise states 三种状态:
// Pending  待定 → operation in progress
// Fulfilled 已完成 → operation succeeded (.then)
// Rejected 已拒绝 → operation failed (.catch)

fetch(url)
    .then(response => response.json())  // Fulfilled: handle success
    .catch(error => console.log(error)) // Rejected: handle error
    .finally(() => console.log('Done')) // Always runs
```

### async/await 异步等待

`async/await` makes asynchronous code look synchronous 让异步代码看起来像同步代码:

```javascript
// async 标记函数为异步
async function fetchStockData(symbol) {
    // await 暂停执行直到Promise完成
    const response = await fetch(`/api/stocks/${symbol}`);

    if (!response.ok) {
        throw new Error(`HTTP error! status: ${response.status}`);
    }

    const data = await response.json();
    return data;
}
```

### CORS (Cross-Origin Resource Sharing) 跨域资源共享

Browsers block requests to different domains by default for security.
浏览器默认阻止对不同域的请求（安全机制）:

- **Same Origin**: `https://mysite.com/api` → `https://mysite.com/data` ✅
- **Cross Origin**: `https://mysite.com` → `https://api.other.com` ❌ (blocked by default)
- Server must set `Access-Control-Allow-Origin` header to allow cross-origin requests
- 服务器必须设置该header才能允许跨域请求

---

## 14. Big Data & NoSQL 大数据与NoSQL

### RDBMS for Big Data 关系数据库在大数据中的应用

Traditional RDBMS may struggle with 传统关系数据库在以下场景可能力不从心:
- Extremely large datasets 超大数据集
- High velocity data (streaming) 高速数据流
- Unstructured/semi-structured data 非结构化/半结构化数据

### NoSQL Databases NoSQL数据库

| Type 类型              | Example 例子       | Use Case 使用场景                         |
| :------------------- | :--------------- | :------------------------------------ |
| **Key-Value** 键值     | Redis, DynamoDB  | Caching, sessions 缓存、会话               |
| **Document** 文档      | MongoDB, CouchDB | Flexible schema, JSON data 灵活模式       |
| **Column-Family** 列族 | Cassandra, HBase | Time series, IoT data 时间序列            |
| **Graph** 图          | Neo4j            | Social networks, recommendations 社交网络 |
|                      |                  |                                       |

### SQL vs NoSQL

| Feature 特性 | SQL (Relational) | NoSQL |
|:---|:---|:---|
| Schema 模式 | Fixed, predefined 固定预定义 | Flexible/dynamic 灵活动态 |
| Scaling 扩展 | Vertical (bigger server) 垂直扩展 | Horizontal (more servers) 水平扩展 |
| ACID | Strong ACID 强ACID | Often eventual consistency 最终一致性 |
| Query Language | SQL | Varies by system 各系统不同 |
| Best For 最适合 | Structured data, complex queries | Large-scale, flexible data |

---

## 15. Exam Quick Review 考试速览

### 🔑 Must-Know SQL Syntax SQL必考语法

```sql
-- Complete query template 完整查询模板
SELECT column1, AGG_FUNC(column2)
FROM table1
JOIN table2 ON table1.fk = table2.pk
WHERE condition
GROUP BY column1
HAVING AGG_FUNC(column2) > value
ORDER BY column1 ASC/DESC
LIMIT n;
```

### 🔑 Must-Know SQLAlchemy Pattern SQLAlchemy必考模式

```python
from sqlalchemy import create_engine, text

# 1. Create engine 创建引擎
engine = create_engine("sqlite:///database.db")

# 2. Connect and query 连接并查询
with engine.connect() as connection:
    # 3. Use text() for queries 使用text()
    result = connection.execute(text("SELECT * FROM table"))

    # 4. Process results 处理结果
    for row in result:
        print(row)

    # 5. Commit if modifying data 修改数据时提交
    connection.commit()
```

### 🔑 Key Comparisons 关键对比

| Concept A | vs | Concept B |
|:---|:---|:---|
| `WHERE` | vs | `HAVING` — before vs after GROUP BY |
| `INNER JOIN` | vs | `LEFT JOIN` — only matches vs all from left |
| `engine.connect()` | vs | `engine.begin()` — manual vs auto commit |
| `result.first()` | vs | `result.all()` — one row vs all rows (list) |
| `text()` query | vs | Parameterized `:var` — raw vs safe |
| `GET` | vs | `POST` — retrieve vs create |
| SQL | vs | NoSQL — structured vs flexible |
| `.then()` chain | vs | `async/await` — two ways to handle Promises |
| `fetch()` | vs | `requests.get()` — JavaScript vs Python |

### 🔑 Common Mistakes 常见错误

1. ❌ Forgetting `connection.commit()` → changes lost 忘记提交→更改丢失
2. ❌ `UPDATE`/`DELETE` without `WHERE` → affects ALL rows 没有WHERE→影响所有行
3. ❌ String concatenation in SQL → SQL injection 字符串拼接→SQL注入
4. ❌ Using `result.first()` then trying to iterate → result already closed
5. ❌ Forgetting `text()` wrapper → SQLAlchemy error
6. ❌ Not handling HTTP errors → program crashes 不处理HTTP错误→程序崩溃
7. ❌ `WHERE` with aggregate functions → use `HAVING` instead

---

## 16. Concept Relationship Map 概念关系图

```
┌─────────────────────────────────────────────────────────────┐
│                    DATABASE ECOSYSTEM                        │
│                      数据库生态系统                            │
├──────────────────────┬──────────────────────────────────────┤
│                      │                                      │
│  ┌─────────────┐     │    ┌──────────────────┐              │
│  │  Relational │     │    │   Web APIs        │              │
│  │  Database   │     │    │   网络API          │              │
│  │  关系数据库  │     │    ├──────────────────┤              │
│  ├─────────────┤     │    │ REST Principles   │              │
│  │ Tables 表    │     │    │ HTTP Methods      │              │
│  │ Keys 键      │     │    │ JSON Format       │              │
│  │ Schema 模式  │     │    │ Status Codes      │              │
│  └──────┬──────┘     │    └────────┬─────────┘              │
│         │            │             │                         │
│    ┌────▼────┐       │    ┌────────▼─────────┐              │
│    │  SQL     │       │    │  Python requests  │              │
│    │ 查询语言  │       │    │  .get() .post()   │              │
│    ├─────────┤       │    └────────┬─────────┘              │
│    │ SELECT  │       │             │                         │
│    │ INSERT  │       │    ┌────────▼─────────┐              │
│    │ UPDATE  │       │    │  JS fetch()       │              │
│    │ DELETE  │       │    │  async/await       │              │
│    │ JOIN    │       │    │  AJAX              │              │
│    │ GROUP BY│       │    └──────────────────┘              │
│    └────┬────┘       │                                      │
│         │            │                                      │
│  ┌──────▼──────┐     │                                      │
│  │ SQLAlchemy  │     │                                      │
│  │ Python ↔ DB │     │                                      │
│  ├─────────────┤     │                                      │
│  │ Engine      │────►│──── Connection Pool 连接池             │
│  │ text()      │     │                                      │
│  │ Transactions│     │                                      │
│  │ Params :var │     │                                      │
│  │ → Pandas DF │     │                                      │
│  └─────────────┘     │                                      │
│                      │                                      │
│  ┌─────────────┐     │                                      │
│  │  ACID       │     │                                      │
│  │  事务保障    │     │                                      │
│  └─────────────┘     │                                      │
└──────────────────────┴──────────────────────────────────────┘
```

### Cross-Week Links 跨周链接

- [[Week4-DesignPatterns-Testing#Repository Pattern]] → Connects to [[#8. SQLAlchemy & Python Database Access|Database access patterns]] in Week 5
- [[Week3-Reviews-OODesign-Refactoring]] → OOP concepts used in [[#8. SQLAlchemy & Python Database Access|SQLAlchemy ORM]] models
- [[Week2-Software-Design]] → Design principles apply to [[#12. Web APIs & REST|API design]]
- [[Week1-Software-Engineering-Introduction]] → SDLC includes database design phase

---

> 📝 **Study Tips 学习建议**:
> 1. 练习手写SQL查询 — 特别是JOIN和GROUP BY
> 2. 理解SQLAlchemy的三种事务模式
> 3. 能区分WHERE和HAVING的使用场景
> 4. 理解参数化查询防止SQL注入的原理
> 5. 知道HTTP方法与SQL操作的对应关系
