# Content Orchestrator Skill

内容生产调度器。它**不写内容**，而是作为调度中枢，把一次内容创作请求拆解、决策、并按固定顺序编排三个子 Skill，形成从「检索 → 写作 → 去 AI 味 → 质检」的业务闭环。

## 它解决什么问题

直接让模型"写一篇公众号文章"，往往得到没有事实支撑、AI 味浓、不符合平台调性的初稿。本 Skill 把这件事拆成一条受控流水线，每一环交给专精的子 Skill，最后统一质检，只交付可发布成稿。

## 业务闭环

```
用户输入
   ↓
agent-reach            （条件触发：涉及时效/数据/新闻时全网搜索）
   ↓
wechat-article-writer  （公众号写作，产出初稿）
   ↓
humanizer              （去 AI 味，必经，无例外）
   ↓
QA Gate                （结构 / AI 味 / 来源 / 逻辑 / 平台习惯 / 事实自洽）
   ↓
最终成稿（纯文本 / markdown 正文）
```

## 依赖的子 Skill

| 角色 | Skill name |
|------|-----------|
| 全网搜索 | `agent-reach` |
| 公众号写作 | `wechat-article-writer` |
| 去 AI 味 | `humanizer` |

> 这三个名称是调用契约。若你环境里子 Skill 名称不同，请同步修改 `SKILL.md` 中的「子 Skill 注册表」和「路由规则速查」两处。

## 设计要点

- **只输出成稿**：不暴露调用了哪个 skill、第几步、搜了什么，无任何过程性说明。
- **容错降级**：任一子 Skill 不可用时回退到内置能力（如 WebSearch 或本体改写），保证闭环不断。
- **轻量确认**：需求明确则直接执行；信息严重不足时只问一个关键问题。
- **可扩展**：预留 L3 层（爆款标题、选题策略、情绪增强、用户画像适配），新增时改注册表 + 路由表即可。

## 安装

将本目录放入你的 Skills 目录（与 `agent-reach`、`wechat-article-writer`、`humanizer` 同级），重新加载 Skill 即可。当用户发起任何内容创作请求时，本 Skill 会优先被触发。

## 目录结构

```
content-orchestrator/
├── SKILL.md     # 编排逻辑（核心）
└── README.md    # 本文件
```

## License

MIT
