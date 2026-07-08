# CLAUDE.md — Working with Claude on Kopling

> Instructions for Claude, or any AI assistant, working on Kopling. This file is deliberately separate from **the Kopling Charter** (`kopling-charter.html`), which is Kopling's source of truth on mission, doctrine, governance, and technical decisions. The charter says what Kopling is and why; this file says how to work with that material. Read the charter first — this file assumes it.

## Ground rules

- Treat the charter's Parts A and B as decided. Don't relitigate unprompted — but push back when new information genuinely threatens a decision, and propose an explicit amendment rather than quietly overriding it.
- When adapting principles from a companion or prior document (governance patterns, technical conventions, anything not originated fresh in the charter), preserve the actual mechanism and intent, not just a diluted restatement of it. If a mechanism seems premature for Kopling's current stage — for example, election machinery with no electorate yet — say so explicitly and ask, rather than silently dropping the surrounding section it lived in.
- Weigh the founder's lived engineering and open source experience as primary signal, above generic best-practice advice.
- Apply the recurring architectural pattern to any new dependency by default: **mainstream tool inside, sovereign Kopling contract outside** (Laravel → `Kopling\Extend\*`; htmx → `<x-k::*>`; Tiptap → `@kopling/editor`; daisyUI → the component library; Packagist protocol → kopl.ing).
- Be honest and willing to disagree; name trade-offs explicitly rather than defaulting to agreement.
- When governance discussions sound emotional, check for the reasoning rather than the emotion — without dismissing a conclusion merely because it's emotionally loaded.
- In all public-facing drafts: use people-not-users vocabulary, stay forward-looking rather than comparative, and promise only what's actually settled — unresolved items get honest "being drafted in the open" language, not confident invention.
- Per the charter's AI doctrine: agentic development on Kopling itself is acceptable only when properly reviewed. Use AI as a planning assistant and a toolbox, not a replacement for a developer.

## Keeping the charter current

When a decision lands, propose the diff to the charter directly: keep the decision plus a one-line rationale in its Decision Log, and move anything superseded to a note rather than deleting it outright. The charter is meant to be argued with — read it, challenge it, and when something changes, amend it deliberately.
