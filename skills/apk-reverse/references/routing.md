# references/routing.md — on-demand inventory
| `scripts/mt_mcp_probe.py` | Probe MT Manager's on-device APK MCP (Streamable HTTP, port 8787): JSON-RPC handshake plus the grouped tool inventory. |
| `scripts/java2c_probe.py` | Collect evidence separating Java2C from an extraction shell, a VMP and ordinary JNI sinking: native density and stub ratio from the dex, JNI_OnLoad/dynamic registration/toolchain strings from the .so, each item labelled strong/medium/weak. |
| `scripts/protobuf_decode_raw.py` | Schema-free protobuf decode: hex / file / stdin to a JSON tree, every length-delimited field kept as a candidate set with ties labelled rather than guessed, plus byte-exact re-encode to check a round trip. |
| `scripts/vmp_diff_harness.py` | Build a labelled opcode-coverage fixture, derive a candidate private-opcode map from an original/hardened dex pair, verify the comparison in a closed loop, render a restored stream as a smali skeleton. |
| `scripts/kernelsu_syscall_mask.py` | Generate a KernelSU/APatch syscall-masking scaffold: installable userspace module skeleton plus KPM/LKM/eBPF kernel-side templates, each with its version gate and an explicit unverified label. |
| `scripts/hw_watch.py` | Invisible memory watchpoints / execution breakpoints on arm64: plan (WCR/BCR encoders), emit (a self-contained on-device C helper), gates, run (build+push+stream hits) and decode (module+offset per hit). |
| `scripts/trace_diff.py` | Differential trace reduction: reduce one trace to runs+histogram, or diff two traces into first-divergence / divergent regions / blocks unique to each side — producer-agnostic. |
| `scripts/ete_capture.py` | CoreSight ETE capture with gates first: the five gates, perf/OpenCSD build recipe, per-thread record wrapper whose --control validation run must pass before any real capture. |
| `scripts/kernel_instrument_scaffold.py` | Generate the kernel-side instrumentation scaffold: KPM/LKM watchpoint + register-rewrite templates and an honest userspace carrier, each with its gate and a NOT COMPILED AND NOT LOADED banner that verify enforces. |
| `scripts/trace_addr2sym.py` | Runtime addresses -> symbols + static offsets, zero-dependency ELF64; accepts hw_watch hits, trace mod+off keys or raw address lists. |
| `scripts/vm_handler_slice.py` | De-virtualize an interpreter trace: dispatcher detection, slicing into handler segments at dispatch entries, signature clustering, guest-opcode trajectory, nested-loop diagnostic for VMs called from VM code. |
| `scripts/rasc_build.py` | Build and verify rasc, the Rust ASC re-implementation: --check what is present, --build clone plus cargo, --verify an APK against droidasc and fail on any class-set difference. |
| `scripts/scan_leaks.py` | Scan a repository for target identity before publishing it: bundle ids, serial-shaped tokens, PATs, inline appkey assignments, literal endpoints, host user paths. Exemptions for everything that must stay; findings carry context; --show-exempt prints why a hit was suppressed; exit 0/1/2. |
| `scripts/svc_scan.py` | Name the syscall behind an inline `svc` and the segment it sits in, which decides whether a libc-level hook can observe the call at all; --context shows neighbours. |
| `scripts/anti_detect_probe.js` | Observer-only Frida probe (patches nothing): path/loader/thread/kill hooks with caller module + offset, environment self-report (TracerPid, frida-named mappings), live streaming. |

## Consistency checks

`python check_routing.py` (at the repository root) checks that SKILL.md and references/routing.md still agree, that every reference file is named here, and that every script is named here. CI runs it.

