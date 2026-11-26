# orchestration-kit

orchestration-kit sets up multi-model delegation in a git repository. The commander is the model in the main thread. It keeps planning, judgment and the final summary. Claude models do routine coding through the native `Agent` tool. Gemini and GPT models take hard problems and reviews through Orca orchestration, or through `delegate_task` in T3 Code. Optionally, pstack's `poteto-mode` drives the work.

## Install

Open an agent session in the target repository and paste this prompt. Any agent with a shell and `git` can run it, such as Claude Code, Codex, or Antigravity.

```text
Install orchestration-kit into this repository. Run `git clone --depth 1 https://github.com/HackingU0/orchestration-kit "${TMPDIR:-/tmp}/orchestration-kit"`, then read INSTALL.md in that folder and follow it to the end.
```

If you already have a checkout of the kit, name its `INSTALL.md` path instead of the clone step.

The installer finds the models on the machine, probes each Gemini and GPT model with a worker that edits nothing, writes the role table into `AGENTS.md`, and reports what it chose. A run takes a few minutes, most of it the probes. Running it again replaces the block in place.

## Uninstall

```text
Remove orchestration-kit from this repository. Run `git clone --depth 1 https://github.com/HackingU0/orchestration-kit "${TMPDIR:-/tmp}/orchestration-kit"`, then read UNINSTALL.md in that folder and follow it to the end.
```

## What it writes

| File | Content |
|---|---|
| `AGENTS.md` | The block between `<!-- orchestration-kit:start -->` and `<!-- orchestration-kit:end -->`: the role table, routing rules, how to start a delegate in each harness, the design debate, and the pstack rules when pstack is set up |
| `CLAUDE.md` | A three-line pointer to that block, because Claude Code does not read `AGENTS.md` on its own |
| `.git/orchestration-kit-backup/` | Copies of both files from before the install |
| `.claude/settings.local.json` | The pstack plugin, at local scope, only when it was missing |

Nothing outside the markers changes.

## Requirements

- A git repository.
- For Gemini and GPT seats, either Orca with the `antigravity` and `codex` agents, or T3 Code. Without one, every role falls back to a Claude model.
- For pstack, Claude Code with the `claude` command.
- A commander that is not Claude Code starts Claude seats through Orca with `--agent claude`. Without Orca, it has no Claude seats.

## Files

| Path | Read by | Purpose |
|---|---|---|
| `INSTALL.md` | the installing agent | The install steps |
| `UNINSTALL.md` | the uninstalling agent | The removal steps |
| `roles.md` | the installing agent | Roles, pick order, model discovery, and start strings |
| `templates/routing-block.md` | the installing agent | The `AGENTS.md` block, with `{{placeholders}}` |
| `templates/pstack-section.md` | the installing agent | The pstack part of that block, and the worker kickoff |
| `templates/claude-pointer.md` | the installing agent | The `CLAUDE.md` pointer |
| `probe/probe.md` | each probe worker | The read-only probe task |

The kit does not copy pstack or Orca's orchestration guide. The installer gets the current versions on the target machine.
