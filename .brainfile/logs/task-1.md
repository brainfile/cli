---
id: task-1
title: Implement `contract graph` — DAG-aware batch contract attachment
description: |-
  Add the ability to attach contracts to multiple tasks in a single atomic operation with dependency edges (`dependsOn`). This enables agents to define a full execution graph in one call instead of attaching contracts one-by-one.

  ## Motivation

  The current orchestration workflow is:
  1. Think/refine → create tasks (no contracts)
  2. Attach contracts one at a time → tedious, race-prone with supervisor

  With `contract graph`, step 2 becomes a single atomic call that wires the full DAG.

  ## CLI usage

  ```bash
  brainfile contract graph \
    --task research-1 --deliverable "file:docs/findings.md" \
    --task impl-1 --deliverable "file:src/bridge.ts" --depends-on research-1 \
    --task test-1 --deliverable "test:src/tests/bridge.test.ts" --depends-on impl-1 \
    --ready
  ```

  ## MCP usage

  ```json
  {
    "tool": "contract",
    "action": "graph",
    "tasks": [
      { "task": "research-1", "deliverables": [{ "type": "file", "path": "docs/findings.md" }] },
      { "task": "impl-1", "deliverables": [{ "type": "file", "path": "src/bridge.ts" }], "dependsOn": ["research-1"] },
      { "task": "test-1", "deliverables": [{ "type": "test", "path": "src/tests/bridge.test.ts" }], "dependsOn": ["impl-1"] }
    ],
    "activate": true
  }
  ```

  ## Key behaviors

  - **Atomic**: nothing written until full graph is validated (no partial state)
  - **Cycle detection**: reject graphs with circular dependencies before writing
  - **Missing ref detection**: reject if `dependsOn` references a task ID that doesn't exist on the board
  - **Array-only input**: even a single contract is `tasks: [{ ... }]` — no singular shorthand, nudges agents toward batch usage
  - **`--ready` / `activate: true`**: flip all contracts to ready after writing (default: draft)
  - **Backward compatible**: existing `contract attach` for single tasks still works unchanged

  ## Supervisor integration

  The supervisor tick already has `dependenciesMet()` — extend it to check `task.dependsOn` in addition to `parentId`. Auto-activate drafts whose dependencies are all `done` (new behavior, opt-in via board config or flag).

  ## Visualization

  `brainfile contract graph --show` prints the DAG as an ASCII tree/graph for the current board. The TUI could render this as well.
priority: high
tags:
  - orchestration
  - contracts
  - dag
  - mcp
relatedFiles:
  - core/src/types/base.ts
  - core/src/taskOperations.ts
  - cli/src/commands/contract.ts
  - cli/src/mcp/tools/contract.ts
  - supervisor/src/loop/tick.ts
subtasks:
  - id: task-1-1
    title: "Add `dependsOn: string[]` field to Task type in core (types/base.ts)"
    completed: true
  - id: task-1-2
    title: Parse/serialize `dependsOn` in core task operations and templates
    completed: true
  - id: task-1-3
    title: Cycle detection utility in core (topological sort, reject cycles)
    completed: true
  - id: task-1-4
    title: Add `contract graph` CLI command with multi-task `--task`/`--depends-on` flag parsing
    completed: true
  - id: task-1-5
    title: Add `graph` action to MCP `contract` tool with array-only input
    completed: true
  - id: task-1-6
    title: Extend supervisor `dependenciesMet()` to check `task.dependsOn` alongside `parentId`
    completed: true
  - id: task-1-7
    title: "Auto-activation: supervisor flips draft→ready when all dependsOn tasks are done"
    completed: true
  - id: task-1-8
    title: Add `contract graph --show` ASCII visualization
    completed: true
  - id: task-1-9
    title: "Tests: core cycle detection, CLI graph command, MCP graph action, supervisor dependency resolution"
    completed: true
