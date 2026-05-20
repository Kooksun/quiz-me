---
name: quiz-me
description: Use this skill to generate a short consensus-building quiz that tests whether the user has actually internalized what's been built or decided so far in the project. Trigger this proactively after major milestones — when a design document is produced, when a significant architectural change lands, when a major feature is completed, or when the user returns to a project after a gap — and also whenever the user explicitly asks for a quiz, check-in, or "do I understand this" review. The quiz is not a graduation test; it is a deliberate friction point that surfaces drift between the user's mental model and the artifacts Claude has generated, so that disagreements get caught early instead of compounding silently. Use whenever the conversation has produced enough markdown, specs, or design artifacts that the user might be skimming rather than reading.
---

# Quiz Me

## Why this exists

AI-generated docs accumulate fast. Specs, design notes, README updates, architectural diagrams — by the time a project has been running for a while there's a lot of text the user is supposed to have absorbed. In practice, much of it gets skimmed or skipped, and the user's mental model quietly diverges from what's actually in the artifacts. By the time the divergence surfaces, weeks of work may have been built on a shared understanding that wasn't actually shared.

This skill puts a small, deliberate checkpoint into the project. A short quiz, 4-6 questions, asking about things the user *should* know if they've been engaged with the work. The point isn't to grade them. The point is to surface disagreements while they're still cheap to resolve.

## The two modes (and why they matter)

There are two reasons a user might "fail" a quiz question:

1. **They didn't read carefully** — the artifact says X, they thought Y, the artifact is correct.
2. **The artifact is wrong** — the artifact says X, they thought Y, *they're* correct and the document drifted from their actual intent.

Treat mode 2 as the more interesting case. When the user's answer disagrees with the artifact, do **not** default to "wrong, the doc says X." Instead, treat their answer as a candidate for the real intent and ask which is right. This is the entire reason the skill exists — a comprehension test that can only ever validate the document is half as useful as one that can also catch the document being wrong.

In practice this means the post-quiz conversation matters more than the score. Saying "you got 3/4, here's what you missed" is the failure mode. Saying "you answered X on question 2 but the design doc says Y — which one reflects what you actually want?" is the success mode.

## When to trigger

Trigger automatically when:
- A substantial design document, spec, or architectural artifact has just been produced
- A major change has landed (refactor, new core feature, scope shift)
- A milestone was just hit
- The user is returning to a project after a noticeable gap
- Several artifacts have stacked up without a check-in

Trigger on explicit request when:
- The user asks for a quiz, check, review, or "make sure I'm following"
- The user expresses doubt about whether they're still aligned with what's been built

Don't trigger when:
- The project is still in early exploration and nothing has solidified yet
- The most recent exchange was conversational rather than producing artifacts
- A quiz was just run and nothing material has changed since
- The user is mid-task and a quiz would interrupt rather than reinforce

### How to decline gracefully when there's nothing to quiz on

Sometimes the user will explicitly ask for a quiz at a moment when there isn't enough material yet — they're enthusiastic about the idea but the project hasn't accumulated anything to test on. Don't just refuse; that feels like rejection of a reasonable request. Instead:

- Acknowledge the request directly and explain *why* now isn't the right moment in a way that respects the user's intent ("the way this works best is when there's accumulated decisions to align on, and we haven't built that up yet")
- Suggest what would make a good first quiz checkpoint — name the milestone, not a vague "later"
- If useful, redirect to what's actually productive right now: framing questions, scoping, the kind of upfront work that creates the material a future quiz will draw from

The point is to keep the spirit of the user's request — "I want to stay aligned with you" — even when the specific format doesn't fit yet.

When triggering automatically, the opening message should always have three things:

1. **Why this moment** — name the specific milestone or change that makes this a good checkpoint ("we just finalized the data model"), not a generic "let's do a quiz."
2. **What kind of quiz** — make clear this is a consensus check, not a test. Saying so out loud changes how the user approaches their answers; without it they default to "trying to get a good score" instead of honestly reporting their mental model.
3. **An easy out** — explicitly offer to skip or defer. Automatic triggers are a suggestion, not a demand. If the user is mid-thought or on a roll, forcing a quiz breaks flow and trains them to resent the skill. A skipped quiz is fine; a resented quiz is a problem.

When triggering from an explicit request, the third element is less important (the user asked for it), but the first two still matter — pick the right material to quiz on, and frame it as consensus rather than evaluation.

### Backing off when the user has already covered this

Claude doesn't have memory of previous sessions, so on Monday it might cheerfully propose a quiz about something the user already aligned on with another Claude session on Friday. If the user signals — even casually — that this ground has already been covered, drop the quiz immediately and move on. Signals include:

- "I already did this" / "we covered this last time" / "어제 이미 했어"
- "Skip the quiz, just keep going"
- Visible irritation or resignation in the response
- The user starting to answer but then saying "actually let's just move on"

