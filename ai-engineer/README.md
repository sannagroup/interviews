# AI Engineer Take-Home: Transaction Triage

Thanks for taking the time to do this. Below is everything you need.

The exercise should take around 3 hours. It's intentionally short. We're more interested in how you make tradeoffs under a real time constraint than in how polished the final result is.

After you submit, we'll set up a 45 to 60 minute walkthrough where you show us what you built and we talk through your design choices. That conversation is where most of the signal comes from, so please don't spend your time writing a design doc.

## Context

Sanna is a Norwegian accounting platform. Our thesis: minimize work for the business owner, maximize the accountant's efficiency, and use AI to progressively automate the accountant's workflows.

Each month, a business owner's bank transactions flow into Sanna. Today the accountant categorizes them, decides which need clarification from the owner, and drafts a follow-up question for the ambiguous ones. You're going to build a slice of that and prove it works.

## The exercise

We give you a CSV of about 50 Norwegian bank transactions and a JSON file of about 150 prior categorized transactions for the same business. Build a system that produces, for each new transaction:

1. A category from the provided chart of accounts (~20 buckets).
2. A triage decision: `auto-approve` (confident enough to post without review), `accountant-review` (an accountant should eyeball it first), or `owner-question` (we need to ask the business owner before we can categorize).
3. For owner-question rows, a draft question in Norwegian to send the owner. Group sensibly, so we don't ask the same person three separate questions when one will do.

And, importantly: show us it works. There's a section on that below; it matters as much as the system itself.

How you expose all this is up to you. A CLI is fine. A small web page is welcome but not required. We won't grade you on frontend craft for this exercise.

## Stack

We'd prefer TypeScript or JavaScript (Node, Bun, or Deno, whichever you like). That's our production stack, so any code you ship will be easiest to discuss in the walkthrough if it's in the same language. Other stacks are accepted, but hold the bar a bit higher on readability if you go that route.

For the LLM itself, use whatever's easiest for you to ship with. A frontier API (Claude, GPT, Gemini) is fine, and a local model via Ollama or llama.cpp is equally fine. Please don't spend your own money on API credits. We won't penalize you for picking a smaller or local model. We're grading the architecture around the LLM, not the LLM's raw quality. A simple, well-instrumented pipeline that gets some answers wrong with a 7B local model tells us more than a one-shot prompt that gets them right with GPT-5.

Use whatever framework you find best for the job. We'll ask about the choice in the walkthrough.

## Inputs we provide

In `data/`:

- `transactions.csv` is the batch of ~50 transactions to process. Columns: `id, date, amount_nok, counterparty, description, currency`.
- `history.json` is ~150 prior categorized transactions for the same business. Use them however you like.
- `chart-of-accounts.json` is the ~20 categories you can assign, with short descriptions.
- `labeled-examples.json` is 10 transactions we've labeled with the right answer (category, triage, reasoning). Use them however you find useful.

## Showing it works

We don't want this shipped on vibes. An LLM giving 50 plausible-looking answers is easy. Proving they're the right answers, at scale, is the actual job.

So please build a way to measure how well your system performs, run it, and show us the results. Specifically:

- A repeatable script we can re-run that produces a readable summary of how the system did.
- Something more useful than a single accuracy number. Think about what failure actually means here, and design the summary around that.
- The 10 examples in `labeled-examples.json` are a starting point, not the whole picture. Extend them with cases you'd want to test before trusting this in production.

We're not looking for a perfect headline number. We're looking for evidence you've actually looked at where your system fails, understood why, and can talk about what you'd change. That conversation is where most of the signal lives.

If you have time, it'd be interesting to see your thinking on cost ($/run), latency, how trustworthy your system's own confidence reports are, or how good the Norwegian questions actually read. None required. Pick whatever you find most interesting.

## What you submit

A GitHub repo (or a zip) containing:

1. Your code, with a README listing one command to install, one to run your system, and one to run whatever you built to measure it.
2. An output file (JSON or CSV) showing your system's results on `transactions.csv`.
3. A summary of how well it did, in whatever format makes sense: text, JSON, a printed table.
4. A short `LIMITATIONS.md` with a few bullets covering what you cut and what you'd do differently.

That's it. No design doc, no screen recording.

## Hard constraints

- No fine-tuning. Any LLM is fair game (frontier API or local) as long as you're not training your own.
- At least one meaningful tool call somewhere in the system. Not just structured output. Up to you what the tool does.
- Owner-facing questions must be in Norwegian. Internal output can be in English.
- Aim for around 3 hours. A focused submission tells us more than a heroic 10 hour one. We grade overbuilding down, not up.

## What we're not looking for

- Authentication, multi-tenancy, deployment.
- A polished UI.
- A custom-trained model.
- Edge case handling on things you didn't have time for. Just note them in `LIMITATIONS.md`.

## What happens next

When you're done, push to a private repo and add `@sanna-hiring` (or whoever we've told you). We'll confirm within one business day and schedule the walkthrough. In that call you'll:

- Show us your system end-to-end, including how you measured it.
- Walk us through your architecture choices and what you learned along the way.
- Talk about what you'd do differently with more time, more data, more scale.

Bring questions for us too. We want this to be a real conversation, not a one-way exam.

## Questions?

Email the hiring contact you've been working with. We try to reply same-day. We won't give hints on architecture or design (that's part of what we're evaluating), but anything unclear in the brief itself is fair game.

Good luck.
