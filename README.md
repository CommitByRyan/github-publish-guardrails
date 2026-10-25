# github-publish-guardrails

GitHub 发布守门员技能（WorkBuddy Skill）。

当对话中出现「上传到 GitHub / 提交 / 推送 / 发布 / 打 tag / 开 PR / 部署到 Pages / 新增仓库 / 新项目上线」或要求撰写 Commit / Release Notes 时自动触发。

它管两件事：**发布前的署名红线**，和**发布后的站点同步**。

## 五条署名红线

在推送前强制核验：

- 不修改本地 Git 身份（`user.name` / `user.email` 保持不变）
- 提交信息不添加任何 AI / Agent 署名 Trailer
- 不向 `README` / `LICENSE` / `package.json` 等元数据或文档注入「AI 生成」「Built by Agent」等标签或徽章
- 文件中的版权持有人保持为作者本人，不做替换或追加
- 提交与发布文案采用维护者第一人称或 Conventional Commits，只描述「改了什么 / 修了什么」，不描述 Agent 自身的工作

## 规则 6 · 发布后同步个人站点

每次新建仓库、项目实质更新、仓库更名或删除，在同一次发布流程内更新站点项目索引：

| 位置 | 改什么 |
|---|---|
| `docs/projects.md` | 速览表加 / 改一行；对应分组下加正文小节；分组编号保持连续 |
| `docs/README.md` | 数字条中的仓库数量；项目版块卡片与编号 |
| 根 `README.md` | 项目表与数量表述 |
| 验证 | Pages 构建 `built`、`/` 与 `/projects.md` 返回 200、相对链接有落点 |

两条纪律：

1. **数量是易腐数据。** 写死「N 个公开仓库」就必须每次同步；连续两次忘记就降级为 `N+`，或干脆只写分类不写数量。
2. **同步属于同一次发布，但提交必须分开。** 站点改动不要混进项目仓库的 commit，两边历史都会变脏。

## 安装

将本目录整体放入 WorkBuddy 的技能目录即可，无需额外配置：

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

- `1.1.0` —— 新增规则 6「发布后同步个人站点」，校验清单加入同步类检查
- `1.0.0` —— 五条署名红线与发布前 Gate

## License

MIT
