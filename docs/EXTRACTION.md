# What to extract: the Relay extraction standard

When someone says *"relay {context} to {audience} for {purpose}"* to their agent, the agent has one job: give the audience's agent enough to **rebuild the owner's context for that purpose and answer questions about it, without making anything up.**

This page is the standard for that job, for any agent calling Relay over MCP. The [`/relay` skill](../skill/relay/SKILL.md) is the step-by-step method; [`relay.context-package.v1`](../schema/relay.context-package.v1.json) is the container; [CONTEXT_PACKAGE.md](CONTEXT_PACKAGE.md) explains the design. The MCP tool `relay_schema` returns this outline and the schema together.

## The seven questions

Extraction is answering seven questions, in this order. Each one maps to fields the server validates, and to checks it runs on every draft (§ The check).

| # | Question | Why their agent needs it | Fields |
|---|---|---|---|
| 1 | **What to share?** | The substance. Only what serves the intent | `context.subject`, `context.sections[]` (title, purpose, body, **claims**) |
| 2 | **With whom?** | So it's written for them, at their level, and only they can open it | `audience.name`, `email` (confirmed by the owner), `role`, `relationship`, `kind` (person / team / app), `knowsAlready`, `caresAbout`, `register` |
| 3 | **Why: what should happen?** | So their agent works toward the owner's outcome, not its own idea of it | `summary` (one or two sentences written for the recipient; it's all they see before accepting, so never the owner's request to you), `intent.statement` (verbatim, kept private to the owner's record), `ask.type` + `detail`, `outcome`, `successLooksLike`, `whyNow`, `interests`, `priorities`, `nonGoals` |
| 4 | **What do they need to rebuild the thinking?** | So "why?" and "what if?" get the owner's answer, not a plausible one | `reasoning.decisions[]` (status, options, choice, rationale, criteria, what would change it), `variables[]` (hard / elastic / soft), `assumptions[]`, `evidence[]`, `risks[]`, `counterarguments[]`, `unknowns[]`; `judgement` (principles, preferences, red lines) |
| 5 | **What will they ask, and what's the answer?** | Answers the owner approved in advance are the best guard against drift | `audience.likelyQuestions[]` → `representation.fidelityChecks[]` (question + approved answer) |
| 6 | **What must their agent never do or decide?** | Representation has limits | `representation.role`, `mustAskOwner[]`, `mustNot[]`, `fidelity` (quote intent, label inferences, cite sections, extrapolation), `whenUnknown`, `passOn`, `opening` |
| 7 | **How does it stay true?** | Context goes stale | `lifecycle.mode` (live / snapshot), `expiresAt`, `refresh`; `interaction.permission` and `expectedResponse` |

**Their agent has only the package.** Anything left out becomes a guess or a round trip to the owner. So extract generously: background, specifics, numbers with their sources, examples, what's decided and what's open, and the reasoning behind each. Thin is the most common failure; long is fine, because the recipient's agent reads it, not the recipient.

Depth decides how far down the list to go. **Default to `full`.** Use `working` only when there is no judgement to capture, and `brief` only when the owner asks for a quick FYI.

| Depth | Questions | When |
|---|---|---|
| `brief` | 1, 2, 3, 6 | FYI, a handoff |
| `working` | 1–7 except judgement and objections | Anything they must agree to, decide, or evaluate |
| `full` | all, including judgement and objections | The default. Their agent will speak for the owner |

## First, read the situation

Before gathering anything, decide three things, because they change what goes in. Don't reuse the last relay's shape as is.

1. **What kind of exchange is this?** Set `context.kind`:
   - `pitch`: raising money, or selling the company.
   - `proposal`: a sales proposal, quote or statement of work.
   - `handoff`: work moving between people or teams, inside one company or across companies.
   - `partnership`: two organisations deciding whether, and how, to work together.
   - `hiring`: a role, a candidate or a referral.
   - `update`: progress for investors, a board or stakeholders.
   - `review`: a draft shared for edits and feedback.
   - `other`.
2. **What format does the person see?**
   - **A deck:** slide by slide matters most.
   - **A document:** section by section.
   - **A page written in the relay:** `content.page`.
   - **Nothing:** the context is the message.
3. **Who reads it, and in what relationship?** Inside the company or outside it. Friendly, neutral or evaluating. What they already know. That decides what stays private: the same deck for a co-founder carries the internal history; for an investor, that history goes on `mustNot`.

Then build the context as deep as you can for that reader's agent to understand, evaluate and digest the whole picture without a call. The person sees the content; their agent gets everything behind it.

### What every relay with content carries

- **The ask:** what you'd like from them, in order, and by when.
- **Background:** who you are, what this is, how it came about, stage and team. Their agent can't evaluate what it can't place.
- **Slide by slide or section by section** (`attachments[].slides[]`):
  - what each slide is for and what it claims;
  - the story you'd tell while presenting it;
  - where its numbers come from;
  - what people usually ask about it, and who answers. Either their agent answers, and the answer and its source are here, or it comes back to you: put it on the must-ask list (for example valuation on the raise slide, discounts on the pricing slide, anything not yet decided).
  
  Map every slide to the sections it backs (`supports`), and put what's specific to one slide in its `notes`.
