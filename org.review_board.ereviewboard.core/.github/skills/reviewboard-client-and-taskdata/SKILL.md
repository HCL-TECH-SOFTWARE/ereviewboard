---
name: reviewboard-client-and-taskdata
description: Use when changing Review Board REST operations, JSON decoding, domain models, Mylyn task attributes, comments, diffs, attachments, or synchronization behavior in the eReviewBoard core bundle.
---

# Review Board Client And Task Data

Use this workflow for changes that cross the Review Board API and Mylyn task model boundary. For component ownership, see [architecture.md](../../architecture.md).

## Trace The Complete Path

1. Start at the `ReviewboardClient` method or `ReviewboardRepositoryConnector` operation that expresses the behavior.
2. Follow the REST implementation into `ReviewboardQueryBuilder`/`QueryBuilder`, `ReviewboardHttpClient`, and the matching `RestfulReviewboardReader` method.
3. Follow the resulting model into connector orchestration and the applicable task, comment, diff, repository, or attachment mapper.
4. Search consumers of the affected API method or task attribute before changing its type, ID, nullability, visibility, or ordering.

## Preserve The Contracts

- Keep the `ReviewboardClient` interface as the boundary used by the connector. Add behavior there when the connector needs a new service operation; implement it in the REST client and decode response data in the reader.
- Use existing query-builder and pagination patterns for collection endpoints. Review `PagedLoader` progress behavior before introducing a new paging loop.
- Translate Review Board error payloads in the reader into the existing typed exception hierarchy. Do not silently flatten expected missing-resource, missing-object, or invalid-form cases into generic errors.
- Preserve public versus draft review/comment distinctions, including counts and reply handling.
- Treat `ReviewboardAttributeMapper.Attribute` IDs and the nested `diff-*` / `file-*` task attributes as data contracts with mapper consumers.
- Preserve monitor cancellation/progress behavior. Keep credentials and session cookies out of trace and error messages.
- Keep behavior compatible with the API/server versions implied by existing endpoint patterns unless the task explicitly expands support.

## Validation

Add or update a focused test in the appropriate parent-reactor test bundle if one is available. Then use the relevant command in [build-and-test.md](../../workflows/build-and-test.md). When the behavior cannot be exercised without a Review Board server, validate deterministic parsing/mapping logic separately and document the integration coverage gap.