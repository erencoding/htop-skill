# htop Skill

A comprehensive, agent-agnostic skill for the **htop** interactive process viewer.

It teaches any LLM-powered coding/devops agent how to install, operate, configure, and troubleshoot `htop(1)` across Linux, macOS, BSDs, and Solaris — covering invocation flags, the full set of interactive key bindings, the Setup screen, all 40+ process columns, config file conventions, permissions, and head-to-head comparison with `top` / `btop` / `atop` / `glances`.

The format is a single `SKILL.md` with YAML frontmatter (Anthropic Skills convention) plus a `references/` folder. It loads cleanly into Hermes Agent, Claude Code, OpenClaw, Codex CLI, Cursor, Continue, or any plain LLM context window.

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

### Universal install (symlink one source into every agent)

Clone once into a shared location, then symlink it to each agent's skill directory. This way every tool reads the same files — update once, take effect everywhere.

```bash
# 1. Clone to a shared location
mkdir -p ~/skills
git clone https://github.com/erencoding/htop-skill.git ~/skills/htop

# 2. Symlink into the agents you use
#    (run only the lines for tools you actually have)

# Hermes Agent
mkdir -p ~/.hermes/skills
ln -sfn ~/skills/htop ~/.hermes/skills/htop

# Claude Code
mkdir -p ~/.claude/skills
ln -sfn ~/skills/htop ~/.claude/skills/htop

# OpenClaw
mkdir -p ~/.openclaw/skills
ln -sfn ~/skills/htop ~/.openclaw/skills/htop

# Codex CLI / generic AGENTS.md consumers — point your AGENTS.md at it
echo '@~/skills/htop/SKILL.md' >> ~/.codex/AGENTS.md
```

### Per-agent shortcuts

| Agent | One-liner |
|---|---|
| **Hermes Agent** | `hermes skills install --yes https://raw.githubusercontent.com/erencoding/htop-skill/main/SKILL.md` |
| **Claude Code** | `git clone https://github.com/erencoding/htop-skill.git ~/.claude/skills/htop` |
| **OpenClaw** | `git clone https://github.com/erencoding/htop-skill.git ~/.openclaw/skills/htop` |
| **Codex CLI** | Append `@<path>/SKILL.md` to your project's `AGENTS.md` |
| **Cursor** | Copy `SKILL.md` contents into `.cursor/rules/htop.mdc` |
| **Continue** | Add `SKILL.md` to `~/.continue/rules/` |
| **Any other LLM** | Paste `SKILL.md` into the system prompt or context window |

### Verify

```bash
# Hermes
hermes skills list | grep htop

# Claude Code / OpenClaw
ls -la ~/.claude/skills/htop ~/.openclaw/skills/htop 2>/dev/null

# Generic
test -f ~/skills/htop/SKILL.md && echo "OK"
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

曹俊 (`erencoding`) — written with the Clawhauser Hermes persona, but designed to live anywhere an LLM agent can read Markdown.
