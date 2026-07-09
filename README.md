# 🔐 Secret-Leak Prevention — a ready-to-use AI prompt

A copy-paste prompt you can give to **any AI coding assistant** (Claude Code, Cursor,
Copilot, etc.) to build a cross-platform, defense-in-depth system that stops secrets and
credentials from ever being committed or pushed to git.

It works on **macOS, Linux, and Windows**, uses **two complementary scanners** (gitleaks +
trufflehog), and — most importantly — starts with the layer everyone forgets: making sure a
secret is **never tracked in the first place** (`.gitignore` + `.env` hygiene).

> Why two engines? gitleaks is regex/entropy based; trufflehog verifies secrets against live
> provider APIs. Each catches what the other misses.

## How to use

Copy everything in the code block below and paste it into your AI assistant. Then follow
along — it will install the tools, write the hooks, and **prove they work** before finishing.

---

```markdown
# PROMPT: Set up a cross-platform, multi-layer secret-leak prevention system for git

You are a senior engineer. Build me a defense-in-depth system that stops secrets/
credentials from ever being committed or pushed to git. It must work on macOS, Linux,
AND Windows. Follow this spec exactly. Do NOT skip the testing step. Report evidence.

## 0. Prerequisites — install two complementary engines (per OS)
Install BOTH gitleaks (regex/entropy) and trufflehog (verifies secrets against live APIs).
| OS      | Command |
|---------|---------|
| macOS   | `brew install gitleaks trufflehog` |
| Linux   | `apt/dnf/pacman` if available, else official release binaries, or `go install github.com/gitleaks/gitleaks/v8@latest` + trufflehog install script |
| Windows | `winget install gitleaks` **or** `scoop install gitleaks trufflehog` **or** `choco install gitleaks trufflehog` |
Confirm both are on PATH and print versions before continuing. On Windows, hooks run under
**Git Bash** (bundled with Git for Windows), so bash hook scripts work everywhere — but save
them with **LF line endings** and a `#!/usr/bin/env bash` shebang.

## LAYER 0 — Prevention: .gitignore + .env hygiene (do this FIRST)
A scanner is the safety net; the real fix is that secrets never sit in a tracked file.
1. Add these to every repo's `.gitignore` (and a global one via
   `git config --global core.excludesfile ~/.gitignore_global`):
   ```
   .env
   .env.*
   !.env.example
   *.pem
   *.key
   *.p12
   *.pfx
   id_rsa
   id_ed25519
   credentials.json
   secrets.*
   *.secret
   .aws/
   .gcloud/
   *serviceaccount*.json
   ```
2. Keep a committed **`.env.example`** with PLACEHOLDER values only; real values live in the
   gitignored `.env`. Load them via environment variables or a secret manager — NEVER hardcode.
3. Audit that nothing sensitive is ALREADY tracked (ignore rules don't untrack existing files):
   `git ls-files | grep -E '\.env$|\.pem$|\.key$|credentials|secret'`
   If found: `git rm --cached <file>`, add to `.gitignore`, then treat its history as leaked
   (see Layer 5).

## LAYER 1 — Shared gitleaks config (one tracked file, reused by all repos)
Create `gitleaks-shared.toml`:
- `[extend] useDefault = true` (keep the built-in provider rules).
- Add HIGH-PRECISION org rules only (cloud client_secret, bot tokens, national IBAN,
  `api_key/secret/token = "<24+ high-entropy>"`).
- `[allowlist]`: exclude example/test/placeholder values AND vendored/generated code
  (`node_modules/`, `dist|build|vendor/`, `*.min.js`, plugin bundles, docs). This keeps the
  gate credible — a noisy gate gets ignored.

## LAYER 2 — pre-commit hook (block BEFORE it enters history)
`gitleaks git --staged --redact --config <shared>`.
- FAIL-CLOSED: gitleaks missing → print error, `exit 1`.
- Secret found → block commit, clear message, never suggest `--no-verify`.
- Prepend a robust PATH so it works under cron/launchd/Task Scheduler.

## LAYER 3 — pre-push hook (dual-engine, scans only new commits `<remote>..<local>`)
- Engine 1: `gitleaks git --log-opts=<range>` → FAIL-CLOSED.
- Engine 2: `trufflehog git file://<repo> --since-commit <remote> --fail --no-update`
  → block ONLY on exit code **183**; other/error codes do not block; if trufflehog absent, warn.

## LAYER 4 — Multi-repo installer (portable, not one-off)
Keep hook scripts + shared config as tracked "source of truth" files; symlink them into each
repo's `.git/hooks/`. Installer must:
- install pre-commit + pre-push across a repo list,
- NEVER overwrite a foreign hook (husky etc.) — skip + report; offer `--migrate` (backs up
  `.pre-omc.bak` first).
- `.git/hooks/` isn't tracked → re-run after each clone.
- Windows note: symlinks need Developer Mode/admin; if unavailable, COPY the scripts instead.

## LAYER 5 — Weekly full-history audit (catches what predates the hooks) + scheduling
Script: run gitleaks over FULL history of every repo, log a dated summary, exit non-zero on
findings. Schedule weekly per OS:
| OS      | Scheduler |
|---------|-----------|
| macOS   | launchd `.plist` (`StartCalendarInterval`) |
| Linux   | cron (`crontab -e`) or a systemd timer |
| Windows | Task Scheduler (`schtasks /create ...`) |

## LAYER 6 — Server-side (the only un-bypassable layer)
Recommend enabling the platform's push protection (e.g. GitHub Secret Scanning / Push
Protection, GitLab Secret Detection). Local hooks can be bypassed with `--no-verify` or by
deleting the hook; server-side scanning cannot.

## PROVE it works (mandatory — paste real output)
In a throwaway repo demonstrate all five:
1. clean commit → passes
2. staged secret → pre-commit BLOCKS
3. gitleaks temporarily removed → commit BLOCKS (fail-closed)
4. clean push → passes
5. committed secret (`--no-verify` only to create the test) → pre-push BLOCKS

## Non-negotiable principles
- Layer 0 (never track the secret) beats every scanner; do it first.
- Defense in depth; fail-closed everywhere; `--no-verify` forbidden — fix the root cause.
- Never silently skip — if something is skipped, say so.
- Secrets already in history: verify if live → rotate live ones at the provider → remove with
  `git filter-repo --replace-text` (PRESERVES commit dates) → double backup, never
  `filter-branch`, push with `--force-with-lease`.
```

---

## The 7 layers at a glance

| Layer | What it does | Blocks at |
|-------|--------------|-----------|
| 0 | `.gitignore` + `.env` hygiene | never tracked |
| 1 | Shared gitleaks config | — |
| 2 | pre-commit (gitleaks staged, fail-closed) | commit time |
| 3 | pre-push (gitleaks + trufflehog) | push time |
| 4 | Multi-repo installer | rollout |
| 5 | Weekly full-history audit | after the fact |
| 6 | Server-side push protection | un-bypassable |

## License

MIT — use it, share it, adapt it.
