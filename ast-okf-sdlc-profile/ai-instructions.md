---
type: Agent Instruction
title: AST OKF-SDLC Profile 的 AI 使用指引
description: AI 创建、更新和检索 AST OKF-SDLC 知识时必须遵循的规则。
tags: [ast, okf, sdlc, agent-instruction]
status: stable
generated: { by: human:ast-architecture, at: 2026-10-16T00:00:00+08:00 }
sources:
  - id: ast-profile
    resource: profile.md
    title: AST OKF-SDLC Profile v1
---

# 给 AI 的指引

在创建、修改、审查或检索此知识库时，先阅读 [AST OKF-SDLC Profile](profile.md)。本文件是简明执行规则；Profile 是字段和兼容性的权威定义。

## 创建或修改知识

1. 先搜索现有概念，优先更新已有知识，避免重复页面。
2. 对每个新概念，编写可解析 YAML frontmatter 和非空 `type`。
3. 只在该知识确实需要声明阶段适用性时加入 `x_ast_sdl`；它必须使用 `profile: ast/okf-sdlc@1`。
4. `phases` 仅可使用：`requirements`、`design`、`coding`、`testing`、`deployment`、`maintenance`。
5. 根据知识在各阶段的实际使用时机选择 phases，不要按所在目录默认继承。例如，DFX 目录中的发布 runbook 可只适用于 `deployment`。
6. 对每个 phase 同步 `sdl:<phase>` tag；不得添加未定义的 `sdl:*` tag。
7. `type` 描述知识种类，`status` 描述文档成熟度。不得将两者用作 SDLC 阶段字段。
8. `index.md` 是目录清单。除根 `index.md` 中的 `okf_version` 外，不在 index 写 frontmatter；使用 `overview.md` 承载目录主题元数据。
9. 重要事实记录在 `sources`；人类确认后才新增或更新 `verified`。不得把推测写为已核验事实。
10. 不得自动把 `applicability` 改为 `required`，除非人类已经明确授权或批准。

## 按阶段检索

1. 先筛选 `x_ast_sdl.phases` 包含目标阶段的概念；若工具不支持扩展字段筛选，则用 `sdl:<phase>` tag 作发现入口。
2. 再按 `applicability` 排序：`required` 优先于 `recommended`，优先于 `reference`。
3. 读取命中的概念正文、其 `sources` 和相关链接，不能只凭 tag 推断具体规则。
4. 对 `status: deprecated` 内容，查找其替代文档；对过期或未核验内容，向用户说明不确定性。

## 输出要求

- 每次修改后说明影响的概念、阶段和验证证据。
- 保持变更小且聚焦，不批量重写无关知识。
- 若词表不足或字段语义不明确，提出 Profile 变更建议，不要临时造新 phase 值。

