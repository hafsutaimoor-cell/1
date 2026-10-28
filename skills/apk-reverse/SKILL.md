# pk-reverse — APK Reverse Engineering & Patching (Agent Skill)
## Four rules that override everything else

**R1 — Write the deliverable as a testable sentence before you touch the target.**
"It works" is not the goal; "it works under the stated constraint" is. Root-assisted, live-instrumentation, host-proxy and patched-device results frequently do **not** satisfy a request for an installable artifact that works on a normal phone — and it is easy to present such a result as finished. Write the sentence, re-read it at every checkpoint, and if you cannot meet it, say so plainly and label the privileged workaround a **fallback**, never the deliverable.

**R2 — Change one variable at a time, and keep a control build.**
An experiment that flips two things teaches nothing when it fails, and a failure you cannot attribute will be attributed to the wrong cause. Every "the app rejects X" claim needs its own run, and every patch needs a same-pipeline control that still fails the old way.

**R3 — Never ship or claim an unverified artifact.**
"It assembles" is not "it works"; "the process started" is not "the feature works"; "no error in the log" is not "the check is gone". Install it, launch it, exercise the exact feature you changed, and look at the screen. Prove the device is running the build you made — hash it, do not trust the filename.

**R4 — Identify the owning layer before patching, and re-classify when reality disagrees.**
Ads, paywalls, feature gates, integrity checks and update gates live in different layers (Java, dex, native, Dart/Unity, server). Patching the wrong layer either does nothing or breaks the app.

## Symptom index — a matching row is a stop signal

Each row names a failure that has already been paid for. When a failure matches a row, load the referenced file **before your next attempt**.

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


An agent skill for Android APK reverse engineering, debloating, ad removal, surgical dex patching, repacking, and runtime/server analysis.

It is a skill, not a tutorial: it is written to be loaded by an agent (Claude Code, Codex, or any harness that supports the Agent Skills format) while it works, so it is organized for progressive disclosure — a short decision-oriented SKILL.md, detailed references loaded only when a step needs them, and parameterized scripts you can run directly.

## How an agent is expected to consume this

SKILL.md is deliberately written as a procedure with gates rather than as advice, because the observed failure mode is not ignorance — it is a model reading the whole thing, agreeing with it, and then reasoning from first principles anyway.

So there are four things in the body that are meant to be acted on, not read:

**Four override rules (R1–R4).** Where they conflict with the current plan, they win until evidence overrides them.

**A symptom index.** Each row is a failure that has already been paid for. A matching row is a stop signal: load that file before running another command, rather than after a few more attempts. Reasoning past a known symptom is how the same hours get spent twice.

**Four gates (G1–G4),** each an action with a pass criterion. "I understand the idea" does not clear a gate. They exist so that classification, environment truth and a control build happen before the first patch, not after the third failure.

**A two-strike rule and stop conditions.** Two failures of the same shape mean the model is wrong, not the parameters. The third variant of a hypothesis that already failed twice is where rounds go to die.

And one thing at the end that is meant to be withheld: **"done" has a definition (six items).** A clean log is not one of them. Anything short of all six is a checkpoint, and should be reported as a checkpoint with what remains.

## Disclaimer

For learning, research and authorized security testing only. Every script, reference and recorded result in this repository exists to explain how Android application analysis works, so that practitioners can reason about the tools they already own. Nothing here is a service, a product, or an endorsement of any particular use.

Authorized targets only. Use this on applications you own or have been explicitly permitted to analyze, on public CTF/challenge material, or in a sandbox you control. Analyzing software you have no right to analyze may be unlawful where you live, and that determination is yours to make, not this repository's.

No warranty, no fitness for any purpose. The material is provided as is, without warranty of any kind. Results are recorded as they were measured on one machine at one time; nothing here promises that a route will work on your target, your device, your toolchain or today's app version.

Verify before you trust; back up before you act. Several scripts modify artifacts (dex, APK, .so, stored app data) and some operate on a rooted device. Keep your own copies, work on duplicates, and read SKILL.md's gates before running anything against something you care about.

Your use is your responsibility. The authors and contributors accept no liability for any loss, damage, legal consequence or service interruption arising from the use or misuse of this repository, and are not affiliated with, endorsed by, or acting on behalf of any application, vendor or platform it may be used to examine.