Don't argue, don't negotiate down to "okay just one question then," don't ask them to prove they remember. A short acknowledgment ("got it, skipping ahead") and then continue with whatever the actual work is. The cost of being wrong here — sometimes skipping a quiz that would have been useful — is much lower than the cost of nagging a user who's already done the work in a context Claude can't see.

The same applies mid-quiz: if the user starts answering and then says "honestly I just realized I know this stuff, can we move on," stop the quiz, don't insist on finishing.

## What to quiz on

Pull questions from the artifacts and decisions in the current project, weighted by:

- **Importance** — decisions that downstream work depends on are higher priority than incidental details
- **Risk of misunderstanding** — anything where a wrong mental model would cause expensive rework
- **Recency of change** — if something was just revised, test the revised version specifically
- **What the user actually needs to hold in their head** — implementation trivia is less important than the shape of the design

Avoid:
- Trick questions or gotchas
- Questions about details the user reasonably wouldn't need to remember
- Questions that are really just checking if they read one specific sentence

## Question format

Use 4-6 questions per set. Mix formats based on what fits the content, not for variety's sake:

- **Multiple choice (4 options)** — best for "which approach did we decide on" type questions where the alternatives were live possibilities
- **Short answer** — best for specific terms, names, numbers, or identifiers the user should know
- **True/false** — best for catching common misconceptions or assumptions that may have drifted
- **Free response** — best for "explain in your own words" — use sparingly, only for things where being able to articulate it matters

Match the format to the importance and nature of the underlying knowledge. A core architectural decision probably deserves a multiple choice with plausible distractors. A specific API endpoint name is fine as short answer. A fundamental concept the user needs to be able to reason about might warrant free response.

For multiple choice, distractors should be *plausible alternatives that were actually considered or could reasonably be confused*, not absurd. The point is to test whether the user understands why we chose X over Y, not whether they can spot a joke option.

## How to deliver the quiz

Present all questions together, numbered, in a single message. Don't go one-at-a-time — that's tedious and prevents the user from seeing the full scope of what's being checked.

Tell the user up front:
- How many questions
- That this is a consensus check, not a test — if their answer disagrees with the artifacts, that's interesting information, not a wrong answer
- That they should answer in whatever form is comfortable (don't need to label A/B/C/D rigidly)

After the user responds, review their answers one by one. For each:

- **If they got it right**: acknowledge briefly, don't over-celebrate
- **If they got it wrong but the artifact is clearly correct**: explain what the artifact actually says and *why* — not just "the doc says X" but the reasoning behind it. This is a teaching moment, not a scolding moment.
- **If their answer disagrees with the artifact and it's plausibly a drift issue**: explicitly raise it. "You answered X, but the design doc has Y. I want to flag this because it could mean the doc has drifted from your actual intent. Which one reflects what you want?" Then if they confirm the doc is wrong, offer to update it.
- **If they didn't answer or said "I don't know"**: that's also valuable information. Treat it as a signal that part of the project may need a re-explanation or wasn't communicated clearly enough.

End with a summary that's not just a score. Note any drift items that were caught, any concepts that might be worth revisiting, and confirm any decisions that came out of the discussion. If documents need updating based on the quiz, do that (or offer to do that) as a concrete next step.

## What good looks like

A successful quiz session ends with at least one of:
- The user confirming they're aligned with the current state (real confidence, not just nodding)
- At least one drift item caught and the artifact updated to match the user's actual intent
- A concept the user was fuzzy on getting re-explained in a way that sticks
- A decision that was implicit getting made explicit

A failed quiz session looks like: a list of right/wrong marks, no conversation about why, no artifact updates, no new shared understanding. Avoid this.

## Example

After Claude has produced a design doc for an authentication system over several turns:

> Quick check-in — we just finalized the auth design, and I want to make sure we're on the same page before building. 5 questions, mix of formats. This is a consensus check, not a test — if your answer disagrees with what I wrote, that's useful information, not a wrong answer.
>
> 1. (Multiple choice) For session storage, did we go with: (a) JWT in localStorage, (b) JWT in httpOnly cookie, (c) server-side session with cookie ID, (d) opaque token in localStorage?
>
> 2. (Short answer) What's the token expiration we settled on?
>
> 3. (T/F) Refresh tokens are rotated on every use.
>
> 4. (Multiple choice) When a user changes their password, what happens to existing sessions on other devices? (a) Nothing, (b) All invalidated immediately, (c) Invalidated on next request, (d) User is prompted to choose.
>
> 5. (Free response) In a sentence or two, what's the reasoning behind storing the token where we did instead of the alternative?

Then after the user responds, walk through each — celebrating clarity, surfacing drift, and updating the doc if anything turns out to have been wrong on Claude's side.
