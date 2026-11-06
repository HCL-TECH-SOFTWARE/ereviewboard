# Copilot Instructions

Use [AGENTS.md](../AGENTS.md) as the entry point and [index.md](index.md) to route to detailed knowledge. This file contains agent-wide rules for this module; architecture facts and procedures are owned by their linked documents.

## Working Rules

- Establish the owning layer before editing. Follow [architecture.md](architecture.md), then read the narrowest relevant source and nearby call sites.
- Preserve the existing public Java and OSGi contracts unless the task explicitly requires a contract change. The manifest's execution environment and dependency ranges are compatibility constraints.
- Keep changes local to the requested behavior. Avoid unrelated cleanup, dependency changes, or broad modernization.
- For REST and task-data changes, follow [the client/task-data skill](skills/reviewboard-client-and-taskdata/SKILL.md). For other changes, follow [the change workflow](workflows/change-workflow.md).
- Follow applicable [Java instructions](instructions/java.instructions.md), [bundle instructions](instructions/bundle-metadata.instructions.md), and [Java style](code_styles/java.md).
- Treat server responses, task attributes, and repository settings as external input. Preserve typed errors and do not expose credentials or session data in logs.
- Update these documents only when a lasting project fact, workflow, or compatibility contract changes. Do not duplicate source inventories or procedures already owned elsewhere.

## Validation

Run the narrowest relevant Maven/Tycho validation described in [the build workflow](workflows/build-and-test.md). Report the exact command and outcome, and distinguish build validation from test coverage. Do not claim unrun checks passed.