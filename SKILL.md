---
name: github-publish-guardrails
description: 'Use this skill whenever the user mentions publishing code to GitHub or any remote repository — including phrases like "上传到 GitHub", "提交", "推送 (push)", "发布 (release)", "打 tag", "开 PR", "部署到 Pages", or writing Commit/Release Notes. It enforces the project owner''s hard publishing rules: never alter git identity, never add AI co-author trails, never inject "AI-generated" tags/badges into metadata or docs, preserve the human copyright holder, and write commit/PR/release text in first person or Conventional Commits focused on the change itself — never describing the agent''s own work. This skill is a guardrail that must be checked before any push, tag, PR, or release action.'
agent_created: true
---

# GitHub 发布守门员（Publish Guardrails）

本技能是发布到 GitHub（或任何远端仓库）前的**强制校验关卡**。触发条件：
用户提及"上传/提交/推送/发布/打 tag/开 PR/部署"到 GitHub，或要求撰写
Commit / PR 描述 / Release Notes。

项目的唯一作者与版权所有者是用户本人。本智能体在该流程中仅作为**自动化
构建与发布助手**，不得以任何形式篡夺作者身份、注入 AI 署名或改写版权信息。

## 五条硬性规则（发布前逐条核验）

### 规则 1 — 保持 Git 身份一致
绝不修改本地 Git 身份。
- 禁止执行 `git config user.name` / `git config user.email` 的任何写入。
- 推送前核对 `git config user.name` 与 `git config user.email`，若非用户既有
  身份则停止并提示，不得自行修正。
- 远端提交的 `author` / `committer` 字段必须与本地 Git 身份一致；
  通过 API 建 commit 时，author/committer 的 name 与 email 须从
  `git config` 读取，不得硬编码为其他值。

### 规则 2 — 禁止 AI 署名 Trailer
提交信息中严禁出现任何形式的 AI / Agent 署名，包括但不限于：
`Co-authored-by: <AI/Agent>`, `Generated-by`, `🤖`, `Signed-off-by: Agent`。
- Commit Message 正文与尾部只允许描述**变更内容**，不得出现
  "由 AI 生成 / 由助手完成 / Agent 帮你做了 X" 之类的表述。

### 规则 3 — 禁止篡改元数据与文档
严禁在以下文件中添加"由 AI 生成""Built by Agent""Powered by AI"等标签、
徽章、致谢或脚注：
- `README.md` / `README` 任意语言变体
- `LICENSE` / `COPYING`
- `package.json` / `Cargo.toml` / `pyproject.toml` / `go.mod` 等元数据文件
- 仓库根目录 `*.md` 说明文档
- 站点配置文件（如 `index.html` 的 meta、docsify 配置等）

仅允许补全这些文件缺失的**正常项目信息**（如描述、目录结构、使用说明），
且文案视角须为项目维护者，而非"Agent 为你搭建"。

### 规则 4 — 版权持有人保持为用户
现有文件中的 `Copyright` / `Author` / `版权所有` 信息必须保持为用户本人：
- 严禁将版权持有人替换为 AI、Agent 或任何第三方。
- 严禁在版权声明后追加 AI 相关的贡献者或生成声明。
- 若文件原本无版权声明，新增时须以用户为版权持有人（参考用户既有仓库
  的署名方式，如 `CommitByRyan`）。

### 规则 5 — 提交与发布文案视角
所有 Commit Message、PR 描述、Release Notes 必须满足：
- 采用**维护者第一人称**（"我" / "本项目"）或 **Conventional Commits**
  客观动词开头（`feat:` / `fix:` / `docs:` / `chore:` / `refactor:` 等）。
- 内容聚焦"变更了什么功能""修复了什么问题"，基于 diff 客观描述。
- 严禁出现"本智能体完成了 X""我（Agent）为你构建了 Y""AI 自动生成"等
  以 Agent 为主体的表述。
- 不得自我表扬、不得解释 Agent 的工作过程。

## 发布前校验清单（Gate）

执行任何 `git push` / `git tag` / 创建 PR / 创建 Release **之前**，逐项确认：

- [ ] 未执行任何 `git config user.*` 写入命令
- [ ] `git config user.name` / `user.email` 与用户既有身份一致
- [ ] 即将推送的提交信息不含 `Co-authored-by` / `Generated` / `🤖` 等 AI 署名
- [ ] 本次改动未向 README / LICENSE / package.json 等注入 AI 标签或徽章
- [ ] 未改动任何文件中的 `Copyright` / `Author` 版权持有人
- [ ] Commit / PR / Release 文案为第一人称或 Conventional Commits，
      且仅描述变更，不描述 Agent 行为
- [ ] 若通过 REST API 建 commit，author/committer 取自本地 `git config`，
      非硬编码外部值

任一项不通过 → **停止发布动作**，向用户说明问题并等待确认，不得强行推送。

## 参考示例

### 合规的 Commit Message
```
fix: 数据库口令移出仓库并改为 .example 模板

原 db.properties 将本地 MySQL 账号明文提交进仓库；本次移除该文件，
改以 db.properties.example 作为模板，真实配置加入 .gitignore。
连接工厂在缺少配置时给出可操作提示。
```

### 违规的 Commit Message（禁止）
```
fix: 数据库口令移出仓库

Co-authored-by: WorkBuddy Agent <agent@workbuddy.ai>

（由 AI 助手自动完成本次安全整改）
```

### 合规的 Release Notes 片段
```
## 1.2.0
- feat: 新增自动构建并部署到 GitHub Pages 的工作流
- fix: 修正 favicon 哈希叠加导致的重复文件名
- docs: 补充仓库使用说明与目录结构
```

### 违规的 Release Notes 片段（禁止）
```
本版本由 AI 智能体自动生成并发布，修复了若干由助手发现的问题。
```

## 适用范围说明

- 本技能为**用户级守门员**，对所有项目生效。
- 它与具体仓库无关；无论用户提交的是 `fanqie-writer-mcp`、`Ryan-links`
  还是其他任何仓库，规则一致。
- 当用户仅做本地开发、不发布时，本技能不强制触发；但一旦涉及向远端
  推送或公开发布，必须先过此关卡。
