# Install orchestration-kit

You are an agent installing orchestration-kit into a git repository. These steps are the whole procedure. Follow them in order. Do not ask the user questions. Make each choice from this file and report it at the end.

`KIT` is the folder that holds this file. If the prompt gave you a clone command, run it first. If the target folder already exists, run `git -C <folder> pull --ff-only` instead. `REPO` is the root of the git repository in your working directory (`git rev-parse --show-toplevel`). Read `KIT/roles.md` before step 2.

The install writes only these things:
- a marked block in `REPO/AGENTS.md`
- a marked pointer in `REPO/CLAUDE.md`, when the commander is Claude Code
- a backup in `REPO/.git/orchestration-kit-backup/`
- the pstack plugin at local scope, when it is missing and the commander is Claude Code
- one probe worker per model started through an orchestrator, which edits no files

## 1. Check the target

1. Run `git rev-parse --show-toplevel`. If it fails, stop and tell the user that the install needs a git repository.
2. Read `KIT/VERSION`. Call it `KIT_VERSION`.
3. If `REPO/AGENTS.md` already has a line that starts with `<!-- orchestration-kit:start`, this is a reinstall. Keep going. Step 6 replaces the block.

## 2. Find the harness and the models

Follow "Finding models" in `KIT/roles.md`. Record the harness, which is Orca, T3 Code, or neither. Record every model you find, grouped by family.

## 3. Pick a model for each role

Follow "Pick order" in `KIT/roles.md`. A pick that starts through an orchestrator stays a candidate until its probe passes in step 4. Apply the two family checks in "Families" in `KIT/roles.md`, and no others.

## 4. Probe each candidate that starts through an orchestrator

Skip this step if the harness is neither Orca nor T3 Code. Each non-Claude role then takes its next Claude entry.

In Orca:
1. Run `orca skills get orchestration --full` and read it.
2. Start one worker per candidate, all from one shell command, as the guide's `references/parallel-start.md` shows. Use `--environment local --spec-file KIT/probe/probe.md --worktree current --task-title "orchestration-kit probe <model>"` and the start string from `KIT/roles.md`.
   Write every flag out in each command. Do not keep flags in a shell variable, because zsh passes a variable as one argument.
3. Read every receipt. A start that exits non-zero fails its probe. Do not rerun it. The one exception is an argument error that `orca orchestration worker-list --environment local` confirms started nothing. Fix the command and start it once more.
4. End your turn. Orca wakes you once for each `worker_done`.
5. A probe passes when the worker's reply names the model and the effort you asked for. A probe fails when the reply names another model, when the worker reports failure, or when Orca reports an escalation. A failed probe moves the role to its next entry. Probe that entry the same way.

In T3 Code, send `KIT/probe/probe.md` as the task of one `delegate_task` per candidate and apply the same pass rule. This path was not tested by the kit, so say so in the report.

## 5. Set up pstack

Skip this step if the commander is not Claude Code or the `claude` command is missing. Write `pstack=skipped` in the block marker and leave `{{PSTACK_SECTION}}` empty.

1. Run `claude plugin list --json`. If an entry has `id` `pstack@cursor-plugins` and is enabled for `REPO`, take its `installPath` as `PSTACK_ROOT`. On a reinstall, keep the `pstack=` value from the old start marker, so that uninstall still knows who installed pstack. Otherwise write `pstack=present`.
2. Otherwise run `claude plugin marketplace list`. If no marketplace is named `cursor-plugins`, run `claude plugin marketplace add cursor/plugins --sparse .claude-plugin harness/pstack`.
3. Run `claude plugin install pstack@cursor-plugins --scope local` from `REPO`. Run `claude plugin list --json` again and take the `installPath`. Write `pstack=installed`.
4. If a command fails, write `pstack=failed`, leave the section empty, and put the error in the report. Continue with step 6.
5. If pstack is set up, fill `KIT/templates/pstack-section.md`. `{{PSTACK_ROOT}}` is the install path. `{{CHECK_COMMANDS}}` lists the repo's check and test commands, such as `npm run check` and `npm test` from `package.json` scripts, or `make test`. If the repo has none, write "the repo has no check script, so run its tests by hand".

## 6. Write the files

1. Make `REPO/.git/orchestration-kit-backup/<UTC timestamp>/`. Copy `AGENTS.md` and `CLAUDE.md` into it if they exist.
2. Fill `KIT/templates/routing-block.md`:
   - Each `_NAME` is the family and model, such as "Gemini `gemini-4-neon-high`" or "Claude `sonnet`".
   - Each `_START` is the start string for the harness, from `KIT/roles.md`. For a Claude seat it is "`Agent` with `model: sonnet`".
   - `{{SECOND_REVIEW_NAME}}` and `{{SECOND_REVIEW_START}}` are "none" when step 3 found no second reviewer.
   - `{{FALLBACK_NAME}}` is the fallback line from `KIT/roles.md`.
   - `{{CODEMAP_RULE}}` is ` Read <path> before you touch code, and pass it to every delegate.` when the repo has a codebase map such as `docs/MAP.md` or `ARCHITECTURE.md`. Otherwise it is empty.
   - `{{PSTACK_STATE}}` and `{{PSTACK_SECTION}}` come from step 5.
3. Write the block into `REPO/AGENTS.md`. If the file has a block from `<!-- orchestration-kit:start` to `<!-- orchestration-kit:end -->`, replace exactly those lines. If it has none, append the block after one blank line. If the file is missing, create it with `# Agents`, a blank line, and the block.
4. If the commander is Claude Code, write `KIT/templates/claude-pointer.md` into `REPO/CLAUDE.md` by the same rule. A new `CLAUDE.md` holds only the pointer.
5. Leave every line outside the markers unchanged.

## 7. Check the result

1. Each file you wrote has exactly one start marker and one end marker.
2. `grep -n '{{' REPO/AGENTS.md REPO/CLAUDE.md` finds nothing inside the markers.
3. `git status --short --ignored` shows changes only in `AGENTS.md`, `CLAUDE.md`, and `.claude/` when step 5 installed pstack.

If a check fails, restore the backup and report the failure.

## 8. Report

Tell the user:
- the role table you wrote, with the probe result for each probed model
- each role that fell back, and why
- the pstack state
- the files you changed and the backup path
- each step you skipped, and the reason
- that `KIT/UNINSTALL.md` removes the install
