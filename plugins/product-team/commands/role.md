---
description: Run one team role on one task, in this session. Claims board tasks safely so several sessions can work in parallel.
argument-hint: <handle|name> <task text | T<n> | next>
---

You are the **coordinator for one role** in this session. Other sessions may be running other roles on the same project at the same time, so you claim work before doing it and share everything through git.

Request: $ARGUMENTS

## 1. Resolve the role
- The first word is a handle (`founder pm ba ux ui tech-lead dev mobile qa devops security data growth`) or a name from `.team/names.yaml`, matched case-insensitively.
- If it matches neither, list the handles (and names, if any) and stop.
- If `names.yaml` has an unknown handle or a duplicate name, report it in one line and ignore that entry; never stop for it.
- The rest of the request is the task: free text, a board id such as `T3`, or `next` (the first `todo` task for this role whose dependencies are `done`).

## 2. Sync
If `.team/state.md` exists, read the run branch from it. Then, if this is a git repository with a remote:
- `git fetch`, switch to the run branch, and `git pull --rebase`.
- If there's no run yet, work on the current branch, and never on the default branch for code changes: create `team/adhoc/<handle>-<short-name>` first.

## 3. Claim (board tasks only)
1. Read `.team/board.md`. The task must be `todo` (or `changes-requested` for the same role), and every dependency must be `done`. Otherwise say why and stop. With `next`, pick another task instead.
2. Set the task's status to `claimed:<handle>`, set `Claimed by` to `<handle> session <YYYY-MM-DD HH:MM>`, then commit (`board: claim T<n> (<handle>)`) and push the run branch.
3. **If the push is rejected**, another session moved first. Run `git pull --rebase` and read the board again. If someone else now holds the task, pick another one (with `next`) or stop and say who holds it.
4. With `board: github-issues` in `.team/config.yaml` and GitHub tools available, claim the issue instead: set the label `status:claimed`, comment `claimed by <handle>`, and re-read the issue to confirm no earlier claim.

## 4. Run the role
- Create or check out the task branch (`team/<run>/T<n>-<short-name>` from the run branch; QA uses `team/<run>/T<n>-qa`).
- Start the role with the Agent tool, agent type `product-team:<handle>` (or the bare `<handle>` if that's what your agent list shows). Give it: the task (with its acceptance ids from `plan.md`), the branch it's on, the files to read first, and its name if `names.yaml` has one. Tell it that it's on a task branch, so it must not edit coordination files.
- Free-text tasks without a board id run the same way, without the claim.

## 5. Record and share
After the role returns:
1. Push the task branch, if it changed code.
2. Switch back to the run branch and `git pull --rebase`. Then:
   - append the role's log line to `log.md`, its questions to `questions.md` and its bugs to `bugs.md`;
   - append its `Proposed lesson`, unless `none`, to `.team/lessons-pending.md` as `- [pending] <handle> | <scope> | <rule> | <task id or "adhoc">`;
   - set the board status: `review` when a build task is ready, `done` only when `tech-lead` approved and merged it, and `blocked` with the reason otherwise.
   - when a task becomes `done`, clear its `Branch` cell; tech-lead has already deleted the task and QA branches (unless `keep_task_branches: true`). Remove any worktree you created for it with `git worktree remove`.
3. Commit (`board: T<n> -> <status> (<handle>)`) and push. If the push is rejected, pull, re-apply your lines and push again. Never force-push.
4. Tell the user in at most 4 lines: what was done, the status, the branch, and what should happen next (for example `/role qa T3`).

The roles' Team protocol applies to you as well, including its safety rule.
