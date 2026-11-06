# Project Knowledge Base

This knowledge base documents the `org.review_board.ereviewboard.core` bundle, the core module of eReviewBoard, an Eclipse Mylyn connector for Review Board.

## Read By Need

| Need                                               | Read                                                                                         |
| -------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| Orient an agent or find the authoritative document | [AGENTS.md](../AGENTS.md) and this index                                                     |
| Understand component ownership and data flow       | [architecture.md](architecture.md)                                                           |
| Build or test the module in context                | [Build and test workflow](workflows/build-and-test.md)                                       |
| Implement a REST/client/task-data change           | [Client and task-data skill](skills/reviewboard-client-and-taskdata/SKILL.md)                |
| Implement a broader feature or refactor            | [Change workflow](workflows/change-workflow.md)                                              |
| Work on Java sources                               | [Java instructions](instructions/java.instructions.md) and [Java style](code_styles/java.md) |
| Change OSGi bundle metadata or dependencies        | [Bundle instructions](instructions/bundle-metadata.instructions.md)                          |

## Knowledge Ownership

- [architecture.md](architecture.md) is the single project map for module purpose, component responsibilities, and important runtime contracts.
- Workflow files own procedures and commands. The skill owns the domain-specific checklist for REST and Mylyn data mapping.
- Scoped instruction files state when guidance applies; code-style files own formatting and compatibility conventions.
- When documentation conflicts with the code or build metadata, the checked-in source and metadata win. Correct the documentation if the discrepancy is lasting and relevant.

## Module At A Glance

- Packaging: Maven `eclipse-plugin`, built within a Tycho reactor.
- OSGi bundle: `org.review_board.ereviewboard.core`; the manifest declares the execution environment and imported/exported packages.
- Main integration: Eclipse Mylyn Tasks and Reviews APIs, Review Board REST API, JSON parsing, and optional SCM-facing API used by sibling integration bundles.
- Parent reactor: `..\pom.xml` relative to this module; it declares Tycho 0.21.0 and uses `ereviewboard.target` as its target platform.
- There are no test sources in this module. Project tests are separate reactor modules.
