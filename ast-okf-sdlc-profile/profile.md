---
type: OKF Extension Profile
title: AST OKF-SDLC Profile
description: AST 组织用于标注 OKF 知识概念在六阶段 SDLC 中适用范围的兼容性 Profile。
tags: [ast, okf, sdlc, knowledge-governance]
status: stable
generated: { by: human:ast-architecture, at: 2026-10-16T00:00:00+08:00 }
sources:
  - id: okf-v0-2
    resource: https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md
    title: Open Knowledge Format v0.2 Specification
  - id: ast-profile-rationale
    resource: ../research-okf-sdl-knowledge-architecture.md
    title: 在代码仓以 OKF 建立带 SDLC 阶段的知识库：架构调研与建议
---

# AST OKF-SDLC Profile v1

## 1. 目的和范围

本 Profile 为 AST 组织的 OKF v0.2 知识概念增加“阶段适用性”元数据。阶段适用性回答的是：**在完成某个 SDLC 阶段的决策、实现或验证时，是否应阅读或遵循此知识？**

它不描述软件组件的运行生命周期，也不描述知识文档本身的草稿或稳定状态。

本 Profile 适用于单仓或多仓中的需求、业务设计、DFX、架构、接口、测试、发布与运维知识。采用它的每个知识 bundle 应以本文件为组织级参考，并在根 `index.md` 声明 `okf_version: "0.2"`。

## 2. 与 OKF v0.2 的关系

`x_ast_sdl` 是 AST 的额外 YAML frontmatter 键，不修改 OKF 所定义的字段。

OKF v0.2 只要求每个概念文档拥有非空的 `type`。规范允许生产者加入额外键，要求消费者在 round-trip 中保留未知键，且不得因未知键拒绝文档。因此，含有 `x_ast_sdl` 的概念文档仍是 OKF v0.2 兼容文档。

不得挪用以下 OKF 字段表达阶段适用性：

| 字段 | 既有语义 | 不可替代的原因 |
| --- | --- | --- |
| `type` | 概念内容种类，如 `Business Rule`、`DFX Guideline`、`Test Strategy` | 一种知识可跨多个阶段使用 |
| `status` | 知识状态，如 `draft`、`stable`、`deprecated` | 文档状态和读者应使用它的阶段是不同维度 |
| `tags` | 跨切面分类和发现 | tags 不能表达适用强度、门禁或 Profile 版本 |
| `verified` | 人或工具的核验记录 | 它只表达文档可信度 |

## 3. 受控词表

阶段 ID 固定如下，值必须为小写英文标识：

| ID | 中文阶段 | 含义 |
| --- | --- | --- |
| `requirements` | 需求收集与分析 | 定义目标、范围、规则、验收条件与非功能需求 |
| `design` | 设计 | 形成架构、接口、数据、安全与可运维性方案 |
| `coding` | 实现/编码 | 完成代码、配置、基础设施定义与持续集成 |
| `testing` | 测试 | 验证功能、集成、性能、安全与验收条件 |
| `deployment` | 部署 | 构建制品、变更控制、发布、回退和观测准备 |
| `maintenance` | 维护 | 监控、事件响应、漏洞维护、运营反馈与持续改进 |

这些 ID 是 AST Profile v1 的封闭词表。需要新增、弃用或改名时，必须发布 Profile 的新版本，不能在单个文档中自行发明值。

## 4. `x_ast_sdl` 扩展字段

每个需要声明阶段适用性的 OKF 概念文档应带以下字段：

```yaml
x_ast_sdl:
  profile: ast/okf-sdlc@1
  phases: [design, coding, testing, maintenance]
  applicability: required
  entry_criteria:
    - 架构方案进入评审
  exit_evidence:
    - 可观测性检查清单
    - 告警演练记录
```

字段定义：

| 字段 | 必需 | 类型与约束 | 说明 |
| --- | --- | --- | --- |
| `profile` | 是 | 固定为 `ast/okf-sdlc@1` | 声明所用 AST Profile 版本 |
| `phases` | 是 | 非空数组；成员必须在第 3 节词表中 | 此知识适用的 SDLC 阶段 |
| `applicability` | 是 | `required`、`recommended` 或 `reference` | 适用强度 |
| `entry_criteria` | 否 | 字符串数组 | 何时必须或建议打开该知识 |
| `exit_evidence` | 否 | 字符串数组 | 完成该阶段时需要保留的证明或交付物 |

适用强度解释：

| 值 | 含义 |
| --- | --- |
| `required` | 对列出的阶段有组织级或项目级的强制要求；跳过时必须有记录的例外 |
| `recommended` | 默认应使用，但可由任务负责人基于风险和范围裁剪 |
| `reference` | 作为背景资料提供，不构成阶段门禁 |