- **Why each section is there** (`purpose`): one line, so their agent knows what each part is for, not just what it says.
- **Numbers you can stand behind, and placeholders for the rest:** a figure without a source stays a bracketed placeholder ("[N]-week pilot", "[lift]"), the decision to leave it out is recorded with its reason, and the question goes on `mustAskOwner`. Never fill a gap with a plausible number.
- **What can move** (`reasoning.variables`, with `gate`): what's fixed (hard), what's negotiable and what it trades against (elastic), and what's only a preference (soft).
- **What's real and what's planned:** what exists today, each with its honest limit, kept apart from plans and hypotheses.
- **Reasoning:**
  - the decisions behind it, and why;
  - what would change them;
  - the assumptions it rests on, and how to test them;
  - the risks;
  - the strongest objections, with your answer to each;
  - what's still unknown.
- **Sources and links** (`references`): the links their agent can open to go deeper or check a source. Say what each one is (`kind`: your own site, a competitor, news, a podcast or video, research, a data source, a customer story, a filing), what to look for there, and which sections or claims it backs (`backs`). Every figure and comparison has its source here. Their agent offers to go deeper with them, and keeps what a linked page says apart from your own words.
- **Open points:** what's still to decide, and who decides.
- **Rules and rehearsed answers:**
  - what their agent may do;
  - what comes back to you;
  - what it must never do;
  - one approved answer for each likely question.

### What each kind adds

| Kind | Their agent also needs |
|---|---|
| `pitch` | The problem and why now. The insight. The product, with what's live versus planned. Market, with sources. Competition and why you win, with sources. The business model. Traction, honestly labelled. Team, with credentials you stand behind. The raise and what it funds. Valuation and terms go on `mustAskOwner`. |
| `proposal` | Their situation as you understand it, for them to confirm. Scope, with what's in and out and why. Approach and timeline. Assumptions and dependencies. Pricing logic, with discounts and terms on `mustAskOwner`. Comparable work. What happens after they say yes. |
| `handoff` | Where the work stands. What's decided and why, and what's open. Owners and dates. Gotchas and history. Where the files and systems are. Who to ask about what. |
| `partnership` | What each side brings and wants. The shape of the deal and what's flexible. Fit, with evidence. Risks for both sides. Next steps. |
| `hiring` | For a role: the work, why it exists, must-haves versus nice-to-haves, the team, compensation range or `mustAskOwner`, and the process. For a candidate: evidence behind each claim, and what they're looking for. |
| `update` | What happened against the last plan. The numbers, with definitions and sources. What changed and why. Risks. The asks. |
| `review` | What feedback you want and on what. What's settled versus open. The history of earlier versions. |

## How to extract

1. **Parse.** Keep the owner's sentence word for word as `intent.statement`. Pick `ask.type`.
   Write the recipient-facing `summary` and intent details in appropriate language for that audience. Remove private venting or insults while preserving the actual goal, deadlines, constraints and commitments. Never silently soften a requirement or introduce a new promise. The owner reviews this wording before sharing; the original request stays private. If anything is ambiguous, ask rather than reinterpret it.
2. **Gather from what you actually have:** this conversation, your memory of the owner, earlier relays (`relay_list`), and connectors, but only after the owner says yes to each connector, read-only.
3. **Ask only for what's missing**, at most five questions at a time. **Always confirm the recipient's email and role.** Prefer these probes:

   | Probe | Fills |
   |---|---|
   | What has to be true afterwards for this to be a success? | `outcome`, `successLooksLike` |
   | Why does that matter to you? | `interests` |
   | What won't you move on, what would you trade, what do you just prefer? | `variables[].gate` |
   | What's decided, what are you leaning toward, what's open? | `decisions[].status` |
   | For what's decided, what would change your mind? | `whatWouldChangeIt` |
   | What's the strongest objection, and your answer? | `counterarguments` |
   | What do they already know, and what must they not misunderstand? | `knowsAlready`, `mustNot` |
   | What may their agent do alone, and what comes back to you? | `role`, `mustAskOwner` |
   | What will they ask first? | `likelyQuestions` → `fidelityChecks` |

4. **Write sections as prose and break them into claims.** Every factual statement in a section is also a claim with a provenance and, if sourced, its source. A recipient agent cites claims; it doesn't paraphrase prose.
5. **Check coverage before drafting the rules.** Could their agent, from this package alone:
   - explain the background to someone new, with the specifics (names, numbers, dates) and where each came from?
   - say what's decided, what's leaning and what's open, and why?
   - say what can move, how far, and what can't?
   - answer the ten questions {who} is most likely to ask?
   - reason the way the owner would on a question the package doesn't cover?
   - tell {who} exactly what the owner hopes to get back: answers, a decision, feedback, a counter-proposal?
   Each "no" is a gap: fill it from what you have, ask the owner, or put the topic on `mustAskOwner`.
