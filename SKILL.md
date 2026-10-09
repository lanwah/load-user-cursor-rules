---
name: load-user-cursor-rules
description: >-
  为指定项目启用中心化用户级 Cursor 规则：仅在项目写入极薄 alwaysApply 加载器，
  令 Agent 读取并遵守 %USERPROFILE%\.cursor\rules\*.mdc；用户规则正文只保留在该目录一份，
  禁止复制/分发到各项目。在用户点名 load-user-cursor-rules、说「为本项目启用用户规则」、
  「加载用户级 rules」「项目里挂上用户规则」「统一维护用户规则不要拷进仓库」时使用。
---

# 为项目启用中心化用户规则

**唯一正文**：`%USERPROFILE%\.cursor\rules\*.mdc`（Windows 即 `C:\Users\<user>\.cursor\rules`）。  
**本 Skill 只做一件事**：给需要的项目装上「去读上述目录并遵守」的门禁，便于你只维护用户目录那一份。

```text
%USERPROFILE%\.cursor\rules\*.mdc     ← 用户规则正文（有且仅有这里）
         ↑ 开聊后 Read 并遵守
<repo>/.cursor/rules/load-user-cursor-rules.mdc
         ← 本 Skill 写入的极薄加载器（不是用户规则副本）
```

## 如何使用（给人 / Agent）

### 1. 确保技能已被 Cursor 加载

- `%USERPROFILE%\.agents\skills\load-user-cursor-rules\`
- 或 `%USERPROFILE%\.cursor\skills\load-user-cursor-rules\`

### 2. 对话里怎么触发

```text
按 load-user-cursor-rules，为本项目启用用户规则
给当前仓库挂上用户级 Cursor rules
启用中心化用户规则（不要把规则拷进项目）
```

指定其他路径时带上仓库根，例如：`按 load-user-cursor-rules，为 D:\Projects\Foo 启用`。

### 3. 不必使用本技能的情况

- 项目已有同名/等价加载器且内容一致
- 用户只要改 `%USERPROFILE%\.cursor\rules` 里的规则正文（直接编辑该目录，与本 Skill 无关）

---

## Agent 强制工作流（启用项目）

用户要求为本项目启用时，按序执行：

1. **确认目标仓库根**（当前工作区或用户给出的路径）。
2. **禁止**把 `%USERPROFILE%\.cursor\rules\` 下任何 `.mdc` 复制、粘贴或同步进项目。
3. 确保 `<repo>/.cursor/rules/` 存在。
4. 将本 Skill 的 [templates/load-user-cursor-rules.mdc](templates/load-user-cursor-rules.mdc) 写入：

   `<repo>/.cursor/rules/load-user-cursor-rules.mdc`

5. 若目标已存在：先说明差异，问是否覆盖；勿静默覆盖。
6. 简短确认：已写入加载器；用户规则仍只在 `%USERPROFILE%\.cursor\rules`；之后该仓开聊会要求 Agent 读取并遵守那一份。

推荐用脚本（在目标仓库根，或传 `-ProjectRoot`）：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File "$HOME\.agents\skills\load-user-cursor-rules\scripts\Enable-ForProject.ps1" `
  -ProjectRoot .
```

覆盖已有加载器时加 `-Force`。

## 加载器生效后 Agent 必须做什么

项目里的加载器（`alwaysApply: true`）要求：改业务代码前先 Read 用户目录下**全部** `.mdc` 全文，并按 frontmatter 遵守（逻辑见模板，勿在项目里另写一份用户规则正文）。

## 维护边界（强制）

| 改什么 | 改哪里 |
|--------|--------|
| 编码约定、流程、偏好等**规则正文** | 只改 `%USERPROFILE%\.cursor\rules\*.mdc` |
| 某仓库要不要启用中心规则 | 用本 Skill 增删项目内加载器 |
| 加载器门禁文案本身 | 改本 Skill 的 `templates/`，再按需 `-Force` 同步到已启用项目 |

### Do not

- 把用户级 `.mdc` 正文分发/复制进各个 `<repo>/.cursor/rules/`
- 在项目加载器里内联大段用户规则（应继续指向用户目录）
- 在 `%USERPROFILE%\.cursor\rules` 再放一份本加载器（加载器只属于项目侧）

## 取消启用

用户要求某项目不再挂中心规则时：删除该项目的 `.cursor/rules/load-user-cursor-rules.mdc`（勿动用户目录下的规则正文）。