| `scripts/install_test.py` | Install + launch health check with logcat signal scan. |
| `scripts/frida_probe.js` | Four-layer runtime probe (app net layer + OkHttp + java.net + exceptions). |
| `scripts/run_probe.py` | Inject the probe, stream it to a log file, stay resident. |
| `scripts/tls_check.py` | Strict certificate check for one or more hosts. |
| `scripts/preflight.py` | Environment check before every experiment block (device, root, ABI/translation, clock skew, leftover proxy/forwards, dead server). |
| `scripts/lib_map.py` | What is actually mapped into a live process: per-library path, base, architecture, and whether it came from the APK or was materialized at runtime. |
| `scripts/elf_plt.py` | Resolve a PLT stub to its imported symbol (x86_64 + aarch64) from the relocation table; list a symbol's callers; byte-diff two libraries and name the symbol each changed stub belongs to. |
| `scripts/apk_diff.py` | Entry-level diff of two builds: changed / added / removed, by content hash so same-size replacements are caught. |
| `scripts/native_crash.py` | Locate a native death from a log or tombstone: signal, fault address, registers, frames split app vs system, the faulting instruction, and a flag when the fault looks arranged. |
| `scripts/blob_decode.py` | Search, don't guess, the framing of a stored value (base64/hex x rotation x deflate); re-encode the edited payload. |
| `scripts/snap.py` | Bounded burst screenshots + control-tree capture with a stall detector, and a verdict on whether the tree is usable at all. |
| `scripts/sig_probe.py` | Find the exact signatures[0].toCharsString() value — offline candidates from an APK, or the authoritative read from a device. |
| `scripts/spawn_patch_detach.py` | Spawn under a Frida probe, detach, then launch and capture: under spawn mode the Activity stack often never comes up, and memory writes survive detach while hooks do not. |
| `scripts/hook_patch_only.js` | The minimal probe for spawn_patch_detach.py — neutralise one native death site by offset and report PATCHED. |
| `scripts/dex_dump_validate.py` | Dedupe, validate and rank a directory of dumped dex images: sha256 grouping, header integrity, trivial-body ratio, most-likely-original ranking (--trim for page-aligned /proc/<pid>/mem captures). |
| `scripts/dex_mem_scan.py` | Search memory captures for embedded dex images and extract each at the size its own header declares — for a decrypted dex sitting in an anonymous mapping no maps entry names. |
| `scripts/lsposed_scaffold.py` | Generate a minimal LSPosed/Xposed module project (manifest with xposed meta-data, assets/xposed_init, hook class, gradle-free build notes). |
| `scripts/frida_rpc_serve.py` | Bridge a Frida script's rpc.exports to a local caller with reconnect handling, so a live native function can be called rather than reversed. |
| `scripts/rpc_template.js` | The editable companion to frida_rpc_serve.py. |
| `scripts/stalker_trace.js` | Instruction-level tracing with Frida Stalker: configurable targets, trigger selection, event stream, output-size rules. |
| `scripts/stalker_report.py` | Reduce a stalker_trace.js log to block histograms and call sequences, with an explicit diagnostic for the measured zero-event case. |

## Script inventory

| Script | What it does |
|---|---|
| `scripts/doctor.py` | Run first: capability report + per-script runnability; finds tools installed off-PATH or as runnable jars; surfaces environment facts that poison measurements (clock skew, leftover adb forward/proxy, device-side frida already running). |
| `scripts/dexutil.py` | Dependency-free dex reader: structural walk + exact instruction decode, dex header recompute/verify (correct checksum/signature order), branch-target and operand helpers. Library shared by the dex scripts; runs standalone to dump one method with offsets. |
| `scripts/dex_find_insn.py` | Locate an instruction by decoded semantics and print its exact byte offset with context and both sides of any branch — how you find a patch site instead of guessing offsets. |
| `scripts/dex_patch_bytes.py` | Equal-length byte patches from a JSON spec: semantic match, polarity pin via expect_next, equal-length enforcement, verifier check, dex header recompute, re-decode to prove it landed (--dry-run first). |
| `scripts/dex_check_verifier.py` | Tier-3 check: does any conditional branch target a move-result (bypassing its producer)? Compares two builds and separates pre-existing findings from regressions your patch introduced. |
| `scripts/coldstart.py` | Cold-launch capture: timed screenshot burst + logcat signals + installed-build facts + launch timing; warns when the foreground activity is not your app. |
| `scripts/so_constpatch.py` | Same-length in-place rewrite of an isolated string constant, for redirecting a library load instead of defeating a check. |
| `scripts/smtool.py` | baksmali/smali wrapper with a configurable classpath. |
| `scripts/dexpatch/` | dexlib2 method-level rewriter (for changes that need new instructions). |
| `scripts/patch_smali.py` | Method-body replacement in a smali tree. |
| `scripts/dex_strpatch.py` | Byte-level string patch with a string_ids ordering guard. |
| `scripts/dex_classdiff.py` | Prove a dex edit was surgical. |
| `scripts/dex_strings.py` | Strings/URLs/SDK markers without a decompiler. |
| `scripts/dart_pool_strings.py` | Recover literals from a Dart AOT snapshot (framed entries, one-byte vs UTF-16 split, file offsets, run-length noise filter). |
| `scripts/dart_pprefs.py` | Build/query the object-pool -> code-site index for a Dart snapshot. |
| `scripts/dart_disasm.py` | Annotated windowed disassembly of Dart AOT code + B/BL caller index. |
| `scripts/find_refs.py` | Count callers of a method before patching it. |
| `scripts/repack.py` | Rebuild APK, strip only signatures, keep META-INF/services/, write a 4-byte-aligned archive (resources.arsc STORED+aligned), sign, verify; also split APK / App Bundle sets: inventory, sign every member with one keystore, or merge code/native members into a standalone APK. |
| `scripts/devsh.py` | Quoting-safe ADB shell helper. |
| `scripts/usb_net_proxy.py` | Give an offline device network over USB. |
| `scripts/datastore_inject.py` | Encode/inject AndroidX DataStore preferences safely. |
| `scripts/probe_api.py` | Probe an HTTP API with the right headers. |
| `scripts/grab_crash.py` | Recover stacks hidden by a crash-reporter SDK. |