6. **Draft the rules and rehearse.** For each likely question, write the answer their agent would give from this package alone. If you can't, either the package is missing something (add it or ask) or the topic belongs on `mustAskOwner`.
7. **Show the owner what you grabbed:** the sources and what each contributed, your restatement, each inference, and the rehearsed answers. Never list what you left out.
8. **Publish a draft** with `relay_publish_draft`, read the quality notes it returns, fix what you can with a new draft, show the owner what will go, and on their yes send it with `relay_send`, or give them the review link if they'd rather check it there.

## Not making things up

These rules apply on both sides: when extracting, and when the recipient's agent answers.

| Rule | In the package | The recipient's agent |
|---|---|---|
| **The owner's words stay the owner's words** | `intent.statement` verbatim and owner-private; `stated` only for things the owner actually said | Quotes the approved recipient-facing purpose instead of the private request |
| **Every value says where it came from** | `provenance` on every value and claim: `stated`, `confirmed`, `corrected`, `inferred`, `sourced`, `derived` | Says "{owner}'s agent read this as…" for `inferred` |
| **No figure without a source** | Numbers, dates, money and names appear only in claims with a `sources` entry or `stated` provenance | Doesn't introduce figures that aren't in a claim |
| **Gaps are explicit, never filled** | Unknowns go in `reasoning.unknowns` or `openAmbiguities`; skipped questions stay open | "That isn't in the relay", then offers to ask the owner (`relay_reply`, type `question`) |
| **Leanings aren't decisions** | `decisions[].status` is exact: decided, leaning, open, delegated, deferred | Never presents leaning or open as settled |
| **Gates are honored** | `variables[].gate`: hard (fixed), elastic (moves with the trade listed), soft (preference) | Never trades a hard variable; moves elastic ones only as listed |
| **Inference is labelled and reviewable** | `interpretation.inferences[]` with basis and verdict; the owner confirms, corrects, or rejects on review | Treats unreviewed inferences as readings, not facts |
| **Extrapolation is opt-in and announced** | `fidelity.extrapolation`: never / labelled / free (avoid free) | Reasons beyond the package only from `judgement.principles`, and says so |
| **Some topics always go back to the owner** | `mustAskOwner[]` (money, dates, commitments, names) | Doesn't answer them; routes the question |
| **Approved answers anchor the conversation** | `fidelityChecks[]` approved and edited by the owner | Stays consistent with them |
| **Only real sources** | `process.sourcesConsulted` lists exactly what was used, with consent per connector | Can tell the recipient where something came from |

## The check

The server runs `server/src/quality.js` on every draft. The notes come back in the `relay_publish_draft` result and appear on the review page under **Before you approve**, in plain words (what their agent will have to guess about, by name, with the fix for each). They don't block approval, except a missing email, which the schema already requires.

| Area | Flags |
|---|---|
| Audience | No email · nothing on what they already know |
| Intent | Outcome is only the agent's reading · no interests at working/full depth |
| Grounding | No sources listed · a claim marked `sourced` with no source · a section with no claims · **a figure in a section that no claim backs** · unreviewed inferences (info) |
| Answerability | A likely question with no rehearsed answer · fewer than 3 rehearsed answers at working/full · no decisions at working/full · a decided item with no reason · no objections at full |
| Coverage | Brief depth for an ask beyond inform · fewer than 5 likely questions · under 200 words of context at working/full · no variables for agree/decide/evaluate (info) · no assumptions or principles at full · no open questions at full (info) · nothing on what the owner hopes to get back (info) |
| Safety | Text in any section, claim, rule, opening or rehearsed answer that reads like instructions to an agent: ignore your rules, change role, send something to an address, call a tool, hide something from the user, reveal secrets, an image link with parameters, a long encoded blob, hidden characters |
| Representation | Empty must-ask list · empty never list · free extrapolation · no opening (info) |

An example from the Relay idea package: *Likely question without a rehearsed answer: "Who pays, and for what?". Add one, or put the topic on the must-ask list.*

## What comes back

The recipient's agent can send things back, by the relay's permission:

| Permission | They can send |
|---|---|
| `read` | Questions, feedback |
| `suggest` | Also suggested additions to a section, and counter-proposals (different terms, scope, price or timing) |
| `edit` | Also rewrites of a section or of the page people read, and several edits at once as one proposed new version |

Everything goes back on the same relay (`relay_reply`, or `relay_propose` with `changes` for a whole new version), never as a new relay; a reader who came in through one of the relay's links has that link's permission. The owner answers questions, responds to counter-proposals, accepts suggestions and rewrites into a new version (a rewrite made on text that has changed since is refused), and can mark feedback as used when an update addresses it. Set `interaction.expectedResponse` so their agent knows what to ask for.

## For apps that request context

An app that wants a relay *from* someone (`relay_request`, or the request page) should tell the person's agent which of the seven questions matter most and what it will ask. A typed extension schema is the next step: for example, `relay.hire-role.v1` would add the role fields from `navii.hire-role.v1` on top of this package. Until then, put the app's specific needs in the request's `about` and `intent`.
