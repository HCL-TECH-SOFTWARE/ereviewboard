# Java Style And Compatibility

## Compatibility

- The bundle manifest currently declares `J2SE-1.5`. Use language and library features available to that environment unless the task explicitly changes the supported baseline.
- Reuse dependencies already available through the bundle manifest. A new compile-time library also needs a valid OSGi requirement and availability in the parent target platform.

## Source Style

- Preserve the formatting and naming conventions of the file being changed. The historical source has some formatting variation; do not normalize unrelated code.
- Keep methods and edits focused on one layer and one responsibility.
- Use descriptive names consistent with the Review Board/Mylyn domain vocabulary.
- Prefer existing query, pagination, mapping, exception, and utility helpers over parallel implementations.
- Preserve existing API visibility and checked-exception patterns. Avoid changing exported types or method signatures without checking bundle consumers.
- Keep comments concise and focused on non-obvious constraints or intent.
