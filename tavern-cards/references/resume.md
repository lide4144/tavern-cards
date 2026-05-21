# 断点续接

当用户中断后返回时，检测进度并恢复。

## 步骤

1. **检测环境**：查找 `.cardrc.json`，确认工作区。未找到则提醒用户
2. **读取编写规划文档**（`创作规划.yaml`）
3. **按顺序检查四部分进度**：条目 → MVU → EJS → 开场白
4. **向用户展示进度概要**，确认从哪个部分/条目继续
5. **继续创作流程**

进度判断需同时参考 `tavern-cards-state.json` 的 entryManifest 和 `创作规划.yaml`。EJS 相关信息仅存于创作规划文档。

## 使用 forge CLI

查询项目状态：

```bash
node scripts/tavern-cards-forge.mjs query {project} '$.mvu' '$.form' '$.characters'
node scripts/tavern-cards-forge.mjs query {project} '$.entryManifest.*~'           # 已有条目的类型列表
node scripts/tavern-cards-forge.mjs query {project} '$.entryManifest[*][*]'        # 所有条目的完整信息
```

`query` 返回 JSON 到 stdout，用 `--format yaml` 可获取 YAML 输出。

## 判断条目完成状态

对 `创作规划.yaml` 的 `entries` 数组中每个条目，按数组顺序判断：

- entryManifest 中存在该条目 → 已完成，跳过
- entryManifest 中不存在该条目 → 未完成，从此条目继续

```bash
# 检查某个条目是否已注册
node scripts/tavern-cards-forge.mjs query {project} '$.entryManifest.{类型}.{条目名}'
# 返回 null 表示未注册
```

文件存在但不被 entryManifest 引用的情况：
- 该条目在 `创作规划.yaml` 的 entries 中 → 编写未完成（内容已写但未注册），继续编写并注册
- 该条目不在 `创作规划.yaml` 的 entries 中 → 询问用户是否保留

## 判断 MVU 完成状态（project.mvu: true 时）

先确认 mvu 开关：

```bash
node scripts/tavern-cards-forge.mjs query {project} '$.mvu'
```

| 检查项 | 命令 | 完成条件 |
|---|---|---|
| schema.ts | 检查文件 | `schema.ts` 文件存在 |
| initvar.yaml | `query {project} '$.entryManifest.MVU.*~'` | 有 part=initvar 条目且对应文件存在 |
| 变量更新规则 | 同上 | 有 part=update_rules 条目且对应文件存在 |

三项都存在 → MVU 完成。否则从第一个缺失项继续。

## 判断 EJS 完成状态

从 `创作规划.yaml` 的 `ejs.entries` 读取 EJS 条目列表：

| 检查项 | 命令 | 完成条件 |
|---|---|---|
| EJS预处理 注册 | `query {project} '$.entryManifest.EJS预处理'` | EJS预处理 类型条目存在 |
| 各 EJS 条目已处理 | — | 创作规划文档 ejs.entries 中每条，entryManifest 对应条目的 `contents` 首片段以 `@@if ` 开头，或对应内容文件正确使用 EJS 语句 |

两项都满足 → EJS 完成。否则从第一个缺失项继续。

## 判断开场白完成状态（project.form: charactercard 时）

```bash
node scripts/tavern-cards-forge.mjs query {project} '$.form'
```

| 检查项 | 方法 |
|---|---|
| 默认开场白 | `开场白/0.txt` 文件存在 |

## 无编写规划文档时

如果没有编写规划文档（旧项目或手动创建的项目），通过以下命令推断项目状态：

```bash
node scripts/tavern-cards-forge.mjs query {project} '$.form' '$.mvu' '$.characters'
node scripts/tavern-cards-forge.mjs query {project} '$.entryManifest.*~' --format yaml
```

向用户展示已有条目列表和项目属性，确认后继续。