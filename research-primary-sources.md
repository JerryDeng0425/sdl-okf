# AI 辅助软件开发生命周期：一手资料研究笔记

调研日期：2026-10-16。本文服务于最终报告，区分来源陈述与研究者建议；仅引用第一方官方资料。主要研究对象是“用 AI 开发软件”，不是训练模型的 MLOps 流程。

## 1. 名称与边界

- **事实**：Microsoft 的 SDL 指 Security Development Lifecycle，包含需求、设计、实现、验证、发布五个核心阶段，并辅以培训、响应。因此用户口语中的“软件开发流程 SDL”应在报告中解释为 SDLC；安全 SDL 是贯穿其中的保障。来源：[Microsoft Learn](https://learn.microsoft.com/en-us/windows/security/threat-protection/msft-security-dev-lifecycle)。
- **事实**：NIST SP 800-218A（2024-07）是针对生成式 AI 与双用途基础模型的 SSDF 补充 Profile，面向模型生产者、使用模型的系统生产者和采购者，要求与 SP 800-218 配套使用。它不等于通用 AI 编程助手流程。来源：[NIST SP 800-218A](https://csrc.nist.gov/pubs/sp/800/218/a/final)。

## 2. AWS AI-DLC：方法论与可执行工作流

- **事实**：AWS 将 AI-DLC 划分为 Inception、Construction、Operations 三阶段。Inception 把业务意图变成需求、故事、工作单元；Construction 形成逻辑架构、领域模型、代码和测试；Operations 运用累积上下文管理 IaC 与部署。AI 提计划、问问题、执行，人类验证关键决策。需求、计划、设计进入项目仓库，支持跨会话持续上下文。来源：[AWS 方法论介绍，2025-07-31](https://aws.amazon.com/blogs/devops/ai-driven-development-life-cycle/)。
- **事实**：AWS 实践文章展示的工作流可按复杂度调整。Inception 包含工作区识别、需求分析、工作流规划；按需进行既有系统逆向理解、用户故事、应用设计、工作单元生成。Construction 包含按需的功能设计、非功能需求与设计、基础设施设计，随后代码生成、构建测试。来源：[Building with AI-DLC using Amazon Q Developer](https://aws.amazon.com/blogs/devops/building-with-ai-dlc-using-amazon-q-developer/)。
- **事实**：同一实践文章要求澄清、执行计划、审批和决策审计；新系统从需求分析起步，存量系统先做逆向理解。来源：[同上](https://aws.amazon.com/blogs/devops/building-with-ai-dlc-using-amazon-q-developer/)。
- **实施边界**：方法论覆盖运维，并不表示每种工具配置都已实现完整运维自动化。当前 awslabs 主分支编排器文档中，`classic` 配置的 Operation 仍是 placeholder，其他配置包含部署与可观测性等阶段；应按配置核实。来源：[awslabs/aidlc-workflows 编排器参考](https://github.com/awslabs/aidlc-workflows/blob/main/docs/reference/03-orchestrator.md)。
- **建议**：最终报告可用三阶段表达宏观框架，用更细的步骤解释工程动作；不要把 AWS 方法称作全行业统一标准，也不要把“小时/天交付”营销措辞写成普遍验证的提效结论。

## 3. GitHub Spec Kit：规范驱动开发

- **事实**：当前 Spec Kit 是支持多种结构化过程的开源工具包。SDD、修 Bug、创意评估是独立入口，并非必须依次执行。来源：[官方仓库 README](https://github.com/github/spec-kit)。
- **事实**：项目先建立 constitution；按功能执行 specify → plan → tasks → implement → converge。specify 描述需求、用户行为和目标；plan 明确技术栈、架构与约束；tasks 形成有依赖顺序的任务；implement 执行；converge 将实现与规范、计划、任务比较，把遗漏追加为任务后继续迭代。来源：[Agentic SDD 官方参考](https://github.github.com/spec-kit/reference/agentic-sdd.html)。
- **事实**：clarify、checklist、analyze 是可加入的质量门禁，分别处理模糊需求、需求质量、一致性；checklist 不能等同于代码测试，analyze 检查 spec/plan/tasks 间冲突和缺项。来源：[同上](https://github.github.com/spec-kit/reference/agentic-sdd.html)。
- **建议**：把 spec、plan、tasks、代码、测试形成可追溯链条。对小修复适度裁剪，避免把所有开发强制套进沉重文档流程。最终报告应注明当前参考中存在 converge，避免照搬旧版流程只到 implement。

## 4. Google DORA：AI 的收益取决于工程系统

- **事实**：2025 DORA 报告概括 AI 的作用为放大既有组织优势和弱点，投资回报来自工程组织系统，而不仅是工具。来源：[2025 State of AI-assisted Software Development](https://dora.dev/research/2025/dora-report/)。
- **事实**：DORA 七项 AI 能力为：清晰且充分沟通的 AI 立场、健康数据生态、AI 可访问的内部数据、健全版本控制、小批量工作、以用户为中心、优质内部平台。其研究以访谈、已有研究、专家意见形成候选，再通过年度调查评估。来源：[Google Cloud 官方模型介绍](https://cloud.google.com/blog/products/ai-machine-learning/introducing-doras-inaugural-ai-capabilities-model)。
- **事实**：DORA 问卷将应用代码、配置、构建自动化代码、AI 提示词等是否受版本控制纳入测量；另测量单任务耗时、单次发布变更数量、用户反馈和平台是否支持安全与审查流程。来源：[DORA 模型调查问题](https://dora.dev/ai/capabilities-model/questions/)。
- **事实**：DORA 对 2025 年研究的解释指出，较高 AI 使用与更高交付吞吐量及更高交付不稳定性同时相关。应避免只报告个人编码速度或把相关性写成因果。来源：[Balancing AI tensions](https://dora.dev/insights/balancing-ai-tensions/)。
- **事实**：DORA 当前交付指标分两类：吞吐（变更前置时间、部署频率、失败部署恢复时间）与不稳定性（变更失败率、部署返工率）。来源：[DORA 软件交付指标](https://dora.dev/guides/dora-metrics/)。
- **建议**：评估 AI 工程试点应同时观察需求到上线周期、评审等待、缺陷/返工、生产稳定性、用户结果和成本；不要以代码行数、AI 采纳率、生成量替代业务收益。

## 5. 可用于最终报告的综合步骤（研究者归纳，不是某机构原文标准）

1. 业务发现与价值定义：问题、用户、成功指标、范围、约束。
2. 需求规范与验收标准：用户故事、边界条件、非功能需求、验收条件。
3. 上下文准备与存量理解：代码地图、架构、依赖、数据字典、规则、环境。
4. 架构与技术设计：接口、领域模型、ADR、安全、性能、可运维性。
5. 工作拆分与计划：可独立验证的小批量任务、依赖、责任、完成条件。
6. 编码与持续集成：AI 生成与修改，人类评审，构建与自动检查。
7. 测试、安全验证和规范一致性：功能、集成、端到端、性能、安全，验证实现满足原始意图。
8. 发布与变更控制：制品、灰度、发布审批、监控、回滚演练。
9. 运维反馈与持续改进：事件响应、用户反馈、缺陷回溯、更新规范和上下文。

跨阶段：安全隐私、最小权限、版本与审计、人类责任、成本和效果度量。以上应标为基于 AWS、GitHub、DORA、Microsoft 的综合建议，而非已获行业统一认可的九阶段规范。

