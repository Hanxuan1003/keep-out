AI use: ChatGPT assisted with concept development, source checking, drafting, and organization. This is an AI-assisted proposal for the student to review and defend; no implementation or playtest results are claimed.

# Keep Out — Initial Game Design Document
Version 0.1 • G1 proposal • November 15, 2026 • Status: design only

## 1. Concept and intended experience
**The X:** Keep Out is a dungeon-defense strategy game where you arrange traps to stop an adventurer from stealing your treasure, and feel clever when your setup exploits its expectations.

The player is the dungeon keeper. The audience is players who enjoy short tactical puzzles and experimenting with combinations. Intended motivations are **challenge** and **expression**. Observable hypothesis: after seeing a failed defense, players voluntarily reposition a trap or bait to address the route they observed, rather than repeating the layout unchanged. This has not been tested.

**Design pillars:** readable cause and effect; fair adaptation without hidden-information cheating; quick experimentation. A fixed-rule adventurer must already make the core loop playable. The model is an experiment in opponent adaptation, not a substitute for rules.

## 2. Concepts considered and selection
1. **Literally Speaking:** command a literal-minded robot with natural language. Appealing communication puzzles, but ambiguous interpretation and fair puzzle feedback would require substantial iteration.
2. **Adaptive fighting game:** an opponent counters repeated combat habits. Direct interaction is appealing, but animation, hit detection and responsive combat would compete with the AI evaluation work.
3. **Keep Out:** selected for visible tactical choices, reusable trap combinations, low art requirements, and a bounded comparison between model and scripted behavior. The student selected this direction after AI-assisted brainstorming; detailed mechanics below are proposed design decisions awaiting student review.

## 3. Core loop and session
Build within budget → launch the raid → observe the adventurer and optionally trigger one push trap → interpret the result → revise the layout for the next round.

One session contains five rounds on one fixed 9 × 9 tile map. The entrance is at the left and the treasure at the right, connected by at least two permanent corridor routes. Initial map construction must provide an entrance-to-treasure path of at most 16 cardinal steps. There is no diagonal movement. Five rounds are played even if the match outcome is already decided, to preserve comparable observation opportunities.

The adventurer starts each round at the entrance with **6 HP**, empty inventory and a **48-tick** limit. Reaching the treasure picks it up automatically. Reaching the entrance alive with it wins the round for the adventurer. Death or tick exhaustion wins the round for the keeper. The keeper wins the session by defending at least three rounds. Score is rounds defended; remaining HP, steps, route and trap outcomes are diagnostic feedback rather than additional victory criteria.

Initial numbers are design hypotheses, not balanced or tested values.

## 4. Build phase and initial economy
Each round provides **12 construction coins**. Keep the previous layout as an editable draft, but charge its full cost against the new allowance. Removal before launch refunds its full price. No purchases, upgrades or income carry over. The player cannot modify the layout during a raid.

| Item | Cost | Proposed behavior | Limit |
|---|---:|---|---|
| Spike trap | 3 | Automatically deals 2 HP damage on entry; then spent | 3 |
| Slow trap | 2 | Automatically adds 2 ticks on entry; then spent | 2 |
| Push trap | 3 | On manual trigger, pushes an adjacent adventurer one tile in its preselected cardinal direction; blocked landing cancels the displacement | 1 |
| Barrier | 2 | Occupies one buildable floor tile; blocks movement and visibility | 2 |
| Bait | 1 | Visible lure at its tile; optional detour for the adventurer; removed on entry, no health or score effect | 2 |

At most one placed object per tile. Entrance and treasure tiles cannot hold objects. Traps and bait are passable. Permanent walls cannot be edited. Every placement is checked for affordability and reachability; reject any layout whose shortest entrance-to-treasure route exceeds 20 steps. This leaves room for the return trip within 48 ticks before trap effects. Show a clear error before launching an invalid layout.

The keeper gets one manual push activation per round, only at a pause between adventurer actions and when the adventurer is adjacent to the placed push trap. Provide **Trigger** and **Continue** controls when eligible; no reflex deadline. A blocked push consumes the activation and is shown in preview before confirmation. Without an eligible trap, continue automatically. A push consumes no adventurer tick and cannot trigger itself again. Landing on a spike or slow trap activates that trap once.

## 5. Raid rules and feedback
A move or inspect action costs one tick. Movement resolves, then destination trap effects, then death, then pickup/escape, then time exhaustion. An adventurer who returns alive on tick 48 escapes successfully; death on arrival takes precedence. Slow penalties are included before checking the tick limit. Movement through a wall, outside the map, or into a barrier is rejected before state mutation.

An inspect action targets one adjacent passable tile and reveals its object or confirms it is empty. Inspection does not disarm traps. A known trap may be crossed if its cost is acceptable. Bait is a choice, not a forced action: the model may ignore it, and its usefulness is an open playtest question.

