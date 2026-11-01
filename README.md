# J4U — Just For You

A growing collection of **ready-to-use prompts** for AI coding assistants (Claude Code, Cursor,
Copilot, and friends). Each one is a self-contained, copy-paste prompt that gets your AI to set
up a real, battle-tested engineering practice — the right way, with the details most guides skip.

> Copy a prompt, paste it into your AI assistant, and let it do the setup. Each prompt tells the
> AI exactly what to install, research, build, and — importantly — how to **prove it works**.

## 📚 Prompts

| Prompt | What it sets up |
|--------|-----------------|
| [🔐 Secret-Leak Prevention](secret-leak-prevention.md) | Cross-platform (macOS/Linux/Windows), 7-layer defense that stops secrets from ever being committed or pushed — `.gitignore`/`.env` hygiene, `gitleaks` + `trufflehog` dual-engine hooks, multi-repo installer, weekly full-history audit, server-side push protection. |
| [✍️ Git Commit Conventions](git-commit-conventions.md) | Clean, consistent commit messages (Conventional-Commits style) with real safety rails — no force-push to main, no rewriting pushed commits, no AI attribution trailers, secret scan before commit. |
| [⚡ Faster Website in 10 Checks](web-performance.md) | Measure, fix, measure again: modern image formats, image dimensions, lazy loading, font loading, code splitting, unused code, Brotli/gzip, CDN, cache headers, Core Web Vitals targets. English + 🇹🇷 Türkçe. |
| [🖥️ Windows Performance](windows-performance.md) | Measured, safe Windows tuning: discovers the exact PC, baseline first, one change at a time, rollback for anything that does not help. Security stays on. English + 🇹🇷 Türkçe. |
| [🎮 Windows Gaming Performance](windows-gaming-performance.md) | Highest stable gaming performance with proof: frametime and latency measured, one change at a time, GPU undervolt plus overclock, stability gates, per-game profiles, rollback that survives reboots. English + 🇹🇷 Türkçe. |

## 🎯 Why these exist

Most "how-to" guides stop at the happy path. These prompts encode the hard-won details:
fail-closed (not fail-open) hooks, why you need two secret scanners, preserving commit dates
when rewriting history, keeping the git log free of tool noise. The kind of thing you only
learn after it bites you.

More prompts coming. ⭐ the repo if it's useful.

## 📄 License

[MIT](LICENSE) — use it, share it, adapt it.
