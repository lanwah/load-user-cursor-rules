# load-user-cursor-rules

为指定项目启用**中心化用户级** Cursor 规则：只在项目里写入极薄的 `alwaysApply` 加载器，让 Agent 去读并遵守 `%USERPROFILE%\.cursor\rules\*.mdc`。

用户规则正文只保留在用户目录一份，**不会**被复制进各个仓库。

```text
%USERPROFILE%\.cursor\rules\*.mdc     ← 用户规则正文（有且仅有这里）
         ↑ 开聊后 Read 并遵守
<repo>/.cursor/rules/load-user-cursor-rules.mdc
         ← 本 Skill 写入的极薄加载器（不是用户规则副本）
```

## 安装技能

把本仓库放到 Cursor 可加载的 skills 目录之一：

- `%USERPROFILE%\.agents\skills\load-user-cursor-rules\`
- 或 `%USERPROFILE%\.cursor\skills\load-user-cursor-rules\`

例如：

```powershell
git clone https://github.com/lanwah/load-user-cursor-rules.git `
  "$HOME\.agents\skills\load-user-cursor-rules"
```

## 使用方式

### 方式一：对话触发（推荐）

在目标项目的 Cursor 对话里说类似：

```text
按 load-user-cursor-rules，为本项目启用用户规则
给当前仓库挂上用户级 Cursor rules
启用中心化用户规则（不要把规则拷进项目）
```

指定其他路径时带上仓库根，例如：`按 load-user-cursor-rules，为 D:\Projects\Foo 启用`。

Agent 会按 `SKILL.md` 把加载器写到：

`<repo>/.cursor/rules/load-user-cursor-rules.mdc`

### 方式二：脚本安装

在目标仓库根执行（或传 `-ProjectRoot`）：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass `
  -File "$HOME\.agents\skills\load-user-cursor-rules\scripts\Enable-ForProject.ps1" `
  -ProjectRoot .
```

覆盖已有加载器时加 `-Force`。

## 启用后会发生什么

项目里的加载器（`alwaysApply: true`）要求：改业务代码前，Agent 先读取 `%USERPROFILE%\.cursor\rules` 下**全部** `.mdc` 全文，并按 frontmatter 遵守。

之后你只需维护用户目录那一份规则；已启用的项目会继续指向它。

## 维护边界

| 改什么 | 改哪里 |
|--------|--------|
| 编码约定、流程、偏好等**规则正文** | 只改 `%USERPROFILE%\.cursor\rules\*.mdc` |
| 某仓库要不要启用中心规则 | 用本 Skill 增删项目内加载器 |
| 加载器门禁文案本身 | 改本仓库 `templates/`，再按需 `-Force` 同步到已启用项目 |

## 取消启用

删除该项目的 `.cursor/rules/load-user-cursor-rules.mdc` 即可。不要动 `%USERPROFILE%\.cursor\rules` 下的规则正文。

## 不必使用本技能的情况

- 项目已有同名/等价加载器且内容一致
- 只想改用户规则正文：直接编辑 `%USERPROFILE%\.cursor\rules`，与本 Skill 无关

## 仓库结构

```text
SKILL.md                              # Agent 工作流说明
templates/load-user-cursor-rules.mdc  # 写入项目的加载器模板
scripts/Enable-ForProject.ps1         # 一键安装脚本
```
