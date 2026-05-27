# htop Skill for Hermes Agent

A comprehensive [Hermes Agent](https://github.com/NousResearch/hermes-agent) skill for the **htop** interactive process viewer.

This skill teaches an AI agent to install, operate, configure, and troubleshoot `htop(1)` across Linux, macOS, BSDs, and Solaris — covering invocation flags, the full set of interactive key bindings, the Setup screen, all 40+ process columns, config file conventions, permissions, and head-to-head comparison with `top` / `btop` / `atop` / `glances`.

## What's Inside

```
.
├── SKILL.md                  # Main skill (≈21 KB)
└── references/
    └── columns.md            # Full column glossary (≈6 KB)
```

The main `SKILL.md` covers:

- **Installation** — package managers for 10+ Linux distros, Homebrew/MacPorts, FreeBSD/OpenBSD/NetBSD/Solaris, and source build with all the meaningful `./configure` flags
- **CLI flags** — every command-line option with practical one-liners
- **Interactive keys** — 59 key bindings grouped by Navigation / Search / View / Tagging / Inspect
- **Setup screen** — meters, display options, color themes, columns
- **Common recipes** — CPU/memory triage, kill workflows, latency diagnosis (CPUD%/IOD%/SWAPD%), container-aware view, read-only mode, headless snapshots via `script(1)`
- **Permissions** — when you need `CAP_SYS_PTRACE` / `CAP_NET_ADMIN` and how to grant them with `setcap`
- **Comparison table** — `htop` vs `top`, `btop`, `atop`, `glances`, `iotop`/`iftop`/`nethogs`
- **Troubleshooting** — the canonical FAQ (memory-bar vs MEM% confusion, missing per-process SWAP, capability errors, broken terminal recovery)
- **Common pitfalls** + **Verification checklist**

`references/columns.md` documents every column htop can display, organized by category (Identity, Command, State/Priority/Time, Memory, CPU%, IO, Delay accounting, Threading & Containers, Misc), each with a short note on what it measures and any permission requirements.

## Install

### Via Hermes Agent

```bash
hermes skills install --yes https://raw.githubusercontent.com/erencoding/htop-skill/main/SKILL.md
```

### Manual install

```bash
git clone https://github.com/erencoding/htop-skill.git ~/.hermes/skills/htop
```

Then verify:

```bash
hermes skills list | grep htop
```

## Source Material

- Official site: <https://htop.dev/>
- Upstream repo: <https://github.com/htop-dev/htop>
- `man 1 htop` (htop 3.3.0+)

## License

MIT — same spirit as the surrounding Hermes skill ecosystem.
The htop project itself is GPL-2.0-or-later and is **not** redistributed here; this repo only contains documentation that teaches an agent how to use it.

## Author

曹俊 (`erencoding`) — created with the Clawhauser Hermes persona.
