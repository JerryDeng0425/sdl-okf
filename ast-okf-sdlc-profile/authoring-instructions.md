---
type: Agent Instruction
title: AST OKF-SDLC 知识创建与修改指引
description: AI 创建或修改 AST OKF-SDLC 知识时使用的治理规则。
tags: [ast, okf, sdlc, agent-instruction, authoring]
status: stable
generated: { by: human:ast-architecture, at: 2026-10-17T00:00:00+08:00 }
sources:
  - id: ast-profile
    resource: profile.md
    title: AST OKF-SDLC Profile v1
---

# 创建与修改指引

创建或修改知识前，阅读 [AST OKF-SDLC Profile](profile.md)。该 Profile 是字段定义、兼容性和词表的唯一权威来源。

## 写入步骤

1. 搜索现有概念；已有合适页面时更新它，而不是创建重复页面。
2. 新概念使用可解析 YAML frontmatter，并提供非空 `type`。
3. 需要阶段披露时，添加 `x_ast_sdl`，其中 `profile` 固定为 `ast/okf-sdlc@1`，`phases` 仅能使用六个受控阶段 ID。
4. 根据该知识实际被使用的时机填写 `phases`；目录范围只提供提示，每篇概念文档必须显式声明自己的范围。
5. 为每个 phase 写匹配的 `sdl:<phase>` tag，使 tags 与 `x_ast_sdl.phases` 双向一致。
6. 用 `type` 表达知识种类，用 `status` 表达文档成熟度；将证据写入 `sources`，将人工确认写入 `verified`。
7. `index.md` 仅作目录清单；子目录使用 `overview.md` 承载机器可读的主题元数据。
8. 修改后说明受影响概念、阶段和验证证据；词表或字段不足时，提交 Profile 变更建议。

`applicability: required` 仅在已有明确的人类授权或批准时使用。

