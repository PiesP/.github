# Shared Windows validation environment

This is the common operating contract for the prepared Windows VM used by
PiesP projects. It describes environment preparation, exclusive desktop access,
evidence, and recovery. Each repository owns its test scenarios, assertions,
result interpretation, and release decisions. This repository supplies no VM
controller, executable runner, product profile, or live environment inventory.
Its publication does not establish that the current VM is qualified.

The maintainer's unpublished `windows-acceptance` controller and `hyperv-control`
helper are the existing local entry points. Locate their current source, installed
help, configuration, and owner before use. Do not infer a command or option from
this page. Keep connection details, VM identities, and raw results private.

## Prepare and reserve the environment

1. Read the selected repository's current Windows contract and record its source
   revision, relevant submodule revisions, profile, candidate and fixture hashes,
   and the local controller/helper revisions. Decide which ordinary, optional,
   public-site, game, or release scope is actually selected.
2. Inspect the prepared VM and host headroom before changing them: OS and update
   state, resources and disk space, installed tools, browsers and fonts, display
   capability, routes, security controls, and desktop availability. Record
   observed facts separately from desired settings. Qualify a working baseline
   as found; change only a demonstrated gap through its authorized owner.
3. Reserve one VM and one interactive workload at a time across repositories.
   Keep the per-VM lock, browser-profile lease, and guest-side mutex together;
   check their actual owners before recovery. A second run must wait or fail
   before product execution. Do not reuse another run's browser profile or
   desktop session.
4. Verify the intended host and guest identities, trusted SSH host key and
   executable, authenticated RDP endpoint and account, and that both transports
   refer to the intended guest and account. Check the expected VM GUID,
   interactive session ID, and actual desktop state. A failed identity, trust,
   or session check blocks the run; administrative transport is used only when explicitly
   selected and supported by the owning contract.
5. Run a bounded preflight for required tools, writable owned output, protected
   inputs, declared resource/disk headroom, display geometry and DPI capability,
   reboot or update prerequisites, and the selected profile's other capabilities.
   Unsupported mandatory conditions remain blocked or failed according to the
   owning profile. Do not silently substitute headless, host, emulated, or lower
   resolution evidence.

Preserve authenticated profiles, UAC, certificate validation, SSH host-key
checking, and narrowly scoped networking. DarkReNamer's trusted elevated
observer uses the owning project's local-administrator account, while the
candidate GUI runs with its verified linked-medium token. Do not elevate the
candidate GUI to make a check pass. Use existing trusted staging, verifier, and
cleanup boundaries. Keep browser debugging and fixture servers on guest loopback,
and transfer only declared, verified assets.
Ordinary browser runs use fresh task-owned profiles with no host/default profile,
login, cookie, or authentication import. Keep browsers muted where the selected
profile requires it; a muted run gives no audible-output evidence.

## Record and interpret a run

Bind the attempt to source and submodule revisions, candidate/distribution and
fixture digests, controller/helper and profile identity, private VM/baseline
identity, observed OS/browser/runtime versions, resource and display conditions,
raw results, and cleanup. Verify material environment drift again at completion.
Changed conditions require separate attempts rather than one combined verdict.

Store bounded original and partial results in durable, private storage outside
disposable guest roots. Set Linux output directories to owner-only `0700` and
use the owning project's stricter ACL rules where applicable. Preserve failed
attempts even after a later pass. Verify collection before deleting any owned
guest output. Record interrupted runs, incomplete collection, and failed cleanup
as such, with the resources still
owned by the run. Do not disturb unrelated processes or another maintainer's
authenticated session to obtain a clean result.

Report environment readiness, product assertions, visual observation,
public-site observation, and release acceptance separately. A screenshot or
requested display setting does not prove actual per-profile work-area geometry,
window DPI, native contrast, text scale, or reachability. Browser Forced Colors
emulation does not prove Windows high contrast; page screenshots do not prove
native dialogs. Observe each selected display cell, restore task-owned display,
theme, text, and browser settings, and record restoration failures. Apply the
owning profile's passed, failed, blocked, skipped, and not-run semantics without
turning missing mandatory evidence into success.

When an environment guard changes, test harmless refusals before inappropriate
product execution: wrong identity or session, competing lease, missing or
untrusted tool, changed artifact, unwritable output, timeout, incomplete
collection, and failed owned cleanup. Check bounded diagnostics and preservation
of unrelated resources, then run the affected real VM path. Synthetic results
alone do not qualify the environment.

