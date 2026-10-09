---
name: load-user-cursor-rules
description: >-
  为当前项目/会话手动启用 %USERPROFILE%\.cursor\rules 下的用户级 Cursor 规则：
  开聊后读取全部 .mdc 并遵守，未读完前禁止改业务源码。
  在用户点名 load-user-cursor-rules、说「加载用户规则」「启用用户规则」、
  「不要每个项目单独装 load-user-cursor-rules 规则」、或要求把用户级规则
  生效到本仓库时使用。可只对本会话生效，也可按需写入极薄项目 Rule。
---

# 加载用户级 Cursor 规则

个人技能一次安装，任意项目可手动启用；**不必**把完整 `load-user-cursor-rules.mdc` 复制进每个仓库。

用户级规则目录：`%USERPROFILE%\.cursor\rules`（Windows 即 `C:\Users\<user>\.cursor\rules`）。

## 如何使用（给人 / Agent）

### 1. 确保技能已被 Cursor 加载

目录应在：

- `%USERPROFILE%\.agents\skills\load-user-cursor-rules\`
- 或 `%USERPROFILE%\.cursor\skills\load-user-cursor-rules\`

### 2. 对话里怎么触发

**推荐显式点名**（最稳，零项目改动）：

```text
按 load-user-cursor-rules
加载用户规则
为本会话启用用户级 Cursor 规则
```

**为本仓库长期贴身提醒**（可选，写入极薄 Rule）：

```text
按 load-user-cursor-rules，为本项目启用（写入 .cursor/rules）
```

### 3. 不必使用本技能的情况

- 项目里已有等价的 alwaysApply 规则，且内容与本 Skill 一致
- 用户明确只要项目 `.cursor/rules`，不要读用户级目录

## 模式选择

| 用户意图 | 模式 | 是否改仓库 |
|----------|------|------------|
| 「加载/启用用户规则」且未提写入项目 | **A. 本会话加载** | 否 |
| 「为本项目启用 / 写入规则 / 长期生效」 | **B. 写入极薄项目 Rule** | 是：`.cursor/rules/` |
| 两者都提 | 先 A，再按需做 B | 视 B |

未说清时默认 **A**，不要擅自改仓库。

---

## 模式 A：本会话加载（推荐）

在本会话中，对任何**业务代码**的写入/修改之前，必须先完成：

1. 用 Read（或等价文件读取）读取 `%USERPROFILE%\.cursor\rules` 下**全部** `.mdc` 文件全文。
2. 按各文件 frontmatter 遵守：
   - `alwaysApply: true` → 本会话全程遵守
   - 仅有 `globs` → 当编辑/涉及匹配路径时遵守
   - 二者皆无 → 仍视为应知晓的用户级约定；有冲突时先问用户
3. 未读完全部 `.mdc` 前：**禁止**新建或修改业务源码；仅允许列目录、读规则、澄清问题。

### Do

- 每个新对话开聊后尽快读取；同一会话内已读且文件未变可不再重读。
- 用户级规则与项目 `.cursor/rules` 冲突时，先指出冲突并询问，再动手。
- 读完后用一两句确认已加载的规则文件名（勿大段复述正文）。

### Do not

- 假设用户级 `.mdc` 已由系统注入而跳过 Read。
- 只读文件名或 frontmatter 而不读正文。
- 把用户级规则全文粘贴进项目仓库。

若目录不存在或没有任何 `.mdc`：说明情况，询问是否仍继续，或是否要先创建用户级规则。

---

## 模式 B：为本项目写入极薄 Rule（可选）

仅当用户明确要求「为本项目启用 / 写入 / 长期」时执行。

1. 确认目标仓库根（当前工作区，或用户指定路径）。
2. 确保存在 `<repo>/.cursor/rules/`。
3. 将本 Skill 的 [templates/load-user-cursor-rules.mdc](templates/load-user-cursor-rules.mdc) 复制为：

   `<repo>/.cursor/rules/load-user-cursor-rules.mdc`

4. 若目标已存在同名文件：先 Diff/说明差异，问是否覆盖；勿静默覆盖。
5. 完成后说明：之后该仓库新对话会由 alwaysApply 触发同等门禁；技能目录仍是说明与模板的唯一维护处。

也可用脚本（在仓库根执行）：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File "$HOME\.agents\skills\load-user-cursor-rules\scripts\Enable-ForProject.ps1" `
  -ProjectRoot .
```

---

## 与「每项目安装完整规则」的边界

| 方式 | 维护成本 | 何时用 |
|------|----------|--------|
| 本 Skill + 模式 A | 最低：只维护用户级 `.mdc` | 默认、临时、多仓库切换 |
| 本 Skill + 模式 B 极薄 Rule | 每仓一个薄文件，逻辑不复制 | 希望该仓开聊即门禁 |
| 每仓复制完整 `.mdc` | 高，易漂移 | **避免**；用本 Skill 替代 |

参考来源（历史项目内完整规则）：`EverythingLabeled/.cursor/rules/load-user-cursor-rules.mdc`。行为以本 Skill 与 `templates/` 为准。
