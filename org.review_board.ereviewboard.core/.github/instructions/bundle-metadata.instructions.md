---
applyTo: "META-INF/MANIFEST.MF,build.properties,pom.xml"
---

# Bundle Metadata Instructions

- Treat `META-INF/MANIFEST.MF` as the source of truth for OSGi bundle identity, execution environment, required bundles, exports, activator, and activation policy.
- Treat `build.properties` as the PDE source/output and included-resource declaration.
- The module POM inherits from the parent reactor and uses `eclipse-plugin` packaging. Inspect the parent POM and `ereviewboard.target` before changing Maven/Tycho or dependency configuration.
- Keep imported/exported package changes aligned with actual source use and sibling-bundle consumers. The `internal.scm` export has a friend restriction; preserve it unless the API boundary is intentionally changing.
- Validate metadata changes from the parent reactor with the applicable command in [build-and-test.md](../workflows/build-and-test.md).