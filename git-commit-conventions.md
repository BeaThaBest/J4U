# ✍️ Clean Git Commit Messages — a ready-to-use AI prompt

A copy-paste prompt you can give to **any AI coding assistant** (Claude Code, Cursor,
Copilot, etc.) so it writes consistent, reviewable git commit messages — and never sneaks in
noise like AI attribution trailers.

It is based on Conventional-Commits, hardened with real safety rails (no force-push to main,
no rewriting pushed commits, secret scan before commit).

## How to use

Copy everything in the code block below and paste it into your AI assistant, or add it to your
project rules file (`CLAUDE.md`, `.cursorrules`, `AGENTS.md`, etc.) so it applies to every commit.

---

```markdown
# PROMPT: Write my git commit messages by these rules

You write every git commit message following the rules below. When I ask you to commit,
first show me a short proposal (files + message + docs impact), then commit. Never bypass
these rules, and never claim a commit is done without doing it.

## Message format
    <type>(<scope>): <summary ≤72 chars> — <optional detail>

- One line if simple. For more context, continue after an em-dash `—`, or add a blank line
  and a body explaining WHY (not what — the diff already shows what).
- **summary**: imperative-ish, says WHAT changed, ≤72 characters, no trailing period.
- **scope**: lowercase module/area (`api`, `ui`, `auth`, `db`, `ci`…). Optional but preferred.

## Types
| type     | when |
|----------|------|
| `feat`   | a new feature |
| `fix`    | a bug fix |
| `docs`   | documentation only |
| `refactor` | code change that neither fixes a bug nor adds a feature |
| `perf`   | performance improvement |
| `test`   | adding/fixing tests |
| `chore`  | build, deps, tooling, housekeeping |
| `style`  | formatting/whitespace, no logic change |

## Language
Match the project's convention. Open-source / shared repos: **English**, and reference the
PR/issue when relevant (e.g. `(#123)`). Solo/personal projects: use whatever language you
write in — just be consistent across the repo.

## FORBIDDEN — never add attribution trailers
Do NOT append any of these (some tools add them by default — strip them):
- `Co-Authored-By: <AI> ...`
- `🤖 Generated with ...`
- `Powered by ...`
- any AI/tool marketing or "co-partner" trailer
The commit is the author's. Keep the log clean.

## Safety rails (non-negotiable)
- Scan staged changes for secrets before committing; if a secret is found, STOP — do not commit.
- Never `git push --force` to `main`/`master`. Prefer `--force-with-lease` on your own branches.
- Never `--amend` a commit that is already pushed; create a new commit instead.
- Never use `--no-verify` to skip hooks — fix the root cause the hook is flagging.
- Don't commit `.env`, credentials, or generated artifacts (`node_modules/`, `dist/`, `bin/`).

## Workflow
1. Group related changes into one logical commit (don't dump unrelated changes together).
2. Propose: **files to commit** + **the message** + **docs that should be updated** (README/
   CHANGELOG). If docs are stale, offer to update them first.
3. On approval, commit. Split into multiple commits when the work spans distinct concerns.

## Examples
Good:
    feat(auth): add refresh-token rotation — invalidates old token on reuse
    fix(api): handle empty pagination cursor (#412)
    docs: document the retry/back-off config
    refactor(db): extract query builder into its own module
Bad:
    update stuff                         (no type, vague)
    Fixed the bug!!!                     (not imperative, no scope, noise)
    feat: add feature 🤖 Generated with …  (forbidden trailer)
```

---

Add it to your project rules file and every commit stays clean and consistent.