## 5. 标准示例

```markdown
---
type: DFX Guideline
title: 可观测性 DFX 设计准则
description: 对日志、指标、追踪和告警的设计与交付约束。
tags: [domain:dfx, sdl:design, sdl:coding, sdl:testing, sdl:maintenance]
status: stable
sources:
  - id: observability-standard
    resource: /knowledge/standards/observability.md
    title: AST 可观测性工程标准
verified:
  - by: human:platform-architecture
    at: 2026-10-16T00:00:00+08:00
x_ast_sdl:
  profile: ast/okf-sdlc@1
  phases: [design, coding, testing, maintenance]
  applicability: required
  entry_criteria: [架构方案进入评审]
  exit_evidence: [可观测性检查清单, 告警演练记录]
---

# 适用说明

设计阶段确定采集点；编码阶段落实 SDK 与字段；测试阶段验证告警；维护阶段按本准则排障。
```

## 6. 目录、索引与范围

OKF 的 `index.md` 是目录清单。除 bundle 根的 `okf_version` 外，`index.md` 不得含 YAML frontmatter。因此，目录阶段范围必须按以下方式表达：

```text
knowledge/
  index.md
  _governance/
    ast-okf-sdlc-profile.md
  business-design/
    index.md
    overview.md
    order-lifecycle.md
  dfx/
    index.md
    overview.md
    observability.md
```

- `index.md`：纯 Markdown 目录说明，可写“本目录主要用于需求、编码和测试阶段”。
- `overview.md`：普通 OKF 概念文档，必须带 `x_ast_sdl`；可作为目录主题的机器可读说明。
- 其他概念文档：必须各自显式声明 `x_ast_sdl`。目录范围只能作为人工阅读提示和新文档的建议，不能静默继承到子文档。

每个目录可有不同阶段范围。例如，`business-design/overview.md` 可以标记 `[requirements, coding, testing]`；`dfx/overview.md` 可以标记 `[design, coding, testing, maintenance]`。其中某个子文档若只用于部署，必须只标记 `[deployment]`。

## 7. Tags 投影规则

`x_ast_sdl.phases` 是唯一事实来源。为了让普通 OKF 浏览器、静态站点或搜索工具无需理解 Profile 也可检索，必须为每个阶段生成或校验对应 tag：

```text
x_ast_sdl.phases: [design, testing]
tags: [..., sdl:design, sdl:testing]
```

规则：

1. 每个 `phases` 值必须有恰好一个匹配的 `sdl:<phase>` tag。
2. 任何 `sdl:<phase>` tag 必须在 `phases` 中有匹配值。
3. 不允许 `sdl:development`、`sdl:operations` 等非 v1 词表 tag。
4. tags 是投影，不得与 `x_ast_sdl.phases` 分别手动维护。应由脚本生成，或在 CI 中阻断不一致的 Pull Request。

## 8. 最低质量门禁

AST Profile 的 CI 校验器至少必须执行：

1. 每个非 `index.md`、非 `log.md` 的 Markdown 概念文档有可解析 YAML frontmatter 和非空 `type`。
2. 需要阶段披露的文档存在 `x_ast_sdl`。
3. `profile` 等于支持版本，`phases` 非空且是词表子集，`applicability` 为允许值。
4. `x_ast_sdl.phases` 与 `sdl:*` tags 双向一致。
5. `index.md` 在 bundle 根以外没有 frontmatter。

下列规则是建议的 AST 加强项，而不是 OKF 的强制要求：

- `status: stable` 文档至少有一个 `sources` 条目和一条 `verified` 记录。
- `applicability: required` 文档应填写至少一条 `entry_criteria` 或 `exit_evidence`。
- 已废弃文档应标记 `status: deprecated`，并指向替代文档。

## 9. 维护与版本策略

1. 本 Profile 的规范文件、词表和校验器必须同仓、同 PR 维护。
2. 兼容性新增使用 `@1` 内部的 minor 文档版本记录；字段重命名、词表 ID 改变或语义改变必须发布 `ast/okf-sdlc@2`。
3. 业务或工程所有者负责确认阶段适用性；AI 可以提出建议，但不得自行把 `applicability` 提升为 `required`。
4. Profile 变更应更新 `log.md`，并对现有文档执行 CI 迁移检查。

## 参考资料

- [OKF v0.2 规范](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md)
- [AST 架构调研与路线判断](../research-okf-sdl-knowledge-architecture.md)
- [检索指引](retrieval-instructions.md)
- [创建与修改指引](authoring-instructions.md)
- [OKF v0.2 摘要](references/okf-v0.2.md)
- [Backstage 与 GitHub Docs 相邻实践](references/docs-as-code-and-catalogs.md)

