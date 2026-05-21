# quiz-me

A Claude skill that runs short consensus-building quizzes to keep you aligned with the artifacts Claude is generating in your project.

## What problem does this solve?

Working with Claude on a real project produces a lot of markdown. Design docs, specs, README updates, architectural notes — over a few sessions, it piles up faster than you actually read it. You skim. You nod. You move on.

Then three weeks later you find out your mental model of the auth flow doesn't match what's actually in the design doc, and the work built on top of it is now slightly wrong in ways that are expensive to unwind.

`quiz-me` puts a small, deliberate checkpoint into the process. Claude will occasionally — at sensible moments like after a major design doc lands or a milestone is hit — propose a short 4-6 question quiz on what's been decided so far. You answer, Claude reviews.

The point isn't grading. The point is catching divergence while it's still cheap.

## What makes it different from a normal quiz tool

The skill treats your answer as a candidate for the real intent, not as something to be marked right or wrong against the document.

If you answer "X" and the design doc says "Y", the default response isn't "wrong, the doc says Y." It's "you said X but the doc says Y — which one actually reflects what you want?" Sometimes the doc is right and you forgot. Sometimes you're right and the doc drifted from your intent during a long session. Either case is useful information; the second case is the more valuable one, because it catches a bug in the documentation before it propagates.

This is the whole reason the skill exists. A comprehension test that can only validate the document is half as useful as one that can also catch the document being wrong.

## When it triggers

**Automatically**, when Claude judges it's a sensible moment:
- A substantial design document or spec was just produced
- A major architectural change landed
- A milestone was hit
- You're returning to a project after a noticeable gap
- Several artifacts have stacked up without a check-in

**On request**, whenever you ask:
- "quiz me on what we've built so far"
- "make sure I'm following"
- "check-in time"

**It backs off** when:
- The project is still in early exploration and there's nothing to quiz on yet
- You signal you've already covered this (in another session, etc.) — even casual signals like "I already did this" or "skip the quiz" cause it to drop the quiz immediately, no negotiation
- You're mid-task and a quiz would interrupt rather than reinforce

## Installation

1. Download [`quiz-me.skill`](./quiz-me.skill) from this repo (or clone the repo and grab the file)
2. In Claude.ai → Settings → Capabilities → Skills → Upload skill
3. Select the `.skill` file
4. Done — Claude will automatically use it when the trigger conditions match

For Claude Code, drop the `quiz-me/` folder into `~/.claude/skills/` (personal) or `.claude/skills/` (project-scoped).

## Repo structure

```
quiz-me/
├── README.md          ← you're here
├── quiz-me.skill      ← packaged skill, ready to upload
└── quiz-me/
    └── SKILL.md       ← source — the actual skill definition
```

If you want to modify the skill, edit `quiz-me/SKILL.md` and repackage. The `.skill` file is just a zip of that folder.

## Using it well

A few notes for team members getting started with this skill:

**Answer honestly, not strategically.** If you don't remember, say so. "I don't know" is a valid answer and a useful signal — it tells Claude that part of the project may need to be re-explained or wasn't communicated clearly enough. Trying to look good defeats the entire purpose.

**Push back when you think Claude is wrong.** The skill is designed to handle this — if your answer disagrees with the artifact, Claude will explicitly ask which one is right rather than assuming the document. Take that seriously. Drift in design docs is a real thing and this is the moment to catch it.

**Skip when it's bad timing.** Automatic triggers are suggestions, not demands. "Not now, let's keep going" is a normal and expected response. The skill won't nag.

**Heads up on cross-session memory.** Each Claude session is independent, so a session on Monday doesn't know about a quiz you did on Friday. The skill might propose a quiz on something you already aligned on with another session. If that happens, just say "already covered this" and Claude will move on. The skill is built to back off cleanly when you signal this.

## Why each person on the team should run their own quiz

You might wonder whether it's redundant for multiple team members to take quizzes on the same project. It isn't.

Even given the same set of design docs, two people will build slightly different mental models. The doc says one thing, but person A reads it assuming X and person B reads it assuming Y. Both think they're aligned with the doc; neither is aligned with the other. A quiz that surfaces this is more valuable than one that just confirms the doc was read.

So when a teammate takes a quiz and confirms they're aligned, that's about *their* alignment. Yours is a separate check.

## Contributing

If you find a failure mode — Claude triggers when it shouldn't, doesn't trigger when it should, gets pushy when you try to skip, defaults to "you're wrong" instead of "let's figure out which is right" — open an issue with the actual conversation as context. Those are the kinds of edits this skill benefits from.

PRs that change the trigger conditions, refine the back-off behavior, or improve how Claude frames the post-quiz discussion are welcome.

## License

MIT — feel free to use, modify, and share. See [LICENSE](./LICENSE) for details.
