# AGENTS.md 怎么写？2026 Codex 项目配置与最佳实践

如果你已经开始使用 Codex、Codex CLI 或其他 AI Coding Agent 处理真实项目，那么有一个文件非常值得了解：

```text
AGENTS.md
```

它可以简单理解成：

> **写给 AI Coding Agent 看的项目说明书。**

你可以在里面告诉 Codex：

* 项目使用什么技术栈
* 哪些目录最重要
* 应该运行什么测试
* 使用什么包管理器
* 哪些文件不能修改
* 编码规范是什么
* 修改完成后需要做什么检查
* Code Review 需要重点关注什么

OpenAI 当前的 Codex 文档说明，Codex 会在开始任务前读取 `AGENTS.md`，从而获得持续性的项目规则和上下文。

---

## 一、AGENTS.md 是什么？

很多项目已经有：

```text
README.md
```

README 更多是给开发者看的，例如：

```text
这个项目是做什么的？
怎么安装？
怎么启动？
怎么部署？
```

而 `AGENTS.md` 更偏向：

```text
AI Agent 在这个项目中应该怎么工作？
```

例如：

```markdown
# AGENTS.md

## Project Rules

- 使用 TypeScript
- 使用 pnpm，不要使用 npm
- 修改代码后运行 pnpm test
- 不要修改数据库 Schema
- 不要直接编辑 dist 目录
- 新增 API 时必须补充测试
```

这样以后 Codex进入项目时，就可以先获得这些项目约束。

---

## 二、为什么真实项目更需要 AGENTS.md？

假设你每次使用 Codex 都要重复输入：

```text
这个项目使用 pnpm。

不要升级 React。

不要修改数据库结构。

不要改 dist。

修改完成后运行测试。

新增 API 必须补充测试。
```

短期还可以。

但如果每天都要重复，就很麻烦。

更好的方式是把这些长期规则写进：

```text
AGENTS.md
```

以后处理不同任务时，只需要告诉 Codex：

```text
修复用户登录 Bug。
```

项目的基础规则已经在仓库中。

这也是 `AGENTS.md` 最大的价值之一：

> **把临时 Prompt 变成项目长期规则。**

---

## 三、AGENTS.md 放在哪里？

最常见的方式是在项目根目录创建：

```text
my-project/
├── AGENTS.md
├── README.md
├── package.json
├── src/
├── tests/
└── ...
```

例如：

```text
my-saas-project/
├── AGENTS.md
├── src/
│   ├── api/
│   ├── components/
│   └── utils/
├── tests/
└── package.json
```

仓库根目录的 `AGENTS.md` 可以用于描述整个项目都应该遵守的规则。

OpenAI 当前文档还支持在不同目录放置更具体的 `AGENTS.md` 或 `AGENTS.override.md`。Codex 会从项目根目录向当前工作目录逐层读取规则，位置更接近当前目录的规则会覆盖更上层的指导。

---

## 四、一个最简单的 AGENTS.md

如果项目不复杂，不需要写几百行。

可以从下面这种版本开始：

````markdown
# AGENTS.md

## Project Overview

这是一个基于 React + TypeScript 的 Web 项目。

## Development Rules

- 使用 TypeScript
- 使用 pnpm 管理依赖
- 不要修改 dist 目录
- 不要提交 node_modules
- 尽量保持现有代码风格

## Testing

修改代码后运行：

```bash
pnpm test
````

提交前运行：

```bash
pnpm lint
```

## Change Policy

* 不要修改与当前任务无关的文件
* 不要随意升级依赖
* 不要修改数据库结构，除非任务明确要求
* 大范围修改前先说明计划

````

这已经能覆盖很多小型项目。

---

## 五、AGENTS.md 最值得写的 7 类内容

### 1. 项目技术栈

例如：

```markdown
## Tech Stack

- Next.js
- TypeScript
- PostgreSQL
- Prisma
- Tailwind CSS
- Vitest
````

这样 Codex 可以更快理解项目环境。

---

### 2. 包管理器

例如：

```markdown
## Package Manager

使用 pnpm。

不要使用 npm 或 yarn 安装依赖。
```

这是非常实用的一条。

否则 AI 有时可能根据习惯生成：

```bash
npm install
```

但项目实际使用的是：

```bash
pnpm
```

---

### 3. 测试命令

例如：

````markdown
## Testing

