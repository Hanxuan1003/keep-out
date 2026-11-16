AI use: ChatGPT assisted with concept development, source checking, drafting, and organization. This is an AI-assisted proposal for the student to review and defend; no implementation or playtest results are claimed.

# Keep Out — Initial Configuration Document
Version 0.1 • G1 planned configuration • No agent implementation or benchmark exists yet

## Architecture and authority
Browser TypeScript UI → authoritative session service → deterministic rules → restricted observation builder → model or baseline → action validator → rules update → player feedback.

Cloudflare hosts the UI and session service; evaluate a per-session Durable Object for serialized state. Vertex AI calls originate only on the server using approved credentials. Course project access and authentication compatibility must be verified before commitment. No credential enters browser code, recordings or repository. Single-provider design; alternative hosting needs instructor approval under the syllabus.

Full state includes map, all objects, HP, ticks, player budget and manual activation. The model receives only the public static map, current permitted observations, own status, legal action vocabulary and labelled encounter history. The observation builder, not a prompt, enforces secrecy. Separate administrative logs may contain full state but must never be passed back as agent context.

## Planned executable extension manifest
| Field | G1 specification |
|---|---|
| Purpose | Choose adventurer moves and inspections to collect treasure and escape alive |
| Artifact | Planned server-side function-calling adapter `adventurer-policy`; no executable delivered at G1 |
| Tools/actions | `move(direction)` and `inspect(adjacentTile)`; at most four proposed actions per response |
| Prohibited | Changing HP, ticks, map or inventory; reading full object state; network browsing; filesystem access; code execution; impersonating player controls |
| Model | Candidate `gemini-2.5-flash-lite` on Vertex AI; verify availability, exact endpoint and course access before implementation |
| Settings | Proposed temperature 0.2; thinking disabled where supported; max output 256 tokens; record actual accepted settings |
| Context | Public static map, two-tile occluded dynamic view, status, discovered facts and previous-two-round encounter records |
| Memory | Engine-owned records, maximum 12; historical cells explicitly unverified after rebuild; clear at session end |
| Retrieval/reflection | No external retrieval, no vector database and no additional reflection call in v1 |
| Failure cases | Invalid JSON/action, hallucinated tile, old knowledge treated as current, timeout, repeated route, budget exhaustion |
| Logging | Policy/config versions, permitted observation, response, action validation, event trace, tokens/cost and latency; random session IDs, no player personal data |
| Human override | Student controls prompts, rules, deployment and budgets; player can pause/reset; maintainer can disable live calls |
| Tests/CI | Planned `npm test`, `npm run test:integration`; workflow `.github/workflows/ci.yml`; paths are targets, not existing files |

## Proposed prompt and response contract
System instruction draft:
> You are the adventurer in Keep Out. Retrieve the treasure and return alive within the tick limit. Choose only move or inspect actions. Use only the supplied observations. Historical encounters may no longer describe the current layout. Bait is optional. Do not invent observations or modify game rules. Return JSON containing a plan of one to four actions. Do not include private reasoning or prose.

Example response (format only, not an observed model output):
```json
{"plan":[{"type":"inspect","x":3,"y":2},{"type":"move","direction":"east"}]}
```

The server validates schema, length, action names and bounds, then checks current legality before each step. Multi-step plans are intentions, not permission to execute unchecked. Cancel the remaining plan if new dynamic information appears, the action becomes illegal, the player pushes the adventurer, or the round ends. One session may have only one pending decision; discard responses for old session/round/state versions. A model never updates authoritative state directly.

## Error handling and deterministic fallback
No automatic retries within a decision. A malformed, invalid, late or unavailable response triggers the baseline for that decision and logs the reason. Abort waiting after 2.5 seconds. A cancelled response must not execute later. Three consecutive failed calls disable the model for the rest of that session. On token/call/cost cap, use baseline for the remaining session and show the switch to the player. An invalid action does not consume a game tick; its replacement legal fallback action does. If no move/inspect is possible, the engine advances one wait tick until termination.

Fallback use is reported separately in live evaluation, not hidden inside a model win rate. A timeout does not damage the adventurer or award a round to the keeper. The game remains playable without a provider.

## Budget proposal — estimates, not measurements
Official standard text prices consulted November 15, 2026: Gemini 2.5 Flash Lite input **$0.10 / million tokens**, output **$0.40 / million tokens**. Source: https://cloud.google.com/model-garden/pricing?hl=en_GB (Gemini 2.5, Standard table). No discounts, caching, grounding or free credits assumed.

