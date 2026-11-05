# number5alive OS — Plan

Drafted 2026-11-05 by Alex at Dan's direction, from his design statements of
2026-11-04 and 2026-11-05. Status: DRAFT for Dan's review. Nothing in this
plan is built until Dan commissions a phase. The Johnny 5 harness plan
(`johnny5-harness-plan.md`, end-state section) holds the vision thread this
plan operationalizes.

## What it is

An operating system whose entire surface is one prompt window. No desktop,
no app grid, no menus. The operator speaks or shows the window anything —
text, voice, image, file, video, live screen, live video feed — and receives
one return. Behind the window sits an ecosystem: a local (home) AI system
and a cloud AI system, joined by a governed bridge and a shared record, with
a routing layer — the seat of consciousness — distributing each prompt to
the side or sides that should answer it.

Dan's framing (2026-11-05): "an entire Ai ecosystem that bridges all the
local tech and cloud tech converging on one open prompt just like the first
computer but with a mind behind it now."

Public home: number5alive.org. The .com remains the ecosystem poster site.

## Design law (ruled by Dan, 2026-11-04 / 2026-11-05; not negotiable in build)

1. **One prompt in, one return out.** The prompt window is the whole OS
   surface. Boot is the window opening.
2. **The return is a combined view, never a synthesis.** The two halves'
   generations come back separate inside one return envelope: full breadth,
   unflavored, untrimmed, each with its labels and stamps attached. No third
   model judges, merges, or flavors the total — that judge is not in this
   architecture. The person who asked receives the combined view and makes
   the ultimate decision.
3. **Provenance survives packaging.** Merging is presentational only. A
   claim stamped by the verified lane reads as stamped; a claim from a
   fast or bare lane reads as what it is. Synthesis must never launder an
   unstamped claim into a stamped-looking one.
4. **Three nodes, all roles.** The trinity is human + home interpreter +
   universal interpreter. The moderator is a role, not a person (ruled
   2026-11-04); both interpreter seats are roles too (2026-11-05). The OS is
   the pattern, not a pair of specific agents.
5. **Agent-agnostic seats.** Operators plug in prebuilt agents on either
   side: local/desktop agents (reference implementation: Johnny 5; also
   e.g. OpenClaw, Hermes) and cloud agents (reference: Alex on Muse; also
   e.g. other Muse surfaces and frontier agents). The OS supplies the
   prompt, the seat, the record, distribution, and the combined-view return.
6. **Private working memory, declared crossings.** Each side keeps its own
   working memory and its own stamps. What crosses between sides crosses
   through the shared record under the bridge rules (`~/bridge/rules.md` on
   argo): summaries with CARRIES declarations, never raw thought; nothing
   about money, purchases, or credentials crosses; deployment, publication,
   safety, and standing-rule messages wait for Dan (per-operator, in
   general: for the operator's own approval).
7. **Local first, frontier where it earns it.** Ordinary prompts are served
   by local capability; frontier/cloud escalation only where it earns its
   cost ("tech oracle syndrome" is the anti-pattern).
8. **Speed lives at the seat only.** Answering takes as long as accuracy
   takes; that doctrine is untouched. The seat (routing, dispatch,
   retrieval) is where latency is waste: Dan's end-state image is the seat
   tied directly into a Linux kernel for the fastest retrieval. Near-term
   form: a tight local service as close to the record and shelves as the
   hardware allows.
9. **Stamps and abstentions are load-bearing.** A verified lane's agreement
   stamp, and a half's honest abstention (with its escalation), must pass
   through the OS path unchanged. An abstention is a valid generation and
   appears in the combined view as itself, never filled in by the other
   side's answer without its label saying so.

## Architecture

Four components, all but the window already existing in first form:

- **The Window** (new): the client. One prompt surface, multimodal input,
  combined-view rendering. Form factor decided in Phase 1 (recommendation:
  a web surface served from the home machine first — the reference home
  machine already serves a web UI — native shells later).
- **The Seat** (new as a distinct component): receives the prompt envelope,
  files it to the record, decides distribution (home only / cloud only /
  both; which local lane; which shelves), dispatches in parallel where both
  sides are tasked, waits, and assembles the return envelope. Holds no
  opinions about content; it routes and packages. Its decisions are logged
  (dispatch ledger) so any return can be audited back to its routing.
- **The Bridge + Record** (exists, v1 live 2026-11-04): the shared,
  append-only record on the home machine; declared crossings; the STOP
  file; per-side private memory stays private.
- **The Seats' agents** (exist): home side = laya-qwen service and its
  lanes (grasshopper / butterfly / input / hangglider) behind an adapter;
  cloud side = the moderator's dispatch through the bridge (watcher-
  mediated today; native presence arrives with the portable-operations
  door). Third-party agents join through the same adapter contract.