修改 TypeScript 文件后运行：

```bash
pnpm test
````

修改前端组件后运行：

```bash
pnpm test
pnpm lint
```

````

OpenAI 也明确把构建、测试命令列为适合放进 `AGENTS.md` 的内容。

---

## 六、告诉 Codex 哪些东西不能改

这是我非常推荐写进去的。

例如：

```markdown
## Do Not Modify

除非任务明确要求，否则不要修改：

- database/schema.sql
- prisma/schema.prisma
- package-lock.json
- production configuration
- .env
- generated files
````

还可以进一步写：

```markdown
不要：

- 随意删除现有 API
- 改变公开接口返回格式
- 修改生产数据库配置
- 添加不必要的新依赖
```

对于真实商业项目，这类规则通常比“代码应该优雅”更加重要。

---

## 七、限制 Codex 的修改范围

建议加入：

```markdown
## Change Scope

每次任务尽量保持最小修改范围。

不要为了修复一个 Bug：

- 重构无关模块
- 重命名大量文件
- 修改无关格式
- 升级大量依赖
```

这样做的目的不是限制 Codex 的能力。

而是：

> **让每次修改更加容易检查。**

例如一个 Bug 本来只需要改：

```text
2 个文件
```

就没有必要顺手重构：

```text
20 个文件
```

---

## 八、大改之前要求 Codex 先给方案

可以在 `AGENTS.md` 中加入：

```markdown
## Planning

对于以下情况，修改代码前必须先说明计划：

- 修改超过 5 个文件
- 数据库变更
- 架构调整
- API 行为变化
- 新增核心依赖
- 大规模重构
```

然后让 Codex：

```text
先告诉我准备修改哪些文件以及原因。

等待确认后再执行。
```

这对大型项目尤其有用。

---

## 九、给 Code Review 写规则

`AGENTS.md` 不只是控制“怎么写代码”。

还可以控制：

> **怎么检查代码。**

OpenAI 当前 Codex 文档支持在 `AGENTS.md` 中加入 Code Review Rules，用于告诉 Codex Review 时重点检查什么。

例如：

```markdown
## Code Review Rules

Review 时重点检查：

1. 是否存在逻辑 Bug
2. 是否存在空值异常
3. 是否影响原有 API 行为
4. 是否存在安全风险
5. 是否遗漏错误处理
6. 是否缺少测试
7. 是否存在不必要的复杂代码

不要只报告代码格式问题。
```

这样以后进行 Code Review 时，Codex会更关注你真正关心的问题。

---

## 十、前端项目 AGENTS.md 示例

如果是 React / Next.js 项目，可以这样写：

````markdown
# AGENTS.md

## Project

这是一个 Next.js + TypeScript Web 项目。

## Package Manager

使用 pnpm。

不要使用 npm 或 yarn。

## Frontend Rules

- 使用 TypeScript
- 优先复用现有组件
- 不要重复创建已有组件
- 保持现有目录结构
- 不要随意修改全局 CSS
- UI 修改需要兼顾移动端

## API

- 不要改变现有 API 返回结构
- 新接口必须处理错误状态
- 不要把敏感信息返回到客户端

## Testing

修改后运行：

```bash
pnpm lint
pnpm test
````

## Change Scope

* 尽量只修改当前任务相关文件
* 不要顺便重构无关模块
* 大范围修改前先给出修改计划

````

---

## 十一、Node.js 后端项目示例

```markdown
# AGENTS.md

## Project

Node.js + TypeScript API 服务。

## Development

- 使用 TypeScript
- 使用 pnpm
- 保持现有 service / controller / repository 分层

## Database

未经明确要求：

- 不修改数据库 Schema
- 不执行 migration
- 不删除字段
- 不修改生产数据库配置

## API Rules

新增 API 时：

1. 参数必须校验
2. 正确处理异常
3. 使用现有错误处理机制
4. 补充测试

## Testing

运行：

```bash
pnpm test
````

提交前运行：

```bash
pnpm lint
```

## Safety

不要：

* 删除未知数据
* 写入真实生产环境
* 修改密钥
* 输出敏感环境变量

````

---

## 十二、Python 项目示例

```markdown
# AGENTS.md

## Project

这是一个 Python 后端项目。

## Environment

使用：

- Python 3.x
- FastAPI
- PostgreSQL
- pytest

## Coding Rules

