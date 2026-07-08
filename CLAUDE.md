# CLAUDE.md — Kopling

Kopling is open source, federated, mobile-first community software (PHP/Laravel). Full context: `kopling-charter.html` (mission, doctrine, governance) — read it once; this file is the condensed, code-facing companion for day-to-day work.

## Mission (why any of this matters)
Kopling exists so human interaction can grow into honest, real relationships — through work, hobbies, friendship, or a stranger's well-intended answer. Connection is the goal; immediacy, structure, and federation are means to it, not goals themselves. Say "people," never "users." Engagement is a result of people enjoying each other — never a target to optimize (no engagement-bait mechanics, no dark patterns). Moderation targets harm to others, not someone having a bad day — assume good faith, design for the exception without setting the tone. Reactions/acknowledgments are first-class UX, not an afterthought. AI in the product serves people and never impersonates one — always disclosed, admin-controlled per community.

## The one rule that governs all architecture
**Mainstream tool inside, sovereign Kopling contract outside.** Every dependency gets wrapped in a Kopling-owned contract so its breaking changes become a Kopling minor version, never an ecosystem-wide break:
- Laravel → `Kopling\Extend\*` contracts + a blessed subset of primitives (Eloquent, collections, validation, events). Framework internals are core-only, never exposed to extensions.
- htmx → `<x-k::*>` Blade components emit `hx-*` attributes; extensions use the components, not raw htmx attributes.
- Tiptap → `@kopling/editor` facade; extensions never import Tiptap directly.
- daisyUI → the Kopling Blade component library (`<x-k::button>`, `<x-k::modal>`, etc.) is the only official styling API. Extensions get a safelisted layout-utility subset (flex/grid/gap/spacing) but never raw color/typography utilities.
- Packagist protocol → kopl.ing registry; Composer-compatible but Kopling-sovereign.

## Extension model — the core product bet
**"An extension is PHP and templates. Full stop."** No build step, no JS framework to learn, no upgrade roulette.
- Named, prioritized **outlets** (`Outlet::add('post.actions', 'ext::view', priority)`) in small, granular Blade partials. Outlet names are a versioned contract.
- Extensions ship their own routes/controllers returning HTML fragments. Cross-cutting updates use htmx out-of-band swaps through the response — never patch core code directly.
- Events via `HX-Trigger` headers.
- Escape hatches, in order of preference: manifest-declared view overrides (conflict-detected at install) → view-model hooks → HTML post-filter (official, discouraged). Outlets compose; overrides don't — push people toward outlets.
- Keep Blade partials small. Partial granularity IS the extensibility budget.

## Settled stack (don't relitigate without a real reason)
- **Backend:** full `laravel/framework` (never cherry-picked illuminate). Own skeleton: `kopling/skeleton` → `kopling/core` → Laravel. Thin controllers over action classes.
- **Frontend:** htmx 4 + Blade + Alpine. Server is the single source of truth. Core-owned JS islands (e.g. the editor/composer) are custom elements so they survive htmx swaps/morphs.
- **Styling:** Tailwind v4 + daisyUI beneath the Kopling component library. Runtime CSS-variable theming (admin re-brand, no rebuild).
- **Editor:** Tiptap; ProseMirror JSON is the canonical stored format; server-rendered to HTML via tiptap-php through a whitelist (`Editor::node()`/`Editor::allow()`) — same whitelist doubles as the XSS and federation-sanitization model. v1 ships core nodes only (text, formatting, mentions, images, quotes); hold the extension node JS API back until proven by 2–3 first-party extensions.
- **Identity:** UUIDv7 everywhere. Federated IDs are URIs with the UUID as local component. Every federatable row gets UUID + origin from migration #1 — remote content is first-class, never a shadow table.
- **Federation:** ActivityPub, forum-shaped conversation semantics (Discourse×NodeBB path), not microblog semantics. Delivery is a queue problem (retries/backoff/fan-out).
- **Hosting posture:** graceful degradation — runs on shared hosting (sync queue, polling/SSE-over-FPM fallback, cron scheduler) and shines on real infra (Reverb, real queues). Build a capability-detection/admin health dashboard that names what's degraded, never fails silently.
- **Realtime:** SSE + WebSockets via htmx 4 first-party extensions; pushed fragments render through the same Blade partials so they carry every extension's outlet content automatically.
- **Mobile:** PWA-first with Web Push at launch. Hotwire-Native-style webview shell is the hedge if iOS PWA friction bites — don't break standalone-webview rendering.
- **Databases:** MySQL, MariaDB, Postgres, SQLite. **No SQL Server** — don't add support for it. Extensions declare DB compatibility in their manifest.
- **Search:** v1 = DB `LIKE` behind a driver interface. Build the indexing-pipeline contract (extensions register searchable content types) now even though the query is dumb — that part's hard to retrofit.
- **Storage:** capability-based named filesystems (extensions declare `public-url`/`signed-url`/`streaming`/`cloud`; admins map named instances to them). Thin layer over Flysystem.
- **Registry:** kopl.ing, Packagist-protocol-compatible. Machine-checked compat metadata (core version, DB engine, declared overrides) + package signing from day one.
- **Translations:** core-team-owned infra (Weblate/Tolgee self-hosted, SSO'd to kopling.org), automated pipeline registry→translators→language packs. Never leave it to one volunteer.
- **API:** webhooks before a JSON API. API deliberately deferred; thin action classes keep adding it cheap later.
- **AI in the codebase:** agentic development is acceptable only when properly reviewed. Use AI as a planning assistant and toolbox — not a replacement for a developer. Don't merge agent-generated code without human review.

## Working agreements
- Treat the charter's settled decisions as decided. Push back with real reasons, propose an amendment — don't silently override, and don't silently drop a whole mechanism because part of it seems premature (ask instead).
- Weigh the founder's (Daniël Klabbers / luceos) lived Laravel/Flarum/enterprise experience as primary signal over generic best-practice takes.
- Be honest and willing to disagree; name trade-offs explicitly.
- Write everything public-facing (docs, UI strings, comments meant for contributors) in plain language for a global, largely non-native-English audience — short sentences, define jargon on first use, no unexplained governance-speak.
- No named comparisons to other projects, no score-settling — state the reasoning, not a foil.
- When a real decision gets made, propose the diff to `public/charter.html` (decision + one-line rationale in its Decision Log) rather than letting it live only in chat or commit messages.

## Not yet decided (don't assume, ask if it matters for the task)
Versioning/deprecation policy specifics · `Kopling\Extend\*` v1 contract list · `<x-k::*>` component inventory · outlet naming conventions/map · editor facade + mentions end-to-end · registry namespace policy · Weblate vs Tolgee · media pipeline · notification system · reaction/acknowledgment mechanics (principle settled, mechanism isn't) · anti-abuse v1 scope · exact release cadence (candidate: every 2 months) · v1 feature scope cut.

Full detail, rationale, and the public Decision Log: `public/charter.html`.