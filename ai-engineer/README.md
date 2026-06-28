# AI Engineer Take-Home — Transaction Triage

Welcome, and thanks for taking the time.

**Time budget: ~3 hours.**
That's short on purpose. We want to see how you make tradeoffs under a real time constraint — not how thoroughly you can polish a side project.

After you submit, we'll do a **45–60 minute walkthrough together** where you show us what you built and we talk through the design choices. Most of the design-thinking signal comes from that conversation, not from documents — so don't spend your time writing a design doc.

---

## Context

Sanna is a Norwegian accounting platform. Our core thesis:

> Minimize work for the business owner. Maximize the accountant's efficiency. Use AI to progressively automate the accountant's workflows.

Each month, a business owner's bank transactions flow into Sanna. Today, the accountant categorizes them, decides which need clarification from the owner, and drafts a follow-up question for the ambiguous ones. **You're going to build a slice of that — and prove it works.**

---

## The exercise

Given a CSV of ~50 Norwegian bank transactions and a JSON file of ~150 prior categorized transactions for the same business, build a system that produces, for each new transaction:

1. **A category** from the provided chart of accounts (~20 buckets).
2. **A triage decision**, one of:
   - `auto-approve` — confident enough to post without review
   - `accountant-review` — accountant should eyeball before approving
   - `owner-question` — we need to ask the business owner before we can categorize
3. **For `owner-question` rows: a draft question in Norwegian** to send the owner. Group sensibly — don't ask the same person 3 separate questions if they can be combined.

**And — show us it works.** See the "Showing it works" section below; this matters as much as the system itself.

How you expose all this is up to you. A CLI is fine. A tiny web page is welcome but **not required** — we won't grade you on frontend craft for this exercise.

---

## Stack

**Strong preference: TypeScript / JavaScript** (Node, Bun, Deno — whichever you prefer).
That's our production stack and any code you ship here will be easiest to discuss in the walkthrough if it's in the same language. Other stacks accepted, but if you go that route, hold the bar higher on readability.

**For the LLM itself, use whatever's easiest for you to ship with.** A frontier API (Claude, GPT, Gemini) is fine, and a local model via Ollama / llama.cpp is equally fine — don't spend your own money on API credits. We won't penalize you for picking a smaller or local model: **we're grading the architecture around the LLM, not the LLM's raw quality.** A simple, well-instrumented pipeline that gets some answers wrong with a 7B local model tells us more than a 1-shot prompt that gets them right with GPT-5.

Use whatever framework you find best for the job. We'll ask about the choice in the walkthrough.

---

## Inputs we provide

In `data/`:

- `transactions.csv` — ~50 transactions to process. Columns: `id, date, amount_nok, counterparty, description, currency`.
- `history.json` — ~150 prior categorized transactions for the same business. Use them however you like.
- `chart-of-accounts.json` — the ~20 categories you can assign, with short descriptions.
- `labeled-examples.json` — 10 transactions we've labeled with the right answer (category, triage, reasoning). Use them however you find useful.

---

## Showing it works

We don't want this shipped on vibes — an LLM giving 50 plausible-looking answers is easy. Proving they're the *right* answers, at scale, is the actual job.

So: **build a way to measure how well your system performs, run it, and show us the results.** Specifically:

- A repeatable script we can re-run that produces a readable summary of how the system did.
- Something more useful than a single accuracy number — think about what failure actually means here, and design the summary around that.
- The 10 examples in `labeled-examples.json` are a starting point, not the whole picture. Extend them with cases you'd want to test before trusting this in production.

We're not looking for a perfect headline number. We're looking for evidence you've actually *looked* at where your system fails, understood *why*, and can talk about what you'd change. That conversation is where most of the signal lives.

If you have time: think about cost ($/run), latency, how trustworthy your system's own confidence reports are, or how good the Norwegian questions actually read. None required; pick what you find most interesting.

---

## What you submit

A GitHub repo (or a zip) containing:

1. **Your code.** README with one command to install, one to run your system, one to run whatever you built to measure it.
2. **An output file** (JSON or CSV) showing your system's results on `transactions.csv`.
3. **A summary of how well it did**, in whatever format makes sense — text output, JSON, a printed table.
4. **`LIMITATIONS.md`** — a few bullets: what you cut, what you'd do differently. Keep it short.

That's it. No design doc, no screen recording.

---

## Hard constraints

- **No fine-tuning.** Any LLM is fair game — frontier API or local — as long as you're not training your own.
- **At least one meaningful tool call** somewhere in the system — not just structured output. Up to you what the tool does.
- **Owner-facing questions must be in Norwegian.** Internal output can be English.
- **Aim for ~3 hours.** A focused submission tells us more than a heroic 10h one. We grade overbuilding *down*, not up.

## What we are NOT looking for

- Authentication, multi-tenancy, deployment.
- A polished UI.
- A custom-trained model.
- Edge-case handling on things you didn't have time for — just note them in `LIMITATIONS.md`.

---

## What happens next

When you're done, push to a private repo and add `@sanna-hiring` (or whoever we've told you). We'll confirm within one business day, then schedule a 45–60 min walkthrough where you:

- Show us your system end-to-end, including how you measured it.
- Walk us through your architecture choices and what you learned along the way.
- Talk about what you'd do differently with more time, more data, more scale.

Bring questions for us too — we want this to be a real conversation, not a one-way exam.

---

## Questions?

Email the hiring contact you've been working with. We try to reply same-day. We won't give hints on architecture or design — those are part of what we're evaluating — but anything unclear in the brief itself is fair game.

Good luck. 🇳🇴