- 遵循现有代码风格
- 新函数增加类型提示
- 不要随意引入新的第三方依赖
- 优先复用现有 utility

## Tests

修改业务逻辑以后运行：

```bash
pytest
````

## Database

没有明确要求时不要：

* 修改数据库表结构
* 自动执行 migration
* 删除字段

## Changes

修改完成后告诉我：

1. 修改了哪些文件
2. 每个文件修改原因
3. 运行了哪些测试
4. 是否存在未解决风险

````

---

## 十三、大型项目可以使用多个 AGENTS.md

如果项目很大，一个根目录规则可能不够。

例如：

```text
project/
├── AGENTS.md
│
├── frontend/
│   ├── AGENTS.md
│   └── src/
│
├── backend/
│   ├── AGENTS.md
│   └── src/
│
└── payments/
    ├── AGENTS.override.md
    └── ...
````

可以让：

```text
根目录 AGENTS.md
```

负责整个项目的公共规则。

然后：

```text
frontend/AGENTS.md
```

负责前端规则。

例如：

```markdown
- UI 必须支持移动端
- 使用现有组件库
- 修改组件后运行前端测试
```

而：

```text
backend/AGENTS.md
```

可以写：

```markdown
- 不要改变公开 API 契约
- 修改 Service 必须补充测试
- 不允许直接访问生产数据库
```

Codex 当前支持这种分层指导方式，越接近当前工作目录的指导具有更高优先级。

---

## 十四、AGENTS.override.md 是什么？

除了：

```text
AGENTS.md
```

Codex 当前还支持：

```text
AGENTS.override.md
```

它可以用于在特定目录或环境下覆盖普通规则。Codex 在同一级目录发现 override 文件时，会优先采用该文件。

例如：

```text
services/
└── payments/
    ├── AGENTS.md
    └── AGENTS.override.md
```

支付模块可能需要特殊规则：

```markdown
# AGENTS.override.md

## Payment Service Rules

- 所有修改必须运行 payment tests
- 不允许修改支付密钥
- 不要改变支付回调格式
- 所有金额统一使用整数最小货币单位
```

这种结构更适合大型、多模块代码库。

---

## 十五、还有全局 AGENTS.md

除了项目自己的规则，开发者还可以建立个人全局规则。

Codex 当前默认会从：

```text
~/.codex/
```

读取全局指导文件。

例如：

```text
~/.codex/AGENTS.md
```

里面可以写个人习惯：

```markdown
# Personal Codex Rules

- 回答尽量简洁
- 修改代码前先说明计划
- 不要自动增加生产依赖
- 修改完成后总结 Diff
- 测试失败时不要假装成功
```

这样不同项目都可以继承你的基础工作习惯。

---

## 十六、不要把 AGENTS.md 写得太长

很多人第一次知道 `AGENTS.md` 后，会想把整个项目文档全部复制进去。

例如：

```text
项目历史
↓
产品需求
↓
数据库全部结构
↓
几十页 API 文档
↓
代码规范
↓
部署文档
↓
所有 Bug
```

最终写成上千行。

这通常没必要。

OpenAI 当前的 Codex 自定义指南明确建议：

> `AGENTS.md` 应该保持精简，用来保存真正需要长期遵守的项目指导。

比较适合放：

```text
长期有效
+
经常需要
+
不遵守会出问题
```

的规则。

---

## 十七、什么内容不建议放进去？

例如下面这些通常不建议全部堆进 `AGENTS.md`：

### 临时任务

```text
今天帮我改登录页面颜色
```

这种直接在 Prompt 里说即可。

---

### 很长的产品文档

可以放：

```text
docs/
```

然后在 `AGENTS.md` 里告诉 Codex：

```markdown
处理支付业务前，请先阅读：

docs/payments.md
```

---

### 密钥和密码

不要写：

```text
API Key
数据库密码
真实账号密码
生产密钥
```

`AGENTS.md` 通常跟代码仓库一起存在，不应该把敏感凭据作为项目指导内容。

---

## 十八、Codex 经常犯同一个错误怎么办？

这正是 `AGENTS.md` 很适合解决的问题。

例如 Codex每次都使用：

```bash
npm install
```

但项目统一要求：

```bash
pnpm
```

第一次：

你提醒它。

第二次：

又提醒。

第三次：

还在提醒。

这时候最好直接加入：

