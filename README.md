# AI Product Team

A senior product team as [Claude Code](https://claude.com/claude-code) agents. Give it an idea; a founder, product manager, business analyst, designers, tech lead, developers, QA, DevOps, security, data and growth roles take it to working, tested code and a launch plan. Each run is sized to the work, you see where it is after every step, you approve the decisions that matter, and the team improves through lessons you approve.

It's a Claude Code plugin, so it works in any project: every role reads the project's own stack and conventions before it does anything.

## Install

**From GitHub** (in any project, inside Claude Code):
```
/plugin marketplace add mayurkorat99/ai-product-team
/plugin install product-team@ai-product-team
```

**From a local checkout:**
```
/plugin marketplace add /path/to/ai-product-team
/plugin install product-team@ai-product-team
```

**Cloud sessions and teams.** A cloud container starts fresh each time, so declare the plugin in the project's committed `.claude/settings.json` and it is installed automatically:
```json
{
  "extraKnownMarketplaces": {
    "ai-product-team": {
      "source": { "source": "github", "repo": "mayurkorat99/ai-product-team" }
    }
  },
  "enabledPlugins": { "product-team@ai-product-team": true }
}
```
If this repository is private, every session that installs it needs GitHub access to it.

Every command also has a namespaced form: `/product-team:team`, `/product-team:role` and so on. Use it if `/team` isn't found or another plugin already uses that name. Agents are `product-team:<handle>`.

## Quick start

```
/team a habit tracker where friends can see each other's streaks
```

First the team proposes a **run size** and waits for you to confirm it (see [Run sizes](#run-sizes)). It then writes everything into a `.team/` folder in your project and works on its own branch, `team/<slug>/main`. It stops for you at these gates:

| Gate | You approve |
|---|---|
| 1 | The **PRD**: target user, MVP stories, out of scope, success metrics |
| 2 | The **plan**: architecture decisions, tasks, risks, security requirements |
| 3 | **Go / no-go**: what was built, test results, open bugs and security findings |

Small runs combine gates 1 and 2 into one. Nothing is ever deployed, published or merged into your default branch. At the go/no-go gate you get the branch to merge.

After every step you see a short status block:
```text
[Medium] Stage 2/7 - Specify and design
Done:    ux - 6 screens, all states covered
Running: ba (spec.md)
Next:    ui + security design review
Tasks:   0/0 done, 0 in review, 0 blocked | Bugs S1/S2: 0 | Blocking questions: 1
```

## Run sizes

| Size | For | Roles | Gates |
|---|---|---|---|
| **Small** | A feature on an existing product, no new screens or flows | `pm`, `tech-lead`, `dev`/`mobile`, `qa`, `founder` for go/no-go | 2 |
| **Medium** | A larger feature, or one with new screens or flows | Small + `ba`, `security`, and `ux` + `ui` when there is a UI | 3 |
| **Full** | A new product, or a change that needs positioning, metrics and a launch | All 13 roles | 3 |

- `security` joins any size when the work touches authentication, payments or personal data, either at the start or when `tech-lead` marks a task `sensitive`.
- At confirmation you can add or remove individual roles. At gates 1 and 2 you can raise the size; the added roles catch up and the plan is revised. The size is never lowered.
- Each document has a length target for its prose: Small 400 words, Medium 800, Full 1500.

## Model per role

Each role runs on the Claude model its work needs, so judgement-heavy work gets the strongest model and the rest saves tokens:

| Model | Roles |
|---|---|
| Opus | `tech-lead`, `security` |
| Sonnet | `founder`, `pm`, `ba`, `ux`, `ui`, `data`, `growth`, `qa` |
| Per task | `dev`, `mobile`, `devops`: Opus when `tech-lead` marks the task `complex: yes` in the plan (concurrency, migrations, large refactors, performance-critical work), Sonnet otherwise |

Override any role per project in `.team/config.yaml` (`opus` or `sonnet`):
```yaml
models:
  dev: opus      # all code on Opus
  ba: opus
```
The start line names each role's model, and every gate shows tokens used per role and per model; go/no-go adds the run total and the share spent on Opus. Totals cover the roles, not your own session, which stays on the model you chose.

## Commands

| Command | What it does |
|---|---|
| `/team <idea>` | Run the team, idea to go/no-go, sized to the work. `/team status` prints the status block; `/team resume` continues an interrupted run. |
| `/role <role> <task>` | Run one role on one task: free text, a board id (`T3`) or `next`. This is how you run roles in parallel sessions. |
| `/retro` | Review recent work and propose lessons for each role. |
| `/lessons` | Approve, edit or reject the proposed lessons. |

## The team

| Role | Handle | Owns | Works with |
|---|---|---|---|
| Founder / Product Owner | `founder` | `brief.md`, `decisions.md`, `go-no-go.md` | Vision, scope, kill criteria, final call |
| Product Manager | `pm` | `prd.md` | Jobs to be done, story map, MoSCoW + RICE, Given/When/Then |
| Business Analyst | `ba` | `spec.md` | Business rules, data dictionary, state machines, edge cases, test scenarios |
| UX Designer | `ux` | `ux.md` | Flows, wireframes, every screen state, Nielsen heuristics, WCAG 2.2 AA |
| UI Designer | `ui` | `ui.md` | Design tokens, contrast ratios, components and states, dark mode |
| Tech Lead / Architect | `tech-lead` | `architecture.md`, `plan.md`, `review.md`, merges | C4, ADRs, task breakdown, code review |
| Full-stack Developer | `dev` | Code + tests, `dev-notes.md` | Test-first, project conventions, small commits |
| Mobile Developer | `mobile` | Mobile code + tests | Platform conventions, offline, accessibility, store readiness |
| QA Engineer | `qa` | `test-plan.md`, acceptance tests, `bugs.md` | Risk-based testing, test pyramid, severities S1-S4 |
| DevOps | `devops` | CI config, `release.md` | Pinned, least-privilege CI; deploy, rollback and monitoring plan |
| Security Engineer | `security` | `security.md` | STRIDE threat model, OWASP Top 10 / ASVS review, secrets, dependencies |
| Data Analyst | `data` | `metrics.md` | North Star, KPI formulas, event plan, experiment design |
| Sales & Marketing | `growth` | `gtm.md` | ICP, positioning, pricing hypothesis, launch plan, copy |

`ux` and `ui` skip themselves for products without a user interface, and `mobile` only runs when the product includes a mobile app.

### How they talk

A Full run:
```
founder -> pm -> [GATE 1] -> ba + ux + data -> ui + security (design) -> tech-lead -> [GATE 2]
  -> dev/mobile <-> qa (up to 3 rounds per task) -> tech-lead review + merge
  -> security (code) + devops + growth -> founder -> [GATE 3] -> retro
```
A Small run:
```
pm -> [security (design), if sensitive] -> tech-lead -> [GATE 1+2]
  -> dev/mobile <-> qa -> tech-lead review + merge -> [security (code)] -> founder -> [GATE 3] -> retro
```

Every handoff is a file, so you can read, edit or challenge any of it. Roles ask each other questions in `.team/questions.md` (`[qa -> ba] Is a zero-amount order valid?`) and never silently rewrite each other's work.

## The `.team/` folder

| File | Purpose |
|---|---|
| `idea.md`, `brief.md`, `prd.md`, `spec.md`, `ux.md`, `ui.md`, `metrics.md`, `architecture.md`, `plan.md`, `test-plan.md`, `security.md`, `release.md`, `gtm.md`, `go-no-go.md` | Each role's deliverable |
| `board.md` | Tasks and their status: `todo -> claimed:<role> -> review -> done` (also `changes-requested`, `blocked`) |
| `questions.md`, `bugs.md`, `review.md`, `decisions.md`, `log.md` | Conversation and history |
| `state.md` | Where the run is; used by `/team resume` and other sessions |
| `tokens.md` | Token use per role start (append-only); summed at every gate |
| `config.yaml` | Settings (below) |
| `names.yaml` | Optional persona names |
| `memory/<role>.md`, `memory/team.md` | Approved lessons for this project |
| `lessons-pending.md`, `retro.md`, `upstream-lessons.md` | The learning loop |

Commit `.team/`: it is the team's shared memory, and the channel between sessions.

### `config.yaml`
```yaml
max_parallel: 3        # agents running at once
board: file            # file | github-issues
qa_max_rounds: 3       # QA/dev rounds per task before escalating to you
base_branch: auto      # branch the run starts from; auto = current branch
keep_task_branches: false  # true keeps task and QA branches after they are merged
# models:              # override a role's model: opus | sonnet
#   dev: opus
```

## Working in parallel

**In one session**, `/team` starts independent roles at the same time (up to `max_parallel`), and code-writing roles each get their own git worktree, so their edits never collide.

**Across sessions**, open one session per role and run, for example:
```
Session A:  /role dev next
Session B:  /role qa T2
Session C:  /role dev next
```
Sessions coordinate through git:
- **Claims.** Taking a task commits the claim to the board and pushes it. If two sessions claim the same task, the second push is rejected, so that session pulls and picks another task.
- **Branches.** Each task has its own branch, `team/<slug>/T<n>-<name>`, and QA's tests live on `team/<slug>/T<n>-qa`. `tech-lead` reviews both and merges them into the run branch.
- **Cleanup.** Once a task is merged, `tech-lead` deletes its task and QA branches and the coordinator removes its worktree, so a finished run leaves only `team/<slug>/main`. Merges use `--no-ff`, so each task's history stays in the run branch. Set `keep_task_branches: true` to keep them.
- **Test-first QA.** QA writes acceptance tests from the spec before the code exists, and runs them when a task reaches `review`.
- **GitHub Issues.** Set `board: github-issues` to use issues as the board (labels `role:` and `status:`, comments for handoffs). This needs GitHub tools in the session; otherwise the file board is used.

Each parallel agent spends its own tokens, which is why `max_parallel` defaults to 3.

## Persona names (optional)

By default roles are called by their handle. To give them names in a project, copy [`plugins/product-team/names.example.yaml`](plugins/product-team/names.example.yaml) to `.team/names.yaml` and edit it:
```yaml
pm: Priya
qa: Quinn
```
Roles then sign as `Priya (pm)`, and `/role priya ...` works. Lessons and files stay keyed by handle, so renaming loses nothing. Unknown handles and duplicate names are reported and ignored.

## How the team learns

1. Every role ends its report with a proposed lesson, and `/retro` looks for repeated problems (the same review comment twice, a bug class that kept escaping, rework after a gate) and turns them into concrete rules.
2. `/lessons` shows each one with its evidence. **You approve, edit or reject every lesson**; nothing changes on its own.
3. Approved **project** lessons go to `.team/memory/<role>.md`, and that role follows them from its next run.
4. Approved **general** lessons also go to `.team/upstream-lessons.md`. Run `/lessons --plugin-repo <path>` from a checkout of this repository to add them to the agents' `## Learned lessons` sections on a branch. Merging that branch gives the lesson to every project on the next plugin update.

Rejected lessons are remembered, so they aren't proposed again.

## Safety

No role ever deploys, publishes, sends email or messages, posts anywhere, spends money, reads or commits secrets, pushes to your default branch or force-pushes. Steps like these are written down for you to do: `release.md` for deploys, `gtm.md` drafts for posts.

## Repository layout

```
.claude-plugin/marketplace.json        # this repo as a plugin marketplace
plugins/product-team/
  .claude-plugin/plugin.json
  agents/<handle>.md                   # 13 roles, each with the shared team protocol
  commands/{team,role,retro,lessons}.md
  names.example.yaml
REQUIREMENTS.md                        # what v1 was built to do
```

Every agent file ends with the same **Team protocol** section. If you change it, change it in all 13 files.
