---
description: Run a retrospective on the team's recent work and propose lessons for each role. Proposes only; nothing changes until you approve with /lessons.
argument-hint: [run slug | since YYYY-MM-DD]
---

Run a blameless retrospective on the product team's work and turn it into lessons. You only **propose** lessons; nothing is applied until the user approves them with `/lessons`.

Scope: $ARGUMENTS (empty means everything since the last `## Retro` heading in `.team/retro.md`, or since the start)

## 1. Gather evidence
Read, for the scope:
- `.team/log.md`, `.team/board.md`, `.team/state.md`;
- `.team/questions.md`: questions that blocked work or were answered late;
- `.team/bugs.md`: bugs by severity and task, and how many QA rounds each task needed;
- `.team/review.md`: requested changes and their themes;
- `.team/security.md`: findings, and which ones the design review could have caught;
- gate outcomes in `state.md`: which gates needed changes, and why;
- `.team/lessons-pending.md` (lessons the roles proposed themselves) and `.team/memory/*.md` (lessons already approved).

## 2. Find the patterns
Look for **repeated** causes, not one-offs:
- a review or QA comment on two or more tasks;
- rework after a gate;
- a question that should have been answered upstream;
- a bug class that kept escaping;
- a step that was slow or skipped.

For each pattern, decide which role could prevent it next time. That is often an upstream role, not the one that hit the problem.

## 3. Write the lessons
Each lesson is **one imperative rule** that a role can follow and someone could check. For example: "Before splitting tasks, list every file each task touches and don't run two tasks that touch the same file in parallel." Not: "Be more careful about conflicts."

Scope each lesson as:
- `project`: true only for this codebase (its commands, conventions, quirks);
- `general`: useful on any project.

Drop any lesson that duplicates or contradicts an approved one in `.team/memory/`; mention a contradiction instead of proposing it. Rewrite the roles' own proposals so they meet the same bar, merge duplicates, and drop vague ones.

Rewrite `.team/lessons-pending.md` so every pending entry has this form, keeping `approved` and `rejected` entries as they are:
```
- [pending] L<n> | <handle or team> | <project|general> | <rule> | evidence: <ids, e.g. BUG4, BUG7, T3 review>
```

## 4. Record the retro
Append to `.team/retro.md`: a `## Retro <date> - <scope>` heading, then what went well (with evidence), what didn't, and the ids of the lessons proposed.

Then tell the user how many lessons are pending, per role, and that `/lessons` reviews them. Commit the `.team/` changes on the current branch (never the default branch) if this is a git repository.
