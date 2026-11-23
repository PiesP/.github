# Browser product optimization and Rust/WASM qualification

## Active implementation round

[Portfolio issue #48](https://github.com/PiesP/.github/issues/48) tracks the original ten decisions below and ten additional implementation candidates in the product issues. The first round established performance baselines for four designs. The original implementation round revisits those four and adds six more. A missed target is an **OPTIMIZE** result. Keep the qualified implementation in production, diagnose the shortfall and work on the next design.

### Work and ownership

| Product and candidate | First implementation or measurement | Design change to investigate after a miss |
| --- | --- | --- |
| [Converter](https://github.com/PiesP/wasm-motion-converter/issues/550) · Packed RGB alpha | Skip writes to the alpha byte when the packed result discards it. | Confirm which readback actually ran and verify the work removed per frame. Attribute remaining packed-RGB cost to conversion and ingress separately. |
| Converter · Native WebP | Pass an eligible opaque, unrotated, unscaled frame directly to the existing native encoder. Compare the ordinary parallel pool and its current worker count. | Separate readback, transfer/queue time, encode and mux. Use a serial direct-draw control, then investigate bounded worker handoff or eligible visible rectangles when the profile supports it. |
| Converter · Software VP8 | Preserve the three-byte input to the owned encoder. Profile packing directly into an admitted WASM destination. | If packing and copy cannot explain the product target, investigate the largest measured software-route cost. Compare at the same quality, preset and resource limits. |
| Converter · GIF | Measure readback, remaining input copy, pixel preparation and histogram work separately. | Compare a bounded destination lease or one pass over the prepared pixels for dithering and histogram updates. Choose by attribution, then prove palette, timing and output parity on both index paths. |
| [Overlay](https://github.com/PiesP/yt-live-chat-overlay/issues/478) · Author height | Use supplied author bounds before invoking Canvas text measurement. | Verify the actual call count, font/baseline restoration and drawn coordinates. If drawing remains expensive, identify the next measured text cost. |
| Overlay · Paid-card layout | Reuse one prepared layout per active card within its renderer. | If full-plan retention adds too much invalidation or memory work, reuse only wrapped body and translation. Test content/size/font changes and release on removal. |
| Overlay · Placement/reflow | Trace arrival, deferral, queueing, reservation and first presentation by ID. Include the known burst seed and independent seeds. | Compare the strongest simple TS with copy-only and a bounded resident-geometry or coarser-batch kernel. If delay is elsewhere, optimize the identified stage. Account for work still pending at measurement end. |
| [Gallery](https://github.com/PiesP/xcom-enhanced-gallery/issues/553) · Blob-backed ZIP | Compute CRC using bounded source reads and retain the immutable member Blob through finalization. | Compare single bounded reads for small entries with slicing large entries. Attribute the peak to cache, bodies, outstanding reads and final output reservation. |
| Gallery · DOM ownership | Reuse an element's owner within the uninterrupted synchronous pass. Revalidate the clicked tile after awaiting source resolution. | If source probes dominate first display, trace the selected tile and neighbours independently. Compare scheduling only with both gallery-open and save latency measured. |
| Gallery · CRC and archive pipeline | Compare incremental CRC in an already required read with the existing pipeline. Reuse the established small control and look for permitted, representative larger inputs. | If isolated CRC cannot supply the required saving, identify the critical path through reads, scheduling and finalization. A combined design may improve the archive while a pure-kernel candidate remains open for new evidence. Record the contribution and disposition of each. |

### Engineering sequence

1. **Establish controls.** Refresh the current source, submodule, installed artifacts, open issues and prior decisions. Reuse matching worktrees and PRs. Freeze the user action, eligible input, measurable benefit, guardrails and comparison method before timing each variant. Use absolute and relative changes, paired observations and dispersion. Separate first-use and warm runs. Keep uninstrumented controls and the full sample distribution. Quote the specific environment and route.
2. **Land small changes independently.** For discarded alpha, author bounds and duplicate DOM observations, show the work count eliminated, exact semantics, representative controls and no resource or latency regression. These changes can qualify as simplifications even when the component gain is below the threshold used to justify a new subsystem. Keep product latency claims proportional to what was measured.
3. **Improve the boundaries.** The converter owns frame/arena leases until their physical consumer settles, including cancellation. Retain existing fallback capacity and ordered output. The overlay owns timing and fairness in JS, retains layout data only for its useful lifetime, and tests recovery in each executing realm. The gallery owns input, borrowed cache, pending read and final output credits through their actual release points. Preserve limits, partial downloads and source-bound names. Prefer a small change to an existing owner over a new general interface.
4. **Iterate on a miss.** Keep the row open as OPTIMIZE. Quantify the gap, the observed noise and the maximum plausible gain from the timed stage. Identify a specific bottleneck, select the next distinct design and write down its predicted change before rerunning. Improve the measurement if the interval, route or sample was invalid. Retain all prior attempts. Do not pool incompatible workloads, repeat an identical failed trial without new information, or lower a frozen target to declare a pass. A pivot to another stage must preserve the original candidate's recorded status.
5. **Qualify the product.** Run the scoped parity and resource tests, then the owning repository's applicable full checks on the candidate. Use the real product entry point and the [prepared Windows VM](../WINDOWS_VALIDATION.md) for installed-browser evidence. For WMC use the designated private owner clip locally, a small control, and the normal native multi-Worker path. For the two browser add-ons distinguish installed extension, actual Worker/main path, packaged userscript, and available supported browsers. Preserve output bytes where deterministic, user-visible behavior, cancellation, retry, memory ceilings, startup and fallback. Keep private media and authentication/session data local.
6. **Deliver and reconcile.** Use focused product PRs. Fix recoverable CI, review and integration failures. Follow [normal reviewed merge admission](../MAINTENANCE_POLICY.md#reviewed-merge-admission) and verify the landed SHA, artifact and applicable product behavior. Progress independent work while another row is blocked.

### Measuring transfer costs

Use this comparison when an owning issue proposes moving computation or data across the JavaScript/WebAssembly boundary. Start with the same eligible input, work order, output contract, resource limits and strongest equivalent production or optimized JS/TS control.

1. Replay one set of inputs through **the control**, **a copy-only diagnostic**, and **the full candidate**. The diagnostic performs the same packing, directional copies, calls and output marshalling as the candidate. Where the next request depends on the result, feed the recorded decisions back into the diagnostic to preserve the work sequence. Only the real implementations can qualify for shipping. Compare uninstrumented end-to-end actions separately in the installed product.
2. Record the executing realm, actual call count, argument/result sizes, explicitly copied bytes in each direction and live backing sizes. Separate a borrowed view from a byte copy. Identify Worker transfers, serialized payload sizes, browser readback and GPU drawing separately. If the browser does not expose a physical copy, label its size unknown. For memory, record overlapping live allocations and the high-water mark. For time, keep first use, warm use, initialization and the user-visible interval distinct; concurrent stage times can overlap.
3. For each borrowed range, name its owner, when it may move and what event permits reuse. Reserve capacity before exposing a view, and reacquire or validate it after operations that can grow memory. If a consumer can outlive cancellation, hold its lease until it settles. On a missed target, record **OPTIMIZE**, the measured shortfall and the next changed design under the owning issue.

| Product | First comparison | Product acceptance |
| --- | --- | --- |
| [Converter #554](https://github.com/PiesP/wasm-motion-converter/issues/554#measure-gif-and-packet-transfers-before-adopting-the-candidate) and [#550](https://github.com/PiesP/wasm-motion-converter/issues/550#isolate-the-outstanding-webp-boundary-costs) | For one GIF hold split into `s` records of `N` pixels and `L` compressed bytes, writer-owned replay can avoid `(s−1) × N` uploaded index bytes on the JS-index fallback, and `(s−1) × L` repeated reads of WASM output on either route. The writer still emits all records. Report the Rust mapper's internal passes separately. | Compare the same delays, palettes and decoded output, memory admission and Stop/retry. For software VP8, record input copies and borrowed output lifetime. For native WebP, time Worker and GPU handoff independently. [Landed GIF evidence](https://github.com/PiesP/wasm-motion-converter/issues/550#issuecomment-7077100874) · [Hardware WebP comparison](https://github.com/PiesP/wasm-motion-converter/issues/550#issuecomment-7085332456) |
| [Overlay #478](https://github.com/PiesP/yt-live-chat-overlay/issues/478#placement-boundary-experiment) and [#481](https://github.com/PiesP/yt-live-chat-overlay/issues/481#paragraph-abi-and-measurement) | Replay identical geometry revisions, candidate order and decisions to compare repeated full upload, a reusable snapshot or changed slots, and the TS control. For ordinary bidi text, first improve TS, then compare one request per new or revised paragraph. Send grapheme representatives and line ranges; return levels and indices. | Preserve the 4 ms yield budget, 32-attempt limit and per-message first display including pending messages. Measure installed Worker and userscript Main separately. The original four-run WASM arm made 54,231 batches and uploaded 34,802,496 bytes; the copy control did different work. [Installed comparison](https://github.com/PiesP/yt-live-chat-overlay/issues/477#issuecomment-7074966339) |
| [Gallery #553](https://github.com/PiesP/xcom-enhanced-gallery/issues/553#implementation-trial-for-the-crc-boundary) and [#559](https://github.com/PiesP/xcom-enhanced-gallery/issues/559#boundary-experiment-for-item-4) | Keep the immutable ZIP member Blob. Compare serial optimized JS CRC with one admitted staging copy per slice and a synchronous `crc_update(previous, ptr, len) -> u32`. Keep the running CRC per member and lease scratch only for the synchronous copy and call. For GraphQL, profile parsed-only JS first. A Rust trial accepts bounded UTF-8 and returns only the requested post/direct-quote projection. | Charge stream scratch and resident WASM memory together. On the three-video workload, one full input upload would cost 68,626,483 bytes. Compare native saves and final Blob timing with identical inputs. For parsing, include input/output copies, materialization and downstream cache work in time to first usable tile. [CRC measurements](https://github.com/PiesP/xcom-enhanced-gallery/issues/553#issuecomment-7085463289) |

<details>
<summary>Reproduce the byte-stream and WASM view lifetime check</summary>

Run the following in a disposable browser console or a Node ES module. Each read uses a new Blob stream. The ordinary read verifies the destination API on this runtime before testing the WASM view. This checks ownership semantics; use the installed product for timing and peak memory.

```js
async function readInto(view) {
  const reader = new Blob([Uint8Array.of(7, 11, 13, 17)])
    .stream().getReader({ mode: 'byob' });
  try {
    const { value } = await reader.read(view);
    return { bytes: Array.from(value), returnedCapacity: value.buffer.byteLength };
  } finally {
    reader.releaseLock();
  }
}

const input = new ArrayBuffer(65536);
const ordinary = await readInto(new Uint8Array(input, 2, 4));
const memory = new WebAssembly.Memory({ initial: 1, maximum: 2 });
const oldView = new Uint8Array(memory.buffer, 2, 4);
oldView.set([7, 11, 13, 17]);
let wasmRead;
try {
  wasmRead = await readInto(oldView);
} catch (error) {
  wasmRead = error.name;
}
memory.grow(1);
console.log(JSON.stringify({
  ordinary, inputCapacityAfterRead: input.byteLength, wasmRead,
  oldViewBytes: oldView.byteLength,
  refreshedBytes: Array.from(new Uint8Array(memory.buffer, 2, 4))
}));
```

On Node 24.22.3, the ordinary read returned the four original bytes and a 65,536-byte backing. Its original buffer had length zero. The default fixed-length WASM view produced `TypeError` on the BYOB read. After growth, the old view had length zero and a fresh view read the original four bytes. A converter destination accepted by `VideoFrame.copyTo()` and a stream destination accepted by a BYOB reader have different ownership contracts. [Streams pull-into algorithm](https://streams.spec.whatwg.org/#readable-byte-stream-controller-pull-into) · [WebAssembly JS memory buffers](https://webassembly.github.io/spec/js-api/#create-a-fixed-length-memory-buffer)

</details>

### Decisions and completion

| State | Next action or completion condition |
| --- | --- |
| READY / IN PROGRESS | Baseline or implement the named candidate. |
| OPTIMIZE | A target was missed. Record the reason, predicted next design, owner and next comparison. Keep working. |
| READY TO SHIP | Functional, resource and per-candidate acceptance checks pass. Open or update the product PR and complete normal admission. |
| VERIFIED ON MASTER | Confirm the landed revision and required product evidence. Record the actual gain and remaining limits, then close the row. |
| BLOCKED / WAITING FOR EVIDENCE | All presently executable alternatives have been investigated and a named prerequisite prevents the next meaningful action. Identify the missing input, owner and exact resume trigger. Leave the owning issue and tracker open. |

The worker may choose and revise designs. Only an explicit maintainer scope decision can retire an open candidate without a qualified outcome. Failure to meet a threshold, exhaustion of an identical retry, or merging this planning document cannot retire it. Report any unresolved rows honestly at handoff.

### Starting evidence

The [component measurements](evidence/browser-optimization-components.json) include the raw paired WebP and RGB timings, synthetic ZIP comparison and author-height call counts. In one serial Chromium Worker, eight opaque synthetic frames had a 59.885 ms paired median saving with direct drawing. The previous installed Windows control used four native encoder Workers. These results need separate product qualification. Eight synthetic Blob members produced identical archives with 16,780,772 fewer bytes of typed-array member bodies retained by the experimental writer; browser peak memory remains to be measured. Each product issue specifies its own follow-up.

## Historical first round

The original policy, observations and thresholds below document the completed first round. The active follow-up uses the decision rules above. [First-round document at its merged revision](https://github.com/PiesP/.github/blob/44fe58932ed4e68c55ce201d829c5d0456fd0388/plans/browser-rust-wasm-qualification.md)

### Decisions and evidence

| Decision | Disposition | What determined the decision |
| --- | --- | --- |
| Software VP8 input | **RETAIN_CURRENT** | Keep packed RGB. Across 80 timed conversions on the same owned encoder, four-channel input missed the 5% primary latency gate for both first and warm use. Installed Windows Chrome used Canvas pixel readback. Managed Linux Chromium exercised native RGBX. Four-channel input increased the primary WASM reservation by 512 KiB. [Paired measurements, route proof and output checks](https://github.com/PiesP/wasm-motion-converter/issues/549#issuecomment-7075046195) |
| Direct GIF input | **DEFER** | On the 720 × 720 workload, the remaining copy took a median 15.625 ms on first use and 17.290 ms warm. Even the largest copy took less than 0.82% of the shortest corresponding conversion. Reopen if a representative workload reaches both 50 ms and 5%, or if readback alone shows material cost. A direct destination must remain reserved through asynchronous settlement, including after cancellation. [Copy measurements and lease requirements](https://github.com/PiesP/wasm-motion-converter/issues/549#issuecomment-7074499043) |
| Chat placement and reflow | **RETAIN_CURRENT** | The 40-sample Edge baseline identified headroom in Worker bursts. In the 64-sample comparison, the TypeScript trial improved paired frame CPU p95 by 0.45 ms (9.26%), below the 1 ms and 10% gate. WASM gained 0.00 ms over that trial. Keep the shipping renderer. [Main and Worker baseline](https://github.com/PiesP/yt-live-chat-overlay/issues/477#issuecomment-7074710183) · [Four-arm comparison and fairness checks](https://github.com/PiesP/yt-live-chat-overlay/issues/477#issuecomment-7074966339) |
| Gallery ZIP CRC32 | **RETAIN_CURRENT** | The new 40-photo fixture produced a 252,822-byte ZIP. In the existing 2,993,158-byte cohort, the largest warm CRC and table setup cost was 10.20 ms against a required saving of 50 ms and 10%. The workload review found no representative increase in bytes or CRC cost to warrant another benchmark. [Workload review and retained measurements](https://github.com/PiesP/xcom-enhanced-gallery/issues/534#issuecomment-7074481279) |

### Original qualification plan

The sections below preserve the experiment design and its conditional verification steps. The product issue records above identify the checks that ran and the environments they covered.

#### Design and decision sequence

1. Refresh the heads, submodule pins, relevant issues, PRs, product limits and repository instructions. Identify the real caller and record where each input is produced, copied, borrowed, consumed and released. Follow the normal user entry point through the delivered artifact. Identify the executing realm and verify which route ran.
2. Keep browser APIs, async scheduling, UI and recovery under their existing owners. Put only the candidate's bounded computation or buffer handling behind a narrow interface. Express pixel format, stride, valid length and lifetime at the conversion boundary; use message IDs and numeric geometry at the renderer boundary. Reuse current owners and tests. Keep these interfaces product-local by default. Consider sharing code if multiple consumers already need the same behavior and the change reduces maintenance cost.
3. Freeze the comparison before measuring a candidate. Record the baseline source, fixture identity, sample plan, measurement interval, noise rule, minimum worthwhile user-visible benefit and control-workload regression budgets. Use each repository's established thresholds where they apply. Alternate baseline and candidate runs under comparable conditions. Measure first use and warm behavior, including load/compile, copy, scheduling and output costs where applicable. Check unchanged fallback paths. Keep correctness instrumentation outside timed production runs.
4. Start with the simplest correct TypeScript or existing-ABI alternative. If even ideal removal of the measured work cannot meet the target, record the result and stop that experiment. If it can, evaluate the implementation including boundary overhead. Use actual supported browser packages and contexts, with separate results for available browsers and paths. Name any missing coverage. Never turn an unavailable measurement into a zero.
5. Keep production, test-only instrumentation and private raw evidence distinguishable. Publish reproducible commands, source/artifact hashes, fixture properties or digests and sanitized measurements. Keep private media, sessions, identifiers and VM receipts local. Use the [prepared Windows VM contract](../WINDOWS_VALIDATION.md) for installed-browser checks. A single machine or VM must have one owner during a timing run.

#### Converter

On the **software VP8 route**, compare today's packed RGB input with the same owned encoder receiving four-channel input. Start with opaque, unrotated, unscaled RGBX and smart skip off. Count the eliminated JavaScript pass and intermediate storage, the extra byte per pixel copied into WASM, and their effects on peak live and reserved memory. Keep native Canvas as an independent control and report software-route eligibility. Compare the designated local 720 × 720 clip and a small control. Expand a promising result to channel order, padded stride, odd sizes, smart skip, geometry transforms, memory pressure, cancel and retry. Compare logical pixel values, selected frames, timestamps and decoded output, plus byte parity where deterministic. The legacy V8 capture fixes a different comparison and three-byte input. [Current host call](https://github.com/PiesP/wasm-motion-converter/blob/ffd1fadce8951135d748c32b3a5d02b9aba2117e/src/services/webp-encoder-service.ts#L110-L179) · [Current encoder ABI](https://github.com/PiesP/wasm-motion-converter/blob/ffd1fadce8951135d748c32b3a5d02b9aba2117e/crates/owned-vp8/README.md#L339-L354)

For **direct GIF input**, assess the remaining four-byte-per-pixel host copy on an eligible workload. Direct asynchronous writes require a destination lease that lasts through settlement, including cancellation. Prove geometry/color equivalence, safe growth/reuse/release, retained-index and fallback behavior, frame sequence and timing before adoption. Record a trigger if the evidence supports continued deferral. Keep the existing independent GIF implementation, owned demux and adopted VP8 intact. The previous motion-signature experiment, historical GIF rights and regional cache follow-ups have their own decisions. [GIF copy decision](https://github.com/PiesP/wasm-motion-converter/blob/ffd1fadce8951135d748c32b3a5d02b9aba2117e/docs/experiments/496-gif-fusion-results.md#L65-L69) · [Motion-signature measurements](https://github.com/PiesP/wasm-motion-converter/blob/ffd1fadce8951135d748c32b3a5d02b9aba2117e/docs/experiments/483-owner-video.md#L45-L55)

#### Overlay

Measure frame and placement p50/p95, first-visible latency, drain-budget exhaustion, backlog and drops with ordinary chat and repeatable mixed-priority bursts. Separate steady placement from resize/reflow. Capture actual extension Worker traces and packaged userscript main-thread traces; the main HUD's moving average cannot establish Worker or tail timing. Preserve the synchronous drain limits of 4 ms and 32 attempts, along with interruption and fairness behavior. If there is headroom, compare the best simple TypeScript alternative, a boundary-copy control and one bounded batch of IDs and numeric geometry. Verify lane, collision, timing, priority, drops, replay and recovery using the existing parity tests. Test the shipped loader and fallback in each actual execution context before adopting WASM. Keep DOM, shaping, canvas drawing and bidirectional text with their current owners unless additional profiling changes the case. [Queue budget](https://github.com/PiesP/yt-live-chat-overlay/blob/b496a29e151819767be2c6cedba7d92c5e79941f/src/renderer/canvas/drain-batch.ts#L23-L55) · [Canvas/Worker parity test](https://github.com/PiesP/yt-live-chat-overlay/blob/b496a29e151819767be2c6cedba7d92c5e79941f/test/browser/renderer/placement-timing-integration.browser.test.ts#L541-L584)

#### Gallery

The prior Edge cohort measured 10.20 ms at most for CRC plus table setup across ten warm runs on a 2,993,158-byte four-member archive. The established gate requires at least 50 ms and 10% improvement in warm action-to-final-Blob latency. The merged 20/40-item feature supplies a new supported cardinality. Reopen the performance question only for a representative change in total bytes or measured CRC attribution. Check locally available permitted inputs during this campaign. Without qualifying evidence, record retention and finish the row. [CRC result](https://github.com/PiesP/xcom-enhanced-gallery/blob/405496e2539f404e7d707e0fb3bd1f61c70cfdef/docs/experiments/534-transport-followup.md#L90-L121) · [20/40-item PR](https://github.com/PiesP/xcom-enhanced-gallery/pull/541)

If the trigger is met, repeat the end-to-end and CRC interval-union measurements with a small control before writing a kernel. Adapt the historical harness to current source in an isolated experiment. Use the existing five first-use and ten qualified warm observations per variant and the recorded noise rule when running a candidate comparison against the best correct JS. Preserve chunking (up to 1 MiB), yields, aborts, live-byte ceilings, archive member names, member ownership, bytes, sizes and CRCs. Verify actually saved archives in the relevant shipping environments. Link new evidence to the existing decision; open a new implementation issue only if warranted.

#### Verification and handoff

Use each repository's checked-in commands at the tested revision and its normal [merge admission](../MAINTENANCE_POLICY.md#reviewed-merge-admission). Starting points for the converter are the frame-utils, decoder, owned VP8 memory/product and GIF tests, `vp run verify:full` and `vp run test:e2e:resource`. For the overlay, run the placement/timing integration and layout tests, `vp run verify:full`, then restore production artifacts with `vp run build:all:ci` and `vp run check:artifacts`. Gallery product changes use `vp run verify` plus saved-archive checks. A retention decision only needs the evidence required to establish it.

Record **ADOPT** for a qualified implementation and follow its product PR through applicable checks, authorized normal admission and landed-source readback. Record **RETAIN_CURRENT** when the current design wins or the measured upper bound is insufficient. Use **DEFER** for an optional experiment with a precise re-entry condition. Use **BLOCKED** for required evidence or admission that cannot be obtained, and identify the missing capability, owner and next action. Fix recoverable problems and finish independent rows. Keep the tracker open while required work is blocked. Finally reconcile all four decisions (VP8, GIF, overlay and gallery), product PRs, measured limitations and any successor issues in the portfolio tracker.
