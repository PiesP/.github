# Common maintenance policy and exceptions

This reference describes intended operating principles. It does not change
repository settings or replace each project's source, tests, and release gates.
Review the project's own guidance and live GitHub settings before a change.

| Common principle | Application |
| --- | --- |
| Independent repositories | Validate and land changes in each project at its own final source revision. Consumer `packages/core` entries are Git submodules; edit shared source in `browser-core`. |
| Reproducible tools | Keep frozen lockfile installs, existing trust and peer policies, Actions pinned to full commits, and tool versions selected from each project's declared source. Maintain the minimum 1,440-minute dependency release age unless a narrowly documented security exception is approved. |
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
trigger. A changed toolchain must leave every existing responsibility and public
entrypoint with a final command, owner and verified behavior. Private helpers
belong with the command they serve. Reuse DarkReNamer's
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
and source/reuse binding for checks that remain. Successful, intentionally
skipped, failed, pending and unavailable checks remain distinct. Mutation-only
workflows, success receipts, caches and tests may be removed with their last
consumer; do not preserve an empty generic deep-check system.

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

### Vite+ migration and mutation-testing retirement

Vite+ is the selected integrated toolchain for applicable JS/TS development,
formatting, linting, testing and building. The migration is being coordinated in
[`browser-core` #69](https://github.com/PiesP/browser-core/issues/69),
[`yt-live-chat-overlay` #256](https://github.com/PiesP/yt-live-chat-overlay/issues/256),
[`xcom-enhanced-gallery` #323](https://github.com/PiesP/xcom-enhanced-gallery/issues/323),
and [`wasm-motion-converter` #346](https://github.com/PiesP/wasm-motion-converter/issues/346).
These links describe planned implementation, not completed migration. Each
repository's manifest, lockfile and contributor command catalog own its exact
selected version, commands, retained-tool reasons and verification evidence.
The identified [Vite+ 1.3.0 release](https://github.com/voidzero-dev/vite-plus/releases/tag/v1.3.0)
does not waive verification of the chosen stable version or its actual npm
release-age eligibility before installation.

The three applications are retiring Stryker and mutation-score gates. Maintained
unit tests, coverage, type checks, lint, browser checks, artifact checks,
security checks and release-source checks remain. Coverage is not equivalent to
mutation testing. Preserve assertion strength, intended test discovery,
TypeScript project boundaries, coverage file sets and threshold semantics,
supported browsers and emitted artifact contracts. Retire superseded tool
dependencies, configuration, commands, workflows, update restrictions and
tool-specific tests after tracing their callers; an unexplained dual-tool
arrangement is incomplete migration work. Retain exact TypeScript project checks,
standalone Playwright, Knip/dpdm, design/i18n/version/artifact/source guards or
narrowly required CSS lint where their named purpose and behavior are verified.
Unmeasured speedups, deletion counts and added test volume are not acceptance
criteria.

Keep compatible Node/pnpm bootstrap and package-manager authority. Vite+ does
not require moving every setting into one file, bundling `browser-core`, or
enabling task caching. If runtime ownership changes, migrate all bootstrap,
release and deployment readers together. `browser-core` is the preferred pilot;
apply validated decisions to YTLC, XEG and WMC as their integration complexity
increases. A shared provider change must land and be verified before a consumer
adopts its new pin. Record old/new `packages/core` gitlinks and shared Action
commit SHAs, with rollback, independently. Keep `browser-core` private with raw
TypeScript exports, no required distribution build or publication, and its
supported Node matrix. Consumers retain privileged workflows, source eligibility,
release/deployment decisions and required-check ownership. Their existing
[prepared Windows contract](WINDOWS_VALIDATION.md) and project scenarios still
govern real browser/VM acceptance; headless and fixture results retain their
separate scope.

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
| `wasm-motion-converter` | Local media conversion needs real browser and media artifact checks. Its coordinated Stryker retirement has no replacement mutation-run prerequisite. |
| `xcom-enhanced-gallery` | Host-site behavior and userscript plus Chrome/Firefox extension artifacts need their own validation. |
| `yt-live-chat-overlay` | Canvas/worker chat rendering and userscript plus extension boundaries need their own validation. |
| `DarkReNamer` | Linux and Windows gates differ. Preserve candidate/source/EXE identity, VM evidence, and exact-byte release promotion in the project workflow. |
| `peglin-korean-revised` | Automatic merge stays disabled. Supported-game hashes fail closed; release tags and source eligibility require manual review. Avoid unrequested mass retranslation. |

Live protection and product-specific release controls belong to the owning
repository. A policy edit here does not apply settings. Document a proposed
exception with its repository, reason, scope and review date in the maintainer's
review record before an authorized change to live settings.

## Shared Windows validation

Follow the [prepared-VM operating contract](WINDOWS_VALIDATION.md) for shared
environment preparation, exclusive desktop access, private evidence, and
recovery. Each project's linked Windows contract controls its scenarios,
verdicts, and release acceptance.
