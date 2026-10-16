---
type: Reference
title: OKF v0.2 与 AST SDLC Profile 相关的规范摘要
description: AST Profile 依赖的 OKF v0.2 约束、扩展点和目录规则摘要。
tags: [okf, reference, specification]
status: stable
sources:
  - id: okf-spec
    resource: https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md
    title: Open Knowledge Format v0.2 Specification
generated: { by: human:ast-architecture, at: 2026-10-16T00:00:00+08:00 }
---

# OKF v0.2 摘要

以下内容来自 [Open Knowledge Format v0.2 Specification](https://github.com/GoogleCloudPlatform/open-knowledge-format/blob/main/SPEC.md)，用于解释 AST Profile 的设计边界。

| OKF 规则 | AST Profile 的使用方式 |
| --- | --- |
| 概念是 YAML frontmatter 加 Markdown 正文；`type` 是唯一始终必填字段 | 所有 AST 知识概念保留 `type`，并将阶段适用性放入额外字段 |
| 允许任意额外 frontmatter 键；消费者应保留未知键，不能因此拒绝文档 | `x_ast_sdl` 是兼容的 AST 扩展 |
| `tags` 是跨切面分类 | `sdl:*` tags 用作 `x_ast_sdl.phases` 的检索投影 |
| 目录组织由生产者决定 | 使用业务设计、DFX、运维等目录组织知识 |
| `index.md` 是保留的目录清单；仅 bundle 根可有 `okf_version` frontmatter | 子目录 index 只写人可读说明，机器元数据写在 `overview.md` |
| `sources`、`verified`、`status` 和 `stale_after` 有既定的来源、核验和生命周期语义 | 这些字段用于可信度与时效，不能替代 SDLC 阶段字段 |

规范本身没有定义 SDLC 阶段、组织前缀规则或目录元数据继承。这些内容由 AST Profile 定义，不能称为 OKF 的标准字段。