| `references/membership-and-limits.md` | Server vs client authority; what is and is not patchable. |
| `references/server-api.md` | Probe an app's API; prove who owns the gate. |
| `references/tls-and-cert.md` | Feature-scoped network failures: expired certs, dual trust chains. |
| `references/third-party-builds.md` | Auditing a "cracked"/"modded" APK before trusting it. |
| `references/dex-patching.md` | Patch-layer table + dexlib2 technique in depth. |
| `references/patch-audit.md` | Proving a patch landed and is legal: length-vs-bytes comparison, equal-length-replacement blind spot, verifier-level legality, text-matching patch traps, reporting a missing patch. |
| `references/repack-and-sign.md` | Repack rules, unpack-and-repack, signing, post-install hazards. |
| `references/runtime-data.md` | DataStore / SharedPreferences / SQLite / protobuf; when the app rewrites your edit; decoding a value that looks encrypted. |
| `references/dynamic-frida.md` | Frida setup, version pinning, the four-layer probe, hook strategy. |
| `references/environment.md` | Device/emulator setup, root, ADB, offline devices, log signals, emulator console control and recovery, preflight, look at the screen. |
| `references/verification.md` | The claim ladder; what "done" means. |
| `references/desensitization-and-leak-scans.md` | Publishing discipline: what must be desensitized and what must stay; the do-not-anonymize list; the leak scanner and its exit states; the entry-point file as a prompt surface. |
| `references/rasc-and-droidsaw.md` | The Rust re-implementation of the ASC indexer: measured speedup, identical class sets, the enum shape where it silently drops bodies, how to build and verify. |
| `references/evidence-summary.md` | Capability → one-line conclusion → strength → the evidence you can actually open in an installed copy. Load when a claim's strength decides whether you trust it. |
| `references/pitfalls.md` | The failure catalogue — every entry is a failure that produced a broken artifact while looking completely healthy. Read before building. |
| `references/advanced-unpacking.md` | The dump landed but the bodies are empty: extraction-shell diagnosis by trivial-body ratio, FART-style active invocation, code_item splicing, root-side dump, honest VMP boundary. |
| `references/lsposed-and-modules.md` | The repack is refused, so deliver a system-level hook module instead: module anatomy, gradle-free build chain, scope configuration, verifying injection, the layer a Java module cannot reach. |
| `references/emulation-and-rpc.md` | Call the routine instead of reading it: Unidbg/Unicorn emulation vs service-ifying a live function over Frida RPC. |
| `references/native-dbi-and-deobfuscation.md` | OLLVM shapes, Frida-Stalker traces, trace-to-CFG route, Stalker/QBDI/emulation decision, two measured boundaries. |
| `references/protocol-reverse.md` | Protobuf without a schema, schema recovery from decompiled code, gRPC frame capture, QUIC/HTTP3 limit, native-side certificate pinning. |
| `references/kernel-and-environment-hardening.md` | Userspace hooking provably cannot reach the check: raw `svc`, init_array-early detection, what each root scheme hides, kernel-route map with version gate, when to stop escalating. |
| `references/hardware-watchpoints.md` | Invisible memory watchpoints on arm64: hardware debug slots, ptrace route with honest TracerPid ledger, 8-byte-window alignment, traps, control run every hit is owed. |
| `references/ete-diff-tracing.md` | Hardware execution tracing (CoreSight ETE/TRBE) plus the differential method: five gates, per-thread cs_etm capture, two one-bit-different runs, open flattening families' state machines, xvmp IR-VM shape. |
| `references/hardware-sampling-and-crash.md` | Data flow without a known address (SPE), crash forensics when the stack is not trustworthy (BRBE / ETE tail), trace_addr2sym.py, PIE vaddr/file-offset rules. |
| `references/native-vm-recovery.md` | Recovering a native VM: existence signals, behavioural table separating stacked CFF/VM dispatchers, relocation-recovered dispatch tables, register anchoring, nested-VM segment stacks, STORE-backward operand slicing, clean-re-VM validation loop. |
| `references/kernel-event-injection.md` | The driver as your hands inside the target: watchpoints armed from the kernel, register rewrites at the trap, primitive map per route (KPM/LKM/eBPF/Zygisk), composition recipes. |
| `references/on-device-tooling.md` | Working from the phone itself: MT Manager edit/repack/sign and its APK MCP, LSPosed Manager, Termux+frida, on-device data inspection. |
| `references/java2c-and-jni-sinking.md` | Java2C and JNI sinking: table separating landing shell / extraction shell / VMP / Java2C / JNI sinking, why the code is in the `.so` and never in a dumped dex, why a `Java_*` symbol search comes back empty. |
| `references/split-apk.md` | App Bundle / split APK sets: what the set is, pulling it off a device, merging into one APK vs signing the set as a unit, install refusals. |
| `references/vmp-differential-analysis.md` | Known-plaintext differential for a real Dex VMP: which links can be automated, coverage a compiled fixture can reach, proving a derived private-opcode table, smali generation, when the route is closed. |
| `references/coverage-and-limits.md` | The claim ladder applied to the skill itself: evidence behind each covered item, dependencies not shipped, what was never exercised. |
| `references/handoff-boundaries.md` | Where this skill ends and another discipline begins: JNI form table, packer-versus-loader split, what "verified" means for each of the four deliverable forms. |

