---
type: Agent Instruction
title: AST OKF-SDLC 知识检索指引
description: AI 检索 AST OKF-SDLC 知识库时使用的最小规则集。
tags: [ast, okf, sdlc, agent-instruction, retrieval]
status: stable
generated: { by: human:ast-architecture, at: 2026-10-17T00:00:00+08:00 }
sources:
  - id: ast-profile
    resource: profile.md
    title: AST OKF-SDLC Profile v1
---

# 检索指引

1. 先按目标阶段筛选 `x_ast_sdl.phases`；工具无法筛选该字段时，使用 `sdl:<phase>` tags。
2. 优先阅读 `applicability: required`，再是 `recommended` 和 `reference`。
3. 读取命中的正文、`sources` 和相关链接后再给出结论。
4. `status: deprecated` 时查找替代文档；未核验或过期内容须说明不确定性。

阶段 ID：`requirements`、`design`、`coding`、`testing`、`deployment`、`maintenance`。

