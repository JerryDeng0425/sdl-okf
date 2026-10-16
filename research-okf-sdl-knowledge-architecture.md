# 在代码仓以 OKF 建立带 SDLC 阶段的知识库：架构调研与建议

**调研日期：2026-10-16**  
**范围：**以 OKF v0.2 在单一代码仓维护知识库；六阶段统一为“需求、设计、编码、测试、部署、维护”。本文把“某知识应在哪些阶段被阅读/使用”称作**阶段适用性**，以免和文档自身的成熟状态混淆。

## 结论

可以做，并且无需修改或 fork OKF。推荐采用“**OKF 概念文档中的受控 `x_<组织>_sdl` 扩展为唯一事实来源 + `tags` 为检索投影 + 子目录 `index.md` 的可读阶段说明**”的路线。

OKF v0.2 明确允许生产者加入任意额外 frontmatter 字段，要求消费者在 round-trip 时保留未知字段、不得因未知字段拒绝文档；因此它是兼容的领域级补强，而不是对 OKF 的破坏。[OKF SPEC §4.1](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#41-frontmatter) 但 OKF 没有 SDL 扩展、扩展命名空间或该字段的官方词表；下文的字段和词表应作为本仓自己的“OKF-SDLC Profile v1”，不能宣传为 OKF 标准。

不要把 `status` 或 `type` 借来表示 SDLC 阶段。OKF 的 `type` 表示知识概念种类，`status` 表示该知识文档的 `draft`、`stable`、`deprecated` 状态；二者回答的问题不同。[OKF SPEC §4、§5.4](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#4-concept-documents) 例如“DFX 设计约束”应是 `type: Engineering Standard`，可在“设计、编码、测试、维护”使用，且文档状态可独立为 `stable`。

## 已核验的 OKF 约束与可用扩展点

| 事实 | 对本需求的含义 |
| --- | --- |
| 一个概念是一个带 YAML frontmatter 的 Markdown 文档；目录如何组织由生产者决定。`index.md` 和 `log.md` 是任意层级的保留名。 [SPEC §3](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#3-bundle-structure) | 可以按业务、DFX、架构等目录组织，也可以放在主代码仓的子目录。 |
| `type` 是唯一必填字段；没有中央 type 注册；`tags` 是跨切面分类的字符串列表。 [SPEC §4.1](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#41-frontmatter) | `type` 应写“Business Rule”“DFX Guideline”“Test Strategy”等内容类型；`tags` 可投影 `sdl:design` 供现成工具筛选。 |
| 生产者可以添加任意 frontmatter 键，消费者应保留未知键且不得拒绝未知键。 [SPEC §4.1](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#41-frontmatter) | `x_acme_sdl` 这样的受控扩展保持 OKF v0.2 兼容。 |
| 任一目录都可有 `index.md`，它用于渐进披露；除 bundle 根的可选 `okf_version` 外，**index 不得有 frontmatter**。 [SPEC §8](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#8-index-files) | 不能在 `dfx/index.md` 的 YAML 放机器可筛选的 SDLC 字段。可在正文说明，或新增 `dfx/overview.md` 承载 frontmatter。 |
| Markdown link 的关系类型只由周边文字表达；消费者通常把它当无类型有向边。 [SPEC §6.1](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#61-links-between-concepts) | 不要依赖链接来编码“适用于设计阶段”这类机器规则；链接用于说明依赖和引用即可。 |
| `sources`、`generated`、`verified`、`status`、`stale_after` 分别覆盖来源、生成、核验、文档生命周期和过期时间。 [SPEC §5](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md#5-provenance-trust-and-lifecycle) | 可作为质量门禁证据，但它们不是阶段适用性字段。 |

官方参考实现也实际利用 `type` 和 `tags` 作检索：OKF README 说明 visualizer 可按 type 过滤，并在标题、concept ID、tags 中搜索；仓库提供多个示例 bundle。 [OKF README](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/README.md) 因而以 `tags` 作为 `x_acme_sdl` 的派生索引能立即兼容基础浏览体验。

## 推荐的领域 Profile：OKF-SDLC Profile v1

### 词表先于字段

在仓内定义且锁定以下 ID，不使用“开发/运维”一类会混淆六阶段的宽泛词：

```yaml
# knowledge/_governance/sdlc-vocabulary.yaml
profile: org.example/okf-sdlc
version: "1.0"
phases: [requirements, design, coding, testing, deployment, maintenance]
```

建议字段加本组织前缀，降低与未来 OKF 保留键冲突的风险：

```yaml
---
type: DFX Guideline
title: 可观测性 DFX 设计准则
description: 对日志、指标、追踪和告警的设计约束。
tags: [domain:dfx, sdl:design, sdl:coding, sdl:testing, sdl:maintenance]
status: stable
sources:
  - id: eng-standard
    resource: /knowledge/standards/observability.md
    title: 可观测性工程标准
verified: { by: human:platform-architecture, at: 2026-10-16T00:00:00+08:00 }
x_example_sdl:
  profile: org.example/okf-sdlc@1
  phases: [design, coding, testing, maintenance]
  applicability: required       # required | recommended | reference
  entry_criteria: ["架构方案进入评审"]
  exit_evidence: ["可观测性检查清单", "演练记录"]
---

# 适用说明

设计阶段确认采集点；编码阶段落实 SDK 与字段；测试阶段验证告警；维护阶段按本准则排障。
```

这里 `x_example_sdl` 是唯一事实来源，`tags` 是可以由校验器自动生成或比对的派生值。这样避免两套人工真相漂移。`applicability` 表示该知识在阶段中是必读、建议或仅供参考；`entry_criteria` 与 `exit_evidence` 是可选字段，只有真正需要阶段门禁时才填写，避免让普通页面变成流程表单。

目录的阶段范围应落在一个**目录主题概念**而不是 index frontmatter：

```text
knowledge/
  index.md                         # 仅 OKF 目录清单；根可含 okf_version
  _governance/
    sdlc-vocabulary.yaml
    okf-sdlc-profile.md
  business-design/
    index.md                       # 人可读：主要用于需求、编码、测试
    overview.md                    # 有 x_example_sdl 的目录主题概念
    order-lifecycle.md
  dfx/
    index.md                       # 人可读：主要用于设计、编码、测试、维护
    overview.md
    observability.md
  operations/
    index.md
    incident-response.md
```

`dfx/index.md` 必须保持 OKF 规定的无 frontmatter 格式；其正文可以写“本目录主要用于：设计、编码、测试、维护”，并列出 `overview.md` 和子项。`dfx/overview.md` 的 frontmatter 保存同一范围，供机器读取。每个子页面必须自己写 `x_example_sdl.phases`，不得默认继承目录范围；这样 DFX 中只适用于部署的 runbook 不会被错误标成全部阶段。

## 四条可选路线

| 路线 | 做法 | 优点 | 代价与风险 | 适用判断 |
| --- | --- | --- | --- | --- |
| A. OKF 扩展 + tags 投影（推荐） | 每个概念写 `x_example_sdl.phases`；同步 `sdl:*` tags；目录有 `overview.md`。 | 仍是纯 OKF；概念粒度准确；可被现有 tag 搜索消费；可写 schema/CI 校验。 | 需维护词表、模板与一条校验规则；默认不继承目录元数据。 | 单仓、以 Markdown/LLM/Git PR 为主要入口时首选。 |
| B. 仅 tags + index 文字 | `tags: [sdl:requirements, sdl:testing]`，目录 index 直接说明阶段。 | 最快，零自定义解析，visualizer 能立即搜索标签。 | tag 没有门禁、适用强度、schema 版本等结构；拼写、同义词和遗漏难治理；index 无前置元数据。 | 小型试点或只需“按阶段找资料”。不宜作为长期唯一数据模型。 |
| C. 外置阶段矩阵/目录清单 | 保持 OKF 文档不带 SDL 字段，在 `knowledge/sdlc-matrix.yaml` 以 concept ID/目录映射阶段。 | 原始 OKF 最纯；可为整个目录设置默认规则；可输出报表。 | 主文档和矩阵两处变更会漂移；移动文件后 concept ID 变化；LLM 单独读页面时看不到阶段。 | 已有大量存量 OKF、短期不想改页面时，作为迁移过渡。 |
| D. OKF + Backstage catalog overlay | 页面仍按 A；每个系统/组件另设 `catalog-info.yaml`，把 owner、系统、运行生命周期、TechDocs 入口放进 Backstage；SDL 字段用组织域名 annotation 或自定义插件读 A。 | 对跨仓系统、责任人、服务/API/资源依赖、门户检索有成熟模型；catalog 描述文件通常与源码同仓维护。 [Backstage 生命周期说明](https://backstage.io/docs/features/software-catalog/life-of-an-entity/) | 引入 Backstage 平台、同步策略和 UI 开发；Backstage `spec.lifecycle` 是服务的运行成熟状态，不能替代 SDLC 阶段。 | 多团队、多仓且已经/计划采用 Backstage 时选；不是为单仓 Wiki 而单独引入的理由。 |

### 行业实践能借鉴什么

1. **Backstage：领域目录和扩展 metadata。**其真实仓库在 package 下维护 `catalog-info.yaml`，例如 `@backstage/catalog-model` 声明 `type`、`lifecycle: production` 和 `owner`。 [真实文件](https://github.com/backstage/backstage/blob/master/packages/catalog-model/catalog-info.yaml) Backstage 把 Component、API、Resource 归入 System，再归入 Domain，以系统目录隐藏实现细节。 [System Model](https://backstage.io/docs/features/software-catalog/system-model/) 这可借鉴为“业务域/系统/模块”目录与所有权模型。

2. **Backstage：自定义字段采取命名空间，且不滥用标签。**`metadata.annotations` 可承载任意非标识元数据，推荐以域名作前缀；`tags` 只是无特殊语义的分类字符串。 [Descriptor format](https://backstage.io/docs/features/software-catalog/descriptor-format/#annotations) [Extending the model](https://backstage.io/docs/features/software-catalog/extending-the-model/#adding-a-new-annotation) 对 OKF 的启示是：结构化 SDL 语义用前缀扩展键，tag 只做发现索引。

3. **GitHub Docs：页面元数据与站点导航分工。**GitHub Docs 在 Markdown frontmatter 中维护版本、元数据和文章布局，同时以单源内容支持多个产品版本。 [GitHub Docs 写作说明](https://docs.github.com/en/contributing/writing-for-github-docs) 其开源内容目录的页面可见 `title`、`versions`、`children` 等 frontmatter。 [实际文件](https://github.com/github/docs/blob/main/content/contributing/writing-for-github-docs/index.md) 对 OKF 的启示是：页面语义放可解析的元数据，索引负责浏览与渐进披露；但不要把 GitHub Docs 的自定义字段误认为 OKF 标准。

## 推荐路线的落地与迁移

1. 在 `knowledge/_governance/` 新增词表与 `okf-sdlc-profile.md`，明确定义六个 phase ID、`applicability` 枚举、`x_example_sdl` schema 及 tag 投影规则；将 profile 版本写入每页，避免未来改词表后无法解释历史页。
2. 先建立 3 个代表目录（例如业务设计、DFX、运维），每个创建无 frontmatter 的 `index.md` 和带 metadata 的 `overview.md`。用“目录的主要阶段”帮助阅读，但以每个概念页面字段决定实际适用性。
3. 选 10–20 篇高价值文档试标。业务设计可标 `[requirements, coding, testing]`；DFX 通常标 `[design, coding, testing, maintenance]`。这些是示例起点，最终应由文档 owner 依据“何时需要用这份知识做决策或交付”确认。
4. 增加 CI 校验：`type` 存在；`x_example_sdl.profile` 是已支持版本；`phases` 是词表子集；`tags` 与 phases 一致；`status: stable` 的页面应至少有 `sources` 和一次 `verified`。后两项属于本组织门禁，非 OKF 强制要求。
5. 生成两个只读视图：按目录的 `index.md`（渐进披露）和按阶段的 `sdlc/<phase>/index.md` 或静态站点筛选。它们由概念页聚合生成，不让生成页反向成为元数据真相。
6. 当跨仓检索、系统依赖图和责任人查询成为刚需，再把每个系统的 `catalog-info.yaml` 接入 Backstage；让 Backstage 管“软件资产目录”，OKF 管“可版本化的深度知识”。

## 需要避免的设计

- 不把 `status: development`、`status: operations` 当作阶段字段：它违反 OKF 已定义的 `draft/stable/deprecated` 语义。
- 不把 `type: Design`、`type: Testing` 当阶段标签：一页“业务规则”可能在多个阶段使用，内容类型和使用时机是两个维度。
- 不在非根 `index.md` 写 YAML frontmatter：这不符合 OKF §8；需要机器字段时使用 `overview.md`。
- 不让目录默认阶段“静默继承”给所有页面：可在生成器中提供建议值，但每页显式落盘才能审计和正确筛选。
- 不同时人工维护 `x_example_sdl.phases`、tags 和外置矩阵；明确一个 canonical source，其余均由 CI/生成器派生。

## 决策

对于当前“在一个代码仓构建 OKF Wiki”的目标，采用路线 A；路线 B 可作为第一周的轻量试点，路线 C 只作为存量迁移缓冲，路线 D 留给多仓服务目录需求。这样保留 OKF 的可读性、Git 可审查性和现有工具兼容，同时把六阶段适用性变成可验证、可检索、可演进的组织领域模型。
