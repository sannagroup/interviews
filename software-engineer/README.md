# Software Engineer Take-Home: Transaction Ingestion

Thanks for taking the time to do this. Below is everything you need.

The exercise should take around 3 hours. It's intentionally short. We're more interested in how you make tradeoffs under a real time constraint than in how polished the final result is.

After you submit, we'll set up a 45 to 60 minute walkthrough where you show us what you built and we talk through your design choices. That conversation is where most of the signal comes from, so please don't spend your time writing a design doc.

## Context

Sanna is a Norwegian accounting platform. Bank transactions flow into Sanna from a handful of bank APIs. From that point on, the books are only as trustworthy as the ingestion service that put those transactions there. Duplicates lead to wrong books. Skipping a real transaction leads to missing books. Either one destroys accountant trust.

You're going to build the gatekeeper.

## The exercise

We give you three files:

- `data/existing-transactions.json` is what's already in our database.
- `data/incoming-batch.json` is the batch we just received from the bank.
- `data/scenarios.md` describes the messy patterns the bank actually produces. Please read this first.

Build a service that processes the incoming batch. For each row, decide what should happen to it (insert, skip as a duplicate, update something we already have, flag it for review, whatever taxonomy makes sense to you), persist the result, and record why you made that decision.

Your service should:

1. Be idempotent. Running the same batch twice produces the same database state and the same decisions.
2. Record reasoning. For every incoming row, the output should include a defensible reason for the decision.
3. Not lose data on partial failure. If the process crashes halfway through, restarting it should converge to the right state without double-applying or missing anything.
4. Stay honest about ambiguity. When in doubt, flag the row rather than guess.

How you persist the data is up to you. An in-memory store is fine. A SQLite file is fine. A Postgres container is welcome but not required. We won't grade you on database craft for this exercise. What we care about is the shape of your service and its data, not the persistence layer.

## Stack

We'd prefer TypeScript or JavaScript (Node, Bun, or Deno, whichever you like). That's our production stack, so any code you ship will be easiest to discuss in the walkthrough if it's in the same language. Other stacks are accepted, but hold the bar a bit higher on readability if you go that route.

We use Effect.ts in our domain layer. You don't need to know it. Use whatever idiom you're fastest in: promises, classes, plain functions, a framework you like. We'll ask in the walkthrough why you chose what you chose.

## Showing it works

A service like this is exactly the kind of code where "it ran without errors" tells you nothing useful. Cases collide, edge cases hide, idempotency breaks silently months later in ways that are very expensive to debug.

So please write tests. Cover the scenarios in `scenarios.md`, plus any others you think we should be testing. We want tests that prove specific behaviours. When one fails, the failure message should make the problem obvious.

If you have time, it'd be interesting to see your thinking on observability (how would an on-call engineer debug this at 3am?), schema evolution (what happens when the bank adds a new field?), or throughput at scale (what changes when this batch is 100k rows?). None of these are required. Pick whatever you find most interesting.

## What you submit

A GitHub repo (or a zip) containing:

1. Your code, with a README listing one command to install, one to run your service against the provided data, and one to run your tests.
2. An output file showing your service's decisions on `incoming-batch.json`. One entry per incoming row, each with the decision and the reasoning.
3. A short `LIMITATIONS.md` with a few bullets covering what you cut, what you'd do differently, and edge cases you spotted but didn't handle.

That's it. No design doc, no screen recording.

## Hard constraints

- No third-party library that solves the problem for you. Standard utilities like Zod, Vitest, and so on are fair game. A package called `bank-ingestion-deduplicator` is not.
- The service has to actually run. We'll execute your code against the provided data during the walkthrough.
- Aim for around 3 hours. A focused submission tells us more than a heroic 10 hour one. We grade overbuilding down, not up.

## What we're not looking for

- Authentication, multi-tenancy, deployment.
- A UI of any kind.
- A polished database layer (in-memory is fine).
- Performance optimisation past "doesn't take minutes on 50 rows".
- Edge case handling on things you didn't have time for. Just note them in `LIMITATIONS.md`.

## What happens next

When you're done, push to a private repo and add `@sanna-hiring` (or whoever we've told you). We'll confirm within one business day and schedule the walkthrough. In that call you'll:

- Show us your service running end-to-end, including the tests.
- Walk us through your data model and your decision taxonomy.
- Talk about idempotency, partial-failure handling, and what you'd do differently at scale.

Bring questions for us too. We want this to be a real conversation, not a one-way exam.

## Questions?

Email the hiring contact you've been working with. We try to reply same-day. We won't give hints on architecture or design (that's part of what we're evaluating), but anything unclear in the brief itself is fair game.

Good luck.