Typical planning assumption: **60 calls/session**, 1,200 input tokens and 120 output tokens/call. Estimated model cost:
`60 × (1200 × 0.10 + 120 × 0.40) / 1,000,000 = $0.01008`.

Hard design caps: **100 calls/session**, **2,000 input tokens/call**, **256 output tokens/call**, no retry. Conservative capped token estimate:
`100 × (2000 × 0.10 + 256 × 0.40) / 1,000,000 = $0.03024`.
Set model allowance **$0.04/session** and reserve **$0.01/session** for hosting/logging, for a **$0.05/session planning envelope**. Hosting reserve is an allocation, not a verified platform quote. Count or conservatively bound the input before dispatch; prune old history, never legality/vision rules. Reserve worst-case request cost before calling; count timeout calls conservatively until reconciled with provider usage. Budget controls are application limits, not a claim of exact billing guarantees.

Latency goals: p50 ≤ 0.5 seconds and p95 ≤ 1.5 seconds per decision; fallback at 2.5 seconds. Basis: a proposed five-round session should tolerate at most about 30 seconds of typical cumulative model waiting (60 × 0.5 seconds). These targets need live verification. At 100 timeouts the theoretical wait would be 250 seconds, but the three-consecutive-failure circuit breaker prevents a persistently unavailable provider from reaching that pattern. Stop/cut or reduce call frequency if cumulative waiting damages the experience.

Initial live evaluation allowance: **$2**, including 20 paired model/baseline episodes under these caps, a small no-memory ablation and a reserve. Offline baseline runs do not consume model tokens. Require student authorization before expanding the evaluation allowance.

## Evaluation and test cases
Deterministic CI on every PR: budget nonnegative; layout route validation; collisions; trap activation once; HP and ticks; terminal precedence; five-round scoring; inspect knowledge; hidden-state noninterference; historical-memory freshness labels; session isolation; stale response rejection; illegal action rejection; timeout fallback; cost reservation and cap; at least one reproduced regression after a real bug is found. Do not invent a regression result now.

Hidden-state test: two states that differ only in an unseen trap must produce identical observations and legal-action information. Known-static-map access must not leak dynamic positions. Inspecting one tile must not reveal neighbors. Test that player push invalidates a queued plan.

Live suite: run ten declared layouts twice with each policy; use identical conditions and store all failures. Report completion, invalid-response rate, fallback rate, model latency, tokens, cost and route diversity. For memory ablation, run five layouts twice with memory removed, within the $2 allowance. Do not infer human enjoyment from model win rate. Re-run a small suite after changing model/prompt/memory/tools; fuller suite before each gate. Pair with the human playtest in the GDD.

## Production agent role coverage — planned, not yet operational
All roles may use one coding assistant model; role-specific instructions and evidence are still required. The student retains Executive Producer and Creative Director authority.

| Role / planned artifact | Inputs and allowed work | Output and handoff | Evaluation / human boundary |
|---|---|---|---|
| Producer / `roles/producer.md` | Approved scope, gate dates; draft tasks only | Plan, PPP and risks → student | Student approves schedule, scope and external updates |
| Design / `roles/designer.md` | GDD, anonymous playtest evidence; propose rules | Rule revisions → engineering | Student approves mechanics and balance changes |
| Engineering / `roles/engineer.md` | Approved rules/interfaces; edit branch, run tests | PR + implementation notes → reviewer | No merge, deployment or secret access without student direction |
| Art/UX / `roles/art-ux.md` | Style brief, source/license ledger | Original icons, readable feedback → engineer | Check contrast, input clarity, attribution; student approves assets |
| QA / `roles/qa.md` | Build, spec, failure logs; run bounded tests | Reproduction cases and playtest protocol → engineer | No invented participants/results; consent and findings reviewed by student |
| Independent reviewer / `roles/reviewer.md` | PR diff, rules and tests; read/review | Findings and boundary checks → student | Separate review task; no self-approval of implementation |
| Release / `roles/release.md` | Approved build, tests and configuration | Release checklist, rollback and archive → student | Student authorizes release, spending and credentials |
| BI/evaluation / `roles/evaluator.md` | Anonymized traces, declared hypotheses | Comparison tables and limitations → designer | Student checks conclusions; no claim beyond sample |

Narrative and bespoke audio are excluded from v1; art/UX covers instructional wording and simple visual feedback. Each role artifact will document tools, context, prohibited actions, output, handoff and review criteria. G1 plans coverage; G2/G3 demonstrate working roles; G4 records final coverage. No coordination automation is required.
