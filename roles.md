# Roles and default picks

This file is reference for the installer. It lists each role, the order in which the installer tries models for it, and how it finds the models on a machine.

## Families

| Family | Agent | Examples |
|---|---|---|
| Claude | native `Agent` tool | `opus`, `sonnet`, `haiku`, `fable` |
| Gemini | Orca `antigravity` | `gemini-4-neon-high`, `gemini-4-hydrogen-high` |
| GPT | Orca `codex` | `gpt-6.2-sol` |

The installer checks two pairs, and only these two. Routine review must differ in family from routine coding, because it reviews that code. The second reviewer must differ in family from routine review. Hard-problem work is reviewed by the commander and the second reviewer at run time, so hard problems and routine review may share a family. Take each pick order as written once these two checks pass.

## Pick order

The installer takes the first entry in each list that the machine has and that passes its probe. The probe is skipped for Claude seats on the native `Agent` tool, because that tool takes only the models in its schema. A Claude seat started through Orca is probed like the others.

| Role | Try in this order |
|---|---|
| Routine coding | Claude `sonnet` |
| Hard problems | Gemini Neon at `high`, then GPT Sol at `high`, then Claude `fable` |
| Routine review | Gemini Hydrogen at `high`, then GPT Sol at `high`, then the commander |
| Second reviewer | GPT Sol at `high`, then Gemini Hydrogen at `high` if routine review is not already Gemini, then none |
| Design adviser | Claude `fable`, then Claude `opus` |
| Fallback | Claude `fable` for hard problems and the second reviewer. Claude `opus` for routine review |

The second reviewer must differ in family from the routine reviewer. If no such model exists, the row says "none" and big changes get one review plus the commander's.

## Finding models

Run each command once. A command that fails means that the family is absent. It does not stop the install.

| What | Command | Read |
|---|---|---|
| Harness | `printenv ORCA_CLI_COMMAND` | Set means Orca. Otherwise check for a `delegate_task` tool, which means T3 Code |
| Orca agents | `orca agent installed --json` | `result.agents[].id` |
| Gemini models | `agy models` | First column. The level is the last part of the id, such as `-high` |
| GPT models | `~/.codex/config.toml` and `~/.codex/models_cache.json` | The `model` line gives the line in use, such as `-sol`. Pick the highest version with that suffix in the cache |
| Claude models | The `Agent` tool schema, when the commander is Claude Code | The values of `model`. Another commander in Orca uses `opus`, `sonnet`, `haiku`, and `fable` if `claude` is in the Orca agent list, and has no Claude models otherwise |

## Start strings

The installer writes these into the "How to start it" column.

| Family | Orca | T3 Code |
|---|---|---|
| Claude, commander is Claude Code | `Agent` with `model: <name>` | `Agent` with `model: <name>` |
| Claude, other commander | `--agent claude --model <name>` | not available |
| Gemini | `--agent antigravity --model <id with level>` | `delegate_task`, Antigravity, `<id without level>`, `thought_level: <level>` |
| GPT | `--agent codex --model <id> --effort <level>` | `delegate_task`, Codex, `<id>`, `reasoningEffort: <level>` |

Orca reports `--model gemini-4-neon-high` as `model=gemini-4-neon effort=high`. Both forms reach the same model.

## What was tested

Tested on 2026-11-26 in Orca 1.4.277: Gemini Neon `high`, Gemini Hydrogen `high`, and GPT `gpt-6.2-sol` at `high`. Each probe worker read its model from its own session and matched the request. The T3 Code start strings come from a working T3 setup but were not probed by this kit.
