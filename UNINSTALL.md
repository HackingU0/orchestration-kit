# Uninstall orchestration-kit

You are an agent removing orchestration-kit from a git repository. `REPO` is `git rev-parse --show-toplevel`. Do not ask the user questions.

1. Read the start marker in `REPO/AGENTS.md`. Note its `pstack=` value.
2. In `REPO/AGENTS.md` and `REPO/CLAUDE.md`, delete every line from `<!-- orchestration-kit:start` through `<!-- orchestration-kit:end -->`. Delete one blank line before the block if the block left two in a row.
3. If a file now holds nothing but `# Agents` or whitespace, delete the file.
4. If the marker said `pstack=installed`, run `claude plugin uninstall pstack@cursor-plugins --scope local` from `REPO`. Leave pstack alone for any other value, because the user had it before the install.
5. Run `git diff --stat` and confirm that only those files changed.
6. Tell the user what you removed. Tell them that the backups in `REPO/.git/orchestration-kit-backup/` are kept, and that the probe Runs stay in Orca's history.
