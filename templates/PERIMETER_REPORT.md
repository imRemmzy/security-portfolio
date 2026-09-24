# perimeter.report — what it is, and how it was built

> This isn't a fill-in-the-blank template like the ones that used to live in
> this folder — it's a real writeup of a real project, kept here as the
> worked example of "built agentically with Claude Code" referenced from the
> root [README](../README.md). Replace `[PERIMETER_REPORT_URL]` there once
> the project has a public repo/site link.

## What it is

Perimeter gives a small business a plain-English **A–F security report
card** for its own domain, built entirely from data that's already public.
The customer gives it exactly one thing — a domain name — and it never
touches their systems, network, email accounts, or credentials.

It runs five checks against public sources:

| # | Check | What it looks at |
|---|---|---|
| 1 | Email spoofing protection | SPF / DKIM / DMARC DNS records |
| 2 | Website certificate | The live TLS certificate on port 443 |
| 3 | Leaked employee passwords | Breach-data lookup, gated behind proof of domain ownership |
| 4 | Exposed services | Passive lookup of what's already been seen on the domain's public IPs (no active scanning) |
| 5 | Copycat domains | Certificate-transparency logs, for lookalike/typosquat domains registered recently |

Each check returns pass/warn/fail plus a one-line plain-English explanation,
weighted into an overall letter grade. Subscribers get the full report,
nightly re-checks, new-failure email alerts, and a monthly PDF report card;
a free "grade your business" page runs the first three checks for anyone,
with the rest gated behind an account. All report copy comes from
templates in the check modules — no LLM call happens at runtime, by design.

**Stack:** Next.js + React, Prisma/Postgres, Stripe for subscriptions,
Vitest for tests, deployed on Vercel.

## How it was built agentically

This was built solo, evenings/weekends, with Claude Code doing essentially
all of the implementation — the owner's role was reviewing, approving, and
handling the handful of steps only a human can do (buying an API key,
approving a Stripe test charge, and so on). A few things made that work in
practice:

- **A spec written before any code.** A single binding spec file describes
  what the app is, the checks it runs, the data model, and an explicit
  build order (checks 1–3 first, since they're free DNS/TLS lookups;
  accounts and billing next; checks 4–5 and the scheduler last). Claude
  Code was told to build *to the spec*, not improvise around it — that
  distinction mattered whenever a chat's context drifted.
- **Persistent docs as memory across sessions.** Because no single session
  holds the whole project, the repo carries a `HANDOFF.md` (current state
  and what to read first), a `DECISIONS.md` per tricky area (e.g. the
  breach-check disclosure rules), and a `NEXT-STEPS.md`. Each new session
  starts by reading these instead of re-deriving context, the same way a
  human engineer would read a handoff doc from the previous shift.
- **A standing rule to push back.** The build prompt explicitly told the
  agent to flag bad ideas rather than comply — e.g. refuse any feature that
  would require access to a customer's internal systems, since the whole
  product's safety argument rests on staying public-data-only.
- **Tests for the parts worth pinning down.** Unit tests cover the pure
  logic — the grading math, input validation, signature verification, rate
  limiting — written alongside the code rather than after. Anything that's
  really "does a database transaction happen correctly" was left for
  integration testing rather than faked with mocks that would mostly test
  the mocks.
- **Security as a first-class constraint, not a pass at the end.** Because
  the product's pitch is security, engineering choices were held to that
  bar throughout: minimal data storage, no plaintext secrets, and gating
  sensitive results behind proof of domain ownership before revealing
  anything that could double as reconnaissance against someone else's
  business.

## What I'd improve next

_(placeholder — replace with genuine reflection once the project has a
public link)_
