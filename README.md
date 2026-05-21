# Tavern Cards — SillyTavern 角色卡与世界书编写工具

一套面向 Coding Agent 的 SillyTavern 角色卡（Character Card）和世界书（Worldlore）创作 skill，配合离线打包/解包 CLI 工具，覆盖从零创作到成品输出的流程（不涉及前端状态栏或 MVU Zod 以外的酒馆助手脚本）。

## 致谢

角色相关创作的流程和思路参照 [sanmingyue](https://github.com/sanmingyue) 的写卡预设。 
变量更新正则来源于 [StageDog](https://github.com/StageDog)。

## 功能概览

- 流程创作引导: 从需求对齐、世界观构建、角色设定、条目编写到开场白创作，Agent 在每一步提供结构化的写作指导和质量检查
- [tavern-cards-forge](https://github.com/ai4rpg/tavern-cards-forge) CLI: 离线打包（SillyTavern PNG/JSON）、解包还原为项目目录、自动推导运行时配置、JSON Patch 等操作
- MVU 变量系统: 为需要动态变量的角色卡提供 schema 定义、初始变量、更新规则的编写流程和 Zod 校验
- EJS 动态方案: EJS 模板预处理条目，实现条件渲染、变量注入等高级功能

## 适用场景

| 场景 | 说明 |
|------|------|
| 从零创建完整项目 | 需求对齐 → 项目初始化 → 条目创作 → 配置推导 → 打包输出 |
| 从现有材料转化 | 提供角色设定/世界观文档 → 自动提取并转化为 SillyTavern 条目格式 |
| 修改已有角色卡 | 解包 → 断点续接 → 定位条目修改 → 重新打包 |
| 局部任务 | 只编写某个条目、调整某段 MVU 变量、修改开场白等 |
| 评估角色卡质量 | 分析结构完整性、配置合理性、写作质量，生成评估报告 |

## 前置条件

- 任一支持 skill 的 Coding Agent
- Node.js（用于 CLI 工具）

## 安装

1. 克隆仓库到本地
2. 根据你使用的 Agent，将 `tavern-cards/` 目录放入对应位置：
   - Claude Code: 放入 `.claude/skills/` 目录
   - Opencode: 放入 `.opencode/skills/` 或 `.agents/skills/` 目录
   - 其他 Agent: 放入 Agent 指定的 skill 目录
3. 当你提到"角色卡"、"世界书"、"SillyTavern"等关键词时，skill 会自动触发

## 许可

本项目仅供个人使用，二次修改需注明出处。