# Software Engineer Take-Home: Transaction Ingestion

Thanks for taking the time to do this. Everything needed is below.

The exercise should take around 3 hours. It's intentionally short. What matters more is how you handle tradeoffs under a real time constraint, not how polished the final result is.

After submission, there's a 45 to 60 minute walkthrough to go through what you built and the design choices behind it. That conversation is where most of the signal comes from, so please don't spend time writing a design doc.

## Context

Sanna is a Norwegian accounting platform. Bank transactions flow into Sanna from a handful of bank APIs. From that point on, the books are only as trustworthy as the ingestion service that put those transactions there. Duplicates lead to wrong books. Skipping a real transaction leads to missing books. Either one destroys accountant trust.

The task is to build the gatekeeper.

## The exercise

The inputs, in `data/`:

- `existing-transactions.json` is what's already in the database.
- `incoming-batch.json` is the batch just received from the bank.
- `scenarios.md` describes the messy patterns the bank actually produces. Please read this first.

Build a service that processes the incoming batch. For each row, decide what should happen to it (insert, skip as a duplicate, update something already there, flag it for review — whatever taxonomy makes sense), persist the result, and record why that decision was made.

The service should:

1. Be idempotent. Running the same batch twice produces the same database state and the same decisions.
2. Record reasoning. For every incoming row, the output should include a defensible reason for the decision.
3. Not lose data on partial failure. If the process crashes halfway through, restarting it should converge to the right state without double-applying or missing anything.
4. Stay honest about ambiguity. When in doubt, flag the row rather than guess.

How the data is persisted is up to you. An in-memory store is fine. A SQLite file is fine. A Postgres container is welcome but not required. Database craft isn't graded for this exercise — what matters is the shape of the service and its data, not the persistence layer.

## Stack

TypeScript or JavaScript is preferred (Node, Bun, or Deno — whichever you like). It's the production stack here, so code in the same language is easiest to discuss in the walkthrough. Other stacks are accepted, but hold the bar a bit higher on readability in that case.

Effect.ts is used in the domain layer here. No familiarity with it is required — use whatever idiom is fastest: promises, classes, plain functions, a framework you like. The choice will come up in the walkthrough.

## Showing it works

A service like this is exactly the kind of code where "it ran without errors" tells you nothing useful. Cases collide, edge cases hide, idempotency breaks silently months later in ways that are very expensive to debug.

So please write tests. Cover the scenarios in `scenarios.md`, plus any others worth testing. Tests should prove specific behaviours. When one fails, the failure message should make the problem obvious.

If you have time, it'd be interesting to see your thinking on observability (how would an on-call engineer debug this at 3am?), schema evolution (what happens when the bank adds a new field?), or throughput at scale (what changes when this batch is 100k rows?). None required. Pick whatever you find most interesting.

## What to submit

A GitHub repo (or a zip) containing:

1. Your code, with a README listing one command to install, one to run the service against the provided data, and one to run the tests.
2. An output file showing the service's decisions on `incoming-batch.json`. One entry per incoming row, each with the decision and the reasoning.
3. A short `LIMITATIONS.md` with a few bullets covering what was cut, what you'd do differently, and edge cases spotted but not handled.

That's it. No design doc, no screen recording.

## Constraints

- No third-party library that solves the problem for you. Standard utilities like Zod, Vitest, and so on are fine. A package called `bank-ingestion-deduplicator` is not.
- The service has to actually run. The code will be executed against the provided data during the walkthrough.
- Aim for around 3 hours. A focused submission says more than a heroic 10 hour one. Overbuilding is graded down, not up.

Out of scope — don't build:

- Authentication, multi-tenancy, deployment.
- A UI of any kind.
- A polished database layer (in-memory is fine).
- Performance optimisation past "doesn't take minutes on 50 rows".
- Edge case handling on things you didn't have time for. Just note them in `LIMITATIONS.md`.

## What happens next

When you're done, push to a private repo and send the link to your hiring contact, who will let you know which GitHub user to add as a collaborator. (Sending a zip is also fine if you'd rather.) Expect a confirmation within one business day, then a scheduled walkthrough. In that call, you'll:

- Show the service running end-to-end, including the tests.
- Walk through your data model and decision taxonomy.
- Talk about idempotency, partial-failure handling, and what you'd do differently at scale.

Bring questions too. This should be a real conversation, not a one-way exam.

For anything before then, email the hiring contact you've been working with. Replies usually come same-day. Hints on architecture or design aren't available (that's part of the evaluation), but anything unclear in the brief itself is fine to ask about.

Good luck.