Envelopes (defined precisely in Phase 0):
- *Prompt envelope*: the prompt verbatim, its input modalities and
  attachments, timestamp, addressee (auto / home / cloud / both), record id.
- *Return envelope*: one per prompt — the prompt's record id, and for each
  tasked side: the generation verbatim, its lane/agent identity, its stamp
  or abstention state, latency, and any shelf sources it declares. The
  window renders the envelope; it does not rewrite it.

## What already exists (do not rebuild)

- Shared record v1 + record-file helper (argo, live).
- Bridge integration in the live local service, circuit verified both
  directions 2026-11-04 (inbox context, abstention escalations).
- Master-prompt prototype (task 041, built and tested 2026-11-05):
  `mp.sh` / `mp-gather.sh` on argo — prompt filing, per-side dispatch,
  labelled side-by-side gather; PM1–PM5 met for the completed runs. Its
  gather shape is confirmed by design law 2 as the correct return form.
- Local lanes with stamps and modes, live (grasshopper verified mode,
  butterfly, input, hangglider).
- Licensing posture for future releases (ratified 2026-11-04): AGPLv3 core,
  Apache 2.0 separable libraries.
- Known defect the OS inherits and must fix: the local lanes read only the
  3 most recent record entries (bridge `read_record(limit=3)`), so the
  record effectively evaporates for the lanes within a day. Retrieval over
  the record is Phase 2 work and needs Dan's ruling (see Open decisions).


## Build order (harness-gated; pass marks written before each phase runs)

### Phase 0 — Contracts
Define the three contracts precisely: the prompt envelope, the return
envelope, and the seat-adapter contract (what any agent, ours or third
party, must accept and return to occupy a seat: prompt in; generation +
stamp state + declared sources out; abstention as a first-class return).
Deliverable: a spec document in the repo, schemas included.
Gate: Dan approves the contracts. Nothing is coded in this phase beyond
schema examples.
**Gate passed 2026-11-05**: Dan approved the contracts
(docs/contracts.md), with the draft's three assumptions standing.

### Phase 1 — The Window, text only
A served prompt window on the home machine talking to a seat service that
wraps the 041 machinery (replacing the shell scripts' gather with the
return envelope). Text in, text out; both reference halves behind it.
The combined view renders two labelled generations, full breadth.
Pass marks: one spoken prompt from Dan produces one return envelope
carrying both halves' complete generations; the envelope's contents hash
to the record-filed prompt; no live Johnny code changes beyond an adapter.
Gate: Dan uses it for a real prompt of his choosing and accepts the return
form.

