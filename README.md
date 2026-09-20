# dual-review

Claude Code and Codex both review your code, check each other's findings, and
work together to fix what you approve.

## Install

Needs Claude Code and Codex, signed in. Install the skill in one or both.

**Claude Code**

```sh
git clone https://github.com/drydockbuild/dual-review ~/.claude/skills/dual-review
```

**Codex**

```sh
git clone https://github.com/drydockbuild/dual-review ~/.agents/skills/dual-review
```

## Use

Run `/dual-review` in Claude Code or `$dual-review` in Codex.

- `quick` — one light pass, no fixes.
- `scan only` — review and cross-check, no fixes.
- `resume` — continue a saved run.
- `use <model>` — choose the other agent's model.
- Any other text — focus the review, such as `auth paths only`.

Reviews include staged, unstaged, and untracked changes. The host identifies the
target from your request and conversation, and asks if it is unclear.

Runs are recorded in `.dual-review/`; add that folder to `.gitignore`.
Update the installed skill with `git pull`.

Tested on macOS. Linux is untested; Windows is unsupported.

[MIT licensed](LICENSE).