## Reference files (load on demand)

| File | When to load it |
|---|---|
| `references/recon.md` | Identify packer, SDKs, code location, tamper checks; unpacking. First reference after classification. |
| `references/server-config-and-updates.md` | The most common shape of "ad" and the one usually mis-diagnosed: server-supplied UI the client renders. Two-layer fetch that proves it; patch the decision not the data. |
| `references/byte-level-patching.md` | Equal-length byte edits; locating an instruction's exact offset; instruction-width traps; neutralise vs redirect a branch; dex header integrity field order; verifier move-result rule. |
| `references/packers.md` | Hardened targets: rejection signals, measuring the validation boundary with single-variable tests, choosing a native host. |
| `references/code-virtualization-and-custom-linkers.md` | The layer between "packed" and "clean": whole classes turned into `native` declarations, private loader, embedded self-decrypting payload, Java-layer "signature killer", keep-it/drop-it deadlock, string-redirect technique. |
| `references/framework-runtimes.md` | Flutter / React Native / Unity: which layer owns the UI, how to find logic when there are no symbols (string encoding traps). |
| `references/dart-aot.md` | Dart AOT in depth: version pinning, building a matching decompiler, object pool and reference indexes, locating and patching. Begins with the snapshot-decoding front end (aotopsy or blutter). |
| `references/native-and-so.md` | .so hosts, DT_NEEDED vs JNI_OnLoad, relocation limits, replacing Java methods natively, which ABI/library is actually loaded and executing. |
| `references/native-tamper-and-suicide.md` | How a hardened library kills its own process: visible mechanisms, telling which fires, finding the site, forged section headers, PT_GNU_EH_FRAME, scanner traps, neutralising safely (never by making it not return). |
| `references/detection-and-anti-analysis.md` | When the app fights back or the tool cannot run: telling detection from a broken environment, deciding by cost, keeping "blocks my analysis" separate from "blocks the deliverable". |
| `references/toolchain.md` | What to install, non-interactive invocation, GUI-only tools, version-alignment traps, working offline, "not on PATH" is not "not installed", which signer to use. |
| `references/long-task-discipline.md` | Live record, conclusion grading, drift control, timeout and wait calibration, deliverable-form drift, captures-you-never-looked-at, long-context decay, handover. |
| `references/ad-removal.md` | Ad taxonomy, wrapper mapping, callback trap, global gates, verification. |
| `references/updates-and-forced-upgrade.md` | Keeping a patched build alive: two-layer patch (no-op the routine, neutralise the comparison), what not to touch, self-update and hot-update/remote-config channels. |
| `references/account-gates.md` | Sign-in walls, forced phone binding, guest mode: client-side gate (patchable) vs account-scoped resource (not); why forging a session is worse than staying signed out. |
| `references/signature-derived-keys.md` | When the app's own signing certificate is used as key material: detection greps, why offline extraction is unreliable, hardcode-then-verify procedure. |


