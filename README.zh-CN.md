# build-to-learn

[English](./README.md) | 简体中文

一个把「我想学某个包、语言特性、核心概念，或复刻一个参考项目」转成**由学习者亲手编码、逐步深入、每一步都能验证**的项目路线的 Agent Skill。

## 安装

使用 [vercel-labs/skills](https://github.com/vercel-labs/skills) 安装：

```bash
npx skills add Dailiduzhou/build-to-learn
```

## 核心机制

- **单项目渐进演进**：每一关都在同一个可运行代码库里叠加一个可观察的行为和一个主要概念；进度统一记录在 `.learn/PROGRESS.md`，每关任务卡按需生成在 `.learn/stages/` 下。
- **4 类可组合拆解模式**：支持学习**包/库**、**语言特性**、**核心概念**以及**参考源码复刻/跨语言 Port**（可主次叠加，并附带源语言 vs 目标语言对照视角）。
- **渐进撤去脚手架与防剧透**：Agent 负责准备工程配置、mock/夹具、失败态验收测试和早期 `todo!()` 桩，绝不代写你的核心实现；卡壳时按 4 级提示阶梯推进（`L1 现象 → L2 定位线索 → L3 机制解析 → L4 局部伪代码`）。
- **双段验收闭环**：检查实现时同时跑验收测试与审查 `git diff`（防止绕过目标概念），功能通过后解锁短小的迁移验证（功能验收与迁移验证分开记录，支持显式跳过并在后续关自然复测）。

## 使用示例

- **新建学习路线**：「我熟悉 TypeScript 和基础 Rust 所有权，想通过一步步用 Rust 复刻 `~/code/pi/packages/ai` 来掌握 Rust 的 `Stream`/`Pin`/`Poll`，不要替我写核心代码。」
- **检查实现**：「我写完第 1 关了，`cargo test` 也全绿了，帮我检查一下这一关的实现。」
- **卡壳求助**：「第 2 关编译一直报 `Unpin` 错误，先别直接改我的代码，给点提示。」
- **续学**：「帮我从 `.learn/PROGRESS.md` 继续当前关。」

## 仓库结构

```text
.
├── SKILL.md                              # 核心工作流、入口与约束规则
└── references/
    ├── templates.md                      # .learn/PROGRESS.md 与关卡任务卡模板
    ├── verification-and-hints.md         # 渐进脚手架、防绕过审查、4 级提示与迁移验证协议
    └── playbooks/
        ├── library.md                    # 包 / SDK / 框架拆解模式
        ├── language-feature.md           # 语言特性、类型/所有权与运行时语义拆解模式
        ├── core-concept.md               # 协议、算法与系统机制拆解模式
        └── reference-port.md             # 垂直切片复刻与跨语言对照模式
```

## 开源协议

见 [LICENSE](./LICENSE)。
