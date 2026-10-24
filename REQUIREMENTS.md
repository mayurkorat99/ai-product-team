# Requirements - AI Product Team v1

Agreed with the owner on 2026-10-18. Changes to behaviour should update this file.

**Goal:** a reusable team of senior AI agents that takes an idea to a working, tested, documented product, ready to launch, in any project.

| # | Requirement |
|---|---|
| R1 | **Roles (13):** Founder/PO `founder`, Product Manager `pm`, Business Analyst `ba`, UX Designer `ux`, UI Designer `ui`, Tech Lead/Architect `tech-lead`, Full-stack Developer `dev`, Mobile Developer `mobile`, QA Engineer `qa`, DevOps `devops`, Security Engineer `security`, Data Analyst `data`, Sales & Marketing `growth`. |
| R2 | **Senior expertise:** each role works at the level of 15+ years' experience, uses its discipline's standard methods, states its assumptions, pushes back on weak decisions, and checks its work against a quality bar before handing off. |
| R3 | **One owner per deliverable:** each role writes only its own files (see the README's table). |
| R4 | **Communication** through files in `.team/`. Questions go in `questions.md`, addressed from one role to another. A coordinator decides who works next. Full order: founder -> pm -> ba / ux / data -> ui + security (design) -> tech-lead -> dev/mobile <-> qa -> tech-lead review -> security (code) / devops / growth -> founder. Smaller runs skip the roles outside their size (R13). UX and UI are skipped without a UI; mobile runs only with a mobile app. |
| R5 | **Approval gates** for the user: after the PRD, after the technical plan, and the final go/no-go. Small runs combine the first two, so they have two gates; Medium and Full have three. |
| R6 | **"Done"** means working code with passing tests in the project's own runner, plus every role's documents. Nothing is deployed. |
| R7 | **Any project:** packaged as a Claude Code plugin in its own repository. Agents read each project's stack and conventions before working. Works in cloud sessions. |
| R8 | **Learning with approval:** every role proposes lessons. The user approves each one. Project lessons are saved in `.team/memory/`; general lessons go into the plugin's agent files. |
| R9 | **Commands:** `/team`, `/role`, `/retro`, `/lessons`. |
| R10 | **Safety:** no deploying, publishing, outbound messages or posts, spending, secrets, pushes to the default branch, or force-pushes. |
| R11 | **Optional persona names** per project in `.team/names.yaml`. Handles are the default identity, and memory is always keyed by handle. Invalid entries are reported and ignored. |
| R12 | **Parallel work:** within one session (one worktree per agent) and across sessions (task board with claims by commit + push, one branch per task, git as the shared channel, tech-lead merges). Optional GitHub Issues board. At most 3 agents at once by default (`max_parallel`). |
| R13 | **Run sizes:** `/team` proposes Small, Medium or Full and the user confirms or changes it, including individual roles. `security` joins any size when work touches authentication, payments or personal data. The size can be raised at the first two gates, never lowered. Each document has a length target per size. Added 2026-10-24. |
| R14 | **Progress in the chat:** a one-line notice when roles start, and a status block (size, stage n/N, done, running, next, task and bug counts) after every step. Every role runs in the background, so each completion reports progress and token use as it happens. Added 2026-10-24. |
| R15 | **Model per role:** `tech-lead` and `security` default to Opus, the document roles to Sonnet, and `dev`, `mobile` and `devops` use Opus for tasks `tech-lead` marks `complex: yes` and Sonnet otherwise. Projects override any role in `config.yaml` (`opus` or `sonnet`). Start lines name each role's model, and gates report token use per role and per model. Added 2026-10-24. |
