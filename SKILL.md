---
name: dual-review
description: Two-model security and code review by Claude Code and Codex. Run only when the user invokes it by name.
disable-model-invocation: true
---

# dual-review

Review with two models: you, the host, and the other agent (Codex if you are Claude Code,
Claude Code if you are Codex). Resolve the target first: use the user's explicit target,
otherwise the repo or files clearly being worked on in this conversation and workspace.
The current folder need not be a Git repo. State the inferred target and proceed; if there is
no clear target or several plausible ones, ask what the user wants reviewed before setup.
For Git, review changes since the merge-base with the default branch, including staged,
unstaged, and untracked files;
without Git, review the selected files directly. Never include `.dual-review/`.

Tell the other agent: "Review only for this pass. Do not edit repository files. Return your
findings; the host will handle any approved fixes afterward." Run it in the background, prompt
from a file, output forced to a strict JSON schema you write (every property required, `additionalProperties: false`): findings with id,
file:line, severity, claim, concrete failure scenario, smallest fix, confidence; plus not_examined.

```
codex exec -s read-only --ephemeral -c model_reasoning_effort=high --output-schema S -o OUT - < PROMPT
claude -p --permission-mode dontAsk --output-format json --json-schema "$(cat S)" < PROMPT > OUT   # findings: .structured_output
```

Locate a working CLI via PATH, user-local installs (for example `~/.local/bin`), or desktop
app bundles; use a full path as needed. This applies in both GUI-hosted and terminal sessions.
Prefer a working compatible install over the newest version. Use a permitted execution context
with network access. On launch failure, inspect the error and try another discovered install or
context when appropriate; if none works, report the error and remedy.

Record working CLI paths and launch requirements in the ledger and, when available, the host's
persistent memory. Reuse them for the same environment, honor the current model choice, and
rediscover if they stop working.

Use a compact monospaced opening summary with real values:

```
dual-review · <N> files, +<added>/−<removed> · <base>
<agent> · <model> · host
<agent> · <model> · reviewer
```

For a direct file review, show file paths instead of Git diff statistics and a base.
Keep progress to short `Round N · <phase>` lines, with an update at least once a minute during
long waits. If a review cannot start or stops, say so immediately, including what ran and why.
Present findings in a compact table, then finish with counts, commits, and coverage.

1. Once the target is resolved, tell the user the scope, both models, and that "use <model>"
   switches the other one. Don't wait.
2. Both sides review blind, at the same time, your side in a subagent. Start the other agent first;
   if it has already failed after about 10 seconds, stop. Only defects with a concrete failure scenario.
3. Each side tries to refute the other's findings against the code. Then weigh both arguments
   yourself, by the code alone: agreed, dropped, or disputed, keeping both positions.
4. Ask once: fix the agreed ones? Disputed ones only if the user says so.
5. Fix worst-first, one commit each when using Git, with a test that failed first, or mark the fix
   UNVERIFIED-BY-TEST. The other agent then reviews the changed files cold, with no mention of the
   fixes. Repeat from 3 until a round adds nothing agreed at medium or above; 3 rounds at most.
6. Report counts, commits, each dispute's two positions, and what was and wasn't examined. Never
   say "no issues found".

Keep a ledger in `.dual-review/<timestamp>/LEDGER.md` in the target repo or task folder as you go;
"resume" continues from it.
"scan only" skips 4 and 5. "quick" skips 3–5: one pass on each agent's cheapest model at low
effort, a design critique plus the defects that matter, sorted by consequence, nothing changed.
Other text is a focus.
