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
| `browser-core` | TypeScript | Two minimal `.mjs` bootstrap entrypoints run before the selected Node toolchain; review them when runner bootstrap or supported Node versions change. The consumer-impact classifier runs as dependency-free TypeScript after Node selection. |
| `wasm-motion-converter` | TypeScript | The release `.mjs` selector runs before Node setup. Windows TypeScript runs under the qualified portable Node with its complete import closure and media/profile evidence; review that contract when Node or bundled assets change. Retain the browser service worker as a separate runtime contract. |
| `xcom-enhanced-gallery` | TypeScript | The release `.mjs` selector and Git hook guards run before pinned Node setup. Windows validation uses TypeScript; native PowerShell and bounded embedded C# retain Windows API operations. Review these adapters if the native APIs or execution stage change. |
| `yt-live-chat-overlay` | TypeScript | Pre-runtime selectors/hooks, browser-direct fixture JavaScript, and native Windows PowerShell retain their execution contracts. [YT #269](https://github.com/PiesP/yt-live-chat-overlay/issues/269) qualified host ZIP preparation and its test callers under pinned Node 26.9.0, including actual Windows manager installation. [YT #270](https://github.com/PiesP/yt-live-chat-overlay/issues/270) qualified the immutable YT-owned Node 24 SARIF Action in the scanner container. Review these contracts when the ZIP API, store package, transport, runtime, callers or scanner/upload interface changes. Semgrep's own Python runtime remains a separate third-party requirement. |
| `DarkReNamer` | Python for portable tooling; PowerShell for Windows, Hyper-V and UI integration | Windows PowerShell retains compiled guest CRC32 and bounded CLR/native interop and stream operations; review when journal compatibility, native APIs or handle/stream contracts change. CI retains trusted Bash source classification and tree guards before tool setup; review if a trusted pre-tool runtime is established. The Wine gallery was retired. |
| `peglin-korean-revised` | Python | Minimal launch adapters or bounded inline Python where extraction would weaken trusted publication validation. |
| `.github` | Documentation only | No automation runtime or executable tooling. |

YT's host ZIP preparer requires the exact `volta.node` runtime declared in its
[manifest](https://github.com/PiesP/yt-live-chat-overlay/blob/master/package.json).
Its [`prepare-userscript-manager.ts` contract](https://github.com/PiesP/yt-live-chat-overlay/blob/master/validation/windows/README.md#installed-application-profiles)
records the early-development ZIP API and verification scope for that runtime.
The Node 26.9.0 qualification above remains historical evidence. Keep host ZIP
preparation distinct from ordinary Node 22/24 compatibility and the Windows
guest runtime. Older supported Node versions can import the module, but
preparation rejects them before network or filesystem mutation.
The SARIF Action uses GitHub's runner-provided Node 24 in the actual scanner
container; this does not establish `node` in the container shell's `PATH`.
Keep each migration's complete caller/import/fixture closure,
immutable Action ownership, meaningful negative controls and independent runtime
gitlink unchanged unless its owning task requires a verified update.

Direct Windows TypeScript execution requires the owning repository's qualified
portable Node, complete declared import closure, and source-bound VM evidence.
Record deferred and unverified work separately from implemented and verified
dispositions. A blocked check must name the missing capability, responsible
owner, and resolution trigger; unavailable or pending evidence is not a pass.

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

For browser projects, distinguish single-run tests, persistent watch mode,
static checks, ordinary verification and full verification in that local catalog.
State the suites and artifact targets each command includes, plus required
fixtures, browsers and other inputs. Shared names such as `verify` do not imply
equal coverage across projects; a successful command establishes only its
documented, executed scope. Browser, prepared-VM, installed-application and release
acceptance require their owning checks and evidence. Preserve intentional
command differences rather than making every verification command run all suites.
Consult the command documentation in
[`browser-core`](https://github.com/PiesP/browser-core/blob/master/README.md#development),
[`dropconvert`](https://github.com/PiesP/wasm-motion-converter/blob/master/CONTRIBUTING.md#command-catalog),
[`XCOM`](https://github.com/PiesP/xcom-enhanced-gallery/blob/master/CONTRIBUTING.md#command-catalog)
and [`YT`](https://github.com/PiesP/yt-live-chat-overlay/blob/master/CONTRIBUTING.md#command-catalog)
for the current implementations and guarantees.

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

### Comparable footprint evidence

Name the delivered artifact and measurement unit before comparing size. Keep
executable code, resources and debug symbols distinct from installed files and
compressed archives; distinguish browser initial downloads from deferred assets.
Source-only libraries and documentation-only projects may have no independently
delivered binary. Select measurements in the owning project rather than imposing
a shared byte budget or report format.

Bind observations to source revisions, relevant dependency/tool versions,
target/configuration, and input/output hashes or equivalent artifact identity.
Distinguish downloaded release assets from source rebuilds. A historical delta
does not establish its cause: hold relevant inputs constant for attribution or
explain confounders. Missing historical artifacts or toolchains remain unavailable
evidence, not zero-byte baselines.

Preserve a compact baseline/result table, reproduction command, material
tradeoffs and implement/retain/defer decision in the owning PR, issue or existing
documentation. This keeps the reasoning reviewable after temporary artifacts
expire. A supported retain decision is valid; fewer bytes alone do not establish
correctness, compatibility or user benefit. For project-owned examples, see
[Peglin's unchanged player artifacts after compile-reference reduction](https://github.com/PiesP/peglin-korean-revised/issues/46#issuecomment-6381107084)
and [dropconvert's separate initial and first-use loading costs](https://github.com/PiesP/wasm-motion-converter/issues/329#issuecomment-6191454690).
Product-specific runtime acceptance and release/deployment authority remain with
each repository; this guidance adds no executable gate or recurring audit.

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

### Dependency security monitoring

Keep ordinary Dependabot version updates, Dependency Graph alerts/security
updates, and the existing OSV checks distinct. GitHub's
[20 October 2026 support announcement](https://github.blog/changelog/2026-10-20-dependabot-now-supports-npm-12-pnpm-11-and-12-and-bun-1-4/)
documents a limitation for pnpm 12 lockfiles containing two YAML documents:
version updates work, while the graph can omit dependencies and therefore
cannot create their alerts or security updates; existing alerts may close.
Keep those features enabled, but verify actual graph ingestion before claiming
coverage. An empty or closed alert list is not a clean dependency assessment.

The four JavaScript repositories retain their existing weekly and manual
security workflows. Their OSV path scans both lockfile documents, including the
native package-manager bootstrap graph, project direct/transitive dependencies,
and the checked-out core submodule where applicable. Review the actual scan and
report steps, job summary, and uploaded SARIF in GitHub Code Scanning. A
successful changed-path job that skipped scanning is not a fresh assessment;
reuse an earlier scan only after comparing its relevant inputs. Retrieval,
scanner, parsing, and incomplete-result failures remain failed checks.

When a finding applies, track it in the owning repository and submit a reviewed
remediation PR promptly rather than waiting for the ordinary version-update
batch or an automatic security PR. Trace the resolved dependency path, including
transitive dependencies and the package-manager graph. Update the responsible
parent/toolchain or use a narrowly justified override with its owning checks.
For Vite+-managed components, review an aligned Vite+ update or document the
specific component remedy; ordinary update filters do not exempt security work.
Keep toolchain ownership, frozen installs, and the 1,440-minute release-age rule;
any urgent security exception follows the existing narrowly documented approval
process.

Implementation and result-validation owners remain in
[core's OSV guidance](https://github.com/PiesP/browser-core/blob/master/automation/README.md#osv-result-validation)
and the consumer workflow guides for
[dropconvert](https://github.com/PiesP/wasm-motion-converter/blob/master/docs/osv-workflow.md),
[XCOM](https://github.com/PiesP/xcom-enhanced-gallery/blob/master/docs/osv-workflow.md),
and [YT](https://github.com/PiesP/yt-live-chat-overlay/blob/master/docs/osv-workflow.md).
Recheck this qualification after a GitHub ingestion-support announcement or a
material lockfile-format, scanner, or helper change. Keep dated scan/input
matrices in the review record rather than treating package counts as invariants.

### Vite+ migration and mutation-testing retirement

Vite+ is the selected integrated toolchain for applicable JS/TS development,
formatting, linting, testing and building. Implementation and repository-owned
evidence are tracked in
[`browser-core` #69](https://github.com/PiesP/browser-core/issues/69),
[`yt-live-chat-overlay` #256](https://github.com/PiesP/yt-live-chat-overlay/issues/256),
[`xcom-enhanced-gallery` #323](https://github.com/PiesP/xcom-enhanced-gallery/issues/323),
and [`wasm-motion-converter` #346](https://github.com/PiesP/wasm-motion-converter/issues/346).
Each repository's manifest, lockfile and contributor command catalog own its exact
selected version, commands, retained-tool reasons and verification evidence.
Vite+ owns its managed Vite, Vitest, coverage and core-tool versions. Keep Vite+
update proposals visible for reviewed, aligned toolchain changes; exclude only
ordinary version-update proposals for managed components. Keep security alerts
and security updates enabled for those components, subject to the actual
[monitoring coverage and remediation path](#dependency-security-monitoring).

The three applications have retired Stryker and mutation-score gates. Maintained
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
release and deployment readers together. A shared provider change must land and
be verified before a consumer adopts its new pin. Record old/new `packages/core`
gitlinks and shared Action commit SHAs, with rollback, independently. Keep
`browser-core` private with raw
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
