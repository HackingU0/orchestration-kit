<!-- orchestration-kit:start version={{KIT_VERSION}} pstack={{PSTACK_STATE}} -->
## Model routing

The model in the main thread is the **commander**. It plans the work, splits it into tasks, decides what to keep, and writes the final summary for the user. It delegates the rest.

The user chose the models in this table. Pass them exactly as written, even where an orchestrator guide says to add a model only when the user names one.

| Role | Model | How to start it |
|---|---|---|
| Routine coding | {{CODER_NAME}} | {{CODER_START}} |
| Hard problems | {{HARD_NAME}} | {{HARD_START}} |
| Routine review | {{REVIEW_NAME}} | {{REVIEW_START}} |
| Big or risky review | {{REVIEW_NAME}} and {{SECOND_REVIEW_NAME}} | {{SECOND_REVIEW_START}} |
| Design adviser | {{ADVISER_NAME}} | {{ADVISER_START}} |

Hard problems are bugs nobody can pin down, cross-module refactors, and algorithm-heavy or performance work.

Rules:
- Do small edits in the main thread. Delegate only real multi-step work.
- If the same task fails twice on the routine coding model, give it to the hard-problems model.
- The reviewer is from a different model family than the writer. If the only listed reviewer shares the writer's family, the commander reviews instead.
- For a big or risky change, get both reviews.
- Fix small review findings in the main thread. Don't send them back to the writer.
- If the commander is one of the models above, it does that role itself instead of delegating to itself.
- If a model is out of quota or its provider is down, give its role to the fallback model: {{FALLBACK_NAME}}. The reviewer then only needs to be a different model from the writer. Say so in the summary.
- Every brief says what the worker may edit, what proves the task is done, and the answer to any mode question a loaded skill would ask. Workers cannot ask questions.{{CODEMAP_RULE}}

### Starting a delegate

A Claude Code commander starts Claude seats with the native `Agent` tool. Any other commander starts them through Orca with `--agent claude --model <name>`, or has none outside Orca. Other seats, and any worker that needs its own checkout, go through the harness's orchestrator. Check which harness you are in before the first delegate.

- **Orca**, when `ORCA_CLI_COMMAND` is set. Read `orca skills get orchestration --full` once per session. Write the brief to a file. Run `orca orchestration worker-start --environment local --spec-file <brief.md> --worktree <current|new-child> <flags from the table>`. Use `current` for read-only work and `new-child --name <short-name>` when the worker edits files. End the turn after you start workers. Orca wakes you with each `worker_done`. To give a worker another round, run `orca orchestration send --to dispatch:<id>`.
- **T3 Code**, when the `delegate_task` tool exists. Call `orchestrator_capabilities` once per thread, then `delegate_task`. Pass the provider and model from the table, and pass the thinking level as `thought_level` for Antigravity or `reasoningEffort` for Codex. Every round or retry is a new `delegate_task`.
- **Neither.** Non-Claude seats are unavailable. Use the fallback rule above and say so in the summary.

### New features: advise, then argue

Before you implement a new feature, run a design debate. Bug fixes and small edits skip it.

1. **Advise.** Send the design adviser a brief with the goal and the constraints. It returns a plan: the approach, the files it touches, its risks, and what it skips. It reads code and edits nothing.
2. **Argue.** Send the plan to the hard-problems model. Tell it that another model wrote the plan and that its job is to attack it. It answers what is wrong, what breaks, what is simpler, and what it would do instead.
3. **Rebut.** Give the objections to the adviser for one answer, then give that answer to the hard-problems model for one answer. Stop after two rounds, or sooner when they agree.
4. **Decide.** The commander picks the design. The final summary says what it chose, the main objection it overruled, and why.

Each round is a new delegate with the full brief and the latest arguments. Nobody edits code during the debate.
{{PSTACK_SECTION}}<!-- orchestration-kit:end -->
