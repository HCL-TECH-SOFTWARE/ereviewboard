# Build And Test Workflow

## Build Context

This directory is one module in the eReviewBoard Maven/Tycho reactor. Its POM inherits from the parent `..\pom.xml`; build it from the parent reactor or pass that POM explicitly. The parent declares Tycho 0.21.0, includes the checked-in `ereviewboard.target` target platform, and lists separate test bundles. There is no Maven wrapper in the parent root.

Use a JDK and Maven installation compatible with this legacy Tycho and Eclipse target platform. The bundle manifest declares `J2SE-1.5`; do not infer that a modern JDK alone guarantees successful target-platform resolution.

## Commands

From this module directory in PowerShell:

```powershell
mvn -f ..\pom.xml -pl org.review_board.ereviewboard.core -am verify
```

This builds the core module and required reactor dependencies. A focused package build, when dependency artifacts are already available, is:

```powershell
mvn -f ..\pom.xml -pl org.review_board.ereviewboard.core package
```

The core module has no test sources. For project-level test coverage, the parent reactor lists `org.review_board.ereviewboard.tests` and `org.review_board.ereviewboard.subclipse.tests`; build the relevant test bundle with its reactor dependencies, for example:

```powershell
mvn -f ..\pom.xml -pl org.review_board.ereviewboard.tests -am verify
```

Choose the Subclipse test bundle only for changes affecting that integration. Check the reactor output to confirm whether the tests actually ran; a successful compile/package alone is not a test result.

## Validation Guidance

- For core Java changes, run the focused core reactor build. If the change affects shared contracts, run the appropriate test bundle or full reactor validation.
- For manifest, target-platform, or dependency changes, validate from the parent reactor because isolated module compilation may not resolve the full OSGi target.
- For documentation-only changes, check Markdown links and whitespace/diff errors; a Java build is not required.
- If Tycho cannot resolve the target platform, report the exact failure and environment assumptions. Do not treat unresolved dependencies as a source-code failure without evidence.
