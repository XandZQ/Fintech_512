# Week 1: Software Engineering Introduction 软件工程导论

> **Course**: Software Engineering
> **Textbook**: *Software Engineering: A Modern Approach* — Marco Tulio Valente
> **Coverage**: Slides 01, 02, 03 + Chapters 1 & 3

---

## Table of Contents

- [[#1. What is Software Engineering 什么是软件工程]]
- [[#2. Why Software Engineering Matters 为什么重要]]
- [[#3. Software Types 软件类型]]
- [[#4. Software Process Models 软件过程模型]]
- [[#5. Requirements Engineering 需求工程]]
- [[#6. User Stories 用户故事]]
- [[#7. Use Cases 用例]]
- [[#8. MVP — Minimum Viable Product 最小可行产品]]
- [[#9. Lean Canvas 精益画布]]
- [[#10. Git Version Control 版本控制]]
- [[#11. Test-Driven Development (TDD) 测试驱动开发]]
- [[#12. Requirements to Specification 从需求到规格]]
- [[#Exam Quick Review 考试速览]]

---

## 1. What is Software Engineering 什么是软件工程

### Definition 定义

Software Engineering is the **systematic application of engineering principles** to the development, operation, and maintenance of software. It covers the **entire lifecycle** — not just coding, but also design, testing, deployment, and maintenance.

> 简单理解：软件工程 ≠ 只写代码。它是用**工程化的方法**来开发、运行和维护软件的整个过程。

### Key Distinction: Programming vs. Software Engineering

| Aspect | Programming 编程 | Software Engineering 软件工程 |
|--------|-----------------|---------------------------|
| Scale | Small programs, individual | Large systems, teams |
| Focus | Writing code | **Entire lifecycle** (design → deploy → maintain) |
| Duration | Short-term | Long-term (years of maintenance) |
| Approach | Ad-hoc | **Systematic, disciplined** |
| Testing | Maybe some | **Rigorous** ([[#11. Test-Driven Development (TDD) 测试驱动开发\|TDD]], unit tests, etc.) |

> **Exam tip**: The course emphasizes that SE is about **more than coding** — it's about process, people, and quality. 考试重点：SE不仅仅是编码。

### IEEE Definition

> "Software Engineering is the application of a **systematic, disciplined, quantifiable** approach to the development, operation, and maintenance of software."

Three key words to remember: **Systematic（系统化）**, **Disciplined（有纪律的）**, **Quantifiable（可量化的）**

---

## 2. Why Software Engineering Matters 为什么重要

### Software is Everywhere 软件无处不在

Modern society depends heavily on software: banking, healthcare, transportation, communication, entertainment. A failure in critical software can have **catastrophic consequences**.

### The Software Crisis 软件危机

In the 1960s-70s, the industry experienced a "software crisis":
- Projects **over budget** 预算超支
- Projects **late** 延期交付
- Software **didn't meet requirements** 不满足需求
- Code was **unmaintainable** 难以维护

> This crisis motivated the birth of Software Engineering as a discipline. 这场危机催生了软件工程这门学科。

### Famous Software Failures

The slides mention several real-world failures to illustrate why SE matters:
- **Ariane 5 rocket explosion** (1996) — software bug caused $370M loss
- Healthcare.gov launch failures
- Various banking system crashes

> 这些例子说明：没有好的工程实践，软件失败的后果可能非常严重。

---

## 3. Software Types 软件类型

Understanding what **type** of software you're building affects which [[#4. Software Process Models 软件过程模型|process model]] you choose.

### Categories from the textbook:

| Type | Description | Example |
|------|-------------|---------|
| **Information Systems** 信息系统 | Store, retrieve, process data | Banking system, ERP |
| **Embedded Systems** 嵌入式系统 | Run on specific hardware | Car engine controller |
| **Real-time Systems** 实时系统 | Must respond within strict time | Air traffic control |
| **Mobile Apps** 移动应用 | Run on smartphones | Instagram, WeChat |
| **Batch Processing** 批处理系统 | Process large data volumes | Payroll, billing |
| **Web Applications** 网络应用 | Browser-based | Gmail, Amazon |

### Internal vs. External Software

- **Internal (in-house)**: Built for use within the organization 内部使用
- **External (COTS/Market)**: Built for sale to customers 对外销售

> The type of software influences requirements, process, and testing strategies. 软件类型影响你选择的[[#5. Requirements Engineering 需求工程|需求方法]]和[[#4. Software Process Models 软件过程模型|开发过程]]。

---

## 4. Software Process Models 软件过程模型

A **software process model** defines the **sequence of activities** (phases) used to develop software. 软件过程模型定义了开发软件的活动顺序。

### 4.1 Waterfall Model 瀑布模型

The **oldest and most traditional** model. Each phase must be completed before the next begins — like water flowing down.

```
Requirements → Design → Implementation → Testing → Deployment → Maintenance
     ↓            ↓          ↓              ↓           ↓            ↓
  (完成后才能进入下一阶段，不能回头)
```

**Characteristics**:
- **Sequential** 顺序执行: one phase at a time
- **Document-driven** 文档驱动: heavy documentation at each phase
- **No going back** 不能回头: changes are very expensive once a phase is complete
- **Big upfront design** 前期大量设计

**When to use 适用场景**:
- Requirements are **well-understood and stable** 需求明确且稳定
- Mission-critical systems (medical, military, aviation) 关键任务系统
- Regulatory requirements demand documentation 法规要求文档

**Problems 问题**:
- Real-world requirements **change frequently** 现实中需求经常变
- Users only see the final product — too late for feedback 用户只能在最后看到产品
- High risk of building the wrong thing 容易做出错误的产品

> **Exam key point**: Waterfall 适合需求**稳定**的项目。现代项目大多需求不稳定，所以更常用 [[#4.2 Agile Methods 敏捷方法|Agile]]。

### 4.2 Agile Methods 敏捷方法

Agile is an **iterative, incremental** approach that embraces change. Instead of one big delivery, software is built in **small increments** (iterations/sprints).

#### The Agile Manifesto 敏捷宣言 (2001)

Four core values:

| Value | Meaning |
|-------|---------|
| **Individuals and interactions** over processes and tools | 人和沟通 > 流程和工具 |
| **Working software** over comprehensive documentation | 可工作的软件 > 详尽的文档 |
| **Customer collaboration** over contract negotiation | 客户合作 > 合同谈判 |
| **Responding to change** over following a plan | 响应变化 > 遵循计划 |

> 注意："over" 不是说右边不重要，而是说**左边更重要**。

#### Key Agile Principles:
- Deliver working software **frequently** (weeks, not months) 频繁交付
- Welcome **changing requirements** 欢迎需求变更
- Business people and developers **work together daily** 每天合作
- Face-to-face conversation is the best communication 面对面沟通最有效
- **Working software** is the primary measure of progress 可工作的软件是进度的主要衡量标准
- Continuous attention to **technical excellence** 持续关注技术卓越

### 4.3 Scrum

**Scrum** is the most popular agile framework. It defines specific **roles, events, and artifacts**.

#### Scrum Roles 角色:

| Role | Responsibility |
|------|---------------|
| **Product Owner (PO)** 产品负责人 | Represents the customer; manages the [[#Product Backlog]]; prioritizes [[#6. User Stories 用户故事\|user stories]] |
| **Scrum Master (SM)** | Facilitates the process; removes obstacles; NOT a project manager |
| **Development Team** 开发团队 | Cross-functional, self-organizing; typically 3-9 people |

#### Scrum Events 事件:

| Event | Duration | Purpose |
|-------|----------|---------|
| **Sprint** | 2-4 weeks (fixed) | One iteration cycle; produces a potentially shippable increment |
| **Sprint Planning** | Start of sprint | Team selects stories from backlog; creates sprint backlog |
| **Daily Standup** | 15 min, every day | 3 questions: What did I do? What will I do? Any blockers? |
| **Sprint Review** | End of sprint | Demo working software to stakeholders; get feedback |
| **Sprint Retrospective** | After review | Team reflects: what went well? what to improve? |

#### Scrum Artifacts 工件:

##### Product Backlog
An **ordered list** of everything needed in the product. Managed by the **Product Owner**. Items are usually [[#6. User Stories 用户故事|user stories]].

##### Sprint Backlog
The set of stories selected for the **current sprint**, plus a plan for delivering them.

##### Increment
The sum of all completed backlog items. Must be in a **usable condition** (potentially shippable).

#### Scrum Flow 流程图:

```
Product Backlog → Sprint Planning → Sprint Backlog → Sprint (2-4 weeks)
                                                         ↓
                                              Daily Standup (every day)
                                                         ↓
                                              Sprint Review (demo)
                                                         ↓
                                              Sprint Retrospective (改进)
                                                         ↓
                                              Increment (可交付的产品增量)
```

> **Exam tip**: 记住 Scrum 的三个角色、五个事件、三个工件。这是高频考点。

### 4.4 Kanban

Another agile approach, originally from Toyota's manufacturing system.

**Key concepts**:
- **Visual board** 看板: columns represent workflow stages (To Do → In Progress → Done)
- **WIP Limits** (Work In Progress): limit the number of items in each column to avoid overload
- **Continuous flow**: no fixed-length sprints; work flows continuously
- **Pull system**: new work is "pulled" when capacity is available

**Kanban vs Scrum**:

| Aspect | Scrum | Kanban |
|--------|-------|--------|
| Iterations | Fixed sprints | Continuous flow |
| Roles | PO, SM, Team | No prescribed roles |
| Changes | During sprint = no changes | Can change anytime |
| WIP Limits | Sprint capacity | Per-column limits |

### 4.5 Extreme Programming (XP) 极限编程

XP emphasizes **engineering practices** more than other agile methods.

Key XP Practices:
- **Pair Programming** 结对编程: two developers work at one computer
- **Test-Driven Development** ([[#11. Test-Driven Development (TDD) 测试驱动开发|TDD]]): write tests BEFORE code
- **Continuous Integration**: merge code frequently (multiple times/day)
- **Refactoring**: improve code structure without changing behavior
- **Simple Design**: don't over-engineer; do the simplest thing that works
- **Collective Code Ownership**: anyone can change any code
- **40-hour Week**: sustainable pace

> XP 的核心思想：通过极致的工程实践来保证代码质量。See also [[#11. Test-Driven Development (TDD) 测试驱动开发]].

### 4.6 Comparison Summary 模型对比总结

```
                    Flexibility（灵活性）
                         ↑
         Kanban ←--------+--------→ Waterfall
          XP             |          (rigid 僵化)
         Scrum           |
      (flexible 灵活)    |
                         ↓
                    Predictability（可预测性）
```

| Model | Best For |
|-------|----------|
| **Waterfall** | Stable requirements, regulated industries |
| **Scrum** | Most commercial software projects |
| **Kanban** | Maintenance, support, continuous delivery |
| **XP** | Projects needing high code quality |

---

## 5. Requirements Engineering 需求工程

> *"The hardest single part of building a software system is deciding precisely what to build."* — Frederick Brooks

Requirements define **what a system should do** and **the constraints** under which it should operate. This is the bridge between the **real-world problem** and the **software solution**.

```
Real World (Problem) → [Requirements] → Software (Solution)
```

### 5.1 Types of Requirements 需求类型

#### Functional Requirements (FR) 功能性需求

**What the system should DO** — the features and behaviors.

Examples for a banking system:
- Report account balance 查询余额
- Process transfers between accounts 转账
- Execute bank draft payments 执行汇票支付
- Cancel debit cards 注销借记卡

> Functional requirements are usually written in **natural language**.

#### Non-Functional Requirements (NFR) 非功能性需求

**Constraints and quality attributes** — HOW the system should perform.

| NFR Type | Metric 衡量指标 | Example |
|----------|------|---------|
| **Performance** 性能 | Transactions/sec, response time, latency | Response < 1 second |
| **Efficiency** 效率 | Resource usage per operation | Process 1000 records using < 50MB RAM |
| **Reliability** 可靠性 | Uptime %, MTBF (Mean Time Between Failures) | 99.9% uptime |
| **Availability** 可用性(系统) | % of time system is operational | Available 24/7, < 1hr downtime/year |
| **Security** 安全性 | Encryption standard, access control | AES-256 encryption |
| **Functionality** 功能性 | Feature completeness, correctness | All specified features work correctly |
| **Modifiability** 可修改性 | Cost/effort to make changes | Add new payment method in < 2 days |
| **Maintainability** 可维护性 | Ease of fixing bugs, updating | Bug fix turnaround < 4 hours |
| **Usability** 易用性 | User training time, error rate | Learn in 30 min |
| **Portability** 可移植性 | % of portable code, platform count | Runs on Windows, Mac, Linux |
| **Testability** 可测试性 | Ease of testing, coverage achievable | 90% code coverage achievable |
| **Reusability** 可复用性 | % of reusable components | Core modules usable in 3+ projects |

> **Exam tip**: NFR 用**具体指标**衡量，不能说"系统要快"，要说"响应时间 < 1秒"。Avoid vague terms like "the system should be fast."

> 🎯 **历年考题重点**: 2023/2024 midterm 都考了 Quality Attributes 识别！给你一段项目描述，判断是哪种 NFR:
> - Performance → speed, response time, throughput 速度相关
> - Reliability → failure, crash, MTBF 故障相关
> - Security → encryption, authentication, authorization 安全相关
> - Usability → easy to learn, user-friendly 用户友好
> - Modifiability → easy to change, extend 易修改
> - Maintainability → easy to fix, update 易维护
> - Availability → uptime, always accessible 系统可用时间
> - Testability → easy to test, verify 易测试

#### User Requirements vs System Requirements

| | User Requirements 用户需求 | System Requirements 系统需求 |
|-|--------------------------|---------------------------|
| **Written by** | Users/customers | Developers |
| **Level** | High-level, non-technical | Detailed, precise, technical |
| **Close to** | The **problem** | The **solution** |
| **Example** | "Allow fund transfers" | "Implement ACH protocol for transfers with 256-bit encryption" |

### 5.2 Requirements Engineering Process 需求工程过程

```
Elicitation（获取）→ Documentation（文档化）→ Validation（验证）→ Prioritization（优先级排序）
```

#### Elicitation 需求获取

The process of **discovering and understanding** requirements from stakeholders.

**Techniques 技术**:
- **Interviews** 访谈: talk to stakeholders directly
- **Questionnaires** 问卷: collect data from many people
- **Document review** 文档审查: study existing systems/documents
- **User workshops** 用户工作坊: group sessions with stakeholders
- **Prototyping** 原型: build a quick version to gather feedback
- **Scenario analysis** 场景分析: walk through usage scenarios
- **Ethnography** 人种学研究: observe users in their natural work environment (silently!)

> Ethnography 来自人类学——开发者进入用户的工作环境，**静默观察**他们如何工作，不干预。

#### Validation 需求验证

Requirements should be:

| Quality | Meaning | 中文 |
|---------|---------|------|
| **Correct** | Accurately represent what's needed | 正确的 |
| **Precise** | No ambiguity (avoid "and/or" confusion) | 精确的 |
| **Complete** | All necessary features are included | 完整的 |
| **Consistent** | No contradictions between requirements | 一致的 |
| **Verifiable** | Can be tested/checked | 可验证的 |

> **Classic ambiguity example**: "A student needs to score 60 points during the semester or score 60 points in the Special Exam and attend classes regularly." This can be parsed two different ways! Use parentheses to clarify. 经典歧义案例：用括号消除歧义。

#### Prioritization 优先级排序

Not all requirements will be in the first release. Budget, time, and resources force **prioritization**. Requirements can also **change over time**.

**Traceability（可追溯性）**: The ability to map requirements ↔ code. If a requirement changes, you can find all affected code, and vice versa.

### 5.3 Requirements Engineering Challenges 挑战

Common problems found in industry surveys:
- Incomplete/undocumented requirements (48%)
- Communication flaws between developers and customers (41%)
- Constantly changing requirements (33%)
- Abstractly specified requirements (33%)
- Time constraints (32%)

> RE is a **multi-disciplinary** activity — political, cognitive, and communication barriers all play a role.

---

## 6. User Stories 用户故事

User stories are the **primary way to document requirements in Agile** development. They replace the traditional, heavy requirements specification documents used in [[#4.1 Waterfall Model 瀑布模型|Waterfall]].

### 6.1 Structure: The Three Cs

> **User Story = Card + Conversations + Confirmation**

| Component | Description | 说明 |
|-----------|-------------|------|
| **Card** 卡片 | A few sentences describing a desired feature, written by the customer | 用户用简短语言描述想要的功能 |
| **Conversations** 对话 | Ongoing discussions between developers and the customer representative to clarify the card | 开发者和客户代表之间的持续沟通 |
| **Confirmation** 确认 | High-level acceptance tests that verify the story is implemented correctly | 验收测试，确认功能正确实现 |

### 6.2 User Story Format 格式

The standard template:

```
As a [type of user],          // 作为一个 [用户类型]
I want [some feature],        // 我想要 [某个功能]
so that [some benefit].       // 以便 [获得某个好处]
```

**Examples**:
- "As a **customer**, I want to **search for products by name**, so that I can **quickly find what I need**."
- "As an **admin**, I want to **view all user accounts**, so that I can **manage user access**."
- "As a **professor**, I want to **see the list of enrolled students**, so that I can **track attendance**."

### 6.3 INVEST Criteria INVEST 准则

Good user stories follow **INVEST**:

| Letter | Criterion | Meaning | 说明 |
|--------|-----------|---------|------|
| **I** | Independent | Stories can be implemented in any order | 相互独立，不依赖 |
| **N** | Negotiable | Cards are invitations for conversation, not rigid contracts | 可协商，不是固定合同 |
| **V** | Valuable | Must add value to the customer's business | 对业务有价值 |
| **E** | Estimable | Team can estimate the effort needed | 可估算工作量 |
| **S** | Small | Small enough to implement in one sprint (< 1 week) | 足够小，一个sprint内完成 |
| **T** | Testable | Has clear acceptance tests | 可测试，有明确验收标准 |

> **Exam tip**: "INVEST" 是必考内容。每个字母代表什么，要能举例说明。

### 6.4 Epics 史诗故事

An **Epic** is a **large user story** that is too big to fit in a single sprint. Epics are broken down into smaller stories before implementation.

```
Epic (too big) → Story 1 + Story 2 + Story 3 ... (small enough for a sprint)
```

### 6.5 Writing Good Acceptance Tests (Confirmation)

Acceptance tests are written by the **customer/PO**, not developers. They define when a story is "done."

Example story: "As a customer, I want to pay by credit card"
- Acceptance test 1: Payment succeeds with valid Visa card
- Acceptance test 2: Payment succeeds with valid Mastercard
- Acceptance test 3: Payment fails with expired card and shows error message

> Acceptance tests ≠ unit tests. They are **high-level, business-focused**. 验收测试是高层的、业务导向的，不是代码层面的单元测试。

---

## 7. Use Cases 用例

Use cases are a **more detailed** way to specify requirements compared to [[#6. User Stories 用户故事|user stories]]. They were introduced by **Ivar Jacobson** in 1987.

### 7.1 What is a Use Case?

A use case describes a **sequence of interactions** between a user (called an **Actor**) and the system to achieve a specific goal.

### 7.2 Key Components 关键组成

| Component | Description | 说明 |
|-----------|-------------|------|
| **Actor** 参与者 | An external entity that interacts with the system (user, another system) | 与系统交互的外部实体 |
| **Use Case Name** | A verb phrase describing the goal | 动词短语描述目标 |
| **Preconditions** 前置条件 | What must be true before the use case starts | 用例开始前必须满足的条件 |
| **Main Flow** 主流程 | The "happy path" — normal sequence of steps | 正常情况下的步骤序列 |
| **Alternative Flows** 替代流程 | Variations from the main flow | 主流程的变体 |
| **Exception Flows** 异常流程 | Error handling scenarios | 错误处理场景 |
| **Postconditions** 后置条件 | What must be true after the use case completes | 用例完成后的状态 |

### 7.3 Use Case Example 示例

**Use Case: Transfer Funds 转账**

| Field | Content |
|-------|---------|
| **Actor** | Bank Customer |
| **Precondition** | Customer is logged in; has at least one account |
| **Main Flow** | 1. Customer selects "Transfer" → 2. System shows accounts → 3. Customer selects source & destination → 4. Customer enters amount → 5. System validates → 6. System executes transfer → 7. System shows confirmation |
| **Alternative Flow** | 3a. Customer selects external account → system requests routing number |
| **Exception Flow** | 5a. Insufficient funds → system shows error message |
| **Postcondition** | Funds are transferred; both account balances updated |

### 7.4 User Stories vs Use Cases 对比

| Aspect | User Stories | Use Cases |
|--------|-------------|-----------|
| **Detail level** | Brief (1-2 sentences) | Detailed (full document) |
| **Methodology** | [[#4.2 Agile Methods 敏捷方法\|Agile]] | [[#4.1 Waterfall Model 瀑布模型\|Waterfall]] or detailed Agile |
| **Written by** | Customer/PO | Analyst/Developer |
| **Focus** | What the user wants (value) | How the interaction flows (steps) |
| **When to use** | Requirements change often | Requirements are stable |

---

## 8. MVP — Minimum Viable Product 最小可行产品

### Definition 定义

An MVP is a **functional system** that includes **only the essential features** necessary to prove market feasibility and solve a core problem for early customers.

> MVP 不是一个 demo 或 prototype。它是一个**真正可用**的产品，只是功能最精简。

### Purpose 目的

- **Validate assumptions** 验证假设: Does the problem really exist? Will users use this solution?
- **Learn quickly** 快速学习: Get real user feedback with minimal investment
- **Reduce risk** 降低风险: Avoid building a full product nobody wants

### MVP vs Prototype 原型

| | MVP | Prototype 原型 |
|-|-----|------------|
| **Usable** | Yes, by real customers | Usually not production-ready |
| **Purpose** | Validate market/business | Test technical feasibility or UX |
| **Release** | Released to market | Internal use |

### When to build an MVP

When you're **uncertain** whether the problem truly needs a software solution, or whether users will actually use your product. Common in **startups and new markets**.

> MVP connects to [[#9. Lean Canvas 精益画布]] — the Lean Canvas helps you plan what your MVP should be.

---

## 9. Lean Canvas 精益画布

The **Lean Canvas** is a one-page business plan template for startups. It helps you quickly think through the key aspects of your product idea. Created by **Ash Maurya**, adapted from the Business Model Canvas.

### The 9 Blocks 九个模块

| # | Block | Question | 说明 |
|---|-------|----------|------|
| 1 | **Problem** 问题 | What are the top 1-3 problems? | 你要解决的核心问题 |
| 2 | **Customer Segments** 客户群 | Who has this problem? | 目标用户是谁 |
| 3 | **Unique Value Proposition** 独特价值主张 | Why is your solution different and worth attention? | 你的产品凭什么吸引用户 |
| 4 | **Solution** 解决方案 | What are the top features? | 核心功能是什么 |
| 5 | **Channels** 渠道 | How do you reach customers? | 如何触达用户 |
| 6 | **Revenue Streams** 收入流 | How do you make money? | 怎么赚钱 |
| 7 | **Cost Structure** 成本结构 | What are the costs? | 主要成本是什么 |
| 8 | **Key Metrics** 关键指标 | What numbers tell you you're succeeding? | 衡量成功的关键数据 |
| 9 | **Unfair Advantage** 不公平优势 | What can't be easily copied? | 竞争对手难以复制的优势 |

> Lean Canvas 和 [[#8. MVP — Minimum Viable Product 最小可行产品|MVP]] 配合使用——先用 Lean Canvas 想清楚产品逻辑，再构建 MVP 去验证。

---

## 10. Git Version Control 版本控制

### 10.1 Why Version Control? 为什么需要版本控制？

When multiple developers work on the same codebase:
- How do you **track changes**? 如何追踪变更？
- How do you **merge** different people's work? 如何合并不同人的工作？
- How do you **go back** to a previous version? 如何回退到之前的版本？

**Version Control Systems (VCS)** solve these problems. **Git** is the most popular VCS today.

### 10.2 Git Basics 基础概念

| Concept | Description | 说明 |
|---------|-------------|------|
| **Repository (Repo)** | A project's folder tracked by Git | Git追踪的项目文件夹 |
| **Commit** | A snapshot of your code at a point in time | 代码在某个时间点的快照 |
| **Branch** 分支 | An independent line of development | 独立的开发线 |
| **Merge** 合并 | Combining changes from one branch into another | 将一个分支的改动合并到另一个 |
| **Clone** | Copy a remote repository to your local machine | 将远程仓库复制到本地 |
| **Pull** | Download changes from remote | 从远程下载最新改动 |
| **Push** | Upload your local changes to remote | 将本地改动上传到远程 |

### 10.3 Git Workflow 工作流程

```
Working Directory → Staging Area (Index) → Local Repository → Remote Repository
     (编辑文件)    git add →  (暂存区)   git commit → (本地仓库) git push → (远程仓库)
```

### 10.4 Key Git Commands 常用命令

```bash
git init              # Initialize a new repo 初始化新仓库
git clone <url>       # Clone a remote repo 克隆远程仓库
git status            # Check current status 查看当前状态
git add <file>        # Stage changes 暂存改动
git add .             # Stage all changes 暂存所有改动
git commit -m "msg"   # Commit with message 提交并附带信息
git push              # Push to remote 推送到远程
git pull              # Pull from remote 从远程拉取
git branch <name>     # Create a new branch 创建新分支
git checkout <branch> # Switch to a branch 切换分支
git merge <branch>    # Merge a branch into current 合并分支
git log               # View commit history 查看提交历史
```

### 10.5 Branching Strategy 分支策略

```
main (production-ready)
  │
  ├── develop (integration branch)
  │     │
  │     ├── feature/login (feature branch)
  │     ├── feature/search
  │     └── bugfix/fix-crash
  │
  └── release/v1.0 (release branch)
```

- **main/master**: always deployable 始终可部署
- **feature branches**: one per feature/story 每个功能一个分支
- **Merge back** when feature is complete 功能完成后合并回去

### 10.6 Merge Conflicts 合并冲突

When two developers modify the **same lines** in the same file, Git cannot auto-merge. You must **manually resolve** the conflict.

```
<<<<<<< HEAD
your changes
=======
their changes
>>>>>>> branch-name
```

> You choose which version to keep (or combine both), then commit the resolution.

---

## 11. Test-Driven Development (TDD) 测试驱动开发

### 11.1 What is TDD?

TDD is a development practice where you **write tests BEFORE writing the actual code**. It comes from [[#4.5 Extreme Programming (XP) 极限编程|XP (Extreme Programming)]].

### 11.2 The TDD Cycle: Red-Green-Refactor

```
    ┌─── RED ──────┐
    │ Write a       │
    │ failing test  │
    └──────┬────────┘
           ↓
    ┌─── GREEN ────┐
    │ Write minimum │
    │ code to pass  │
    └──────┬────────┘
           ↓
    ┌─── REFACTOR ─┐
    │ Clean up code │
    │ (tests still  │
    │  pass)        │
    └──────┬────────┘
           ↓
       (Repeat)
```

**Step by step**:

1. **RED** 🔴: Write a test for the next small piece of functionality. Run it — it **fails** (because the code doesn't exist yet). 先写一个测试，运行它——失败（因为代码还没写）
2. **GREEN** 🟢: Write the **minimum amount of code** to make the test pass. Don't over-engineer! 写**最少量**的代码让测试通过
3. **REFACTOR** 🔵: Improve the code structure (remove duplication, improve naming) while keeping all tests passing. 重构代码，保持测试通过

> **Exam key**: The order is critical — **test first, code second**. 顺序很重要——先测试，后编码。

### 11.3 Benefits of TDD 好处

- **Forces you to think about requirements** before coding 迫使你在编码前思考需求
- **Catches bugs early** 早期发现Bug
- **Acts as documentation** — tests show how code should behave 测试即文档
- **Gives confidence** to refactor — if tests pass, refactoring is safe 给重构提供信心
- **Better design** — code written to be testable tends to be better designed 更好的设计

### 11.4 TDD Example 示例

Task: Write a function `add(a, b)` that returns the sum.

```
Step 1 (RED):    test: assert add(2, 3) == 5     → FAILS (add doesn't exist)
Step 2 (GREEN):  def add(a, b): return a + b      → test PASSES
Step 3 (REFACTOR): code is already clean, nothing to refactor
Step 4 (RED):    test: assert add(-1, 1) == 0     → PASSES (already works!)
```

### 11.5 Types of Tests 测试类型

| Test Type | Scope | Who writes | Speed |
|-----------|-------|-----------|-------|
| **Unit Test** 单元测试 | Single function/method | Developers | Very fast |
| **Integration Test** 集成测试 | Multiple components together | Developers | Moderate |
| **System Test** 系统测试 | Entire system | QA team | Slow |
| **Acceptance Test** 验收测试 | Business requirements | Customer/PO | Varies |

> TDD primarily uses **unit tests**. [[#6. User Stories 用户故事|User stories]] use **acceptance tests** for [[#6.5 Writing Good Acceptance Tests (Confirmation)|confirmation]].

---

## 12. Requirements to Specification 从需求到规格

This section covers the **process of transforming raw requirements into precise specifications** that can be implemented.

### 12.1 The Specification Process

```
Stakeholder Needs → Requirements Elicitation → Analysis → Specification → Validation
 (模糊的需求)        (需求获取)                (分析)    (形式化规格)    (验证)
```

### 12.2 IEEE 830 Standard

The **IEEE 830** standard provides a template for a **Software Requirements Specification (SRS)** document. Key sections:

- External Interface Requirements (User, Hardware, Software, Communication)
- Functional Requirements
- Performance Requirements
- Design Constraints
- Other Requirements

> IEEE 830 was designed for [[#4.1 Waterfall Model 瀑布模型|Waterfall]] projects where requirements are specified upfront.

### 12.3 From Ambiguity to Precision 从模糊到精确

The key challenge: natural language is **ambiguous**. Specifications must be:
- **Unambiguous** 无歧义
- **Testable** 可测试
- **Traceable** 可追溯

Techniques to reduce ambiguity:
- Use **structured templates** (e.g., user story format) 使用结构化模板
- Define a **glossary** of terms 定义术语表
- Use **examples and scenarios** 使用示例和场景
- Write **acceptance criteria** 编写验收标准

### 12.4 Requirements → Design Connection

Requirements (what to build) feed into **Design** (how to build it):

```
Requirements → Architecture Design → Detailed Design → Implementation
    WHAT            HOW (high-level)    HOW (detailed)      CODE
```

> This connects to the overall [[#4. Software Process Models 软件过程模型|software process]] — whether you do this all upfront (Waterfall) or incrementally (Agile).

---

## Exam Quick Review 考试速览

### Must-Know Concepts 必知概念

| Topic | Key Points |
|-------|------------|
| **SE Definition** | Systematic, disciplined, quantifiable approach |
| **Waterfall** | Sequential, document-driven, no going back; for stable requirements |
| **Agile Manifesto** | 4 values: individuals, working software, collaboration, responding to change |
| **Scrum** | 3 roles (PO, SM, Team) + 5 events + 3 artifacts |
| **Kanban** | Visual board, WIP limits, continuous flow |
| **XP** | Pair programming, TDD, CI, refactoring |
| **Functional vs Non-Functional Req** | FR = what it does; NFR = how well (with metrics!) |
| **Requirements Validation** | Correct, precise, complete, consistent, verifiable |
| **User Stories** | As a [user], I want [feature], so that [benefit] |
| **INVEST** | Independent, Negotiable, Valuable, Estimable, Small, Testable |
| **Three Cs** | Card + Conversations + Confirmation |
| **Use Cases** | Actor, precondition, main flow, alt flow, exception, postcondition |
| **MVP** | Minimum features to validate market; NOT a prototype |
| **Lean Canvas** | 9 blocks: Problem, Segments, UVP, Solution, Channels, Revenue, Cost, Metrics, Unfair Advantage |
| **Git** | Working dir → staging → local repo → remote; branch & merge |
| **TDD** | RED (fail) → GREEN (pass) → REFACTOR; test FIRST |

### Concept Relationship Map 概念关系图

```
Software Engineering
    │
    ├── Process Models ──┬── Waterfall (heavy documentation, IEEE 830)
    │                    ├── Agile ──┬── Scrum (sprints, backlog)
    │                    │           ├── Kanban (visual board, WIP)
    │                    │           └── XP (TDD, pair programming)
    │                    │
    │                    └── Both use → Requirements Engineering
    │                                       │
    │                                       ├── Elicitation (interviews, ethnography...)
    │                                       ├── Specification (User Stories / Use Cases)
    │                                       │       │
    │                                       │       ├── User Stories (Agile) → INVEST, 3Cs
    │                                       │       └── Use Cases (Waterfall) → Actors, Flows
    │                                       │
    │                                       └── Validation (correct, precise, complete...)
    │
    ├── Engineering Practices
    │       ├── TDD (Red-Green-Refactor)
    │       ├── Git (version control, branching)
    │       └── Continuous Integration
    │
    └── Product Thinking
            ├── MVP (validate assumptions)
            └── Lean Canvas (business model)
```

---

> **Study Strategy 学习建议**:
> 1. 先理解大框架（SE → Process Models → Requirements → Implementation）
> 2. 每个概念都要能用自己的话解释 + 举例
> 3. 特别注意对比题（Waterfall vs Agile, User Stories vs Use Cases, FR vs NFR）
> 4. INVEST 和 Agile Manifesto 的内容要能默写
> 5. TDD 的 Red-Green-Refactor 循环要理解每一步

---

*Notes generated from: 01-Introduction.pdf, 02-Requirements,Git,TDD.pdf, 03-RequirementsToSpecification.pdf, Chapter 1 & Chapter 3 (Software Engineering: A Modern Approach)*