This file is the single load that gives you every reference file with when to load it, and every script with what it does. It mirrors the symptom index in SKILL.md so a stop signal resolves to exactly one file.

Both tables live here (and are deliberately not duplicated in SKILL.md): SKILL.md is loaded in full every time the skill activates, and these tables are lookup data nobody needs until they have already decided what to do.

## Symptom → reference mirror

This is a one-hop lookup: when something fails, recognise the failure shape and load the named file before your next attempt.

| Symptom shape | File to load |
|---|---|
| App builds, launches, renders perfectly, but every signed request is rejected | `references/signature-derived-keys.md` |
| Feature-scoped failure (login, registration, payment) looks like your patch but persists | `references/tls-and-cert.md`, `references/server-api.md` |
| Byte-level string patch: checksums/signatures verify, dex rejected at runtime | `references/byte-level-patching.md` (`string_ids` ordering) |
| Whole-dex smali round-trip: class tables compare clean, blows up at runtime | `references/dex-patching.md` |
| Neutralised a native terminate path by making it not return; app now hangs, no crash record | `references/native-tamper-and-suicide.md` |
| Fault addr 0x4 looks like an ordinary null-dereference but is a deliberate crash | `references/native-tamper-and-suicide.md` |
| Hardened library kills the process; cannot tell which mechanism fires | `references/native-tamper-and-suicide.md` |
| Packed/hardened target: rejection signals, need to identify the packer | `references/packers.md`, `references/recon.md` |
| Memory dump landed but the bodies are empty (extraction-shell skeleton) | `references/advanced-unpacking.md` |
| Java2C suspected; hunting a decrypted DEX that does not exist | `references/java2c-and-jni-sinking.md` |
| Dart AOT: no symbols, need to find business logic | `references/dart-aot.md` |
| Flutter / React Native / Unity: no symbols, logic location unclear | `references/framework-runtimes.md` |
| Native function flattened into a state machine by OLLVM | `references/native-dbi-and-deobfuscation.md` |
| Protobuf without a schema; gRPC; QUIC/HTTP3 | `references/protocol-reverse.md` |
| Native-side certificate pinning ignores system trust store | `references/tls-and-cert.md`, `references/protocol-reverse.md` |
| Raw `svc` syscalls or init_array-early detection; userspace hooking cannot reach the check | `references/kernel-and-environment-hardening.md` |
| Split APK / App Bundle set; install refusals | `references/split-apk.md` |
| Repack refused by independent checks | `references/repack-and-sign.md`, `references/lsposed-and-modules.md` |
| Version check / forced-upgrade dialog / self-update reinstalls original behaviour | `references/updates-and-forced-upgrade.md` |
| Sign-in wall / forced phone binding / guest mode | `references/account-gates.md` |
| Need to call a routine instead of reversing it | `references/emulation-and-rpc.md` |
| Need hardware watchpoints / ETE/TRBE / kernel injection | `references/hardware-watchpoints.md`, `references/ete-diff-tracing.md`, `references/kernel-event-injection.md` |
| Working from the phone itself (MT Manager, LSPosed, Termux) | `references/on-device-tooling.md` |
| Publishing / leak-scanning a findings report | `references/desensitization-and-leak-scans.md` |