createdAt: "2026-03-05T21:42:19.284Z"
contract:
  status: done
  deliverables:
    - type: file
      path: core/src/types/base.ts
      description: Add dependsOn string[] field to Task interface
    - type: file
      path: core/src/taskOperations.ts
      description: Parse/serialize dependsOn in task CRUD operations
    - type: file
      path: core/src/graph.ts
      description: DAG cycle detection utility (topological sort)
    - type: file
      path: cli/src/commands/contract.ts
      description: contract graph CLI command with multi-task flag parsing
    - type: file
      path: cli/src/mcp/tools/contract.ts
      description: graph action on MCP contract tool (array-only input)
    - type: file
      path: supervisor/src/loop/tick.ts
      description: Extend dependenciesMet() to check task.dependsOn
    - type: test
      path: core/src/__tests__/graph.test.ts
      description: Cycle detection and topological sort tests
    - type: test
      path: cli/src/__tests__/contract-graph.test.ts
      description: CLI graph command and MCP action tests
  validation:
    commands:
      - cd core && npm test
      - cd cli && npm test
  constraints:
    - dependsOn is optional — existing tasks without it are unaffected
    - Array-only input on MCP graph action — no singular shorthand
    - Atomic writes — validate full DAG before writing any contracts
    - Do not break existing contract attach/pickup/deliver/validate flows
    - Cycle detection must reject and return clear error listing the cycle path
    - Auto-activation (draft→ready when deps done) lives in supervisor tick only, not CLI
  metrics:
    reworkCount: 1
    pickedUpAt: "2026-03-06T00:59:24.778Z"
    deliveredAt: "2026-03-06T01:04:44.348Z"
    duration: 319570
    deliverablePaths:
      - core/src/types/base.ts
      - core/src/taskOperations.ts
      - core/src/graph.ts
      - cli/src/commands/contract.ts
      - cli/src/mcp/tools/contract.ts
      - supervisor/src/loop/tick.ts
      - core/src/__tests__/graph.test.ts
      - cli/src/__tests__/contract-graph.test.ts
  feedback: "sh: 1: cd: can't cd to core"
updatedAt: "2026-03-06T01:40:18.386Z"
assignee: implement
completedAt: "2026-03-06T01:40:18.386Z"
---

## Description
Add the ability to attach contracts to multiple tasks in a single atomic operation with dependency edges (`dependsOn`). This enables agents to define a full execution graph in one call instead of attaching contracts one-by-one.

## Motivation

The current orchestration workflow is:
1. Think/refine → create tasks (no contracts)
2. Attach contracts one at a time → tedious, race-prone with supervisor

With `contract graph`, step 2 becomes a single atomic call that wires the full DAG.

## CLI usage

```bash
brainfile contract graph \
  --task research-1 --deliverable "file:docs/findings.md" \
  --task impl-1 --deliverable "file:src/bridge.ts" --depends-on research-1 \
  --task test-1 --deliverable "test:src/tests/bridge.test.ts" --depends-on impl-1 \
  --ready
```

## MCP usage

```json
{
  "tool": "contract",
  "action": "graph",
  "tasks": [
    { "task": "research-1", "deliverables": [{ "type": "file", "path": "docs/findings.md" }] },
    { "task": "impl-1", "deliverables": [{ "type": "file", "path": "src/bridge.ts" }], "dependsOn": ["research-1"] },
    { "task": "test-1", "deliverables": [{ "type": "test", "path": "src/tests/bridge.test.ts" }], "dependsOn": ["impl-1"] }
  ],
  "activate": true
}
```

## Key behaviors

- **Atomic**: nothing written until full graph is validated (no partial state)
- **Cycle detection**: reject graphs with circular dependencies before writing
- **Missing ref detection**: reject if `dependsOn` references a task ID that doesn't exist on the board
- **Array-only input**: even a single contract is `tasks: [{ ... }]` — no singular shorthand, nudges agents toward batch usage
- **`--ready` / `activate: true`**: flip all contracts to ready after writing (default: draft)
- **Backward compatible**: existing `contract attach` for single tasks still works unchanged

## Supervisor integration

The supervisor tick already has `dependenciesMet()` — extend it to check `task.dependsOn` in addition to `parentId`. Auto-activate drafts whose dependencies are all `done` (new behavior, opt-in via board config or flag).

## Visualization

`brainfile contract graph --show` prints the DAG as an ASCII tree/graph for the current board. The TUI could render this as well.
