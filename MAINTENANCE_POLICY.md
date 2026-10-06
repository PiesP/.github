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

## Automation ownership

Use each project's primary language for maintained automation. Retire obsolete
responsibilities before consolidating portable logic. Platform and bootstrap
constraints can justify a smaller adapter in another language.

| Repository | Primary automation language | Reviewable exceptions |
| --- | --- | --- |
| `browser-core` | TypeScript | JavaScript that selects the runtime before installation, or preserves an existing bootstrap compatibility contract. |
| `wasm-motion-converter` | TypeScript | Minimal launch adapters and Windows validation JavaScript while its portable-runtime contract requires it. Preserve media/profile validation. |
| `xcom-enhanced-gallery` | TypeScript | Minimal launch adapters and Windows validation JavaScript while its portable-runtime contract requires it. |
| `yt-live-chat-overlay` | TypeScript | Minimal launch adapters and Windows validation JavaScript while its portable-runtime contract requires it. Retire Python validators/downloaders only after verified equivalence. |
| `DarkReNamer` | Python for portable tooling; PowerShell for Windows, Hyper-V and UI integration | Bounded embedded C# native interop; a retained Bash diagnostic needs a specific caller/platform justification. |
| `peglin-korean-revised` | Python | Minimal launch adapters or bounded inline Python where extraction would weaken trusted publication validation. |
| `.github` | Documentation only | No automation runtime or executable tooling. |

Record each exception in the owning command catalog or review record: its exact
entrypoint, required execution stage/platform, preserved contract, verification,
and concrete review trigger. A temporary unresolved migration stays incomplete;
an unavailable required test does not establish a verified exception. This
procedure does not authorize settings, releases, deployments or new environments.

### Command catalog and inventory

Maintain one concise catalog in existing development or script documentation.
For each public command or related command family, record its purpose,
implementation owner/path, runtime/platform, prerequisites, inputs, outputs and
side effects, applicable checks, and any language exception with its review
trigger. Private helpers belong with the command they serve. Reuse DarkReNamer's
existing tooling-test and bundle registries; do not create a parallel registry.

Inventory these surfaces at the implementation's recorded base commit:

- `scripts/`, distributable automation/Actions and `.github/scripts`;
- Git hooks, package/lifecycle commands and workflow `run:`/inline code;
- validation profiles, bundled assets and embedded languages;
- tests and subprocess callers, including callers outside script directories.

Use the same scope before and after a migration. Report handwritten TypeScript,
JavaScript, Bash, PowerShell, Python and embedded C# separately, with the files
and inline blocks included in each count. Separately identify required runtimes,
tiny launch adapters, generated outputs and third-party tools. Product Rust/C#,
JSON/YAML configuration and generated JavaScript are outside handwritten
automation counts; inline code and code embedded in strings still count in their
actual language. Keep this inventory in documentation or the PR record rather
than a new permanent inventory system. File/LOC/test-count reduction and
unmeasured performance claims are not acceptance criteria.

### Implementation and migration

Keep small modules grouped by purpose, using the primary language and standard
library where practical. Ordinary helper imports must not start processes,
publish artifacts or mutate files. Separate reusable logic from CLI/environment
adapters and preserve arguments, environment, working directory, diagnostics,
exit status, interruption and cleanup. Use behavior-oriented tests through the
real entrypoint for failure and side-effect contracts.

Distinguish execution before dependency installation from execution before
runtime installation. A missing-submodule check must work without the submodule
or `node_modules`; a runtime selector must work on the actual bootstrap runtime
without depending on the runtime it selects. Cover Node-direct TypeScript with
Node-appropriate module resolution and erasable syntax checks, separately from
browser/bundler code, and verify the preserved compatibility routes.

Keep manifest-owned tool versions in their existing authority. Consolidate
remaining source/comment-scraped pins into a small structured source only when
needed, without competing versions. Preserve the reproducible-tool requirements
above, download-before-execution checksum/digest checks and token scrubbing.
Network, parse and integrity failures must remain failures.

Trace callers before moving or retiring a command. Migrate package commands,
workflows, hooks, subprocess tests, type/lint/unused-code checks, changed-path
routing, bundle/profile inventories, generated pins, help and documentation
together. Preserve public commands where practical. Remove transitional wrappers
and duplicate handwritten implementations after acceptance, retaining only
justified public compatibility adapters. Do not introduce `tsx`, `ts-node`, a
task DSL, universal runner or new package/service solely to consolidate scripts.

Establish positive and negative contract fixtures before migrating classifiers,
downloaders or security validators. Preserve conservative routing, scanner
failure handling, strict parsing, filesystem protections, trusted-base execution
and deep-check source/reuse binding. Successful, intentionally skipped, failed,
pending and unavailable checks remain distinct.

Share only identical, stable, unprivileged mechanics through the existing
[`browser-core/automation`](https://github.com/PiesP/browser-core/blob/master/automation/README.md)
surface. Land shared code first; verify one consumer at its final revision before
rolling out reviewed full commit SHAs to the others. Record old/new automation
pins and rollback instructions independently of runtime `packages/core`
gitlinks. Consumer secrets, permissions, events, required-check names and
publication/build policy stay in the owning repository.

Complete each audit item with one explicit disposition: implemented and verified,
already satisfied with evidence, retired with caller analysis, or retained as a
narrowly justified exception with a review trigger. Record tested and landed
source identities, actual commands/results and unexecuted checks. Use small
reviewable changes; maintenance closure does not establish release acceptance.

## UX review

Use the shared
[`Quiet Instruments interaction and visual contract`](https://github.com/PiesP/browser-core/blob/master/docs/DESIGN.md)
to review whether users can understand the current state, identify the next
action, and complete core tasks with a keyboard. Respect supported accessibility
preferences and keep completion, cancellation cleanup, errors, and recovery
feedback consistent with actual behavior. Product copy, layout, renderer tests,
and safety authority stay in their owning repositories.

Apply design principles through the target platform's conventions. A platform
or safety exception needs a specific rationale and a review trigger; mechanical
copying of Apple behavior is not required. DarkReNamer retains its Cancel-default
final confirmation and proposal-only reset, without filesystem Undo. Record
rendered and runtime evidence separately from token/unit checks and human or
assistive-technology acceptance. Use existing product gates rather than adding
a common release matrix or central UI automation system.

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