Public-site and duration runs, game validation, physical-device observations,
and release campaigns occur only when selected and authorized by their owning
contracts. A fixture cannot satisfy a public-site assertion, an environment
smoke cannot satisfy product or release acceptance, and unavailable optional
scope is reported as not run.

## Maintain and recover the baseline

Keep a privately inventoried current qualified baseline and previous
recoverable baseline, with hashes, owner, retention decision, and recovery path
for backups, exports, and checkpoints. A checkpoint is not an independent backup
or proof of application cleanup. Keep personal profiles, credentials, signing
keys, and private evidence out of reusable images.

Plan VM allocation, host security, network, trust, credential, and image changes
with exact before/after state, affected resources, preservation, rollback, and
the required maintainer authorization. If a checkpoint is approved, take it
only while the VM is offline and require `ProductionOnly`. Refusal must not fall
back to a memory-bearing Standard checkpoint. Before any authorized restore or
deletion, collect and verify attempt evidence, account for every task-owned
resource, and protect
existing authenticated sessions. Avoid long checkpoint chains and routine
per-test resets.

Refresh Windows, browsers, and tools in a separate maintenance window. Record
the actual versions, inspect current compatibility and security guidance for
that build, reboot as required, run targeted qualification, and promote only
after review. A frozen test campaign does not authorize automatic host security
changes or permanently disabled updates. For interrupted runs, use the current
installed controller's help and ownership checks to choose exact resume and
recovery commands; preserve the first attempt and any failed cleanup receipt.

## Product entry points

| Repository | Owning contract and qualification boundary |
| --- | --- |
| [DarkReNamer](https://github.com/PiesP/DarkReNamer) | [Development](https://github.com/PiesP/DarkReNamer/blob/master/DEVELOPMENT.md), [safety and authoritative VM-Automated v2 profile](https://github.com/PiesP/DarkReNamer/blob/master/SAFETY.md#vm-automated-release-validation), [profile file](https://github.com/PiesP/DarkReNamer/blob/master/config/vm-automated-v2.json), and [distribution](https://github.com/PiesP/DarkReNamer/blob/master/DISTRIBUTION.md) own native GUI, privilege, source/candidate binding, and release evidence. A setup smoke does not replace the v2 campaign. |
| [xcom-enhanced-gallery](https://github.com/PiesP/xcom-enhanced-gallery) | Its [Windows guide](https://github.com/PiesP/xcom-enhanced-gallery/blob/master/validation/windows/README.md) owns installed extension and userscript-manager profiles, privileged downloads, saved-file verification, and cleanup. |
| [yt-live-chat-overlay](https://github.com/PiesP/yt-live-chat-overlay) | Its [Windows guide](https://github.com/PiesP/yt-live-chat-overlay/blob/master/validation/windows/README.md) owns packaged Canvas/Worker behavior and distinct ordinary versus duration launch semantics. |
| [wasm-motion-converter](https://github.com/PiesP/wasm-motion-converter) | Its [Windows guide](https://github.com/PiesP/wasm-motion-converter/blob/master/validation/windows/README.md) owns declared fixtures, real media output and decoding, fallback, cancellation, offline behavior, and cleanup. |
| [browser-core](https://github.com/PiesP/browser-core/blob/master/README.md) | Shared source is checked through affected consumers at their exact gitlinks; it has no separate privileged VM verdict. |
| [peglin-korean-revised](https://github.com/PiesP/peglin-korean-revised/blob/master/README.md) | An optional isolated game run requires the supported source lock, authorized matching game build, isolated saves, and actual plugin/UI observations. Record missing prerequisites as not run. |
| `.github` | This page links the contracts; it owns no executable tooling or product verdict. |

For each selected project, record the current evidence, disposition, limitation,
and owner of any remaining gap. Keep a private gap table with requirement,
observed state, evidence, disposition, owner, authorization, and verification.
Qualify an affected execution path on the real prepared VM with the owning
checks; retain unchanged or optional paths with an accurate reason and evidence
scope. Keep the inventory, gap table, attempt
receipts, and rollback details private. Public handoffs contain only safe source
and tool revisions, summarized results, and limitations.
