# Common maintenance policy and exceptions

This reference describes intended operating principles. It does not change
repository settings or replace each project's source, tests, and release gates.
Review the project's own guidance and live GitHub settings before a change.

| Common principle | Application |
| --- | --- |
| Independent repositories | Validate and land changes in each project at its own final source revision. Consumer `packages/core` entries are Git submodules; edit shared source in `browser-core`. |
| Reproducible tools | Keep installs locked, Actions pinned to full commits, and tool versions selected from each project's declared source. Maintain the existing minimum 24-hour dependency release age unless a narrowly documented security exception is approved. |
| Observable checks | Distinguish a completed successful check from skipped, pending, failed, or unavailable states. Retain current security intelligence and product-specific runtime checks when reducing duplicate code analysis. |
| Reviewed settings | Compare each repository's declared `.github/settings.yml` with live protection and required checks. A declaration is not proof that GitHub enforces it. Record each proposed exception and approve external settings changes separately. |
| Limited automation authority | Keep privileged merge, release, deployment, secrets, and artifact promotion in the owning repository. Shared browser automation may install tools but does not inherit those privileges. |

## Product exceptions

| Repository | Preserved boundary |
| --- | --- |
| `browser-core` | Shared source package without npm publication; runtime source and shared automation have independent immutable references. Keep its older supported Node matrix. |
| `wasm-motion-converter` | Local media conversion needs real browser and media artifact checks. Keep Vitest 4 with Stryker 10 until an actual mutation run proves a replacement combination. |
| `xcom-enhanced-gallery` | Host-site behavior and userscript plus Chrome/Firefox extension artifacts need their own validation. |
| `yt-live-chat-overlay` | Canvas/worker chat rendering and userscript plus extension boundaries need their own validation. |
| `DarkReNamer` | Linux and Windows gates differ. Preserve candidate/source/EXE identity, VM evidence, and exact-byte release promotion in the project workflow. |
| `peglin-korean-revised` | Automatic merge stays disabled. Supported-game hashes fail closed; release tags and source eligibility require manual review. Avoid unrequested mass retranslation. |

Live protection and product-specific release controls belong to the owning
repository. A policy edit here does not apply settings. Document a proposed
exception with its repository, reason, scope and review date in the maintainer's
review record before an authorized change to live settings.