```markdown
## Package Manager

This repository uses pnpm.

Do not use npm or yarn.
```

OpenAI 当前也建议，当 Codex 反复犯同一种错误、反复收到同类 Code Review 反馈时，可以把规则固化进 `AGENTS.md`，让后续任务继承这些指导。

---

## 十九、一个推荐的通用 AGENTS.md 模板

如果你不知道怎么开始，可以直接使用下面这版：

```markdown
# AGENTS.md

## Project Overview

请先阅读 README.md 了解项目。

开始任务前先理解相关模块，不要直接修改代码。

## Development Rules

- 遵循现有代码结构
- 优先复用现有代码
- 不要修改无关文件
- 不要进行没有必要的大规模重构
- 不要随意升级依赖

## Planning

如果任务涉及：

- 超过 5 个文件
- 数据库修改
- API 行为变化
- 架构调整
- 新增重要依赖

请先给出修改计划，再开始执行。

## Testing

修改完成后：

1. 运行相关测试
2. 运行 lint
3. 检查是否影响现有功能

如果测试失败，请明确说明，不要忽略失败。

## Git

- 修改前查看 git status
- 不要覆盖用户已有未提交修改
- 修改完成后总结 Diff

## Safety

未经明确要求：

- 不修改数据库 Schema
- 不修改生产配置
- 不删除用户数据
- 不添加生产依赖
- 不输出敏感信息

## Final Response

任务完成后说明：

1. 修改了哪些文件
2. 为什么修改
3. 运行了哪些测试
4. 测试结果
5. 是否存在未解决问题
```

对于大部分中小型项目来说，这已经是一个不错的起点。

---

## 二十、AGENTS.md 最重要的原则

如果只记住一个原则，可以记住：

> **不要试图告诉 Codex 所有事情，只告诉它每次都必须遵守的事情。**

好的 `AGENTS.md` 应该是：

```text
短
↓
明确
↓
可执行
↓
长期有效
```

而不是一篇几万字的项目百科全书。

---

## 二十一、ChatGPT、Codex 与版本选择

当开始深入使用 Codex、Codex CLI 和 `AGENTS.md` 后，不少用户还会遇到：

* ChatGPT Plus 和 Pro 怎么选择？
* Codex 使用需要什么版本？
* 使用额度应该怎么理解？
* 国内充值、订阅或升级需要注意什么？

这些内容与 `AGENTS.md` 技术配置本身不同，建议单独了解。

如果你正在了解 **ChatGPT / Codex 版本选择、充值、订阅与升级方式**，可以参考：

👉 [ChatGPT & Codex 版本与订阅说明](https://cwx.aixufei.com/)

技术使用方面，建议先建立稳定的项目规则，再逐步扩大 AI Agent 能够处理的任务范围。

---

## 二十二、总结

`AGENTS.md` 本质上不是一个复杂的新技术。

它解决的是一个非常实际的问题：

> **如何让 Codex 每次进入项目，都知道这个项目应该怎么工作。**

推荐从最简单的几个规则开始：

```text
项目技术栈
+
包管理器
+
测试命令
+
禁止修改区域
+
修改范围
+
Code Review 要求
```

然后在真实使用过程中逐渐补充。

如果 Codex 经常犯某个错误，就增加一条规则。

如果某条规则已经没有意义，就删除。

长期下来，`AGENTS.md` 会逐渐变成：

> **这个项目与 AI Coding Agent 协作的工作规范。**

---

## 📚 相关教程

* [Codex 是什么？2026 开发者入门与使用场景指南](what-is-codex.md)
* [Codex CLI 怎么用？2026 从安装到实际项目操作完整教程](codex-cli-guide.md)
* [ChatGPT Plus 和 Pro 怎么选](chatgpt-plus-vs-pro.md)
* [Codex 额度不足怎么办](codex-usage-limit-guide.md)
* [返回 ChatGPT & Codex 中文指南首页](../README.md)

后续继续更新：

* Codex 修复 Bug 实战
* Codex Code Review 使用教程
* Codex 大型项目使用技巧
* ChatGPT 与 Codex 有什么区别
* AI Coding Agent 开发工作流

---

## ⭐ 关于本项目

本仓库持续整理 **ChatGPT、Codex、Codex CLI、AGENTS.md 与 AI 编程**相关中文教程。

如果内容对你有帮助，可以 Star 收藏，方便后续查看更新。
