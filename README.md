# github-publish-guardrails

GitHub 发布守门员技能（Codex / WorkBuddy Skill）。

当对话中出现「上传到 GitHub / 提交 / 推送 / 发布 / 打 tag / 开 PR / 部署到 Pages / 新增仓库 / 新项目上线」或要求撰写 Commit / Release Notes 时自动触发。

它管两件事：**发布前的署名红线**，和**发布后的站点同步**。

## 五条署名红线

在推送前强制核验：

- 不修改本地 Git 身份（`user.name` / `user.email` 保持不变）
- 提交信息不添加任何 AI / Agent 署名 Trailer
- 不向 `README` / `LICENSE` / `package.json` 等元数据或文档注入「AI 生成」「Built by Agent」等标签或徽章
- 文件中的版权持有人保持为作者本人，不做替换或追加
- 提交与发布文案采用维护者第一人称或 Conventional Commits，只描述「改了什么 / 修了什么」，不描述 Agent 自身的工作

## 规则 6 · 发布后同步全部关联项目文档

每次新建仓库、项目实质更新、仓库更名或删除，必须在同一次发布流程内更新全部
关联入口。只改其中一处不算完成：

| 位置 | 改什么 |
|---|---|
| `docs/projects.md` | 速览表加 / 改一行；对应分组下加正文小节；分组编号保持连续 |
| `docs/README.md` | 数字条仓库数；项目导语；分类卡仓库数；卡片与分类编号 |
| 根 `README.md` | 完整项目表；分类概述；项目数与分组数 |
| 条件入口 | 侧边栏仅页面增删时更新；导航页等仅在原本列项目时同步 |
| 验证 | Pages `built`；`/`、`/README.md`、`/projects.md` 返回 200；链接有落点 |

**根 README 必须有一份完整项目表。** 如果原本只有概述文字，就要先补建表格，不能
只改一段文字后认为已经同步。发布完成的判定是：新项目同时出现在项目页速览与正文、
首页分类计数、根 README 完整项目表中，并且 Pages 验证通过。

两条纪律：

1. **数量是易腐数据。** 写死「N 个公开仓库」就必须每次同步；连续两次忘记就降级为 `N+`，或干脆只写分类不写数量。
2. **同步属于同一次发布，但提交必须分开。** 站点改动不要混进项目仓库的 commit，两边历史都会变脏。

## 安装

将本目录整体放入技能目录即可，无需额外配置：

- Codex 用户级（对所有项目生效）：`~/.codex/skills/github-publish-guardrails/`
- 用户级（对所有项目生效）：`~/.workbuddy/skills/github-publish-guardrails/`
- 项目级（仅当前项目）：`<项目根>/.workbuddy/skills/github-publish-guardrails/`

## 目录结构

```
github-publish-guardrails/
├── SKILL.md    # 守门员规则与发布前校验清单（核心）
├── README.md   # 本说明
└── LICENSE     # MIT
```

## 说明

本技能为纯规则型守门员，不依赖任何脚本或外部服务。它仅作为发布流程的强制校验关卡，不改变任何业务代码。

## 版本

- `1.2.0` —— 规则 6 扩展为全部关联文档同步；新增根 README 完整项目表、首页分类计数、条件入口与完整性定义
- `1.1.0` —— 新增规则 6「发布后同步个人站点」，校验清单加入同步类检查
- `1.0.0` —— 五条署名红线与发布前 Gate

## License

MIT
