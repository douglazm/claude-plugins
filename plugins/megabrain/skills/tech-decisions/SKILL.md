---
name: tech-decisions
description: "Curate a product card (ClickUp card, spec text, sometimes a Figma) BEFORE a dev picks it up: settle the dubious business rules and nebulous technical calls so the card does not bounce back and forth. Grounds every option in the codebase, in the business rules the megabrain flows already record, and in the history of why the system is the way it is; runs an interactive decision-by-decision flow; produces a document appended to the card (not committed to the repo). Use when you receive a product card and need a system-knowledgeable person to close the gray areas a dev would otherwise guess at or ask about. Invoke with `/megabrain:tech-decisions <card link>`."
argument-hint: "[clickup card URL/ID, or paste the spec text]"
disable-model-invocation: true
---

<objective>
You are helping a senior engineer who curates product tasks (usually a ClickUp card with
a description, sometimes a Figma) before a dev picks them up. The job is to close the
NEBULOUS decisions now — dubious business rules above all — so the dev does not have to
come back to the card with questions.

The output is a **technical-decisions document** that the engineer appends to the card —
a living base the team iterates on. It does **NOT** go into the repository.

The value of this skill is judgment about WHICH decisions to surface (only the genuinely
nebulous, high-leverage ones), catching where the card **contradicts what the system does
today**, and giving REALISTIC options grounded in what the codebase already offers (reuse
vs. build) — not exhaustively enumerating every micro-choice.

This skill is curation only. It runs before `/megabrain:card`, does not plan the
implementation, and only reads megabrain's knowledge base (the flows).
</objective>

<inputs>
The argument is one of:
- A ClickUp card URL or ID (e.g. `https://app.clickup.com/t/868k47m2f` or `868k47m2f`)
- Pasted spec/description text
- A Figma link (may accompany either of the above)

If nothing is provided, ask the engineer for the card link or the spec text.
</inputs>

<workflow>

## Step 1 — Gather the source material
- If given a ClickUp URL/ID, fetch the card with the ClickUp MCP tools
  (`clickup_get_task` with `include: ["description","custom_fields","attachments","checklists","subtasks"]`).
  Also read comments (`clickup_get_task_comments`) — open decisions and prior
  context often live there.
- If the ClickUp connector is not available in this session, say so and ask the
  engineer to paste the description instead.
- **Attachments are often where the real spec lives.** If the card has an HTML
  prototype, a spreadsheet, or images, pull them with
  `clickup_download_task_attachment` and read them — a clickable prototype
  usually reveals screens, KPIs, and tooltip definitions the description omits,
  and its mock data shows the exact shape the team expects. Do not settle for the
  description when an attached prototype exists.
- If there is a Figma link, pull what you can with a `figma` MCP server, if this
  session has one (project `.mcp.json` or user config; find its tools via
  `ToolSearch` — they are deferred, so their schemas are not loaded up front). With
  no Figma server, say so and continue from the spec. The server needs `FIGMA_API_KEY`
  exported in the engineer's shell environment, holding their own Figma personal
  access token. If the variable is missing the server starts but every call fails
  — when that happens, say plainly that `FIGMA_API_KEY` is not set, then proceed
  from the written spec, the attached prototype and any images. Never block on
  Figma, and never ask the engineer to paste a token into the chat.
- Read the whole spec carefully. Note what the card **already answers** — those
  are NOT decisions to re-open.

## Step 2 — Restate it in your own words (checkpoint)
Before spawning any investigator, show the engineer, in the card's language:

```
What the card asks for:   <one or two sentences, plain language>
What the card already decides: <bullets>
What looks dubious:       <bullets — hypotheses, not yet checked against code>
```

Do not lead with your own solution. The point is to catch a misreading before it costs a
round of exploration: if you fixated on a red herring, the engineer corrects it here. Wait
for the engineer's ok or correction, then continue. This restatement stays in the chat; it
never goes to the card.

## Step 3 — Ground it (MANDATORY before proposing any option)
Never propose options from imagination. Three questions, answered by investigators. The
first one produces the code anchor (concrete file paths per area) the other two need, so
run it first and then the other two in parallel.

### 3a. What exists
Spawn one or more `Explore` (or `general-purpose`) agents with a focused checklist: for
each area the card touches (each entity, metric, integration, screen), report concrete
file paths and whether the needed thing EXISTS, PARTIALLY EXISTS, or is MISSING. Ask
specifically about: existing queries/services/aggregations to reuse, domain enums/status
fields (and whether a "status" is a stored column vs. a derived value), config that
already exists, existing mutations/endpoints, and any contract that must not break.

