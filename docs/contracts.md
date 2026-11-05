# number5alive OS — Contracts (Phase 0)

Status: APPROVED by Dan, 2026-11-05. This document is the Phase 0
deliverable of `PLAN.md`: the three contracts every component is built
against. Rule of the house: pass marks and contracts are written before
code. The three open questions below were approved with the draft's
assumptions standing: spoken returns render one named seat's
generation; cloud-seat transport is the bridge-file dispatch proven in
task 041; the reference build assumes a single operator.

## 0. Vocabulary

- **Operator**: the human node of the trinity. The only seat allowed to
  decide without a stamp.
- **Seat (the component)**: the routing layer ("seat of consciousness").
  Routes, dispatches, packages. Holds no opinions about content.
- **Home seat / cloud seat**: the two interpreter roles. Each is occupied
  by one agent at a time (home reference: Johnny 5; cloud reference: Alex
  on Muse; first third-party occupant: Hermes, home seat).
- **Record**: the shared, append-only store on the home machine. Crossings
  are declared filings in it, under the bridge rules.
- **Generation**: one seat-occupant's complete return for one prompt.

## 1. The prompt envelope

Everything the window sends the seat, and the seat files to the record.

```json
{
  "kind": "prompt",
  "id": "uuid",
  "ts": "2026-11-05T16:50:00Z",
  "operator": "dan",
  "addressee": "auto | home | cloud | both",
  "lane_hint": "optional, e.g. grasshopper",
  "body": {
    "text": "the prompt, verbatim",
    "attachments": [
      {"kind": "text|voice|image|file|video|screen|livefeed",
       "ref": "runtime path or stream id", "mime": "type"}
    ]
  },
  "record_ref": "record entry id, set by the seat when filed"
}
```

Rules: the text is carried verbatim end to end; the seat never rewrites a
prompt. Attachments travel by reference, never inlined into other stores.
`addressee: auto` leaves distribution to the seat's policy; an explicit
addressee is obeyed and logged as operator-directed.

## 2. The generation and the return envelope

One generation per tasked seat; one return envelope per prompt, carrying
every generation whole.

```json
{
  "kind": "generation",
  "prompt_id": "uuid",
  "seat": "home | cloud",
  "agent_id": "johnny5-laya | hermes | alex-muse | ...",
  "lane": "grasshopper | butterfly | input | hangglider | n/a",
  "status": "answered | abstained | failed",
  "stamp": "verified-agreement | labelled | bare | none",
  "text": "the generation, verbatim, full breadth",
  "sources_declared": ["what the agent declares it used"],
  "escalation_ref": "record/bridge ref when an abstention escalates",
  "latency_ms": 0
}
```

```json
{
  "kind": "return",
  "prompt_id": "uuid",
  "ts": "...",
  "generations": ["one generation object per tasked seat"],
  "gaps": [{"seat": "cloud", "reason": "unreachable — watcher offline"}],
  "dispatch_ref": "dispatch ledger id"
}
```

Rules (design law 2, 3, 9 made mechanical):
- A generation's `text` is never edited, trimmed, summarized, or blended
  by the seat or the window. The window renders; it does not rewrite.
- `stamp` is whatever the occupying agent actually issued, passed through
  untouched. The seat may not upgrade, downgrade, or hide it.
- `status: abstained` is a complete, valid generation and renders as
  itself. The other seat's answer never fills or covers it.
- A seat that was tasked and did not answer appears in `gaps` with an
  honest reason. A gap is shown, never papered over.

## 3. The seat-adapter contract

What any agent must satisfy to occupy a seat. An adapter is a small,
single-purpose bridge between the seat component and one agent.

An adapter MUST:
1. Accept a **prompt subset**: the prompt envelope minus anything the
   bridge rules do not allow to cross to that seat. The seat computes the
   subset; the adapter never reaches past it.
2. Return exactly one generation object per prompt, with `status` and
   `stamp` set honestly (an agent that issues no stamps returns
   `stamp: none`; an agent that declines returns `status: abstained`).
3. Pass an abstention through unmodified, including its escalation
   reference where the agent's own machinery files one.
4. Declare its sources in `sources_declared` at the granularity the agent
   itself knows them. No source is invented at the adapter layer.

An adapter MUST NOT:
5. Read or write another seat-occupant's files, memory, or state
   (clean-slate rule: per-agent homes, no crossover).
6. Cache prompts or generations outside its own home.
7. Add framing, apology, summary, or any other text to a generation.

Isolation layout (clean slate, per Dan's 2026-11-05 ruling). Source lives
in this repository. Runtime state lives in ONE runtime root, created
fresh; nothing is inherited from any prior agent install on the machine:

```
<runtime-root>/
  agents/<agent_id>/     that agent's home: config, memory, cache
  seat/                  dispatch ledger, routing policy
  record -> the shared record store (the one crossing point, by design)
```

## 4. The dispatch record

Per prompt, the seat appends one ledger entry: prompt id, addressee as
requested, seats tasked, lanes chosen, the record entries selected as
context for each seat (so any return can be audited to what each side
saw), timestamps, and the return envelope's id. The ledger is append-only
and lives with the seat, not with either agent.

## 5. Versioning and change

Envelopes carry no version field yet; Phase 0 freezes these shapes as
v1 by convention: any change to a field's meaning is a contract change,
proposed in writing, approved by Dan, and noted here with its date —
never a silent drift in an adapter.

## Open questions carried in this draft (resolved at approval, 2026-11-05: the draft's assumptions stand)

- Spoken returns (Phase 3b): the draft assumes speech renders ONE named
  seat's generation, never a blend. Confirm or amend.
- Cloud-seat transport before the portable-operations door exists: the
  bridge-file dispatch proven in task 041 is the assumed mechanism.
  Confirm or amend.
- `operator` identity in the envelope: single-operator assumption for the
  reference build. Multi-operator is a later contract change, not a
  Phase 1 concern.

## Addendum — onboarding order (Dan, 2026-11-05)

Installation hooks up the CLOUD seat first. The cloud-seat occupant runs
the transition protocol (identify files/data; create the pathway; stay
present; transfer where approved) while the local side is prepared. The
seat component therefore treats **home seat vacant** as a first-class
state: prompts route cloud-only, and envelopes carry the home generation
as an explicit gap ("not yet installed"), never as a failure. Once the
local agent is set up or signed in and occupies the home seat, the state
lifts and ordinary distribution applies. See PLAN.md, "Installation and
onboarding."

## Addendum — capability declaration (Dan, 2026-11-05)

The home seat is fluid: occupants range from small local systems to
large rigs. On occupying a seat, an adapter REGISTERS a capability
declaration: lanes/modes offered, stamp types it can honestly issue, and
a capacity class. The seat's distribution policy routes to the capability
actually present, and the dispatch ledger records which declaration was
in force for each prompt. An occupant swap or upgrade is a new
declaration, not a new install.
