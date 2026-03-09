---
id: task-2
title: Use core compound contract operations in CLI contractRunner
description: |-
  Replace direct contract.status mutations and setTaskContractStatus calls in cli/src/lib/contractRunner.ts with the new compound operations from @brainfile/core@0.15.1.

  The V2 code paths (pickupContractV2, deliverContractV2, validateContractV2) should use:
  - pickupTaskContract() — sets in_progress + moves column to in-progress
  - deliverTaskContract() — sets delivered + moves column to review
  - completeTaskContract() — sets done + archives to logs (validate success path)
  - failTaskContract() — sets failed + adds feedback (validate failure path)

  V1 code paths stay as-is (legacy board format).

  Key behavior change: `brainfile contract validate` on a passing task will now archive it off the board. This is the intended UX fix — done = off the board.

  Also bump @brainfile/core dependency to ^0.15.1 in cli/package.json.
priority: high
tags:
  - contract
  - ux
  - core-upgrade
assignee: implement
relatedFiles:
  - cli/src/lib/contractRunner.ts
  - cli/src/commands/contract.ts
  - cli/src/commands/mcp.ts
  - cli/package.json
subtasks:
  - id: task-2-1
    title: Bump @brainfile/core to ^0.15.1 in cli/package.json
    completed: false
  - id: task-2-2
    title: Replace pickupContractV2 internals with pickupTaskContract()
    completed: false
  - id: task-2-3
    title: Replace deliverContractV2 internals with deliverTaskContract()
    completed: false
  - id: task-2-4
    title: Replace validateContractV2 success path with completeTaskContract()
    completed: false
  - id: task-2-5
    title: Replace validateContractV2 failure path with failTaskContract()
    completed: false
  - id: task-2-6
    title: Update MCP contract tool handlers to pass logsDir for completion
    completed: false
  - id: task-2-7
    title: Update existing contract tests for new archive-on-done behavior
    completed: false
  - id: task-2-8
    title: Add tests verifying column sync on pickup/deliver
    completed: false