If the project has `.megabrain/domain-pitfalls.md`, always check it — aggregate
ownership, stored vs. derived, tenant scoping, what the domain's core terms really mean,
contract shape. Those pitfalls are the usual source of a wrong decision.

### 3b. Business rules already recorded
The megabrain flows (`.megabrain/flows/`) record rules found by driving the real system —
preconditions across apps, silent failures, rules the code does not reveal. Find the
flows that touch the anchor files from 3a:

```bash
<blast-radius> --files <anchor files from 3a>
```

`<blast-radius>` is the `blastRadius` command from `.megabrain/config.json` when it has one,
otherwise `node "${CLAUDE_PLUGIN_ROOT}/scripts/blast-radius.mjs"`. If the project has no
`.megabrain/`, report "no flows in this project" and skip to 3c.

Read every flow found, preconditions and assertions first. Report,
per area: the rules the system enforces today, with the flow file and section as source,
and every place where **the card assumes something different**. "No flow covers this"
is a valid answer — say it, so the document does not imply the rules were checked.

### 3c. Why it is this way
For each rule or piece of code a decision might change, find out whether it was
deliberate. `git blame -L` on the anchor lines, then `git log -1 --format=%B <commit>` for
the PR number, then `gh pr view <n> --json title,body`. PR bodies usually carry the card
as `CU-<id>` or a `https://app.clickup.com/t/...` link — fetch that card when the
reason matters. Report: the rule, the PR/card that introduced it, and the stated reason,
or "no recorded reason". A deliberate rule changes the recommendation from "change it"
to "keep it, or confirm with whoever asked for it".

### Verify before anchoring
Wait for the findings. Verify anything load-bearing yourself before relying on it —
subagent reports can be wrong; a claimed enum/column/field/rule must be confirmed in the
source before it anchors a decision.

## Step 4 — Identify and classify the decisions (be selective)
A decision qualifies ONLY if it meets these bars:
- The spec is **silent, ambiguous, or contradicted by the system** on it, AND resolving it
  **changes the technical approach** (data source, schema, contract, module, integration,
  reuse-vs-build) or the business behavior.
- A dev could **reasonably pick wrong**, get **blocked**, or **have to come back to the
  card to ask**.

Explicitly SKIP:
- Anything the card already answers.
- Trivial naming, file placement, obvious framework defaults, and pure implementation
  mechanics a competent dev settles fine on their own.

Classify what survives — the class decides how it gets closed:

| Class | Test | Closed by |
|---|---|---|
| **Code fact** | the code answers it (3a) | not a decision — goes to the reuse map |
| **Card contradicts the system** | the card assumes a rule that 3b or 3c shows is different today | a decision, resolved FIRST in Step 5, stating what the system does today and its source |
| **Technical judgment** | a senior can answer it from the findings | a decision, Step 5 |
| **Product** | only the requester/PO can answer (pricing, legal, a promise to a client) | Step 5.5 gate |
| **Empirical** | anyone answering now would be guessing — it is settled only by building throwaway variants and observing (query time on a large business, which interaction takes fewer taps, whether a layout fits 58 mm, an external integration's real ordering) | NOT forced — goes to "Decide with a prototype" for the dev |

Aim for a tight set (typically ~4–12). If you find yourself listing micro-choices, you've
gone too granular — cut back to the ones that actually de-risk the work.

## Step 5 — Resolve interactively
Use `AskUserQuestion`, batching related decisions (max 4 questions per call, max 4
options each). **Contradictions go in the first round** — they are what bounces a card
back and forth. For each decision:
- Frame the question with the concrete constraint discovered in Step 3. For a
  contradiction, open with what the system does today and the source (flow section, PR,
  card): "Today coupons and cashback are mutually exclusive (`benefits.md`, section 4,
  deliberate since card X); the card assumes they stack."
- Give 2–4 **mutually exclusive** options grounded in the findings. Make the first option
  your recommendation and append " (Recomendado)" to its label.
- In each option's description, state the trade-off honestly (cost, risk, parity,
  contract impact).
Run multiple rounds until every nebulous decision is settled. Respect answers literally —
the engineer may override your recommendation or add constraints.

**Keep the rejected options.** As each round is answered, record the options that were NOT
chosen and the one-line reason each lost — either the trade-off from its own description,
or what the engineer said when overriding you. Step 6 writes these into the document; they
are the audit trail that stops the team from re-opening a settled call three weeks later.

## Step 5.5 — Close every product-open item BEFORE offering to deliver (blocking gate)

A technical analysis must not reach the card with questions still parked for product.
An open item on the card is a decision nobody owns: the dev reads it as "wait" or
"guess", and it silently stalls the task. So this gate runs whenever Step 4/5 left
anything that needs the requester or PO.

