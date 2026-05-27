# htop Skill

A comprehensive, agent-agnostic skill for the **htop** interactive process viewer.

It teaches any LLM-powered coding/devops agent how to install, operate, configure, and troubleshoot `htop(1)` across Linux, macOS, BSDs, and Solaris — covering invocation flags, the full set of interactive key bindings, the Setup screen, all 40+ process columns, config file conventions, permissions, and head-to-head comparison with `top` / `btop` / `atop` / `glances`.

The format is a single `SKILL.md` with YAML frontmatter (Anthropic Agent Skills convention) plus a `references/` folder. It loads cleanly into Claude Code, OpenCode, Codex, Cursor, Hermes Agent, OpenClaw, Continue, and 50+ other agents — or any plain LLM context window.

## What's Inside

```
.
├── SKILL.md                  # Main skill (≈21 KB)
└── references/
    └── columns.md            # Full column glossary (≈6 KB)
```

`SKILL.md` covers:

- **Installation** — package managers for 10+ Linux distros, Homebrew/MacPorts, FreeBSD/OpenBSD/NetBSD/Solaris, and source build with all meaningful `./configure` flags
- **CLI flags** — every command-line option with practical one-liners
- **Interactive keys** — 59 key bindings grouped by Navigation / Search / View / Tagging / Inspect
- **Setup screen** — meters, display options, color themes, columns
- **Common recipes** — CPU/memory triage, kill workflows, latency diagnosis (CPUD%/IOD%/SWAPD%), container-aware view, read-only mode, headless snapshots via `script(1)`
- **Permissions** — when you need `CAP_SYS_PTRACE` / `CAP_NET_ADMIN` and how to grant them with `setcap`
- **Comparison table** — `htop` vs `top`, `btop`, `atop`, `glances`, `iotop`/`iftop`/`nethogs`
- **Troubleshooting** — canonical FAQ (memory-bar vs MEM% confusion, missing per-process SWAP, capability errors, broken terminal recovery)
- **Common pitfalls** + **Verification checklist**

`references/columns.md` documents every column htop can display, organized by category (Identity, Command, State/Priority/Time, Memory, CPU%, IO, Delay accounting, Threading & Containers, Misc), each with a short note on what it measures and any permission requirements.

## Install

The recommended way is the [Vercel Skills CLI](https://github.com/vercel-labs/skills) — one command, works for every supported agent, no per-tool config dance.

```bash
# Install for the agent in the current project (auto-detected)
npx skills add erencoding/htop-skill

# Install globally for your user (recommended for system tools like htop)
npx skills add erencoding/htop-skill -g

# Target a specific agent
npx skills add erencoding/htop-skill -a claude-code
npx skills add erencoding/htop-skill -a codex
npx skills add erencoding/htop-skill -a opencode
npx skills add erencoding/htop-skill -a cursor

# Install to all agents present on your machine
npx skills add erencoding/htop-skill --all -y
```

The CLI handles the directory layout for each agent automatically (Claude Code → `~/.claude/skills/`, Codex → `~/.codex/skills/`, OpenCode → `~/.config/opencode/skill/`, etc.), and by default symlinks so updates propagate.

### Hermes Agent

```bash
hermes skills install --yes https://github.com/erencoding/htop-skill
```

### Manual / Any Other Agent

```bash
git clone https://github.com/erencoding/htop-skill.git
# Then point your agent at SKILL.md however it loads custom skills
```

### Verify

```bash
# Vercel Skills CLI installs
npx skills list

# Hermes
hermes skills list | grep htop

# Manual
test -f /path/to/htop-skill/SKILL.md && echo "OK"
```

## Why "agent-agnostic"?

The frontmatter uses fields shared across the major skill conventions (`name`, `description`, `version`). The body is plain Markdown teaching a tool — nothing in it is bound to a specific agent runtime. The `metadata.hermes.*` block is namespaced and ignored by tools that don't recognize it, so portability costs nothing.

## Source Material

- Official site: <https://htop.dev/>
- Upstream repo: <https://github.com/htop-dev/htop>
- `man 1 htop` (htop 3.3.0+)

## License

MIT for the skill content. The htop project itself is GPL-2.0-or-later and is **not** redistributed here; this repo only contains documentation that teaches an agent how to use it.

## Author

曹俊 (`erencoding`) — written with the Clawhauser Hermes persona, designed to live anywhere an LLM agent can read Markdown.
