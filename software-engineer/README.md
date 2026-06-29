# Software Engineer Take-Home — Transaction Ingestion

Welcome, and thanks for taking the time.

**Time budget: ~3 hours.**
That's short on purpose. We want to see how you make tradeoffs under a real time constraint — not how thoroughly you can polish a side project.

After you submit, we'll do a **45–60 minute walkthrough together** where you show us what you built and we talk through the design choices. Most of the design-thinking signal comes from that conversation, not from documents — so don't spend your time writing a design doc.

---

## Context

Sanna is a Norwegian accounting platform. Bank transactions flow into Sanna from a handful of bank APIs, and from that point on, **the books are only as trustworthy as the ingestion service that put those transactions there.** Duplicates lead to wrong books. Skipped real transactions lead to missing books. Both destroy accountant trust.

You're going to build the gatekeeper.

---

## The exercise

Given:
- A JSON file of transactions already in our database (`existing-transactions.json`)
- A JSON file of an incoming batch from the bank (`incoming-batch.json`)
- A short description of the messy real-world patterns the bank actually produces (`scenarios.md`)

Build a service that, for each row in the incoming batch, decides what to do with it (insert / skip-duplicate / update / flag / whatever taxonomy you design), persists the result, and records *why* the decision was made.

Your service must:

1. **Be idempotent.** Running the same batch twice produces the same DB state and the same decisions.
2. **Record reasoning.** For every incoming row, your output should include a defensible reason for the decision.
3. **Not lose data on partial failure.** If the process crashes halfway through, restarting it must converge to the right state — not double-apply, not miss rows.
4. **Stay honest about ambiguity.** When in doubt, flag — don't guess.

How you persist the data is up to you. An in-memory store is fine. A SQLite file is fine. A Postgres container is welcome but **not required** — we won't grade you on database operations craft for this exercise. What we care about is the *shape* of your service and its data, not the persistence layer.

---

## Stack

**Strong preference: TypeScript / JavaScript** (Node, Bun, Deno — whichever you prefer).
That's our production stack and any code you ship here will be easiest to discuss in the walkthrough if it's in the same language. Other stacks accepted, but if you go that route, hold the bar higher on readability.

We use Effect.ts heavily in our domain layer. **You don't need to know it.** Use whatever idiom you're fastest in — promises, classes, plain functions, a framework you like. We'll ask in the walkthrough why you chose what you chose.

---

## Inputs we provide

In `data/`:

- `existing-transactions.json` — what's already in our database.
- `incoming-batch.json` — the batch you're ingesting.
- `scenarios.md` — narrative description of the messy patterns the bank produces. **Read this first.**

---

## Showing it works

A service like this is exactly the kind of code where "it ran without errors" is meaningless. Cases collide, edge cases hide, idempotency breaks silently months later.

So: **write tests.** Cover the scenarios in `scenarios.md`, plus any you think we should also be testing. We're looking for tests that prove specific behaviours, not just "the function returned something". When your tests fail, the failure message should make the problem obvious.

If you have time: think about observability (how would an on-call engineer debug this at 3am?), schema evolution (what happens when the bank adds a new field?), or throughput at scale (what changes when this batch is 100k rows?). None of these are required — pick what you find most interesting.

---

## What you submit

A GitHub repo (or a zip) containing:

1. **Your code.** README with one command to install, one to run your service against the provided data, one to run your tests.
2. **An output file** showing your service's decisions on `incoming-batch.json` — one entry per incoming row, each with the decision and the reasoning.
3. **`LIMITATIONS.md`** — a few bullets: what you cut, what you'd do differently, edge cases you spotted but didn't handle. Keep it short.

That's it. No design doc, no screen recording.

---

## Hard constraints

- **No third-party libraries that "solve" the problem for you.** Standard utilities (lodash, zod, vitest, your favourite test runner) are fair game. A package called `bank-ingestion-deduplicator` is not.
- **The service has to actually run.** We will execute your code against the provided data during the walkthrough.
- **Aim for ~3 hours.** A focused submission tells us more than a heroic 10h one. We grade overbuilding *down*, not up.

## What we are NOT looking for

- Authentication, multi-tenancy, deployment.
- A UI of any kind.
- A polished database layer (in-memory is fine).
- Performance optimisation past "doesn't take minutes on 50 rows".
- Edge-case handling on things you didn't have time for — just note them in `LIMITATIONS.md`.

---

## What happens next

When you're done, push to a private repo and add `@sanna-hiring` (or whoever we've told you). We'll confirm within one business day, then schedule a 45–60 min walkthrough where you:

- Show us your service running end-to-end, including the tests.
- Walk us through your data model and your decision taxonomy.
- Talk about idempotency, partial-failure handling, and what you'd do differently at scale.

Bring questions for us too — we want this to be a real conversation, not a one-way exam.

---

## Questions?

Email the hiring contact you've been working with. We try to reply same-day. We won't give hints on architecture or design — those are part of what we're evaluating — but anything unclear in the brief itself is fair game.

Good luck. 🇳🇴
