# Authoring a new take-home

Each exercise lives in its own folder named after the role (e.g. `ai-engineer/`, `backend-engineer/`).

## Required files

```
<role>/
├── README.md     # candidate-facing brief
├── data/         # any input data the candidate needs
└── .gitignore    # candidate's solution dirs, env files, etc.
```

## Authoring principles

1. **Scope honestly.** Estimate a real time budget (~2–3h is the norm at Sanna — we pair the take-home with a 45–60min walkthrough) and design the brief so a senior person can finish in that window. Anything else selects for endurance, not skill.
2. **Mirror real Sanna problems.** The closer the exercise to actual product work, the more signal you get.
3. **State what you do NOT want.** Candidates over-build when the brief is silent. Be explicit: "no auth, no deployment, no polish on edge cases you didn't have time for."
4. **Don't lead the candidate to the answer.** If part of the test is whether they reach for the right tool/concept on their own, describe what to achieve, not what to build.
5. **The walkthrough is where most of the signal lives.** Keep written deliverables minimal; let the conversation surface design thinking.

## Before sharing externally

- Re-read the brief from a candidate's POV. Anything ambiguous?
- Sanity-check the time budget — could *you* hit a strong submission in the stated window?
- Make sure no internal-only notes have leaked into the candidate-facing folder.