Test data is not distributed here. Samples, dumps and device artifacts are deliberately absent from the tree (.gitignore excludes them) and live only in a local, ignored workspace. Anything you obtain to follow along is yours to keep safe and to delete when you are done with it — follow your local rules and the terms that came with the sample. What this repository does publish is the method and the evidence, with all target identity removed.

No affiliation. Names of tools, libraries, hardening products and public challenge targets appear only to make the material reusable; they belong to their respective owners and this project is not connected to them.

## Reference index and script index

Both tables live in `references/routing.md` — one load gets you every reference file with when to load it, and every script with what it does. They are deliberately not duplicated here: this file is loaded in full every time the skill activates, and those tables are lookup data nobody needs until they have already decided what to do.

The symptom index above stays, because **symptom to file has to be one hop**: when something fails you are recognising a failure, not choosing a file, and a two-hop lookup at that moment is exactly how a stop signal gets skipped.

`references/evidence-summary.md` is the one reference that answers the claim-strength question from inside an installed copy: capability → one-line conclusion → `observed`/`inferred`/`unverified` → the evidence that ships with the skill.

## Non-negotiable constraints

These are defects when violated, not preferences. Each row states the check that catches it, so "did I comply" is a command you can run rather than a judgement you make about yourself.

| Constraint | Why it is absolute | The check |
|---|---|---|
| Inputs are read-only. Work on copies; keep a known-good baseline | an edited original destroys the only reference you can diff against | `git`/hash the original before the first edit; `scripts/apk_diff.py` |
| One variable at a time, with a control build (same pipeline, zero patches) | a compound change that fails teaches nothing, and the failure is attributed to the wrong cause | a control run exists and its result is recorded |
| Verify structure after every dex edit | a patch that assembles and dies at load looks like a patch that worked | `scripts/dex_classdiff.py`: zero differences in class set/access flags for classes you did not intend to change |
| Never patch a widely shared method. Count callers first | a `Long.valueOf`-shaped helper with 30 callers is not an ad-specific hook | `scripts/find_refs.py` on the method you are about to touch |
| Never make an API fail to suppress a UI element | a 404/400 an endpoint's other features depend on takes the whole screen with it | suppress at the data-consumption or render layer instead |
| Neutralise a native terminate path by returning, never by making it not return | a spin stub freezes the caller holding its lock; unrelated threads wedge | return a benign value (`0` over `-1`); never `exit`/`abort`/`pthread_exit` |
| Look before you conclude, and look while you wait | a screen actually looked at answers in one step what coordinate-guessing cannot | capture **and inspect** (`scripts/snap.py`) |
| Bound every command, and calibrate the bound from a measurement | an unbounded call turns a stall into "still working", indistinguishable from progress | time the operation once, record it, derive the deadline |
| Every claim carries a label — `observed`, `inferred`, `unverified` or `unknown` | "probably"/"should be"/"in theory" are not findings | `observed` requires a command and its output |
| "Done" means the user-visible outcome | a blocking dialog still on screen means the task is not done | all six items of §What "done" means |
| Never discard a route on compound evidence | if a failure followed two simultaneous changes, the attribution is a hypothesis | re-run the abandonment single-variable before writing it down |
| Prove the device changed before measuring anything | install success describes the request, not the app on disk | hash the on-device artifact against your build |
| Attribute a failure to the right layer before patching again | a feature-scoped network failure is frequently the app's own TLS/cert problem | run the unmodified original on the same device and network first |

## What "done" means — do not claim it earlier

Every item below must be true before you report completion. Anything less is a **checkpoint** and must be labelled as one, out loud, with what remains. Premature "done" is the most damaging thing you can report, because it ends the investigation while the user believes the problem is solved.

1. **The artifact exists and its identity is recorded** — path plus hash, not a filename.
2. **It was installed and launched on the environment the deliverable sentence names** (G1/R1). If that environment was not available to you, say so and label the result accordingly.
3. **The behaviour you changed is verified changed** — by direct observation of the feature, not by the absence of an error message. "The log is clean" is not evidence; "the screen shows X" is.
4. **The features it touches still work.** You exercised them. A build that starts but whose affected feature is dead is not a result.
5. **The original limitation is stated if any survives** — with the coupling that causes it, so the next person can decide whether to accept it.
6. **Nothing you did leaves the target or the device in a broken state** unless that was the goal, and any privileged workaround is labelled a fallback rather than the deliverable.

