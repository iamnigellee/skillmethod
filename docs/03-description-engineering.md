# 触发描述工程

## 核心裁决

Description 必须完成两件事（比例灵活）：
1. **包含足够的触发词汇**赢得路由竞争
2. **语义引导 body 的解释**（描述会 prime 模型对 body 指令的理解）

> 不要固定 15/55/30 的比例。测试标准是：读完 description 后，body 的前 50 行是否感觉"在意料之中"而非"出人意料"。

## 描述的三个组件

### 1. TRIGGER 列表（主体，占大部分空间）
列举具体的触发场景。根据领域宽窄调整数量。

### 2. SKIP 列表（必须有）
明确排除相邻但不属于本技能的领域。

### 3. 角色声明（可选）
只在它本身包含触发关键词时才写。

## 领域宽度决定攻击性

| 领域类型 | 触发动词数 | SKIP 条目 | 示例 |
|----------|-----------|----------|------|
| 窄且明确（PDF、OCR） | 8-10 | 2-3 | pdf skill |
| 中等重叠（API开发） | 5-7 | 4-5 | claude-api skill |
| 宽且模糊（代码审查） | 4-5 + 限定语境 | 5-7 | 需要大量 SKIP |

**核心法则**：触发描述通过精确排除来赢得"攻击性"的权利。

## 对比案例

### GOOD — 窄领域，适当攻击性

```yaml
---
name: pdf-processing
description: >
  Handles all PDF file operations including reading, extracting
  text and tables, merging multiple PDFs, splitting documents,
  rotating pages, adding watermarks, creating new PDFs, filling
  forms, encrypting, decrypting, and OCR on scanned documents.
  TRIGGER when user mentions PDF files, scanned documents,
  or form filling.
  SKIP when user asks about document writing/content generation
  (use writing skill) or image editing unrelated to scans.
---
```

**为什么好**：10 个触发动词安全，因为 "PDF" 是明确领域。SKIP 列表命名了边界。

### BAD — 宽领域，危险的攻击性

```yaml
---
name: code-helper
description: >
  Handles all code tasks: reviewing, writing, debugging,
  refactoring, testing, documenting, deploying, optimizing,
  architecting, migrating, profiling, and benchmarking code.
  TRIGGER when user mentions any programming language.
---
```

**为什么差**：触发面太广，会劫持不相关对话。没有 SKIP。会和任何开发相关技能冲突。

### BAD — 只有否定

```yaml
---
name: data-analyzer
description: Don't use this for non-CSV files.
---
```

**为什么差**：纯否定描述失败，因为模型需要正向信号来激活。

## 规范约束提醒

- 最多 1024 字符
- 必须第三人称
- `name`：最多 64 字符，小写+数字+连字符，不能含 "anthropic" 或 "claude"
- 每个触发短语 ≤ 6 个词
- Description 是唯一的触发机制——永远不要把 "何时使用" 放在 body 中