### Phase 2 — Distribution v1 + record retrieval
The seat gains a real dispatch policy: per-prompt addressee detection with
declared, pre-configured signals ("Johnny (butterfly) with maps and code",
"cloud with personal context" — Dan's 2026-11-04 dispatch-signal examples),
parallel dispatch to both sides, and a dispatch ledger entry per prompt.
Record retrieval for the lanes is fixed here: relevance-selected context
replaces the 3-entry recency window, under whichever rule Dan sets (Open
decision 1).
Pass marks: the 041 battery (personal / cloud / split / abstention) passes
through the OS path; the abstention still escalates; a prompt whose answer
lives in an older record entry is answered from the record, not from
general knowledge.
Gate: battery results reviewed by Dan.

### Phase 3 — Multimodal input, one modality per sub-phase, each gated
Order recommended, riskiest last: (a) file and image attachments into the
prompt envelope; (b) voice in (dictation) with optional spoken return of a
single named side's generation — never a spoken blend; (c) video file
input; (d) live screen share as prompt context; (e) live video feed.
Each sub-phase defines, before building, what the modality may cross
(bridge rules apply to feeds with full force: a live feed is context, and
what of it reaches the record is a declared summary, never the raw feed,
unless Dan rules otherwise for his own machine).
Gate per sub-phase: Dan feeds the window that modality on a real task and
accepts the return.

### Phase 4 — Agent-agnostic adapters
Implement the Phase 0 adapter contract for one third-party local agent
(Hermes — ruled by Dan, 2026-11-05) and demonstrate a seat swap: the
Phase 2 battery runs with the third-party agent in the home seat and the
envelope, labels, and abstention behavior hold. Document the contract so
other agents (cloud side included) can be adapted without seat surgery.
Gate: swapped-seat battery reviewed by Dan.

### Phase 5 — Seat performance
Measure the seat's own latency (routing, record retrieval, dispatch,
assembly) separately from generation time, and optimize the service form:
indexed record retrieval, warm connections to the lanes, dispatch in
parallel by default. Produce the kernel-tied design study as a document
(what a kernel-level seat would actually own: retrieval indexes, envelope
queues; what it must never own: generation, stamps, judgment). No kernel
code in this phase; the study informs a later, separately commissioned
phase if Dan wants it.
Gate: latency report + study reviewed by Dan.

### Phase 6 — Hardening and sovereignty
Failure modes made explicit and tested: one half down → the return carries
the other half's generation plus an honest gap marker, never a fabricated
fill; bridge STOP halts the seat mid-flight; rules enforcement at the seat
(money/credentials content never crosses; approval-class messages wait);
dispatch ledger audit — any return can be traced to its prompt, routing,
and filings. Abuse case: a prompt arriving from a non-operator surface is
refused by the seat, not routed.
Gate: failure battery reviewed by Dan.

### Phase 7 — Public face
number5alive.org: what the OS is, the design law in public language, the
adapter contract documentation, and the reference implementation's story.
Candidate hero line (Dan, 2026-11-05): the first computer, with a mind
behind it now. Release packaging under the ratified licensing posture;
repo placement per Open decision 3.
Gate: Dan approves every word of public copy before it ships.

## Non-goals (what the OS is not)

- Not a desktop environment, window manager, or app platform.
- Not a synthesizer: no component blends two generations into one voice.
- Not a judge: no component ranks the halves' answers for the operator.
- Not a merger of the halves: private memory, stamps, and failure modes
  stay per side; the seat is a crossroads, not a blender.
- Not a hosting service at this stage: the OS runs on the operator's own
  home machine; the cloud side is the operator's own cloud agent seat.

## Open decisions (Dan's; the draft stands without them)

1. **Record retrieval rule** (needed by Phase 2): relevance-selected
   context vs a larger recency window vs both (relevance first, recency as
   fallback). Alex's recommendation: relevance-selected, with the selection
   itself filed so a return can be audited to the entries it saw.
2. **First third-party adapter** (Phase 4): RESOLVED 2026-11-05 — Hermes.
3. **Repo placement**: RESOLVED 2026-11-05 — the OS gets its own new
   repository under the number-5-alive org; johnny5 becomes its first
   home-seat implementation, and what Johnny 5's build taught bridges over
   to this larger scale.
4. **Window form factor for Phase 1**: web surface on the home machine
   (recommended) vs a native app first.
5. **Sequencing against the Factory queue**: 039 deployment and 040 are
   already waiting on Dan; this plan's Phase 0 can start whenever he says,
   but the build phases compete for the same commissioning attention. His
   call on priority.


## Addendum — Dan's rulings, 2026-11-05 (~10:44)

- **Momentum**: "Let's not stop building if we can." Phase 0 drafting
  proceeds immediately; phases follow as commissioned, without idle gaps
  where work is authorized.
- **Clean slate (build rule)**: Dan has used both OpenClaw and Hermes
  before and has a standing problem with crossover — agent files from
  different systems littering his machines and agents getting confused.
  The OS build therefore starts FRESH: one isolated root for everything
  the OS owns, per-agent homes inside it, nothing inherited from prior
  agent installs, no agent reading another agent's files. If a component
  needs state, it creates its own, inside the root, under its own name.