createdAt: "2026-03-06T20:35:53.495Z"
updatedAt: "2026-03-07T12:53:33.466Z"
contract:
  status: done
  deliverables:
    - type: file
      path: src/lib/contractRunner.ts
      description: Replace V2 pickup/deliver/validate with core compound ops
    - type: test
      path: src/__tests__/contract.test.ts
      description: Updated tests for archive-on-done and column sync
  validation:
    commands:
      - npm test
  constraints:
    - V1 code paths must remain untouched
    - pickupContractV2 must still return contract context markdown
    - validate success must archive task to logs via completeTaskContract
    - All existing contract tests must pass or be updated for new behavior
  metrics:
    reworkCount: 11
    pickedUpAt: "2026-03-07T12:52:36.236Z"
    baseline:
      src/lib/contractRunner.ts: 7346371ed8445e7a
      src/__tests__/contract.test.ts: 4ee4a1e32a025a2a
    deliveredAt: "2026-03-07T12:53:26.239Z"
    duration: 50
    deliverablePaths:
      - src/lib/contractRunner.ts
      - src/__tests__/contract.test.ts
  evidence:
    assertions: []
    validationPlan:
      intent: smoke
      mode: legacy-raw
      legacy: true
      warnings:
        - validation.commands is treated as a legacy raw-shell alias; prefer validation.profile/checks or validation.raw.commands
      compiledAt: "2026-03-07T12:53:26.240Z"
      passed: true
      steps:
        - id: legacy:smoke:1:1
          label: smoke raw validation 1
          command: npm test
          source: legacy-raw
          legacy: true
          intent: smoke
          passed: true
          output: |-
            > @brainfile/cli@0.17.0 test
            > jest

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-rxJA8i/.brainfile/brainfile.md.v1.bak

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
                  Board config:    /tmp/brainfile-migrate-v2-test-rxJA8i/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-rxJA8i/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-MBTZvO/.brainfile/brainfile.md.v1.bak

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
                  Board config:    /tmp/brainfile-migrate-v2-test-MBTZvO/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-MBTZvO/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-KRP3tH/.brainfile/brainfile.md.v1.bak

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
                  Board config:    /tmp/brainfile-migrate-v2-test-KRP3tH/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-KRP3tH/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Backup: /tmp/brainfile-migrate-v2-test-3eGGjg/.brainfile/brainfile.md.v1.bak

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
                  Completed/logs:  0 files in logs/

                  at migrateBrainfileToV2 (src/commands/migrate.ts:277:11)

              console.log
                  Board config:    /tmp/brainfile-migrate-v2-test-3eGGjg/.brainfile/brainfile.md (config-only)

                  at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

              console.log
                  Backup:          /tmp/brainfile-migrate-v2-test-3eGGjg/.brainfile/brainfile.md.v1.bak

                  at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

              console.log
                Legacy root brainfile detected alongside v2 workspace.

                  at migrateMixedWorkspace (src/commands/migrate.ts:66:15)

              console.log
                  Backed up root file to: /tmp/brainfile-migrate-mixed-tes
            ...[truncated]
          exitCode: 0
          durationMs: 7214
    deltas:
      - path: src/lib/contractRunner.ts
        change: any
        passed: true
        existedBefore: true
        existsNow: true
        baselineHash: 7346371ed8445e7a
        currentHash: 1197269871a20085
      - path: src/__tests__/contract.test.ts
        change: any
        passed: true
        existedBefore: true
        existsNow: true
        baselineHash: 4ee4a1e32a025a2a
        currentHash: 4ee4a1e32a025a2a
    scopeViolations: []
    changedFiles:
      - .brainfile/.gitignore
      - .brainfile/board/task-2.md
      - .brainfile/brainfile.md
      - .brainfile/brainfile.md.v1.bak
      - .brainfile/cache/acp-registry.json
      - .brainfile/logs/task-1.md
      - .brainfile/supervisor.json
      - .github/workflows/release.yml
      - brainfile-core-0.15.1.tgz
      - brainfile.md
      - package-lock.json
      - package.json
      - src/__tests__/add.test.ts
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
      - undefined/brainfile/config.json
    warnings: []
    passed: true
    validatedAt: "2026-03-07T12:53:26.240Z"
  feedback: |-
    Validation step failed (npm test): command exited 1 (> @brainfile/cli@0.17.0 test
    > jest

      console.log
        Backup: /tmp/brainfile-migrate-v2-test-RcO2pH/.brainfile/brainfile.md.v1.bak

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
          Board config:    /tmp/brainfile-migrate-v2-test-RcO2pH/.brainfile/brainfile.md (config-only)

          at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

      console.log
          Backup:          /tmp/brainfile-migrate-v2-test-RcO2pH/.brainfile/brainfile.md.v1.bak

          at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

      console.log
        Backup: /tmp/brainfile-migrate-v2-test-qGBGRY/.brainfile/brainfile.md.v1.bak

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
          Board config:    /tmp/brainfile-migrate-v2-test-qGBGRY/.brainfile/brainfile.md (config-only)

          at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

      console.log
          Backup:          /tmp/brainfile-migrate-v2-test-qGBGRY/.brainfile/brainfile.md.v1.bak

          at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

      console.log
        Backup: /tmp/brainfile-migrate-v2-test-OwU0cE/.brainfile/brainfile.md.v1.bak

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
          Board config:    /tmp/brainfile-migrate-v2-test-OwU0cE/.brainfile/brainfile.md (config-only)

          at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

      console.log
          Backup:          /tmp/brainfile-migrate-v2-test-OwU0cE/.brainfile/brainfile.md.v1.bak

          at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

      console.log
        Backup: /tmp/brainfile-migrate-v2-test-3K0qqt/.brainfile/brainfile.md.v1.bak

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
          Completed/logs:  0 files in logs/

          at migrateBrainfileToV2 (src/commands/migrate.ts:277:11)

      console.log
          Board config:    /tmp/brainfile-migrate-v2-test-3K0qqt/.brainfile/brainfile.md (config-only)

          at migrateBrainfileToV2 (src/commands/migrate.ts:278:11)

      console.log
          Backup:          /tmp/brainfile-migrate-v2-test-3K0qqt/.brainfile/brainfile.md.v1.bak

          at migrateBrainfileToV2 (src/commands/migrate.ts:279:11)

      console.log
        Legacy root brainfile detected alongside v2 workspace.

          at migrateMixedWorkspace (src/commands/migrate.ts:66:15)

      console.log
          Backed up root file to: /tmp/brainfile-migrate-mixed-tes
    ...[truncated])
completedAt: "2026-03-07T12:53:33.466Z"
---

## Description
Replace direct contract.status mutations and setTaskContractStatus calls in cli/src/lib/contractRunner.ts with the new compound operations from @brainfile/core@0.15.1.

The V2 code paths (pickupContractV2, deliverContractV2, validateContractV2) should use:
- pickupTaskContract() — sets in_progress + moves column to in-progress
- deliverTaskContract() — sets delivered + moves column to review
- completeTaskContract() — sets done + archives to logs (validate success path)
- failTaskContract() — sets failed + adds feedback (validate failure path)

V1 code paths stay as-is (legacy board format).

Key behavior change: `brainfile contract validate` on a passing task will now archive it off the board. This is the intended UX fix — done = off the board.

Also bump @brainfile/core dependency to ^0.15.1 in cli/package.json.