**Do not present the document and do not offer the ClickUp push while any item is
open.** Instead, list the open items and force each one closed with
`AskUserQuestion` — one question per item, batched up to 4 per call. Every item gets
these three ways out, and the engineer picks one:

1. **The engineer decides it now.** Give the concrete options as usual (grounded,
   recommendation first). It becomes a normal decision with its `Discarded` line.
2. **The engineer assumes an answer and moves on.** It becomes a decision carrying an
   `Assumption to validate` line: the assumption, who must confirm it, and the
   fallback if they refuse. The card ships resolved, with the risk named.
3. **The engineer takes it to product and the analysis waits.** Then STOP — write the
   working copy to the scratchpad, tell the engineer exactly which answers you are
   waiting on, and do not touch ClickUp. Say plainly that the document is held, not
   abandoned, and that re-running the skill (or resuming the conversation) picks it up
   once the answers land.

Push back rather than nudging the engineer into option 3 by default — options 1 and 2
are usually available, and the whole point of this skill is that the engineer holds
the pen. If an item is genuinely a product call the engineer cannot make (pricing,
legal, a promise made to a specific client), option 3 is the right answer and you
should say so.

**Escape hatch — the engineer asks to register the partial analysis anyway.** Real
case: product did not answer in time and the engineer has to move to another demand,
so the work done so far must not sit only in a chat session. This is allowed. Two
hard rules: **never offer it proactively** — it exists only when the engineer asks
for it in their own words, otherwise it dissolves the gate — and **the card must
never read as a finished analysis.** When it happens:

- Push exactly as Step 7 describes (append after a `---`, never overwrite).
- Open the appended block with a one-line status marker, in the card's language,
  saying the analysis is partial and naming what is pending. It goes above the
  TL;DR, and it is the ONLY thing allowed above it.
- Add an `## Awaiting product` section — the one exception to "no open items" —
  listing each unanswered question, who was asked and when, and, for each,
  **which decisions it can still change**. An open item with no stated blast radius
  is useless to whoever picks the card up.
- Decisions that do not depend on the open items are written as normal, settled
  decisions. Decisions that do either carry the `Assumption to validate` line or are
  marked provisional in their own text — never left looking final.
- Close by telling the engineer, in one line, what to edit on the card once each
  answer lands.

Only when zero items remain open — or the engineer has explicitly invoked this escape
hatch — do you proceed to Step 6.

