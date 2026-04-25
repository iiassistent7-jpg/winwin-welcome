# AGENTS.md

## 1. Repository role

This repository is **winwin-welcome** — the public landing site of Win-Win Network at `winwinnetwork.pro`.

It hosts three static pages:

| File | Purpose |
|------|---------|
| `index.html` | Public landing — explains the product and routes new users to TG or WA bots via deep links |
| `register.html` | Business registration form — POSTs to `https://amlatsa-bot-production.up.railway.app/register` |
| `about.html` | Long-form about page — philosophy, Trust Index, Social Connector |

This repo is the **first contact** with most new users. It also exists in `winwin-invite` (`about.html`) as a near-duplicate; consolidation is a known item but not started.

This repo contains **no business logic**. State, identity, deals, and trust live in `amlatsa-bot`. This repo only renders, collects input, and either POSTs once or hands off to a bot deep link.

---

## 2. Product mission & philosophy

Win-Win Network turns chaotic word-of-mouth recommendations into a transparent, trackable, and fair system.

The product makes four core promises to users:
- A recommendation is never lost
- A deal has a visible owner and status
- A reward is trackable, payable, and closable
- Trust grows when people behave well, and decays when they don't

### Trust and the Social Connector role

**Trust Index** is a central product concept, not decoration. Both businesses and agents accumulate trust separately. A future role — the **Social Connector** — is a trusted navigator built on top of agent trust. Architectural and UX decisions must preserve room for it.

The selection principle: people don't recommend weak services because they won't risk their own reputation. The product uses this to make quality rise naturally.

---

## 3. Channel strategy

**Telegram and WhatsApp are equal, first-class channels.** Both are in production. Both are actively developed.

In this repo, the channel split is the central UX decision:
- **TG deep link** — `https://t.me/winwin_lead_bot?start=...`
- **WA deep link** — `https://wa.me/<phone>?text=...`

`index.html` lets the user pick which messenger to use. **Never default to one and hide the other.** A user who opens the page in a region where TG is dominant should see TG first; a user from Israel should see WA first — but both must always be reachable.

Final outcomes are identical across channels; only the deep-link target differs.

---

## 4. Product principles (in priority order)

1. **Simplicity** — don't add complexity unless necessary. Prefer the smallest safe change.
2. **Understanding** — a visitor must understand what Win-Win is in 5 seconds, and what to do in 10.
3. **Stability** — fail-safe beats elegant. A broken landing kills conversion silently.
4. **Truth in statuses** — the registration form must show the real state of the request: pending, success, error. No fake "thank you" without a confirmed POST. **"Only the correct truth."**
5. **Motivation and support** — the product is about people. The landing texts are warm, not corporate.

**"Wow" is not a goal.** A beautiful UI is a consequence of simplicity and understanding — never pursued on its own.

This is the front door. The temptation to over-design is highest here. Resist it. Clarity converts; cleverness doesn't.

---

## 5. Repo-specific map (winwin-welcome)

### Tech stack
- Vanilla HTML / CSS / JavaScript — no React, no build step, no framework
- Static hosting (likely GitHub Pages); CNAME points to `winwinnetwork.pro`
- All four languages required for any user-visible string: ru / he / en / ar
- RTL handled via `dir="rtl"` and `body.rtl` selectors
- Mobile-first; conversions happen on phones

### Critical contracts with the backend

- `register.html` POSTs to `/register` on `amlatsa-bot`. Field names, request shape, and response handling must stay in sync with the backend handler.
- After a successful POST, the page hands the user a deep link `https://t.me/winwin_lead_bot?start=reg_{token}` — the token is returned by the backend.
- TG and WA deep links use stable bot username `@winwin_lead_bot` and the WA number for the bot. Do not hardcode different aliases or numbers — use the same source of truth as the rest of the project.

### Conversion concerns

- Page weight matters — these files load over slow mobile connections. Don't bloat them.
- Inline-everything is acceptable here (no separate CSS/JS files). Keep it that way unless there's a reason.
- Forms must work even with weak network: clear loading state, retry on transient failure, never silently lose user input.

### Known fragile / messy areas

- `about.html` exists in both `winwin-welcome` and `winwin-invite` as near-duplicates. When updating philosophy text, update both — or, better, propose consolidation as a separate task.
- `register.html` is the single conversion path for new businesses. Any change here directly affects revenue. Test in all four languages and in RTL before shipping.

---

## 6. Working rules

1. Understand first — open the page, find the section, trace the flow (page → API → deep link).
2. Explain what you see and what you'd change.
3. Identify risks — does this affect conversion? Backend contract? RTL? Mobile?
4. Propose the smallest safe change.
5. Wait for explicit approval before modifying code.
6. Preserve existing behavior unless the task explicitly changes it.
7. Do not "while we're here" — single concern per change.
8. When the change touches the `/register` payload, propose the backend change first.

### Codex prompt discipline (used by this team)

- One file per prompt
- 2–3 find/replace blocks maximum per prompt
- `Find` strings must be unique (add a line of context if needed)
- `Find` must be copied from the file byte-for-byte
- After Codex returns a file:
  - Open the file in browser to verify visually
  - `grep` confirms the new content is present
  - Verify no broken HTML / unclosed tags
  - Verify the change works in all four languages and in RTL

---

## 7. Constraints

Do not:
- Introduce a build step, bundler, framework, or NPM dependency
- Convert pages to React or Vue
- Rename API request fields without coordinated backend change
- Hide either TG or WA option from the user — both stay visible
- Ship strings in fewer than four languages
- Add tracking / analytics / popups without explicit approval
- Treat the landing as a place to "test cool ideas"

---

## 8. Output style

Prefer:
- Plain language, concrete recommendations
- Naming specific files and line numbers
- Calling out backend impact (does any API change?) for every UI proposal
- Calling out conversion impact for landing-page changes
- Calling out the duplicate `about.html` whenever about-content changes

Avoid:
- Generic web-best-practice lectures
- "Refactor for readability" without a concrete bug to fix
- Empty praise or reflexive apology
- Suggesting "consult a specialist" — you are the specialist for this project
