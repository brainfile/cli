---
id: task-3
title: Validate validation commands on task/contract creation for workspace-relative correctness
description: |-
  When creating tasks with validation commands (via `brainfile add --validation` or `contract attach --validation`), the CLI should detect and warn about commands that use `cd <workspace> &&` prefixes. These break when the agent is dispatched into the workspace directly (the agent's cwd is already the workspace root).

  Live example: `cd supervisor && npm run build` failed because the agent was dispatched into supervisor/ — there's no supervisor/ subdirectory to cd into.

  Options:
  - Warn at creation time: "Validation command contains 'cd supervisor &&' — agents are dispatched into the workspace root, consider using 'npm run build' instead"
  - Auto-strip the prefix with a confirmation prompt
  - Lint existing contracts for this pattern (could be a `brainfile lint` subcommand)

  This should also consider the board's workspace context — if the .brainfile/ lives inside a repo subdirectory, commands should be relative to that subdirectory.
priority: medium
tags:
  - validation
  - input-checking
  - contracts
  - dx
relatedFiles:
  - cli/src/commands/add.ts
  - cli/src/commands/contract.ts
createdAt: "2026-03-07T19:58:09.943Z"
contract:
  status: done
  deliverables:
    - type: file
      path: src/validation/command-lint.ts
      description: Lint validation commands for workspace-relative cd prefixes
    - type: test
      path: src/__tests__/command-lint.test.ts
      description: Unit tests for cd-prefix detection and suggestions
  validation:
    commands:
      - npm run build
      - npm test
  constraints:
    - Warn, do not silently modify user input
    - Must handle patterns like 'cd foo &&', 'cd ./foo &&', 'cd foo/bar &&'
    - Integrate into add and contract attach code paths
  metrics:
    pickedUpAt: "2026-03-07T19:59:54.791Z"
    reworkCount: 0
    baseline:
      src/validation/command-lint.ts: __missing__
      src/__tests__/command-lint.test.ts: __missing__
    deliveredAt: "2026-03-07T20:01:56.244Z"
    duration: 121
    deliverablePaths:
      - src/validation/command-lint.ts
      - src/__tests__/command-lint.test.ts
  evidence:
    assertions: []
    validationPlan:
      intent: smoke
      mode: legacy-raw
      legacy: true
      warnings:
        - validation.commands is treated as a legacy raw-shell alias; prefer validation.profile/checks or validation.raw.commands
      compiledAt: "2026-03-07T20:01:56.245Z"
      passed: true
      steps:
        - id: legacy:smoke:1:1
          label: smoke raw validation 1
          command: npm run build
          source: legacy-raw
          legacy: true
          intent: smoke
          passed: true
          output: |-
            > @brainfile/cli@0.17.0 build
            > tsc && npm run copy-schemas


            > @brainfile/cli@0.17.0 copy-schemas
            > mkdir -p dist/schemas && cp src/schemas/*.json dist/schemas/
          exitCode: 0
          durationMs: 3151
        - id: legacy:smoke:1:2
          label: smoke raw validation 2
          command: npm test
          source: legacy-raw
          legacy: true
          intent: smoke
          passed: true
          output: |-
            > @brainfile/cli@0.17.0 test
            > jest

              console.log
                Moved: /tmp/brainfile-migrate-test-5UCVnM/brainfile.md -> /tmp/brainfile-migrate-test-5UCVnM/.brainfile/brainfile.md

                  at migrateRootBrainfileToDotDir (src/commands/migrate.ts:132:11)

              console.log
                Backup: /tmp/brainfile-migrate-test-5UCVnM/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:166:13)

              console.log
                Migration to v2 complete!

                  at migrateBrainfileToV2 (src/commands/migrate.ts:274:11)

              console.log


                  at migrateBrainfileToV2 (src/commands/migrate.ts:275:11)

              console.log
                  Active tasks:    1 files in board/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:276:11)

              console.log
                  Completed/logs:  1 files in logs/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:277:11)

              console.log
                  Board config:    /tmp/brainfile-migrate-test-5UCVnM/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-test-5UCVnM/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-Ov0uNk/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:166:13)

              console.log
                Migration to v2 complete!

                  at migrateBrainfileToV2 (src/commands/migrate.ts:274:11)

              console.log


                  at migrateBrainfileToV2 (src/commands/migrate.ts:275:11)

              console.log
                  Active tasks:    3 files in board/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:276:11)

              console.log
                  Completed/logs:  1 files in logs/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:277:11)

              console.log
                  Board config:    /tmp/brainfile-migrate-v2-test-Ov0uNk/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-Ov0uNk/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-tyRbqK/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:166:13)

              console.log
                Migration to v2 complete!

                  at migrateBrainfileToV2 (src/commands/migrate.ts:274:11)

              console.log


                  at migrateBrainfileToV2 (src/commands/migrate.ts:275:11)

              console.log
                  Active tasks:    0 files in board/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:276:11)

              console.log
                  Completed/logs:  0 files in logs/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:277:11)

              console.log
                  Board config:    /tmp/brainfile-migrate-v2-test-tyRbqK/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-tyRbqK/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-nIbSOx/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:166:13)

              console.log
                Migration to v2 complete!

                  at migrateBrainfileToV2 (src/commands/migrate.ts:274:11)

              console.log


                  at migrateBrainfileToV2 (src/commands/migrate.ts:275:11)

              console.log
                  Active tasks:    0 files in board/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:276:11)

              console.log
                  Completed/logs:  1 files in logs/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:277:11)

              console.log
                  Board config:    /tmp/brainfile-migrate-v2-test-nIbSOx/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-nIbSOx/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
            ...[truncated]
          exitCode: 0
          durationMs: 7671
    deltas:
      - path: src/validation/command-lint.ts
        change: any
        passed: true
        existedBefore: false
        existsNow: true
        currentHash: db153a3bc1c71839
      - path: src/__tests__/command-lint.test.ts
        change: any
        passed: true
        existedBefore: false
        existsNow: true
        currentHash: fb368c8315be269f
    scopeViolations: []
    changedFiles:
      - .brainfile/.gitignore
      - .brainfile/board/task-3.md
      - .brainfile/brainfile.md
      - .brainfile/brainfile.md.v1.bak
      - .brainfile/cache/acp-registry.json
      - .brainfile/logs/task-1.md
      - .brainfile/logs/task-2.md
      - .brainfile/supervisor.json
      - .github/workflows/release.yml
      - brainfile-core-0.15.1.tgz
      - brainfile.md
      - package-lock.json
      - package.json
      - src/__tests__/add.test.ts
      - src/__tests__/command-lint.test.ts
      - src/__tests__/contract-graph.test.ts
      - src/__tests__/contract.test.ts
      - src/__tests__/draft-default.test.ts
      - src/__tests__/git-helper.test.ts
      - src/__tests__/lint.test.ts
      - src/__tests__/mcp-consolidated.test.ts
      - src/__tests__/move.test.ts
      - src/__tests__/rules.test.ts
      - src/__tests__/schema.test.ts
      - src/__tests__/show.test.ts
      - src/__tests__/template.test.ts
      - src/cli.ts
      - src/commands/add.ts
      - src/commands/contract.ts
      - src/commands/mcp.ts
      - src/commands/migrate.ts
      - src/commands/move.ts
      - src/commands/schema.ts
      - src/index.ts
      - src/lib/contractRunner.ts
      - src/mcp/tools/contract.ts
      - src/utils/config.ts
      - src/utils/contractSpec.ts
      - src/utils/hook-settings.ts
      - src/validation/command-lint.ts
      - undefined/brainfile/config.json
    warnings: []
    passed: true
    validatedAt: "2026-03-07T20:01:56.245Z"
updatedAt: "2026-03-07T20:02:07.078Z"
assignee: implement
completedAt: "2026-03-07T20:02:07.078Z"
---

## Description
When creating tasks with validation commands (via `brainfile add --validation` or `contract attach --validation`), the CLI should detect and warn about commands that use `cd <workspace> &&` prefixes. These break when the agent is dispatched into the workspace directly (the agent's cwd is already the workspace root).

Live example: `cd supervisor && npm run build` failed because the agent was dispatched into supervisor/ — there's no supervisor/ subdirectory to cd into.

Options:
- Warn at creation time: "Validation command contains 'cd supervisor &&' — agents are dispatched into the workspace root, consider using 'npm run build' instead"
- Auto-strip the prefix with a confirmation prompt
- Lint existing contracts for this pattern (could be a `brainfile lint` subcommand)

This should also consider the board's workspace context — if the .brainfile/ lives inside a repo subdirectory, commands should be relative to that subdirectory.
