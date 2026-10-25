# github-publish-guardrails

GitHub 发布守门员技能（WorkBuddy Skill）。

当对话中出现「上传到 GitHub / 提交 / 推送 / 发布 / 打 tag / 开 PR / 部署到 Pages」或要求撰写 Commit / Release Notes 时自动触发，在推送前强制核验五条发布红线：

- 不修改本地 Git 身份（`user.name` / `user.email` 保持不变）
- 提交信息不添加任何 AI / Agent 署名 Trailer
- 不向 `README` / `LICENSE` / `package.json` 等元数据或文档注入「AI 生成」「Built by Agent」等标签或徽章
- 文件中的版权持有人保持为作者本人，不做替换或追加
- 提交与发布文案采用维护者第一人称或 Conventional Commits，只描述「改了什么 / 修了什么」，不描述 Agent 自身的工作

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
