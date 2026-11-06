# Change Workflow

## 1. Locate The Owner

Use [architecture.md](../architecture.md) to identify the layer that owns the behavior. Read that implementation, its interface or data contract, and nearby callers before editing. Avoid implementing Review Board API parsing in connector code or Mylyn conversion in the HTTP layer.

## 2. Make The Smallest Contract-Coherent Change

- Keep API orchestration behind `ReviewboardClient` and its implementation.
- Keep URL and parameter composition in the query builders; response decoding belongs in `RestfulReviewboardReader`.
- Keep server concepts in `model/`; keep Mylyn attribute and task-operation behavior in mapper/connector classes.
- Preserve progress-monitor, cancellation, typed-exception, and public/draft visibility semantics.
- If a task attribute or nested diff attribute changes, inspect readers and consumers as well as the writer. These attributes are persisted/consumed as Mylyn task data.
- Update OSGi metadata only when the bundle's actual imports, exports, or runtime contract changes.

## 3. Validate And Document

Follow [build-and-test.md](build-and-test.md) for applicable checks. Prefer a regression test in the relevant existing test bundle when the behavior is testable there. If there is no suitable test, state the remaining coverage gap rather than adding an unrelated test harness.

Update the knowledge base only if the change changes a durable ownership, compatibility, or workflow fact. Keep architecture statements in `architecture.md`; avoid repeating them in the index or agent instructions.