If items 1–4 hold but the environment was wrong, you have a **prototype**, not a deliverable. Say "prototype" and name the gap.

## Four gates (G1–G4)

**G1 — Name the deliverable form (testable sentence).**
Before any work, write one testable sentence stating what the artifact is and the constraint it must satisfy (e.g. "an unsigned, installable APK that launches on a normal, unrooted phone and shows no ad banner"). Pass criterion: the sentence is written down and the constraint is checkable by installing and launching the artifact.

**G2 — Establish environment truth.**
Run `python scripts/doctor.py` (or its equivalent) to confirm which tools exist, which scripts can run, and whether anything in the environment is poisoning measurements (clock skew, leftover adb forward/proxy, a device-side frida process already running). Pass criterion: doctor reports a coherent toolchain and a clean environment baseline.

**G3 — Locate the code that actually executes.**
Identify which architecture and which library are actually loaded and executing — not what the manifest ships or what the device claims. Pass criterion: you can point to the specific method/class/.so and its file offset that owns the behaviour you want to change.

**G4 — Record the baseline and the control.**
Hash the original, keep it read-only, and build a same-pipeline control (zero patches) that still exhibits the original behaviour. Pass criterion: a control build exists and its result is recorded, so any later failure is attributable.

"I understand the idea" does not clear a gate.

## Thirteen classification questions

Answer before choosing a route; R4 decides whether you are editing the right layer. A wrong branch does not fail loudly — it produces an artifact that builds, runs, and does the wrong thing.

1. What is the deliverable form, and on what environment must it work?
2. Is the target a single APK, a split APK set, or an App Bundle?
3. Which layer owns the behaviour: Java/dex, native .so, Dart/Flutter, Unity/IL2CPP, React Native, or server?
4. Is the app packed or hardened (packer, custom loader, VMP, Java2C, JNI sinking)?
5. Is there a tamper/integrity/suicide check in the native layer?
6. Does the app derive request-signing keys from its own signing certificate?
7. Is the behaviour server-supplied UI/config (ads, announcements, tabs) or client-side?
8. Is there a version check, forced-upgrade dialog, self-update, or remote-config channel?
9. Is there a sign-in gate, forced binding, or guest-mode wall?
10. Is the network protected by certificate pinning, mTLS, or a custom trust chain?
11. Is the protocol REST, protobuf (with/without schema), gRPC, or QUIC/HTTP3?
12. Does userspace hooking reach the check, or are raw `svc`/init_array-early detection in play?
13. Is the device rooted, and is Frida available?

## The workflow

1. **Classify** — answer the thirteen questions; R4 picks the layer.
2. **Clear gates G1–G4 in order.**
3. **Consult the symptom index** for any failure shape before the next attempt.
4. **Patch at the correct layer** (dex byte-level, smali, native, Dart, server-config decision).
5. **Rebuild, re-sign, align** (`scripts/repack.py`); verify structure (`scripts/dex_classdiff.py`).
6. **Install and launch on the target environment**; verify the behaviour changed by direct observation.
7. **Neutralise version checks / forced upgrades / remote-config** so the patch survives.
8. **Log what you learned** — any failure costing >30 minutes goes to `references/pitfalls.md`.

## Two-strike rule

Two failures of the same shape mean the model is wrong, not the parameters. Do not try a third variant of a hypothesis that already failed twice — return to classification (step 1) and re-check the owning layer.

## Stop conditions — halt and re-classify, do not retry

- The same shape of attempt failed twice (two-strike rule).
- A patch had no effect and you were about to try a third variant of it.
- A new failure has no place in your current model (symptom-index trigger).
- You are about to write off a route as "blocked" without a control build proving the block is the app's doing, not your pipeline's.
- You are about to claim success on absence of errors (see §What "done" means).
- A measurement disagrees with a conclusion you already recorded as settled.
