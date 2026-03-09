---
schema: https://brainfile.md/v2/board.json
title: "@brainfile/cli"
agent:
  instructions:
    - Task files are individual .md files in board/
    - Completed tasks are in logs/
    - Preserve all IDs
    - Make minimal changes
    - "Build: npm run build"
    - "Test: npm test (Jest, 475+ tests)"
    - "MCP server: brainfile mcp (10 consolidated tools)"
rules:
  context:
    - id: 1
      rule: "CLI and MCP server for brainfile task management"
    - id: 2
      rule: "Publishes to npm as @brainfile/cli (bin: brainfile)"
    - id: 3
      rule: "MCP tools: list_tasks, get_task, search, task_add, task_move, task_patch, task_delete, task_complete, subtask, contract"
    - id: 4
      rule: "Contracts default to draft status — use --ready or contract activate to make dispatchable"
columns:
  - id: todo
    title: To Do
  - id: in-progress
    title: In Progress
---

# @brainfile/cli

CLI and MCP server for brainfile task management. 10 composable MCP tools, contract lifecycle, per-repo board support.

## Key paths
- `src/commands/` — CLI command implementations
- `src/commands/mcp.ts` — MCP tool definitions (10 consolidated tools)
- `src/utils/` — shared utilities, contract spec
- `src/__tests__/` — Jest test suite
