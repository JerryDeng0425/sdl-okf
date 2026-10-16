---
type: Reference
title: Docs-as-code 与软件目录的相邻实践
description: Backstage 与 GitHub Docs 对 AST Profile 的可借鉴模式。
tags: [backstage, github-docs, docs-as-code, reference]
status: stable
sources:
  - id: backstage-catalog
    resource: https://backstage.io/docs/features/software-catalog/
    title: Backstage Software Catalog
  - id: backstage-descriptor
    resource: https://backstage.io/docs/next/features/software-catalog/descriptor-format/
    title: Backstage Catalog Entity Descriptor Format
  - id: backstage-techdocs
    resource: https://backstage.io/docs/features/techdocs/creating-and-publishing/
    title: Backstage TechDocs: Creating and publishing docs
  - id: github-docs-writing
    resource: https://docs.github.com/en/contributing/writing-for-github-docs
    title: Writing for GitHub Docs
generated: { by: human:ast-architecture, at: 2026-10-16T00:00:00+08:00 }
---

# 相邻实践

## Backstage

Backstage 将随代码维护的 YAML 元数据文件作为软件目录的事实来源，再采集并展示为可搜索的目录。其 Component 描述的 `spec.lifecycle` 表达组件的运行生命周期，`metadata.annotations` 承载额外的工具和组织元数据。

AST 借鉴其“可版本控制的事实源 + 派生检索和门户”的思想，但不把 `spec.lifecycle` 当作 SDLC 阶段。OKF 概念的 `x_ast_sdl` 才是知识在六阶段中的适用性事实源。

## GitHub Docs

GitHub Docs 使用 Markdown frontmatter 保存页面可解析元数据，并用导航文件和目录组织浏览结构。AST 同样将概念语义保存在 frontmatter，而将 `index.md` 用于渐进披露与人类浏览。

这两个项目都是相邻架构实践，并不定义或实现 AST Profile，也不表示行业中已存在 OKF + SDLC 阶段适用性的统一标准。

