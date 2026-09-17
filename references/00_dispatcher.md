# Anti Coaster Lens Filter — Technical Operational Dispatcher

**Framework Author**: William Fitzpatrick (*Writer Science*)  
**Source Lecture**: [I wish I knew this before I started writing online](https://www.youtube.com/watch?v=iCaX5cZ6roo)  
**Parent Collection**: [Master Collection](../../kirby-fitzpatrick-writers-collection/SKILL.md) | [Global Help](../../../kirby-help/SKILL.md)  

---

## 1. Cognitive Foundation: The Novelty Spike & the Cleverness Tax

Fitzpatrick's lecture is built on a distinction that looks like taste advice and is actually a cost model. The writers who spike early — one dazzling paragraph, one unforgettable metaphor, one structurally exotic piece — are not the writers who compound. The differentiator is not the density of clever devices; it is the **refusal of the device that does not earn its place**. Cleverness in writing is a proxy signal: it announces *I know what I am doing* to an audience that rewards visible sophistication within seconds, while the plain sentence that carries the same payload is invisible and unrewarded. The result is a quality curve that oscillates — brilliant, then muddled, then brilliant — because the writer is optimizing for the moments that get noticed rather than the line that holds. The **anti-coaster filter** is the named, deliberate lens applied before publishing: *is this device load-bearing for the reader, or decorative for the writer?* Substance stays flat and high; the ride is what gets removed.

Engineering reproduces this failure with far worse economics, because in code the clever device is permanent, socialized, and paid for by people who never met the author.

The **coaster** in a diff is the anticipation-tense construct: the generic `Repository<T, Id, Filter>` introduced for the one entity that exists; the `PaymentStrategy` interface with one implementation; the in-process event bus with one subscriber; the `config.advanced.*` block with zero readers; the custom DSL parsing a file that has one sentence. Each of these is a **novelty spike** — the exact line a reviewer compliments, the line that appears in the design doc, the line whose author is remembered as senior. Each of them moves a cost that is **deferred and distributed** (every future reader, every future diff, every future incident, forever) to buy a benefit that is **immediate and concentrated** (the author's reputation now).

Three consecutive flattenings sustain the coaster economy, and all three must be broken by an explicit procedure rather than by individual taste:

1. **The reviewer asymmetry.** Approving an abstraction costs a reviewer nothing today. Rejecting one costs the reviewer a political conversation. Reviewers therefore default to approving the peak and commenting on the valley.
2. **The credit asymmetry.** Nobody is promoted for the framework they did not write. The person who ships the plugin registry gets the design-doc credit; the person who ships the four-line function gets a thumbs-up emoji.
3. **The comprehension asymmetry.** The author pays the comprehension cost once (zero — they already understand it). Every subsequent reader pays it in full, and no line item ever records the payment.

The **Cleverness Tax (CT)** is the sum of those recurring payments: minutes-per-future-diff, onboarding days, incident MTTR, porting and test surface, and the number of readers who must be *the right person* for a change to be safe. The lens does not exist to make code boring. It exists to make CT a visible, reviewable line item — the same move Fitzpatrick makes when he refuses a flourish that only the author enjoys.

```text
[COASTER — anticipation-tense design, feature velocity vs. reader cost]

  novelty per diff
      ▲
      │        ╭──╮                    ╭──╮            ╭──╮      ← COASTER
      │       ╱    ╲                  ╱    ╲          ╱    ╲       spikes: "trait",
      │  ╭──╮╱      ╲___╭──╮    ╭──╮ ╱      ╲___╭──╮ ╱            "generic", "bus",
      │ ╱   V            ╲___╱   V  ╲___╱         V ╲___╱           "plugin registry"
      └─────────────────────────────────────────────────────────▶ feature index
        ▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔▔  ← FILTERED
        one legible plateau: substance is the line, not the spikes
```

```text
[ANTI-PATTERN — "we'll need it later"]                 [FILTERED — "what has a caller today"]

  ticket: "add user lookup by id"                        ticket: "add user lookup by id"
        │                                                      │
        ▼                                                      ▼
  ┌──────────────────────────────────────────┐           ┌──────────────────────────────────────────┐
  │ trait Repository<T, Id, Filter>    0 extra│           │ fn get_user(                             │
  │ struct GenericRepo<T, Id, Filter>  1 inst.│           │     conn: &PgConn,                       │
  │ enum Strategy { Direct }           1 var. │           │     id: UserId,                          │
  │ Bus::publish("user.lookup")        1 sub. │           │ ) -> Result<Option<User>, SqlError>      │
  │ config.advanced.cache.strategy="none" 0 rd│           └──────────────────────────────────────────┘
  └──────────────────────────────────────────┘                 │
        │                                                      ▼
        ▼                                              shipped: lookup + 1 fn + 1 test + 1 index
  shipped: lookup + 5 constructs nobody consumes       revisit: OPS-812 "second provider (Stripe),
  cost: 4 indirections to change one SELECT                    scheduled Q3, owner @dana"
```

Nine load-bearing definitions:

1. **Coaster** — any construct whose justification is stated in the *future tense* ("we'll need", "in case", "suppose later"). The tense is the tell; the syntax is not.
2. **Idiomatic Baseline (the boring line)** — what a competent engineer who joined last month would write today using the standard library, the patterns of the adjacent files, and no new dependency. The baseline is discoverable, not authored: look at the last three merged PRs in the same package.
3. **Novelty Spike** — the locally impressive construct in a diff: metaprogramming, an extra type parameter, a hook seam, a config matrix. Diagnostic heuristic: whichever line a reviewer is most likely to praise is the line most likely to be a spike.
4. **Cleverness Tax (CT)** — the recurring, socialized cost of a construct: comprehension minutes per future reader, indirection per future change, symbols the LSP cannot resolve, paths a stack trace cannot show.
5. **Speculative Kernel** — the *only-for-later* part of a construct: the unused type parameter, the empty `else` branch of a match awaiting a variant, the registry with one entry, the `TODO: generalize`.
6. **Consumer** — a caller that exists in the repository *today*, citable by file path, or a signed contract naming the second case with an owner and a date. A roadmap bullet, a "customers might want", and an unimplemented ticket are not consumers.
7. **Deletion Test** — inline the boring path, delete the construct, and observe what breaks. If nothing outside the diff itself breaks, the construct was speculative. The test is cheap *because* the construct has no consumers.
8. **Rent-Paying Complexity** — complexity with a measured constraint behind it: a benchmark, a wire protocol you do not control, genuinely shared mutable state, a real second consumer. Not a coaster. The lens must never touch it.
9. **Anti-Coaster Failure Mode** — dogmatic simplification: deleting a seam that pays rent, inlining duplication past the third occurrence, refusing a benchmarked optimization because "it looked clever." Fitzpatrick's discipline is against *unearned* device, not against device. Symmetry is the safety check: a filter that produces worse software under pressure is the opposite coaster.

---

## 2. Core Transformation Protocols

**The lens (run before any non-idiomatic construct is written, and before any is approved):**

- **Q1 — Consumer.** Name the present caller, in this repo, today, that cannot use the boring path. (Answer in file paths, not personas.)
- **Q2 — Forfeit.** Inline the boring path and delete this construct: what breaks today? "Nothing yet" is a verdict, not a deferral.
- **Q3 — Comprehension.** Can a reviewer who joined last month explain this line from the repository alone — no author, no chat log, no design doc — and can `grep` and the language server find every use?

Two or more failures = the construct does not ship. One failure = it ships only with the written justification and revisit trigger of **P6** and **P10**.

**Protocols:**

1. **P1 — Baseline first, commit first.** Write the idiomatic one-function version, land it, and let the abstraction argue against working code in a separate diff. Never negotiate an abstraction against a blank page; a device that has to beat four working lines usually loses, and that is the point.
2. **P2 — Name the second consumer, in writing, or inline.** No generic parameter, interface, enum variant, hook, or seam lands without a named second case (file path now, or a contract row with owner + date). Single-instantiation generics and single-implementation interfaces are spikes by default, not by opinion.
3. **P3 — Run the deletion test on every new construct.** Delete it, inline the boring path, re-run the same tests. If the suite and the diff are the only things that noticed, the construct is released in the same PR it arrived in.
4. **P4 — Treat every new dependency as a whole coaster.** Standard library first, an existing transitive dependency second, a new package third — and third only with a named bottleneck: a measured profile, a protocol requirement, a maintainer commitment. Hand the intake to the [Active Dialogue Dependency Evaluator](../../kirby-fitzpatrick-active-dialogue-dependency-evaluator/SKILL.md) before the lockfile changes.
5. **P5 — No extension points without an extender.** Event buses, plugin registries, strategy maps, and `dyn` trait objects require a *plugging consumer*. A seam is legal when (a) a second implementation exists, (b) the boundary is one you do not own, or (c) a test double is impossible without it. "Testing" is not a licence to introduce a seam you would not otherwise need.
6. **P6 — Freeze the config surface; require a reader per knob.** Every flag, environment variable, and `advanced:` key becomes public API forever. A new key requires at least one production caller and a documented default; unread keys (`feature_x_enabled: false`, `strategy: "default"`) are rejected outright. Deletion of a config key is a migration, so adding one is a decision, not a convenience.
7. **P7 — Metaprogramming requires a written constraint and a bypass.** Macros, decorators that rewrite signatures, reflection by string name, codegen, dynamic dispatch, and single-sentence DSLs must carry a comment naming the constraint they pay for (protocol, code size, generated contract) and a boring path for the common case. If a human cannot step through it and a stack trace cannot show it, the construct owes the team a justification.
8. **P8 — Duplicate twice; extract on the third occurrence.** Two similar blocks stay duplicated: duplication is locally cheap and locally reversible, whereas the wrong abstraction is neither. On the third occurrence, extract *and* rewrite both existing call sites in the same diff, so the abstraction is verified against its real consumers rather than against the one that motivated it.
9. **P9 — Extend the baseline, not the peak.** When a clever construct needs a third extension, price the boring rewrite against that extension before adding another parameter, hook, or variant. Extension count is the coaster's odometer: at three, the tuple of flags (`{ mode, strategy, legacy }`) *is* the evidence that the design never fit.
10. **P10 — Justify in the PR body and the ADR, never in a six-line apology comment.** If a construct cannot be defended in one sentence a reviewer can verify — *we chose X because Y; the second consumer is Z; here is the removal plan* — it has been used, not designed. A comment block explaining illegibility is a receipt for CT, not a reduction of it.
11. **P11 — Deferrals carry receipts.** Rejecting a construct does not mean rejecting the idea. Replace the speculation with a ticket: the second consumer's name, the trigger condition, the owner, the date, and the removal/rewrite plan. Silence produces the same construct again next quarter, by a different author, with the same missing consumer.

**Transformation table — anti-patterns and clean replacements:**

| Anti-Pattern (Coaster Peak) | Why It Seduces | Clean Idiomatic Replacement |
|---|---|---|
| `Repository<T, Id, Filter>` with one entity | "Adding entities should be free" | Concrete `get_user(conn, id) -> Result<Option<User>, SqlError>`; extract on the third entity (P8) |
| `trait PaymentStrategy` with one implementation | "We may swap providers" | Call the vendor client directly; introduce the trait the week the second provider is scheduled (P2, P5) |
| In-process event bus with one subscriber | "Decoupling" | Direct function call; a bus is earned by a second process or genuine fan-out |
| Builder / fluent chain over a 3-field struct | "Ergonomics" | Struct literal with named fields plus `Default` (P1) |
| String-keyed dispatch / reflection | "Config-driven, no recompiles" | Exhaustive `match` on a closed enum; unknown input handled at one validated adapter ([Semantic Gap Hunter](../../kirby-fitzpatrick-semantic-gap-hunter/SKILL.md)) |
| Custom DSL for a one-sentence config | "Readable declarative config" | Plain TOML/JSON parsed with the standard library |
| Cache layer added before measurement | "Performance" | Benchmark or profile linked in the PR; then cache *with* an invalidation policy, a metric, and an owner |
| `class UserService` that only delegates to `UserRepo` | "Layered architecture" | Call the repo directly; a layer appears when it *translates* (validation, authorization, transaction, idempotency) |
| Microservice split for one consumer | "Scalability, team autonomy" | A module with a clear boundary inside the monolith; split when the scaling or team boundary is real |
| `config.advanced.*` matrix, zero readers | "Flexibility" | Named constants; one env var with one reader and a documented default (P6) |
| `unsafe` / raw-pointer micro-optimization | "We are performance engineers" | Safe iterators; `unsafe` only with a benchmark and a safety comment enumerating the invariants |
| Async/actor machinery in a CPU-bound script | "Modern, scalable" | A synchronous loop; concurrency added where IO actually waits |
| Error type erased to a generic box at a library boundary | "Convenient propagation" | Typed error enum where callers can *branch*, erased only where they cannot |

**Legitimate complexity — do not filter (the lens's own false-positive list):**

| Construct | Why it pays rent | Required evidence |
|---|---|---|
| Generics over a real second type | Two instantiations exist today | Both call sites in the same PR (P2) |
| Seam at a boundary you do not own | Vendor API, wire format, clock, filesystem | A test double that could not exist otherwise (P5) |
| Bitset / arena / buffer reuse | Measured hot path | Benchmark before and after, recorded in the PR |
| `Arc<Mutex<…>>` / lock ordering discipline | State is genuinely shared | Documented invariant map ([3D Architectural Grounding](../../kirby-fitzpatrick-3d-architectural-grounding/SKILL.md)) |
| Generated clients from a declared contract | The contract is the source of truth | Generator version pinned and reproducible |
| Caching with a stated invalidation policy | Measured latency floor | Metric, invalidation rule, owner, removal plan |

---

## 3. Engineering Application Scenarios

### 3.1 Code Reviews — raising the lens without becoming the style police

The failure mode of review is ordering: a reviewer comments on naming and formatting first, the author defends the naming, the conversation exhausts itself, and the abstraction rides in approved. Run the lens **before** the style pass, and run it as questions rather than verdicts.

Reviewer script, in order:

1. **Ask Q1/Q2 plainly.** *"What calls this today apart from the diff?"* then *"If I inline `get_user` and delete the trait, what breaks?"* These are falsifiable — the author either names a caller or does not.
2. **Separate the construct from the author.** "This seam has one implementation and no scheduled second — I'd rather see the direct call land now and file the ticket for the seam" is a scoping comment, not an insult. The corresponding artifact is a *deferral ticket*, per **P11** — never "let's discuss later."
3. **Accept the three legal answers.** (a) The boring path ships and the idea becomes a ticket with an owner and trigger; (b) the author cites the second consumer by path or contract, and the construct stays; (c) the seam is at an unowned boundary and a test double proves it, so it stays with a one-line comment naming the boundary. Anything else is a coaster.
4. **Hold the line on rent-paying complexity.** Do not demand simplification of a benchmarked optimization or a mandated protocol path; deleting rent-paying complexity is the lens's own failure mode, and it is worse than the coaster it replaces.
5. **Leave a cost sentence, not an accusation.** *"Four readers will pay for this indirection on every change; today nobody consumes it."* Cost framing survives disagreement; taste framing escalates it.

Worked resolution of a real review thread:

```text
diff: + trait ChargeStrategy { fn charge(&self, a: Amount) -> Result<Receipt>; }
      + struct StripeStrategy;  impl ChargeStrategy for StripeStrategy { … }
      + let s: Box<dyn ChargeStrategy> = Box::new(StripeStrategy);

review (lens, first):   Q1 — who else implements ChargeStrategy today?  none.
                        Q2 — delete the trait, call stripe::charge directly: what breaks?  nothing.
                        verdict: coaster. Ship the direct call; file PAY-443
                        "second provider (Adyen) — seam reintroduced when Adyen is scheduled".

author (legal answer b): "Adyen is contracted, ADR-17 §3, sandbox key live this sprint."
                        → trait stays; PR body gains Consumer / Forfeit / Revisit lines (P10).
```

### 3.2 PR Descriptions — the Coaster Declaration

A PR that introduces a non-idiomatic construct without a declaration is asking the reviewer to reconstruct intent from syntax, which is how peaks get approved by fatigue. Require a four-line block in the PR body — the engineering translation of Fitzpatrick's *state the device and why the reader needs it*:

```text
## Coaster Declaration (required for any non-idiomatic construct)

Construct:          Box<dyn ChargeStrategy> seam in payments/charge.rs
Present consumer:   AdyenStrategy (ADR-17, sandbox key live 2026-06-02)
If deleted today:   Adyen integration breaks at compile time — 2 call sites
Revisit trigger:    billing moves to a single vendor, or strategies exceed 2 → inline
Removal cost:       ~1 hour; one file deleted, two call sites rewritten
Boring alternative: direct stripe::charge(a) — rejected because ADR-17 schedules Adyen this sprint
```

Rules that make the declaration binding rather than decorative:

- **Fill every line, including the deletion test.** "Nothing" on the *If deleted* line means the PR must inline the construct before review (**P3**); "unknown" means the author has not done Q2.
- **The boring alternative is named, not implied.** A PR that never states the simple path cannot be reviewed for scope — the reviewer cannot tell whether the author considered it or never saw it.
- **Deferral tickets are cited by ID**, with owner and trigger, not "in a follow-up" (**P11**). A follow-up without an ID does not exist after the merge.
- **The diff's peak gets the fewest words.** Prose effort should scale with *unfamiliarity*, not with author pride; a 400-word essay defending a metaprogramming spike is itself a spike.
- **Strip self-congratulation.** No "elegant", "elegantly", "now finally extensible". Under the [Lexical Anti-Bloat Filter](../../kirby-fitzpatrick-lexical-anti-bloat-filter/SKILL.md), praise vocabulary in a PR body is a reliable marker of a coaster, and it is the same failure Fitzpatrick names in prose: the device advertised instead of the substance stated.

### 3.3 Architecture RFCs / ADRs — pricing the coaster before it is built

An RFC is where speculation acquires legitimacy and becomes unmovable, because design docs are read as evidence of rigor. An ADR whose justification for a construct is "flexibility", "scalability", or "future-proofing" is a status update with diagrams. Require the rejected boring path to appear as a first-class section, with the coaster's cost quantified and a trigger that can falsify the decision.

```text
ADR-17 — Payment provider seam in billing

Status:      Accepted (2026-06-02)          Supersedes: none
Context:     One provider today; Adyen contracted for parity in Q3.

Decision:    Introduce `ChargeStrategy` seam + 2 implementations.

Boring alternative rejected because:
  - named second consumer … AdyenStrategy, sandbox key live 2026-06-02
  - forfeit if deleted … 2 call sites break at compile time; not "nothing"
  - cost accepted … +1 indirection per charge path; onboarding note in billing/README.md

Coaster ledger (what we are buying):
  added constructs = 1 trait, 1 box, 2 impls
  consumers today  = 2 (Stripe, Adyen)
  config debt      = 0 new keys
  dependencies     = 0 new crates

Revisit trigger (falsifiable):
  (a) single-provider decision → inline within 1 sprint; (b) strategies > 3 → revisit taxonomy
  (c) p99 charge latency regresses > 5 ms from dynamic dispatch → static dispatch or generics

Unverified: dynamic-dispatch cost on the charge hot path — no benchmark; owner @dana, before GA.
Removal plan: 1 hour; strategy files deleted, two call sites inlined, ADR marked Superseded.
```

Rules for RFC-level lens application:

- **Every construct in the RFC is a row in the coaster ledger.** A construct with no consumer row is deleted from the document before the document is circulated — not "considered later" (**P2**).
- **Abstractions are priced by their ledger, not by their adjectives.** "Microservices", "event sourcing", and "plugin architecture" are not decisions until a measured constraint (scale, isolation, ownership, contractual latency) sits behind each one; without it, the RFC is importing an identity rather than solving a problem.
- **Reversibility decides the burden of proof.** A two-way door (rename, module split, local refactor) needs a sentence; a one-way door (public API, wire format, persisted schema, config key) needs a ledger, a removal plan, and a trigger. Speculative constructs are usually one-way doors advertised as two-way ones.
- **Cite the ground truth you must not bend.** Locked schemas, golden tests, and mandated protocols stay untouched while the design is debated ([Read-Only Vault Isolation](../../kirby-fitzpatrick-read-only-vault-isolation/SKILL.md)); speculative constructs are frequently introduced precisely to avoid confronting an inconvenient constraint, so make the constraint visible.
- **Rewrite the design in the baseline before adopting the peak.** Write out the boring implementation as pseudocode in the RFC. If the boring version fits in twelve lines and the peak needs a section to defend, the RFC's own text has made the decision for you (**P1**).

**Related dispatchers.** Establish the baseline before arguing about the peak with the [Codebase Navigation Router](../../kirby-fitzpatrick-codebase-navigation-router/SKILL.md) and [Bilbo Simple-to-Complex](../../kirby-fitzpatrick-bilbo-simple-to-complex/SKILL.md); price dependencies with the [Active Dialogue Dependency Evaluator](../../kirby-fitzpatrick-active-dialogue-dependency-evaluator/SKILL.md); audit the branches an accepted seam implies with the [Semantic Gap Hunter](../../kirby-fitzpatrick-semantic-gap-hunter/SKILL.md); refuse cosmetic cleanup while the construct is still speculative with [Substance-First Refactoring](../../kirby-fitzpatrick-substance-first-refactoring/SKILL.md); bind every claim in the PR body to an artefact a reviewer can open with the [Cold Reader PR Auditor](../../kirby-fitzpatrick-cold-reader-pr-auditor/SKILL.md); and gate the review thread itself with the [Rhetorical Preflight Gate](../../kirby-fitzpatrick-rhetorical-preflight-gate/SKILL.md).

---

## 4. Verification Checklist

- [ ] **Consumer named, or the construct is gone.** Every generic parameter, interface, seam, registry, hook, flag, and new dependency in the diff has a caller that exists today (citable by file path) or a signed contract naming the second case with owner and date; nothing is justified by "we'll need", "in case", "for flexibility", or an unscheduled roadmap bullet, and every single-implementation seam not at an unowned boundary was inlined in the same PR.
- [ ] **Deletion test performed, not asserted.** Each new construct was deleted and the boring path inlined, with the test suite re-run and the result recorded; nothing broke outside the diff, or the PR shows exactly which consumer broke and therefore earns the construct; deferrals were converted into tickets carrying an ID, an owner, a trigger condition, and a date (**P11**).
- [ ] **No unearned public surface.** No new config key, environment variable, exported symbol, persisted field, or crate is introduced without at least one production reader and a documented default; no construct exists in an anticipation-tense state (`TODO: generalize`, one-entry registry, empty match arm awaiting a variant, unused type parameter), and any `unsafe`, macro, reflection, or codegen construct names the constraint it pays for plus the boring path for the common case (**P7**).
- [ ] **Legibility holds without the author.** A reviewer who joined last month can explain the construct from the repository alone — no chat log, no design doc, no verbal handoff — `grep` and the language server find every use, a stack trace reaches real code, and the PR body or ADR carries the one-sentence defence (*we chose X because Y; second consumer is Z; removal plan is W*) instead of an apologetic comment block explaining illegibility (**P10**).
- [ ] **The filter did not overreach.** Rent-paying complexity in the diff survived review untouched — benchmarked optimization, mandated protocol path, genuinely shared state, boundary test doubles — and no seam, layering, or documentation was deleted merely because it looked clever; the audit output is a ledger with a verdict per construct (Ship / Inline / Defer-with-ticket) and its findings were reported for human decision rather than silently rewritten into ground truth.