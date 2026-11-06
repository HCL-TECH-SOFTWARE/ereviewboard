# Agent Guide

This module is the eReviewBoard Mylyn core Eclipse/OSGi bundle. Start with [.github/index.md](.github/index.md), then read only the references relevant to the task.

## Source Of Truth

- Treat Java source, `META-INF/MANIFEST.MF`, `build.properties`, and the POMs as authoritative for behavior, bundle metadata, and build configuration.
- The knowledge base explains the current structure and working practices. Update it when a code change materially changes those facts; do not copy source declarations into multiple documents.
- The parent Maven/Tycho reactor is one directory above this module. Reactor-level build and test commands are documented in [.github/workflows/build-and-test.md](.github/workflows/build-and-test.md).

## Before Changing Code

- Follow the relevant scoped guidance in `.github/instructions/` and `.github/code_styles/`.
- Trace behavior to its owner in [.github/architecture.md](.github/architecture.md) before changing contracts between Mylyn, the client, JSON reader, models, or mappers.
- For Review Board REST or task-data changes, use [.github/skills/reviewboard-client-and-taskdata/SKILL.md](.github/skills/reviewboard-client-and-taskdata/SKILL.md).
- Preserve existing APIs and the bundle's legacy execution environment unless the task explicitly changes compatibility requirements.

## Validation

- Use the narrowest applicable reactor build or test command. Do not claim a test passed unless it was run.
- The core module contains no test sources; project test bundles live in the parent reactor. See the build workflow for exact commands and limitations.
