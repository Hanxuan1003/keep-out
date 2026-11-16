AI use: ChatGPT assisted with concept development, source checking, drafting, and organization. This is an AI-assisted proposal for the student to review and defend; no implementation or playtest results are claimed.

# Keep Out — Initial Project Plan and Risk Profile
Version 0.1 • November 15, 2026 • Proposed plan, not a completed-work log

## Gate schedule
| Date (2026) | Milestone / evidence |
|---|---|
| Nov 15 | G1 post: deck, 3–5 minute recording, Initial GDD/configuration and plan |
| Nov 16 | G1 Concept classroom review |
| Nov 17 | Expected Approve/Hold comment; resolve named corrective work |
| Nov 19 | Two G1 Braintrust replies per assignment prose; automatic syllabus summary shows Nov 18, so target Nov 18 pending clarification |
| Nov 23 | Deployed model call, isolated session state and forced-timeout fallback checkpoint |
| Nov 28 | First playable recording for combined G2/G3 |
| Nov 30 | G2 Prototype + G3 Production review; scope lock and updated documents |
| Dec 2 / Dec 4 | Alpha / Beta |
| Dec 5 | Gold recording after external release playtest and verified fixes |
| Dec 7 | G4 Final / Pre-Launch review |
| Dec 12 / Dec 14 | Draft AAR/showcase evidence / G5 final game, AAR and playtest log |
| Dec 17–23 | Assigned Finals slot: retrospective, 8–10 minute walkthrough and live presentation |

Deadlines are the course's published dates; verify CourseWorks timezone rather than assuming the computer's timezone. G1 work is individual. Asana starts after G1; then migrate this plan, add full task history, dependencies, acceptance criteria, PPP and the full risk register. Preserve gate decisions with linked PR/test evidence.

## First tasks through G2
All tasks have the student as accountable owner; AI roles are delegates, not substitute owners. These are estimates; track actual hours after G1.

| Target | Task / delegate | Depends on | Acceptance evidence | Estimate |
|---|---|---|---|---:|
| Nov 16–17 | Resolve G1 decision; student + producer | G1 feedback | Approved scope or recorded corrective plan | 1 h |
| Nov 17 | Repo, role instructions, test workflow; engineering + QA | Scope | Tests run on PR; secrets excluded; one independent review recorded | 2 h |
| Nov 18–19 | Grid engine and building; engineering | Repo | Budget and path validator pass; complete deterministic round possible | 4 h |
| Nov 20 | Baseline and event feedback; design + engineering | Engine | Five rounds terminate; route/HP/ticks readable; baseline legal | 3 h |
| Nov 21–23 | Server model adapter and observation boundary; engineering + reviewer | Baseline | Live request, isolated sessions, forced timeout and fallback shown; no unseen data in payload | 4 h |
| Nov 24 | Rules CI and bounded live suite; QA + evaluation | Adapter | Declared layouts, failures, cost and latency recorded; no hidden-state leaks | 2 h |
| Nov 25–26 | External core-loop playtest; student + QA support | Playable build | Consent, anonymous observations, one finding connected to a revision | 2 h |
| Nov 27 | Fix, retest, scope-lock proposal; engineering + reviewer | Playtest | Critical fixes verified; excluded features and remaining risks explicit | 2 h |
| Nov 28 | Record first playable, update GDD/config/log; student + producer | Fixes | Working build and 3–5 minute recording linked | 1 h |

Total planned effort through recording: **21 hours**, not a statement of available time. If this exceeds actual availability, cut the manual interaction and push trap before expanding the schedule. Reserve later time for release playtests, evaluation and documentation rather than adding features.

## Six-dimension Risk Profile
| Dimension | Rating | Evidence / uncertainty | Mitigation and trigger |
|---|---|---|---|
| IP / Concept | Low | Original bounded design; two commercial reference mechanisms, no copied assets; working title not cleared | Maintain attribution ledger; original assets; check title before any wider release |
| Business / Audience | Medium | Intended strategy audience inferred from concept; no player evidence yet | G2 interviews and observation; revise if players cannot explain a meaningful choice |
| Technology | High | No deployed model integration, measured latency or verified course access yet; partial observations may leak | Prove adapter by Nov 23; noninterference tests; timeout/fallback; freeze engine and use baseline while integration is repaired |
| Team | Medium | One student must supervise design, engineering, art, QA and release; roles are planned | Explicit role artifacts, independent review task, daily next-action list; human retains all scope/release decisions |
| Genre / Mechanics | High | Trap balance, bait and opponent-driven iteration untested; waiting could dominate play | Fixed-rule prototype first; external playtest; simplify traps and cut manual timing if decisions are unclear |
| Timeline | High | About 21 estimated hours to first-playable recording, then only one week to Gold recording | One map/one hero; enforce cut order by Nov 24; scope lock Nov 30; protect testing/release time |

The High ratings share a cause: unvalidated AI behavior and core-loop design must mature within a short solo schedule. More features would worsen all three. Mitigate by proving the deterministic loop first, measuring the model boundary early and cutting mechanics instead of delaying testing.

## Gate request and Hold conditions
**Request:** approve a one-map prototype with the build/raid/revise loop, three trap types, same-information model/baseline policies and one external core-loop playtest by G2/G3.

**Hold would require:** a clear deterministic win/loss loop, enforceable knowledge boundary and a cost/latency plan before model integration; reduce scope if those are not achievable. A Hold is a corrective gate decision, separate from the grade. Do not present this proposal as instructor-approved.

At G2/G3, retain the model approach only if it makes legal decisions with readable feedback and the playtest offers some evidence of opponent-driven layout revision. Failure is reportable evidence: change prompts, simplify the task or revise the hypothesis with the instructor rather than claim success.

## After G2/G3
Test a revised encounter with an external player; after Alpha and before Gold recording conduct another external release playtest, triage and verify fixes. Use at least two distinct players across G2–G4. Run the full bounded live suite, freeze configuration and archive a runnable release. Preserve approved Gold; any corrective release after G4 follows the instructor's approval policy. Between G4 and G5 play three assigned classmates' builds and record showcase feedback.
