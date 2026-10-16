# LLMWiki／OKF 格式调研报告

调研日期：2026-10-16（Asia/Shanghai）  
范围：只采用 Google Cloud 与 `GoogleCloudPlatform` GitHub 组织发布的一手材料；不把社区同名项目当作官方实现。

## 结论

有官方说明和官方代码入口，但名称需要校正：**LLM-wiki 是一种知识库组织模式；OKF（Open Knowledge Format）是 Google Cloud 在 2026-06-12 发布、用来形式化该模式的开放规范**。Google 官方博客明确这样界定两者关系；因此，把“LLMWIKI 的 OKF 格式”理解为“基于 LLM-wiki 模式的 Google Cloud OKF”是合理的，但两者不是同一个产品或文件格式名称。[Google Cloud 发布公告](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing)

官方当前入口是 GitHub 组织 **GoogleCloudPlatform** 下的 [`open-knowledge-format`](https://github.com/GoogleCloudPlatform/open-knowledge-format) 仓库，规范为仓库根目录的 [`SPEC.md`](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md)。截至调研日，规范自称 **v0.2**；初版 v0.1 的公开发布日期为 2026-06-12。[官方规范 v0.2](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md)；[初版发布公告](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing)

OKF 的核心不是一个必须安装的平台：一个 bundle 是 Markdown 文件目录，每份概念文档以 YAML frontmatter 加 Markdown 正文表达，`type` 是概念文档唯一必填的 frontmatter 字段；`index.md` 与 `log.md` 是保留文件名。v0.2 还定义了可选的来源、信任、生命周期和计算证明元数据。 [规范：目录与保留文件](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#3-knowledge-bundles)；[规范：概念文档](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#4-concept-documents)；[规范：来源、信任和生命周期](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#5-provenance-trust-and-lifecycle)

## 官方说明与位置

| 项目 | 官方位置 | 能核验的内容 |
|---|---|---|
| 发布说明 | [Google Cloud Blog（2026-06-12）](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing) | Google Cloud 将 OKF 描述为把 LLM-wiki 模式形式化为可移植、可互操作格式的开放规范；初版为 v0.1。 |
| 当前规范 | [`GoogleCloudPlatform/open-knowledge-format/SPEC.md`](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md) | 当前文件标注 Version 0.2，定义 bundle、frontmatter、链接、索引、变更日志、provenance/trust/lifecycle 等。 |
| 官方项目首页/README | [`GoogleCloudPlatform/open-knowledge-format`](https://github.com/GoogleCloudPlatform/open-knowledge-format) | 明确 OKF 不绑定特定 agent、框架、模型供应商或服务系统，并将参考 agent 与可视化器定位为 PoC。 |
| Google Cloud Knowledge Catalog 接入说明 | [`connectors/gcp-knowledge-catalog.md`](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/connectors/gcp-knowledge-catalog.md) | 官方仓库提供 Knowledge Catalog connector 的说明；发布公告也称 Knowledge Catalog 已可摄入 OKF 并服务给其 agents。 |
| 旧规范位置（迁移辨析） | [`GoogleCloudPlatform/knowledge-catalog/okf/SPEC.md`](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) | 仍可见 v0.2 快照；采用时应以独立仓库 `open-knowledge-format` 的 `SPEC.md` 为准。 |

## 官方工具：是什么、在哪里、如何定位

官方仓库的定位是“规范优先”。README 与发布公告都说生产者和可视化器是 proof of concept，格式本身才是主要交付物。因此，下面工具可以用于试验和参考实现，不能表述成 Google 承诺维护的通用生产级 OKF 平台。

| 工具/能力 | 源码或入口位置 | 已核验的功能与使用边界 |
|---|---|---|
| Python Reference Agent（生产者 PoC） | [`src/reference_agent/`](https://github.com/GoogleCloudPlatform/open-knowledge-format/tree/main/src/reference_agent)，[CLI 源码](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/src/reference_agent/cli.py) | CLI 命令为 `reference-agent`，有 `enrich` 子命令。已实现源类型仅见 `bq`，从 BigQuery 元数据生成/丰富 OKF bundle；可添加受限网页 seed 进行补充。需要 BigQuery、Gemini/Vertex AI 凭据，不是任意本地文档的一键转换器。 |
| 图形可视化器（消费者 PoC） | [`src/reference_agent/viewer/`](https://github.com/GoogleCloudPlatform/open-knowledge-format/tree/main/src/reference_agent/viewer)，[CLI `visualize` 定义](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/src/reference_agent/cli.py) | `reference-agent visualize --bundle <bundle>` 生成单个自包含 `viz.html`，展示概念、链接、反向引用、搜索和类型筛选。README 说明浏览端无需安装后端。 |
| 已生成示例和浏览入口 | [`bundles/`](https://github.com/GoogleCloudPlatform/open-knowledge-format/tree/main/bundles) | 官方仓库已提交 GA4、Stack Overflow、Bitcoin、Acme Retail 等 bundle 与对应 `viz.html`，可用于理解结构和验证消费者实现。 |
| Knowledge Catalog connector | [`connectors/gcp-knowledge-catalog.md`](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/connectors/gcp-knowledge-catalog.md) | 使用 knowledge-catalog 的 `toolbox/mdcode` 构建 `kcmd`，将 bundle 推送/拉取 Knowledge Catalog EntryGroup。文档列出 Dataplex API、`gcloud` 与 Bun 等前置条件；它只搬运 Markdown body 与有限 frontmatter，且没有 rename/delete/merge 同步语义，所以 Git 应作为权威源。 |

参考 Agent 的 Python 包元数据为 `reference-agent`、版本 `0.1.0`，要求 Python >=3.11，并注册 `reference-agent = reference_agent.cli:main` 这个命令行入口。[`pyproject.toml`](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/pyproject.toml)  仓库 README 提供的安装和运行方式为创建虚拟环境、`pip install -e .[dev]`，随后运行 `python -m reference_agent enrich ...` 或 `python -m reference_agent visualize ...`。[README：安装、凭据与运行](https://github.com/GoogleCloudPlatform/open-knowledge-format#install)

## 格式要点（适合纳入工程知识库）

1. **载体**：bundle 可是 Git 仓库、压缩包或大仓库中的子目录；这适合把项目知识随代码评审、差异与版本一起管理。[规范 §3](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#3-knowledge-bundles)
2. **最小互操作约束**：每个非保留 `.md` 概念文档需有 YAML frontmatter 和 Markdown 正文；`type` 必填。`title`、`description`、`resource`、`tags` 等为可选元数据。[规范 §4.1](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#41-frontmatter)
3. **v0.2 的工程价值**：`sources` 记录来源，`generated`/`verified` 记录生成与核验，`status`/`stale_after` 表达生命周期。它们均为可选，规范刻意避免用固定分类法和强制运行时绑定。[规范 §5](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#5-provenance-trust-and-lifecycle)；[规范的非目标](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#goals-and-non-goals)
4. **对六阶段 SDLC 的建议**：可以把需求、架构决策、接口、测试策略、发布手册、运行故障处理分别建成概念文档；引用必须可追溯，并在人工审批后写入 `verified`。这是一种基于格式能力的落地建议，并非 Google 的规定流程。

## 维护状态、版本与风险判断

- **项目归属**：发布文章由 Google Cloud Data Cloud 团队署名，主仓库位于 `GoogleCloudPlatform` 组织，代码文件带 `Copyright 2026 Google LLC` 和 Apache-2.0 许可证头。这足以将该仓库认定为 Google 发布的官方实现/规范源。[发布公告](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing)；[仓库 LICENSE](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/LICENSE.md)；[`cli.py`](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/src/reference_agent/cli.py)
- **当前维护迹象**：公开 Issues 在 2026-10-15 仍有新 issue，且页面显示开放 PR；但 `main` 的最后可见提交为 2026-08-21，仓库也没有 GitHub Releases 或 tags。仅凭这些信号不能承诺维护等级、支持 SLA 或发布节奏；更准确的判断是“早期公开规范，仍有社区讨论”。[Issues](https://github.com/GoogleCloudPlatform/open-knowledge-format/issues)；[commits](https://github.com/GoogleCloudPlatform/open-knowledge-format/commits/main)；[releases](https://github.com/GoogleCloudPlatform/open-knowledge-format/releases)；[tags](https://github.com/GoogleCloudPlatform/open-knowledge-format/tags)
- **版本状态与采用风险**：初版公告为 v0.1；当前官方规范文件标为 v0.2。官方博客也明确把 v0.1 称为起点、预期规范会演进。公开 Issue #24 还指出，2026-08-21 对时间戳 UTC offset 的要求发生变化但版本号仍为 0.2；这是提出的问题，不应当作已裁决事实，但足以说明生产落地要将 `SPEC.md` commit 固定到内部基线，而不能只引用浮动 `main`。[初版公告](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing)；[当前规范](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md)；[Issue #24](https://github.com/GoogleCloudPlatform/open-knowledge-format/issues/24)
- **无法核验/未发现的官方项**：在官方 README、`pyproject.toml`、源码目录和 GitHub Releases/tags 页面中，未找到官方独立 validator、发布到包管理器的稳定 CLI、VS Code 扩展、MCP server 或正式兼容性认证计划。因此不能把社区的 `okf`/`llm-wiki` CLI、编辑器插件或转换器称为“官方工具”。同样，未在本次一手材料中核验到带固定发布日期的 v0.2 发布公告或 GitHub Release；v0.2 的依据是当前 `SPEC.md`。

## 同名与术语歧义

- **LLM-wiki ≠ OKF**：前者是模式，后者是把该模式标准化的格式；官方发布公告是这层关系的一手依据。
- **OKF 不是 Open Knowledge Foundation 的缩写在此处的用法**：本报告只讨论 Google Cloud 的 Open Knowledge Format。
- **“官方工具”需分层理解**：官方仓库内的 Reference Agent、Visualizer、示例与 Knowledge Catalog connector 都可定位；但规范本身不要求任何一个工具，且官方将 agent/viewer 明确定位为 PoC。

## 采用建议

若团队目标是先将项目知识与 AI 开发流程结合，建议先按 v0.2 手工维护一个小 bundle，用 Git PR 作为编辑与审核入口，并用 `sources`、`verified`、`stale_after` 建立可信度和过期复核机制。随后再以官方 Visualizer 做浏览验证；只有数据源确为 BigQuery、且团队能接受 Gemini/Vertex AI 与 Google Cloud 凭据依赖时，才评估官方 Reference Agent。不要因为文件格式开放就跳过现有的权限隔离、敏感信息脱敏和人工评审。

