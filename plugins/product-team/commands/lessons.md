---
description: Review the team's pending lessons and approve, edit or reject each one. Approved lessons change how that role works from its next run.
argument-hint: [--plugin-repo <path to an ai-product-team checkout>]
---

Help the user review the product team's pending lessons. **Nothing is applied without the user's explicit approval of that specific lesson.**

Arguments: $ARGUMENTS

## 1. Show what's pending
Read `.team/lessons-pending.md` and list every `[pending]` entry: id, role, scope, rule and evidence.
- If there are none, say so and suggest `/retro`. Stop.
- If an entry's evidence can't be found in the `.team/` files, say so next to it.

## 2. Ask
For each lesson, ask the user to **approve**, **edit** (they give the new wording) or **reject** (optionally with a reason). Ask in batches of up to 4 lessons; for more, do several rounds. Wait for the answers. Don't decide on the user's behalf, and treat no answer as "leave pending".

## 3. Apply approved lessons
- **Project scope.** Append `- L<n> (<date>): <rule>` to `.team/memory/<handle>.md` (or `.team/memory/team.md` for `team`). Create the file with a `# Lessons for <handle>` heading if needed.
- **General scope.** Do the project step above (so it takes effect here now), then make the lesson available to every project:
  1. Append it to `.team/upstream-lessons.md` as `- <handle> | <rule> | from <project name>, <date>`.
  2. If `--plugin-repo <path>` was given, or the current repository is the `ai-product-team` plugin repository itself:
     - create the branch `lessons/<date>` there;
     - append the rule to the `## Learned lessons` section of `plugins/product-team/agents/<handle>.md` (for `team`, to every agent file);
     - bump the patch version in `plugins/product-team/.claude-plugin/plugin.json`;
     - commit. Don't push unless the user asks; tell them a pull request on that branch publishes it to every project.
  3. Otherwise, tell the user that general lessons are collected in `.team/upstream-lessons.md`, and that running `/lessons --plugin-repo <path>` from a checkout of the plugin repository applies them there.

## 4. Mark the queue
In `.team/lessons-pending.md`, change each entry's `[pending]` to `[approved <date>]`, `[edited <date>]` (with the final wording) or `[rejected <date>: <reason>]`. Rejected entries stay in the file so `/retro` doesn't propose them again.

## 5. Keep memory useful
If any `.team/memory/<handle>.md` now holds more than 30 lessons, propose merging overlapping ones and retiring stale ones. Show the before and after, and apply only on approval.

Finish with a short summary of how many lessons were approved, edited and rejected, and where each approved one was written. Commit the `.team/` changes on the current branch (never the default branch) if this is a git repository.