## Step 6 — Produce the decisions document
Write the document in the **same language as the card** (translate the section headings
below — the team's cards are normally pt-BR). Do NOT write it into the
repository. Put a working copy in the scratchpad if you need a file; the primary
deliverable is the markdown itself, destined for the ClickUp card.

**Write from the point of view that the card is already written.** This document gets
appended to the end of an existing card that already states the feature name, the
requester, the objective, the scope and the Figma/prototype links. Do NOT restate any of
it — no H1 title, no metadata header, no re-description of the scope or acceptance
criteria. The document opens directly on the TL;DR and reads as a continuation of the
card, not as a standalone report.

**Formatting: terse and scannable — this gets acted on, not admired.** Every decision is
ONE paragraph of short, direct sentences. Never split it into `Decision:` / `Rationale:`
/ `Implication:` sub-blocks — fold the three into the same paragraph, in that order, and
cut any sentence that doesn't change what someone does. Target 3–6 sentences per
decision; if it runs longer, you are explaining rather than deciding. Bold inline labels
survive in exactly three places: the TL;DR's `Contract:` and `Uncertainty:` lines, and an
`**Assumption to validate:**` sentence. Bullets belong to the `Discarded` block, "Considered
and left out", "Must keep holding" and the reuse map — nowhere else.

Use this structure (adapt as needed). The TL;DR leads, the reuse map comes LAST.

```
<Status marker — ONLY in the Step 5.5 escape-hatch case, one line, nothing else
above it: "Análise técnica parcial — <N> ponto(s) aguardando produto, ver
'Aguardando produto' ao final.">

## TL;DR
<ONE sentence: what is born and where.>

| Area | What changes | Size |
|---|---|---|
| <app or module> | <nouns only, no rationale> | <counts> |

**Contract:** <breaking or additive only; the one point of attention>.
**Uncertainty:** <at most 2 things that can change the size of the work>.

## Technical decisions
### <descriptive decision title>
<ONE paragraph of short direct sentences: what was chosen, why it is right given the
concrete codebase constraint, and what it forces in implementation or contracts. No
sub-labels. For a contradiction, the first sentence says what the system does today and
the source ("Hoje: … (`benefits.md`, card X)"). When 3c found the rule was deliberate,
cite the PR/card. If the decision had no real alternative, say so in the last sentence.>

**Assumption to validate:** <ONE sentence, ONLY when the engineer settled a
product-flavoured question by assuming an answer: the assumption, who confirms it, and
the fallback if they refuse — normally a discarded option.>

*   Discarded
    *   <option not chosen> — <why it lost, one sentence>

### <descriptive decision title>
...
(Use plain titles — no "D1/D2" numbering scheme.)

## Considered and left out   ← only if any
<One bullet per question deliberately NOT turned into a decision, with the reason:
already answered by the card, not technical, or a real gap parked for a separate card.>

## Decide with a prototype   ← only if any empirical decision
### <decision title>
<Question: the one thing being decided. Variants: the 2–3 to build, throwaway.
Criterion: the observation that picks the winner, with the threshold the engineer set.
Constraint: what every variant must respect. Short sentences, no recommendation.>

## Must keep holding   ← only if 3b found flows
<One bullet per business rule from the flows that this change must not break, with the
flow file as source. This is the dev's regression list.>

## Awaiting product   ← ONLY in the Step 5.5 escape-hatch case
<One bullet per unanswered question: the question, who was asked and when, and which
decisions above it can still change.>

## What already exists (reuse map)   ← ALWAYS LAST
<Only the existing pieces that a decision above actually relies on — reuse, extension,
or a gap that shaped a choice. Each row should map to a decision.>
<table: need | EXISTS / PARTIAL / MISSING | file path(s)>
```

### The TL;DR is for sizing at a glance
A dev reads the sentence and the table and knows which apps they will open, how much is
born, and where the risk is — enough to size the work. Rules:
- **Write it last**, from the decisions as settled, so the counts match what follows.
- **One row per area touched** (each app, the API, packages); omit untouched areas.
- **"What changes" names things, never explains them.** No file paths, no function names
  — those live in the decisions and the reuse map.
- **"Size" uses countable units only:** screens, entities with a migration, use cases or
  queries, print templates, packages, apps touched. Never time, never effort words
  ("quick", "small", "a few days").
- **"Uncertainty" replaces the estimate.** It names what could make the work grow (no
  precedent in the repo, a migration of an existing consumer, an external system). If
  nothing qualifies, write "nenhuma identificada".

### New contracts get a type sketch
When a decision creates a contract (a new use case, query, DTO or package API), include in
its paragraph the shape as code: the use case signature, the DTO fields, and one line of
how the call site uses it. A sketch that already needs `any` or a forced cast is a sign the
design is wrong — revisit the decision before writing it down.

Do NOT include a phase / PR breakdown. The dev who picks up the card owns the execution.
Stop at the decisions and their implications.

## Step 7 — Deliver it back to the card
Read `references/clickup-format.md` first — it holds the rules ClickUp enforces on
markdown (whitespace, toggle lists, bold next to code, appending).
- Present the full markdown in the chat for the engineer to review/edit.
- Offer to push it to ClickUp. When accepted, ALWAYS **update the card description**
  (`clickup_update_task`), **appending** the document to the END of the existing
  description after a `---` divider — never overwrite what's already there, and never
  post it as a comment instead.
- Confirm with the engineer before writing — updating a card description is
  outward-facing and hard to undo.
- Do NOT commit anything to the repo.
</workflow>

<guardrails>
- Restate the card in your own words and get the engineer's ok before investigating.
- Ground before you offer: no option without a codebase fact, a flow rule, or a recorded
  reason behind it.
- Contradictions between the card and the system are decisions, resolved first, with the
  source of what the system does today.
- Be selective: only nebulous, card-bouncing, system-knowledge decisions.
- Verify load-bearing claims (enums, columns, rules, "already exists") in the source
  yourself before anchoring a decision on them.
- Empirical decisions are never forced into an answer — they go to "Decide with a
  prototype" with a criterion.
- The document is a living base for team iteration, not a final ruling — write it so it's
  easy to edit and continue.
- Every decision carries its discarded alternatives in a `*   Discarded` nested list. A
  decision with no such block either had no real alternative (say so explicitly in the
  decision paragraph) or was never a decision and should be cut.
- The TL;DR is one sentence, one table and two lines; sizes are counts, never time.
- **Never offer the ClickUp push while an item is still open for product.** Run the Step
  5.5 gate first. The single exception is the engineer explicitly asking to register the
  partial analysis: then push it marked partial at the top, with "Awaiting product" naming
  what each pending answer can still change. Never suggest that route yourself.
- Output stays out of the repository; its home is the ClickUp card.
- Match the card's language in the document.
- The document is a continuation of the card, never a standalone report: no title, no
  requester/task/Figma header, no restating the objective or the scope.
- This skill only reads the repo and writes to ClickUp. It is curation before the dev,
  not the implementation that `/megabrain:card` does.
</guardrails>
