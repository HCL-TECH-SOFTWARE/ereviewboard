---
applyTo: "src/**/*.java"
---

# Java Source Instructions

- Keep Java changes compatible with the execution environment declared in `META-INF/MANIFEST.MF` (`J2SE-1.5`) unless compatibility is explicitly being changed.
- Follow local conventions in the touched file; avoid repository-wide formatting or modernization.
- Keep concerns in their owning layers as described in [architecture.md](../architecture.md).
- Preserve Mylyn progress-monitor and Eclipse status/exception contracts at integration boundaries.
- Use the existing exception types, utility libraries, and collection patterns where applicable; dependency additions require corresponding OSGi/target-platform review.
- Do not log passwords, authentication headers, session cookies, or sensitive response content.
- Add comments only when they clarify non-obvious intent; do not add narration that restates the code.