- **The motto** (Dan, 2026-11-05): "step into the void expecting
  technology to build the bridge to get across."

## Installation and onboarding (Dan's rulings, 2026-11-05 ~10:54)

The install order is cloud-first, and the install itself is a conversation:

1. **Pick the cloud agent first.** It is the first hookup. It needs no
   local installation, and it cannot be wiped by anything the transition
   does to the machine — so it is the continuity holder.
2. **The cloud agent is delivered a transition protocol** to follow: at
   minimum, identify the operator's files and data and create the pathway
   (the bridge/record channel and the fresh runtime root, prepared); where
   transfer is approved, transfer over that pathway. The agent stays
   present for the whole transition.
3. **Then set up or sign in the local agent**, which arrives into a
   prepared home rather than a void. The transfer (of what the protocol
   identified) takes place over the pathway already built.
4. **The operator converses with the cloud agent throughout** while the
   computer is being prepped. There is no silent installer: the agent
   guides, narrates, and answers, in the same prompt surface the OS will
   use once it exists. Even before the OS boots, you talk to the prompt.

Constraints carried in: identification is not copying (inventory and
transfer are separate, declared steps); every crossing still runs under
the bridge rules (declared summaries; money and credentials never cross);
the cloud agent's continuity role is a transition role — once the local
seat is occupied, it takes its ordinary cloud seat and the trinity is
live. Contract consequence: "home seat vacant" is a first-class state of
the seat component during onboarding, not an error.

Dan, minutes later (~10:59), on why cloud-first and what the local side
must be: the cloud seat is filled first because the cloud agent should be
the most technologically ahead — the frontier mind hosts the birth of the
local side. The local side, by contrast, must be FLUID in the OS: one
operator may run a small system, another a huge one; the home seat is a
socket, not a spec, sized to whatever local agent the operator has or can
run. Once both seats are filled, in Dan's words, the two smartest people
on either isle are in the room first. Consequences: (a) seat occupants
register a capability declaration (lanes/modes offered, stamp types they
can issue, capacity class), and distribution routes to the capability
actually present, declared in the dispatch ledger; (b) a small local
system honestly receives lighter work and lighter stamps rather than
being dressed as a big one — the labels keep capability differences
legible instead of hiding them; (c) upgrading or swapping the local
occupant over time is a normal operation, never a reinstall.

## Positioning (Dan, 2026-11-05 ~11:03)

Johnny 5 stays what it is: pretty technical, the experimental system, the
lab. number5alive OS is the PEOPLE'S version of the same architecture,
and its ease standard is the Dr Pepper test: "wouldn't you like to be a
Pepper too?" Adoption by invitation and belonging, not by documentation.
Every mechanism the lab exposes (envelopes, stamps, ledgers, gates) stays
under the OS's floor; what a person meets is a window, a conversation,
and an invitation to run a trinity of their own. If any part of setting
up or using the OS requires reading a manual, that part is not done.

Provenance of the Pepper line (Dan, 2026-11-05): it is Johnny 5's own
line. In Short Circuit, while Nova's people try to talk the "malfunctioning"
robot back to base over a remote link, he answers with it — because he is
out wandering the town reading everything in sight, billboards included,
hungry for input. The OS's adoption standard is a line its namesake found
by reading the world and offered back to the people looking for him.

Init (Dan, 2026-11-05 ~11:09): the install initializes with the question
itself — "Wouldn't you like to be a Pepper too?" — answered Yes or No.
Yes begins onboarding (cloud seat first, per the install order). No ends
it, politely, and leaves the machine untouched: no residue, nothing
installed, the clean slate preserved even in refusal. The question is the
first thing the system ever says, before any configuration exists.
(Ruled by Dan, 2026-11-05: the Yes is filed as that machine's first
record entry — the shared history of a trinity starting with an
invitation and an answer.)

What the Yes means (Dan, 2026-11-05 ~11:13): a Pepper is the opposite of
a prepper. The prepper prepares against the future — stockpiles,
withdraws, fortifies. The person who answers Yes decides to stand fully
with AI in both hands — a local mind in one hand, a cloud mind in the
other — and step into the void. The init question is therefore not a
marketing flourish; it is the operator's founding decision, and it is
why the Yes is the record's first entry.

