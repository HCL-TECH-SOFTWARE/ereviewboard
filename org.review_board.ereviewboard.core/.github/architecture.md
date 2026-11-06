# Architecture

## Purpose And Runtime Context

This module implements the core of eReviewBoard, an Eclipse Mylyn task-repository connector for Review Board. It is an OSGi bundle, not a standalone service. Mylyn supplies repository, task-data, progress-monitor, network-location, and lifecycle APIs; sibling eReviewBoard bundles provide UI and SCM integrations.

The project README describes core support for mapping Review Board attributes, downloading patches, closing/reopening review requests, and posting/discarding reviews. Posting/updating diffs and opening diffs in a compare editor are supported through selected SCM integrations in sibling bundles; keep SCM-specific behavior out of core unless it is genuinely shared.

The authoritative runtime boundary is `META-INF/MANIFEST.MF`: it declares the bundle identity, lazy activation, activator, required Eclipse/Mylyn/JSON/utility bundles, and packages exported for other bundles. `build.properties` declares `src/` as source and `bin/` as output. The module POM inherits version `0.13.5-SNAPSHOT` and `eclipse-plugin` packaging from the parent reactor.

## Request And Data Flow

1. Mylyn invokes `ReviewboardRepositoryConnector` for repository operations such as task-data retrieval, query, synchronization, task creation, and attachment handling.
2. The connector obtains a `ReviewboardClient` from `ReviewboardClientManager`. The manager keeps clients and serializable repository metadata keyed by repository URL and persists the metadata cache in the plugin state area.
3. `RestfulReviewboardClient` implements the service contract. It builds Review Board API paths and parameters, coordinates pagination, and delegates HTTP and response decoding.
4. `ReviewboardHttpClient` owns HTTP request execution, Mylyn network configuration, authentication/session handling, request bodies, and byte responses. `ReviewboardQueryBuilder` and its lower-level `QueryBuilder` build API paths and query parameters.
5. `RestfulReviewboardReader` parses JSON API responses into domain model objects and translates API error payloads into the `ReviewboardException` hierarchy.
6. Connector and mapper classes convert those models to and from Mylyn `TaskData`, attributes, comments, diffs, operations, repository responses, and attachments.

The normal read path is therefore `Mylyn -> connector -> client interface -> REST client -> HTTP -> JSON reader -> model -> mapper -> TaskData`. Keep those responsibilities separate when adding behavior.

## Ownership Map

| Area                                                | Primary owner                                                                                                                             | Responsibility                                                                          |
| --------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Plugin lifecycle, ID, logging, trace and state path | `ReviewboardCorePlugin`, `TraceLocation`                                                                                                  | OSGi activator and plugin-scoped services                                               |
| Mylyn repository/task integration                   | `ReviewboardRepositoryConnector`                                                                                                          | Connects repository and task operations to the client and Mylyn data APIs               |
| Client lifecycle/cache                              | `ReviewboardClientManager`, `ReviewboardClientData`                                                                                       | Per-repository client reuse and cached users, groups, repositories, and server metadata |
| Review Board service contract                       | `client/ReviewboardClient`                                                                                                                | API consumed by connector code; keep implementations substitutable                      |
| REST endpoint orchestration                         | `client/RestfulReviewboardClient`                                                                                                         | Endpoint selection, pagination, server operations                                       |
| HTTP transport and authentication                   | `client/ReviewboardHttpClient`                                                                                                            | Network requests, session cookie, credentials and binary transfers                      |
| Query construction and pagination                   | `client/ReviewboardQueryBuilder`, `QueryBuilder`, `PagedLoader`, `PagedResult`                                                            | URL/query formation and multi-page result accumulation                                  |
| JSON decoding                                       | `client/RestfulReviewboardReader`                                                                                                         | Response validation and JSON-to-model conversion                                        |
| API failures                                        | `exception/`, `client/ErrorCode`                                                                                                          | Typed transport, API, missing-object/resource, and form-data errors                     |
| Review Board domain                                 | `model/`                                                                                                                                  | Review requests, reviews, replies, diffs, comments, repositories, users, and queries    |
| Mylyn task/repository/comment/diff conversion       | `ReviewboardAttributeMapper`, `ReviewboardTaskMapper`, `ReviewboardRepositoryMapper`, `ReviewboardCommentMapper`, `ReviewboardDiffMapper` | Stable mapping between domain objects and Mylyn structures                              |
| Attachments and utility behavior                    | `client/ReviewboardAttachmentHandler`, `util/`                                                                                            | Attachment lifecycle and shared low-level helpers                                       |
| SCM bridge                                          | `internal/scm/SCMFileContentsLocator`                                                                                                     | SCM-facing file-content lookup; consumed by sibling SCM integration bundles             |

## Mapping And Synchronization Contracts

- `ReviewboardAttributeMapper.Attribute` centralizes Review Board task attribute IDs, display names, types, and Mylyn kinds. When adding a task field, keep its mapping and writable/read-only behavior consistent across the attribute mapper and `ReviewboardTaskMapper`.
- `ReviewboardTaskMapper` creates and updates task attributes and exposes task operations for supported status/review actions. Operation availability depends on task completion state.
- `ReviewboardDiffMapper` stores revision and file-diff metadata in nested task attributes and calculates public/draft comment counts. Its attribute IDs are also consumed when users open or inspect diffs; treat them as a data contract.
- `ReviewboardRepositoryConnector` orchestrates task synchronization, including review requests, diffs, reviews/replies, inline comments, screenshots, and attachments. It uses progress monitors and converts domain failures to Eclipse `CoreException`/status logging at the Mylyn boundary.
- `PagedLoader` expects a non-null monitor from its caller and owns page-level progress accounting. It passes a `NullProgressMonitor` to each individual load operation; do not accidentally double-advance the caller's progress monitor.

## Extension And Compatibility Boundaries

- The bundle exports the packages listed in `META-INF/MANIFEST.MF`; changes to exported public types can affect sibling bundles. `internal.scm` is explicitly marked as friend-exported to the Subclipse UI bundle.
- The manifest currently requires `J2SE-1.5` and Mylyn bundle ranges around versions 3.9.x and 2.x for Reviews. These are compatibility constraints, not evidence that newer Java or Eclipse APIs are safe to introduce.
- Dependency changes may require coordinated updates to the bundle manifest, the parent target platform (`ereviewboard.target`), and related integration modules. Consult [bundle metadata instructions](instructions/bundle-metadata.instructions.md).
- API behavior is implemented for the Review Board REST API represented in the current reader/client. Verify server-version behavior against existing API patterns before expanding or changing endpoint assumptions.

## Errors, Progress, And Diagnostics

- The JSON reader is the layer that interprets Review Board API error codes and should preserve typed exceptions where callers need to distinguish cases.
- The HTTP client raises transport/resource errors; connector boundaries are responsible for translating failures to Eclipse status exceptions where required by Mylyn APIs.
- Network and longer-running client methods accept `IProgressMonitor`. Preserve cancellation/progress conventions when adding operations.
- Plugin trace output is gated by Eclipse debug mode and the plugin's `debug` options. Do not log credentials, session cookies, or sensitive response bodies.
