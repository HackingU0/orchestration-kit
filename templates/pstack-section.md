
### pstack

pstack is the working style in this repo. It is the Claude Code plugin `pstack@cursor-plugins`, installed at local scope. Its skills are written for Cursor. Claude commanders type `/pstack:<name>` and read `{{PSTACK_ROOT}}/docs/harness/claude-code.md` once per session. Other commanders read the skills as files under `{{PSTACK_ROOT}}/skills/`. Where pstack and this file disagree, this file wins. Don't run `/pstack:setup-pstack`. It writes a global model rule, and the table below replaces that rule for this repo.

| pstack role | Here |
|---|---|
| `feature, refactoring`, `bug-fix` | Routine coding, or hard problems when the task is hard |
| `perf-issue`, `hillclimb` | Hard problems |
| `swarm workers`, verification | Routine coding |
| `judgment and prose` | The commander, in the main thread |
| `arena runners` | One seat each for the design adviser, routine coding, and hard problems |
| `arena cross-judge pool` | The second reviewer. If that model has a runner seat, skip the judge as arena allows |
| `interrogate reviewers` | The big or risky review line-up |

Rules:
- Escalation follows this file, not pstack.
- Start Claude seats with the native `Agent` and `run_in_background: false`. Put all seats in one message so they run in parallel. Pass no `effort`. When you start `pstack:poteto-agent`, pass `model`, because its file holds a Cursor model id. Also pass `{{PSTACK_ROOT}}/skills/poteto-mode/SKILL.md` as an absolute path.
- Start every pstack worker with the kickoff below. Without the override line, `worker.md` tells the worker to push and open a ready PR.
- Non-Claude seats go through Starting a delegate. Without that, the adapter runs every Gemini seat on the session model.
- For a new feature, the debate carries pstack's `architect` step. The adviser follows `{{PSTACK_ROOT}}/skills/architect/SKILL.md`. The debate replaces the Feature playbook's mandatory `arena`. Run an `arena` only if two valid shapes remain after the debate.
- Git: commit, push and open PRs only when the user asks. Use `gh` for PRs, or the T3 `pull_request_*` tools in T3 Code. Commit and PR titles are `type(scope): subject`. A PR body is under 40 lines with the headings `Why`, `What changed`, `Scope`, `Tradeoffs`, `Blast Radius`, and `Verification`. Drop empty headings. Merge only on the user's go.
- `cursor-team-kit` is not installed. Use the repo's own checks: {{CHECK_COMMANDS}}. Bugbot, Cursor Automations and Cursor cloud agents get the adapter's one-line skip.

Kickoff for every pstack worker, native or delegated:

```text
Read {{PSTACK_ROOT}}/skills/poteto-mode/worker.md before anything else.
The "Model routing" section of AGENTS.md overrides worker.md and the pstack playbooks: no commits, pushes or PRs unless this brief says so.
poteto-mode SKILL.md: {{PSTACK_ROOT}}/skills/poteto-mode/SKILL.md
Goal: <goal>
Task: <task, what you may edit, what proves it is done>
```