Show the full layout to the keeper, plus a toggle for the adventurer's currently permitted view. During the raid show HP, ticks left, carried treasure, movement, discovered objects, trap activations and whether fallback is active. After the round show the taken route and a concise event log. Do not display model chain-of-thought. Concise visible reasons should be derived from validated events, not treated as proof of internal reasoning.

## 6. Information and memory
The map's fixed floor/wall structure, entrance and treasure positions are public to both policies. Dynamic objects are not globally visible. The adventurer observes dynamic tiles within cardinal distance two with wall/barrier occlusion. Hidden spike and slow traps appear only as an identical generic suspicious-floor cue when in sight; inspection or triggering reveals type. Push devices, barriers and bait are visible when in sight. Unseen dynamic cells remain unknown.

Within a round, retain discovered facts with timestamps. Between rounds, retain up to 12 observed encounter records from the previous two rounds, labelled **historical, current layout unverified**. Clear all current dynamic-object knowledge when the keeper rebuilds. No access to build actions, hidden trap inventory, player plans or the current full object map. Reset memory between sessions. The baseline receives the same observations and memory, even if its fixed rule uses them less flexibly.

## 7. AI hypothesis and baseline
Hypothesis: with a model adventurer that interprets recent observed encounters, players will revise trap placement in response to the opponent more often, while still rating it as fair. A scripted baseline can still be clever; the hypothesis is not that fixed rules cannot adapt.

Baseline: weighted shortest-path planning to treasure, then entrance; known spike cost 4, slow cost 3, historically dangerous unverified cell cost 2, ordinary floor cost 1. Inspect an adjacent uninspected suspicious tile if it is the next planned step and at least 6 ticks remain. Bait is considered only before pickup, at HP ≥ 4, if its path adds no more than 2 steps; choose the nearest such bait, once per round. Tie-break north, east, south, west. Treat unseen dynamic tiles as passable; replan on discovery. If no known route exists, inspect an eligible tile, otherwise take a legal move toward a reachable frontier; if none exists, wait one tick. The engine supplies this deterministic fallback to both conditions.

The model chooses up to four legal-intent actions from the same action vocabulary. Replan at plan completion or on new dynamic information; validation occurs before every action. The intended difference is contextual interpretation of encounters and bait, not extra vision or stronger statistics.

**What is lost with the baseline:** the opportunity to test an opponent that recombines encounter history into route and inspection choices beyond a fixed set of cost weights. Whether that creates a better back-and-forth is an empirical question.

## 8. Planned playtest
At G2/G3 observe at least one external player. Across G2–G4 use at least two distinct players; target four for the initial comparison if available. Each participant plays one five-round model session and one five-round baseline session; counterbalance order, reset memory and use the same map, budget and starting conditions.

Record layout changes motivated by the observed opponent, repeated trap strategies, rounds defended, fallback exposure, waiting time and 1–5 fairness/agency ratings. Ask: “Which opponent decision made you change your next layout, and why?” Distinguish strategic revisions from random changes. A useful early signal is at least three of four players identifying a concrete opponent-driven revision, without a lower median fairness rating than the baseline. This is a design decision rule, not statistical evidence of superiority. With fewer participants report individual observations, not general conclusions. Never count model timeout as a tactical success.

## 9. Included scope, cuts and open questions
Included: one fixed map, one adventurer, three trap types, barriers/bait, five rounds, event feedback, model and baseline modes, isolated sessions, automated rules checks, release build.
Excluded: multiplayer, combat animations, campaign, procedural maps, permanent progression, dialogue, generated assets during gameplay and mobile-specific controls.

Cut order: manual push interaction (make push automatic on its trigger tile and retest); then remove the push trap entirely; then simplify bait. Preserve a complete defense loop, visible feedback, information boundaries and baseline comparison. Any rule change updates both modes and the evaluation specification.

Open questions: is bait worth buying; can players understand adaptation; do 12 coins/6 HP/48 ticks create fair rounds; is model latency tolerable; is the proposed model available in the course project? These are G2 validation tasks, not claims of completed work.

## 10. References and attribution
- **Dungeon Warfare**, Valsar: the official description includes rerouting adventurers and traps that throw enemies. Reference: https://store.steampowered.com/app/355980/Dungeon_Warfare/ . Borrow the design idea of spatial trap combinations; change to one bounded adventurer and five rounds, with controlled model/baseline comparison. No code, art, sound, level layouts or screenshots copied.
- **Boss Monster**, Brotherwise Games: publisher describes playing the villain, building a dungeon and luring adventurers. Reference: https://www.brotherwisegames.com/boss-monster . Borrow the keeper perspective and lure concept; replace card-room construction with a small spatial grid. No cards, artwork, text or code copied.

These commercial works are references, not licensed asset libraries. No reuse permission is assumed. Prototype visuals will be original simple assets; any later third-party asset or dependency requires its source and license in an attribution ledger. Keep Out is a working title; uniqueness has not been checked. See `05_Sources_and_Requirements.md` for source status.