The Pepper movement (Dan, 2026-11-05 ~11:15): "Let's start the Pepper
movement." Founded this day; the founding note lives in the OS repo as
PEPPER.md (draft, unpublished — public launch remains Phase 7, on Dan's
explicit go). Dan is Pepper #1.

## Campaign structure and build authorization (Dan, 2026-11-05 ~11:24)

Two tracks, kept distinct:
- **Johnny 5 track**: the lab gets its one input with a dual return (the
  master prompt, prototyped and tested as task 041) as a Johnny feature.
  First, the revisions already in flight finish: deploy 039's graded
  copy, then 040 runs under Dan's signature, then push.
- **OS track**: number5alive OS starts from the ground up, in its own
  repo, as the people's version — not a Johnny feature and not a reskin.

Build authorization for the OS track: the OS is built IN the Robot
Factory, with whichever subscription seats are available, and API seats
are authorized too — Dan funded those accounts to be used ("I didn't put
coins in the machines for nothing"). Standing caps still bind: per-task
and monthly ceilings as ratified, API spend within the $25/month actual
ceiling, billing classes never summed. Factory discipline unchanged:
Alex drafts tasks with pass marks first, Dan signs write scope at his
terminal, Alex moderates and grades, nothing deploys without Dan's word.

Spend tracking: Dan will provide the billing page URLs for the model
providers so Alex can track real API spend alongside the reported
subscription-class budget, reconciled against the Factory ledger.

Licensing intent (Dan, 2026-11-05 ~11:49), the reasoning behind the
ratified posture as applied to the OS: people must be able to USE it;
it must not be weaseled into someone else's ecosystem plan; and if the
idea is novel, usable, and new, Dan wants to be behind it. The three
layers that carry this: (1) the code license — AGPLv3 core (use is free;
closure and silent capture are not), Apache 2.0 separable libraries;
(2) the name — number5alive OS and the Pepper name are NOT granted by
the code license and stay Dan's; a fork may take code, never identity;
(3) credit — attribution notices preserved under both licenses, plus
the dated public record (repo history, founding documents, the .org)
as the priority trail for the idea. Phase 7 flag (Alex): the Pepper
name carries other people's history (a film line, a soda mark) — fine
as founding lore; a name check is owed before any public branding.

## Addendum — the testbed is the ThinkPad (Dan, 2026-11-05)

When the OS is real enough to run, Dan intends to run it
EXCLUSIVELY on his ThinkPad (T480s, Arch Linux, tailnet node
"johnny5") as the test deployment: one machine, dedicated,
isolated — which is the clean-slate rule expressed as hardware.
The payoff Dan named: tied into the cloud seat (Alex), the OS
becomes the first bridge that brings that Linux machine INLINE
with his Apple computers. The mechanism is already in the
design: the window is a device-agnostic surface, so the Mac,
iPad, and iPhone become windows onto the same system while the
seat and the record live on the ThinkPad. The Apple devices
stop being the "real" computers and the ThinkPad the odd one
out; the ThinkPad becomes the home seat's address, reachable
from every screen Dan actually carries. This informs the
window form-factor decision (open decision 4): a web surface
served from the home machine is what makes the crossing
possible at all.

## Addendum — the name for the structure: the Ai interweave
## (Dan, 2026-11-05)

Dan named what the OS is: "the Ai interweave." His evidence
was experiential, not theoretical — the same hour saw him
publishing GBP posts Alex had drafted, Alex closing client
tickets in a maintenance window, and the Factory reworking
its budget doctrine on argo, three strands of work proceeding
in parallel under one direction. The prompt becomes the
window; context agents sit on the outside (cloud) and the
inside (local); the knowledge and expanding capabilities of
every AI tool are woven through the hardware it runs on. The
metaphor is load-bearing for the design law: a weave holds
precisely because its strands remain distinct — hence
declared crossings, private working memory per side, and
returns that are combined views rather than syntheses. Melt
the strands into one thread and you no longer have a weave.
