深圳跨境电商求职助手（通用 Agent Skill）
一个平台无关的求职 Agent Skill，用于搜索、筛选、核验和跟踪深圳跨境电商岗位，重点关注 Amazon 高级运营、精品运营、运营组长和运营主管。
功能
- 深圳岗位搜索与去重
- 固定底薪门槛检查（默认 ≥ RMB 12,000/月）
- 企业背景调查与招聘信息核验
- 100 分岗位匹配评分
- HR 沟通草稿
- 用户授权后的投递协助
- 求职进度跟踪与复盘
文件结构
shenzhen-ecommerce-job-hunter/
├── SKILL.md
├── README.md
├── LICENSE
├── references/
│   ├── candidate-profile.md
│   └── scoring-rules.md
└── templates/
    └── application-tracker.csv
安装 / 导入
本项目采用 Markdown Skill 格式。将整个目录导入支持自定义 Agent Skill、指令文件或知识库的 Agent 平台。
若平台只支持单文件，将 SKILL.md 作为主指令导入，并将 references/ 中的资料附加为知识文件。
不同平台的 Skill 发现机制和 frontmatter 规范可能不同；请以目标平台文档为准。此项目不依赖特定模型、API 或浏览器框架。
快速使用
/search
请搜索深圳最新的 Amazon 高级运营、精品运营、运营组长和运营主管岗位。
严格执行固定底薪不低于 12K 的条件，核验招聘状态并给出原始链接。
其他入口：
- /screen：核验岗位
- /company：调查公司
- /analyze：分析匹配度
- /message：生成 HR 沟通草稿
- /apply：逐岗授权后协助投递
- /track：更新投递记录
- /review：求职复盘
安全与准确性
- 默认只搜索和生成草稿，不自动联系 HR 或投递。
- 每次正式联系或投递前必须获得用户明确授权。
- 不绕过验证码、登录保护或平台访问控制。
- 不伪造招聘状态、薪资、工作经历或投递结果。
- 未核实的固定底薪不得视为符合门槛。
自定义
候选人画像位于 references/candidate-profile.md；评分标准位于 references/scoring-rules.md。修改时请确保 SKILL.md 与参考文件保持一致。
License
MIT，详见 LICENSE。


## Product roadmap

See [docs/ROADMAP.md](docs/ROADMAP.md) for the v1.1 → v1.2 → v2.0 implementation plan.

- **v1.1:** BOSS直聘 / 猎聘 source adapters, salary normalization, evidence checks and deduplication.
- **v1.2:** Feishu Bitable tables for jobs, companies, HR contacts, communications, applications, interviews and tasks.
- **v2.0:** Scheduled monitoring, change detection, scoring, notifications and approval-gated actions.

Implementation specifications:
- [v1.1 Platform Adapters](docs/V1.1-PLATFORM-ADAPTERS.md)
- [v1.2 Feishu Bitable](docs/V1.2-FEISHU-BITABLE.md)
- [v2.0 Automation Agent](docs/V2.0-AUTOMATION-AGENT.md)

CSV import templates for Feishu are in templates/feishu-*.csv. The current release documents the architecture and import schema; live Feishu API access and scheduled monitoring require the user's workspace authorization and configuration.
