---
name: github-publish-guardrails
description: 'Use this skill whenever the user mentions publishing code to GitHub or any remote repository — including phrases like "上传到 GitHub", "提交", "推送 (push)", "发布 (release)", "打 tag", "开 PR", "部署到 Pages", "新增仓库", "新项目上线", or writing Commit/Release Notes, or asking to update the personal blog / 项目索引 after shipping something. It enforces the project owner''s hard publishing rules: never alter git identity, never add AI co-author trails, never inject "AI-generated" tags/badges into metadata or docs, preserve the human copyright holder, write commit/PR/release text in first person or Conventional Commits focused on the change itself, and keep the personal site''s project index in sync after every new or renamed repository. This skill is a guardrail that must be checked before any push, tag, PR, or release action.'
version: 1.1.0
agent_created: true
---

# GitHub 发布守门员（Publish Guardrails）

本技能是发布到 GitHub（或任何远端仓库）前的**强制校验关卡**。触发条件：
用户提及"上传/提交/推送/发布/打 tag/开 PR/部署"到 GitHub，或要求撰写
Commit / PR 描述 / Release Notes，或**新建 / 更名 / 删除仓库、新项目上线**。

项目的唯一作者与版权所有者是用户本人。本智能体在该流程中仅作为**自动化
构建与发布助手**，不得以任何形式篡夺作者身份、注入 AI 署名或改写版权信息。

## 五条署名红线（发布前逐条核验）

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

---

## 规则 6 — 发布后同步个人站点（项目索引）

**每次新建仓库、项目实质更新、仓库更名或删除，必须在同一次发布流程内更新
个人站点的项目索引。** 项目上线而站点不知道，等于对外不存在。

以 `CommitByRyan.github.io` 的现有结构为例，机械动作是这四步：

### 6.1 项目总览页 `docs/projects.md`

- **速览表**加 / 改一行：仓库名、类别、技术栈、状态
- **对应分组正文**下加小节：一句话定位、技术栈与规模、当前状态、实现要点、
  相关文章互链
- 分组编号要连续（一 / 二 / 三 / 四）；插入新分组时，后续分组的编号与
  章节顺序一起改，不要留下两个「三」
- 有在线演示（Pages）的，条目里给出演示链接

### 6.2 首页 `docs/README.md`

- **数字条**中的「公开仓库」数量同步
- **项目版块卡片**：分组数量变化时增删卡片，并重排 `01 / Category` 编号

### 6.3 仓库根 `README.md`

- 项目表补一行
- 文中若写了仓库数量，同步

### 6.4 验证

- Pages 构建状态为 `built` 且 `error` 为空
- 目标页返回 200：`/`、`/projects.md`
- 新增的相对链接都有落点。注意 `_posts/` 下文件的相对基准是 `_posts/`，
  跨目录要写 `../`；死链检查应跳过代码块围栏，否则示例会被误判

### 数量是易腐数据

首页与 README 里写死「N 个公开仓库」这类数字，每次增删都必须同步，
否则三天后就变成谎言。三种处理，按可靠性排序：

| 做法 | 代价 |
|---|---|
| 只写分类不写数量 | 最稳，永不腐坏 |
| 写成 `9+` | 模糊但不容易错，读者也知道是约数 |
| 精确维护 | 最准确，但每次增删都要记得改 |

默认按第三条维护；一旦连续两次忘记，就降级为 `N+`。

### 同步属于同一次发布，但提交必须分开

项目仓库与站点仓库是两个仓库，各提各的 commit。
**不要把站点改动混进项目仓库的提交**——它们服务于不同目的，
混在一起会让两边的历史都变脏。

站点提交用 `docs:` 开头，说明收录了哪个项目：

```
docs: 项目总览收录 quiet-tech-style 设计规范

- 项目页新增「三、设计规范」分组，原课程项目改为第四节
- 首页数字条更新为 9 个公开仓库，项目版块新增卡片
- 根 README 同步仓库数与分组
```

---

## 发布前校验清单（Gate）

执行任何 `git push` / `git tag` / 创建 PR / 创建 Release **之前**，逐项确认：

**署名类**

- [ ] 未执行任何 `git config user.*` 写入命令
- [ ] `git config user.name` / `user.email` 与用户既有身份一致
- [ ] 即将推送的提交信息不含 `Co-authored-by` / `Generated` / `🤖` 等 AI 署名
- [ ] 本次改动未向 README / LICENSE / package.json 等注入 AI 标签或徽章
- [ ] 未改动任何文件中的 `Copyright` / `Author` 版权持有人
- [ ] Commit / PR / Release 文案为第一人称或 Conventional Commits，
      且仅描述变更，不描述 Agent 行为
- [ ] 若通过 REST API 建 commit，author/committer 取自本地 `git config`，
      非硬编码外部值

**同步类**

- [ ] 若本次新建 / 更名 / 删除仓库，或项目状态变化：
      `docs/projects.md`、首页数字条与项目卡片、根 README 均已同步
- [ ] 写了数量的，数量与实际一致（或已改为 `N+`）
- [ ] 站点 Pages 构建为 `built`，项目页返回 200
- [ ] 站点提交独立成 commit，未混入项目仓库

任一项不通过 → **停止发布动作**，向用户说明问题并等待确认，不得强行推送。

---

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

### 合规的站点同步提交
```
docs: 项目总览收录 quiet-tech-style 设计规范

- 项目页新增「三、设计规范」分组，原课程项目改为第四节
- 首页数字条更新为 9 个公开仓库，项目版块新增卡片
- 根 README 同步仓库数与分组
```

### 违规的站点同步（禁止）
```
feat: 新增 quiet-tech-style 仓库

顺带让助手把博客也更新了一下。
```
两处问题：站点改动混进了项目仓库；文案以助手为主体。

---

## 适用范围说明

- 本技能为**用户级守门员**，对所有项目生效。
- 规则 1–5 与具体仓库无关；无论提交的是 `fanqie-writer-mcp`、`Ryan-links`
  还是其他任何仓库，规则一致。
- 规则 6 绑定用户个人站点 `CommitByRyan.github.io` 的项目索引；
  若站点结构变更，按等价步骤执行（索引入口、数量、分组编号、构建验证）。
- 当用户仅做本地开发、不发布时，本技能不强制触发；但一旦涉及向远端
  推送或公开发布，必须先过此关卡。
