# perimeter.report — what it is, and how it was built

> This isn't a fill-in-the-blank template like the ones that used to live in
> this folder — it's a real writeup of a real project, kept here as the
> worked example of "built agentically with Claude Code" referenced from the
> root [README](../README.md). Replace `[PERIMETER_REPORT_URL]` there once
> the project has a public repo/site link.

## What it is

Perimeter gives a small business a plain-English **A–F security report
card** for its own domain, built entirely from data that's already public. The reasoning behind this is simple; security first.
The customer gives it exactly one thing: a domain name and it never
touches their systems, network, email accounts, or credentials. Everything an attacker can use to pivot, I use to determine what's weak and where to improve.

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
templates in the check modules — no LLM call happens at runtime, by design to reduce overhead cost. (Subscribing doesn't actually charge anything, Stripe is in testing rather than prod mode.)

**Stack:** Next.js + React, Prisma/Postgres, Stripe for subscriptions,
Vitest for tests, deployed on Vercel.

## How it was built agentically

This was built solo, evenings/weekends, with Claude Code doing essentially
all of the implementation — my role was reviewing, approving, and
handling the handful of steps only a human can do (buying an API key,
approving a Stripe test charge, and so on). A few things made that work in
practice:

## What I'd improve next

I'd like to eventually get the actual breached credentials check working, however, finding a way around spending money on an API key has proven to be difficult. That and the security implications surrounding actually being able to randomly put in a domain and just receive a list of users with leaked creds. My attempt to solve this simply put was verifying that the user verified the domain is in their possession via either an email authentication to an admin account, or directly adding a hidden tag in your homepage, or by providing DNS records.


