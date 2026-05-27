---
name: htop
description: Use when monitoring or troubleshooting Linux/Unix system performance — interactive ncurses process viewer with scrolling, tree view, filtering, signal sending, CPU/memory/IO meters, and customizable layout. Covers install, invocation flags, key bindings, setup screen, columns, config files, and common diagnostic recipes.
version: 1.0.0
author: 曹俊 (Clawhauser persona)
license: MIT
metadata:
  hermes:
    tags: [htop, monitoring, processes, sysadmin, performance, linux, ncurses, top]
    homepage: https://htop.dev/
    upstream: https://github.com/htop-dev/htop
    related_skills: [clash-linux]
---

# htop — Interactive Process Viewer

`htop` is a cross-platform, ncurses-based interactive process viewer. It is the de-facto modern replacement for `top(1)` on Linux, FreeBSD, NetBSD, OpenBSD, DragonFly BSD, macOS, and Solaris. Compared to `top`, it adds vertical/horizontal scrolling, mouse support, tree view, multi-process tagging, in-place killing/renicing, configurable meters, and color schemes — all without leaving the terminal.

Maintained by the htop dev team at <https://github.com/htop-dev/htop> (originally created by Hisham Muhammad). Latest release at the time of writing: **3.5.1** (Apr 2026). The build on this machine is **3.3.0**.

## When to Use

Reach for htop when you need to:

- See **what is using CPU, memory, or I/O right now** on a machine you can SSH into.
- Find and **kill / renice / signal** a misbehaving process without copying PIDs around.
- Inspect the **process tree** (parent/child relationships, e.g. which gateway spawned which subagent).
- **Filter or search** the process list by command name (`F4` / `F3`).
- Watch **per-CPU usage**, **load average**, **swap**, **memory breakdown** (used / buffers / cache), all in one screen.
- Trace syscalls (`s` → strace) or list open files (`l` → lsof) for the selected process.
- Confirm a server's load before/after a deployment, in a way that's friendlier than `top`.

Don't use htop for:

- **Headless / scripted monitoring** — use `ps`, `top -b -n 1`, `pidstat`, `vmstat`, or Prometheus node-exporter instead. htop needs a real TTY.
- **Historical metrics** — htop only shows live state. Use `sar`, `atop`, or a metrics stack for history.
- **Per-container deep visibility** — htop respects cgroups via the `CGROUP`/`CCGROUP` columns and the `O` toggle, but `ctop`, `docker stats`, or `nerdctl stats` give richer per-container views.

## Quick Start

```bash
# Run it
htop

# Inside htop — the five most useful keys to memorize first:
#   F1 / h / ?   help
#   F2 / S       setup (meters, colors, columns)
#   F3 / /       search command
#   F4 / \       filter command (like ps | grep, but persistent)
#   F5 / t       toggle tree view
#   F6 / < / >   pick sort column
#   F9 / k       send signal (kill)
#   F10 / q      quit
```

The function-key labels at the bottom mirror the menu, so you never have to memorize them all.

## Installation

### Linux package managers

| Distro | Command |
|--------|---------|
| Debian / Ubuntu | `sudo apt update && sudo apt install -y htop` |
| Fedora / RHEL 8+ / Rocky / Alma | `sudo dnf install -y htop` |
| RHEL 7 / CentOS 7 | `sudo yum install -y epel-release && sudo yum install -y htop` |
| Arch / Manjaro | `sudo pacman -S --needed htop` |
| openSUSE / SLES | `sudo zypper install -y htop` |
| Alpine | `sudo apk add htop` |
| Gentoo | `sudo emerge -av sys-process/htop` |
| Void | `sudo xbps-install -S htop` |
| NixOS / Nix | `nix-env -iA nixpkgs.htop` (or add to `environment.systemPackages`) |

### macOS

```bash
brew install htop      # Homebrew (recommended)
sudo port install htop # MacPorts
```

### BSDs / Solaris

```bash
sudo pkg install htop                # FreeBSD
sudo pkg_add htop                    # OpenBSD
sudo pkgin install htop              # NetBSD / pkgsrc
pkgutil -i htop                      # Solaris (via OpenCSW)
```

### Verify

```bash
htop --version
# htop 3.x.y
# (C) 2004-2019 Hisham Muhammad. (C) 2020-202x htop dev team.
# Released under the GNU GPLv2+.
```

### Build from source

When the distro package is too old (older than 3.0 lacks many features) or you want optional features like sensors / hwloc / PCP:

```bash
# 1. Build deps
sudo apt install -y libncursesw5-dev autotools-dev autoconf automake build-essential pkg-config   # Debian/Ubuntu
sudo dnf install -y ncurses-devel automake autoconf gcc make pkgconf-pkg-config                   # Fedora/RHEL

# 2. Source
git clone https://github.com/htop-dev/htop.git && cd htop

# 3. Configure with the features you want
./autogen.sh
./configure \
    --enable-unicode \
    --enable-sensors \
    --enable-capabilities \
    --enable-delayacct
make -j"$(nproc)"

# 4. Install
sudo make install   # default prefix /usr/local
```

Common `./configure` flags worth knowing:

- `--enable-unicode` (default yes) — Unicode meter graphs; requires `libncursesw`.
- `--enable-sensors` — temperature readings via `libsensors` (Linux).
- `--enable-hwloc` — CPU affinity via `libhwloc` (conflicts with `--enable-affinity`).
- `--enable-capabilities` — Linux capability detection in EXE/USER coloring.
- `--enable-delayacct` — `CPUD%` / `IOD%` / `SWAPD%` columns; needs `libnl-3` + `CAP_NET_ADMIN`.
- `--enable-pcp` — builds the companion **pcp-htop** that pulls metrics via Performance Co-Pilot.
- `--enable-static` — single static binary (drops hwloc + delay accounting).

## Command-line Flags

```
htop [options]

  -d, --delay=DECISECONDS    Update interval in 1/10s. 1 ≤ d ≤ 100.
  -C, --no-color             Monochrome.
  -F, --filter=FILTER        Pre-applied filter (case-insensitive, fixed strings, '|' for OR).
  -p, --pid=PID[,PID...]     Show only the listed PIDs.
  -s, --sort-key=COLUMN      Sort column (use --sort-key=help to list).
  -t, --tree                 Open in tree view.
  -u, --user[=USERNAME|UID]  Show only that user's processes (no arg → $USER).
  -U, --no-unicode           ASCII-only graphs.
  -M, --no-mouse             Disable mouse.
  -H, --highlight-changes[=DELAY]  Briefly color new/dying processes.
  -n, --max-iterations=N     Exit after N frame updates (handy for screenshots/logs).
      --readonly             Disable every system/process-changing feature.
      --drop-capabilities[=off|basic|strict]   Linux only; lower htop's own caps.
  -h, --help                 Help.
  -V, --version              Version.
```

Useful one-liners:

```bash
# Watch only your processes, refresh every 0.3s
htop -u "$USER" -d 3

# Tree view, sorted by RSS, only the gateway processes
htop -t -s M_RESIDENT -F 'gateway run'

# Read-only monitoring on a shared host (no kill/renice)
htop --readonly

# Watch specific PIDs
htop -p 1234,5678

# Print the full sortable column list
htop --sort-key=help
```

## Interactive Keys (Cheat Sheet)

### Navigation

| Key | Action |
|-----|--------|
| `↑` / `↓` / `Alt-k` / `Alt-j` | Move selection |
| `←` / `→` / `Alt-h` / `Alt-l` | Scroll horizontally |
| `PgUp` / `PgDn` | Page up / down |
| `Home` / `End` | First / last process |
| `Ctrl-A` / `^` | Jump to start of line |
| `Ctrl-E` / `$` | Jump to end of line |
| `Ctrl-L` | Redraw the screen |
| `Tab` / `Shift-Tab` | Next / previous screen tab |
| Type a number | Jump to that PID |

### Search & Filter

| Key | Action |
|-----|--------|
| `F3` / `/` | Incremental search across command lines (`F3` again = next match, `Shift-F3` = previous) |
| `F4` / `\` | Persistent filter — only matching commands stay visible. `Esc` to clear. Use `\|` for OR |
| `u` | Show only one user's processes |

### View & Sort

| Key | Action |
|-----|--------|
| `F5` / `t` | Toggle tree view |
| `F6` / `<` / `>` | Open sort-column menu |
| `I` | Invert sort order |
| `+` / `-` / `*` | In tree view: expand / collapse subtree, `*` toggles top-level |
| `M` / `P` / `T` / `N` | Quick sort by MEM% / CPU% / TIME / PID (top-compatible) |
| `K` | Toggle kernel threads |
| `H` | Toggle userspace threads |
| `O` | Toggle containerized processes |
| `p` | Toggle full executable paths |
| `m` | Merge `exe` / `comm` / `cmdline` in the Command column |
| `Z` | Pause / resume the live display |
| `F` | "Follow" mode — selection bar tracks the current process even as it moves |

### Tagging & Acting on Many Processes at Once

| Key | Action |
|-----|--------|
| `Space` | Tag / untag the highlighted process |
| `c` | Tag the process and all its children |
| `U` | Untag all |
| `F9` / `k` | Send signal — applies to all tagged processes if any are tagged, else to the highlighted one |
| `F7` / `]` | Renice up (more priority — root only) |
| `F8` / `[` | Renice down (less priority) |
| `Shift-F7` / `}` | Autogroup renice up (root) |
| `Shift-F8` / `{` | Autogroup renice down |
| `a` | Set CPU affinity (multi-CPU only) |

### Inspect a Single Process

| Key | Action |
|-----|--------|
| `s` | Attach `strace(1)` and watch syscalls |
| `l` | List open files via `lsof(1)` |
| `w` | Show the full command wrapped across multiple lines |
| `x` | Show active file locks |

### Top of Screen

| Key | Action |
|-----|--------|
| `F1` / `h` / `?` | Help |
| `F2` / `S` | Setup screen (meters, colors, columns, options) |
| `F10` / `q` | Quit |

## Setup Screen (`F2`)

The setup screen has four sections:

1. **Meters** — top-of-screen widgets. You can add per-CPU bars, memory, swap, load average, uptime, hostname, tasks, date/time, battery, system temp, network/disk I/O, systemd services, ZFS ARC/CARC, etc. Place them in the **Left** column, **Right** column, or the **inline** row, and pick `Bar` / `Text` / `Graph` / `LED` style.
2. **Display options** — tree view default, highlight base name, hide threads, shadow other users, show CPU temperature/frequency, persistent header, etc.
3. **Colors** — built-in themes: Default, Monochromatic, Black on White, Light Terminal, MC, Black Night, Broken Gray. The terminal's color palette wins over the theme.
4. **Columns** — choose which columns appear in the process list, in what order. Available columns are listed in [`references/columns.md`](references/columns.md).

**Important:** htop saves your setup back to the config file **only on a clean exit** (`F10` / `q`). Killing the terminal or sending `SIGKILL` discards changes.

## Columns Cheat Sheet

The most actionable columns to add when triaging:

- `PID`, `USER`, `PRI`, `NI`, `STATE`
- `M_RESIDENT` (RES), `M_VIRT` (VIRT), `M_SHARE` (SHR), `M_PSS` (proportional set size — fairer for shared libs)
- `PERCENT_CPU` (CPU%), `PERCENT_NORM_CPU` (NCPU%, divided by core count — Solaris mode), `PERCENT_MEM` (MEM%)
- `TIME+`, `STARTTIME`
- `IO_READ_RATE`, `IO_WRITE_RATE`, `IO_RATE` — needs CAP\_NET\_ADMIN or root
- `PERCENT_CPU_DELAY`, `PERCENT_IO_DELAY`, `PERCENT_SWAP_DELAY` (CPUD% / IOD% / SWAPD%) — needs delay accounting + CAP\_NET\_ADMIN; gold for diagnosing "fast CPU but slow app" symptoms
- `OOM` — out-of-memory killer score; high values get killed first under memory pressure
- `CGROUP` / `CCGROUP` — which cgroup/container slice
- `Command` (with `m` toggle to merge `exe`/`comm`/`cmdline`)

Full descriptions of every column live in [`references/columns.md`](references/columns.md).

## Config Files

- Per-user: `~/.config/htop/htoprc` (XDG-compliant). Override with the `$HTOPRC` env var to switch configs per host.
- System-wide fallback: `/etc/htoprc`.
- **Don't hand-edit `htoprc`.** It's overwritten on clean exit. Use `F2` inside htop and quit cleanly.
- For reproducible setups, keep a curated `htoprc` in your dotfiles repo and let your provisioning system (Ansible/chezmoi/stow) drop it into place.

`pcp-htop` reads the same `htoprc` plus extra files described in `pcp-htop(5)`, which lets you surface arbitrary Performance Co-Pilot metrics as columns or meters.

## Common Recipes

### "What's eating CPU on this box?"

```bash
htop -s PERCENT_CPU
```
Inside: press `H` to hide threads if a multi-threaded app is spamming the list, then `t` for tree to see the parent process owning the work.

### "What's eating RAM?"

```bash
htop -s PERCENT_MEM
```
Add the `M_PSS` column (`F2` → Columns) — for shared-library-heavy workloads, PSS is a fairer measure than RES because it divides shared pages by the number of sharers.

### "Find and kill a runaway process by name"

```bash
htop -F nginx
```
Highlight the offender, press `F9`, pick `SIGTERM` (15) first, then `SIGKILL` (9) only if it doesn't exit cleanly. **Reach for SIGTERM before SIGKILL** — most well-written services flush state on SIGTERM and just die on SIGKILL.

### "Why is this app slow even though CPU is idle?"

Add the delay-accounting columns (CPUD% / IOD% / SWAPD%) via `F2`. High **IOD%** = blocked on disk; high **SWAPD%** = paging; high **CPUD%** = scheduler contention (lots of runnable tasks but not enough cores). Needs `CAP_NET_ADMIN` (run via `sudo` or grant the capability with `setcap`).

### "Show only my user's processes"

Either launch with `htop -u` (current user) or press `u` inside and pick.

### Snapshot for a bug report

htop **needs a real TTY** even with `-n 1` — running it from a non-interactive shell (cron, CI, agent terminals) bombs with `Error opening terminal: unknown.`. Wrap it with `script(1)` so a pseudo-TTY is allocated:

```bash
# One-frame snapshot to a file
script -q -c 'htop -C -n 1' htop-snapshot.txt
# Multi-frame typescript at 0.5 s intervals
script -q -c 'htop -C -d 5 -n 4' htop.log
```

The output contains terminal control sequences. Strip them with `col -bp < file` or `sed 's/\x1b\[[0-9;]*[a-zA-Z]//g'` for plain text. For headless metric collection, prefer `top -b -n 1` or `ps`/`pidstat` instead — they were designed for batch use.

### Container-aware view

Inside a container, htop sees only the container's PID namespace. From the host, press `O` to hide containerized processes (so the container's noise doesn't drown out host services), or invert the toggle to focus on container workloads. Use the `CCGROUP` column to see which container slice each process belongs to (`/[lxc:name]`, `/[SNC:scope]`, etc.).

### Read-only monitoring on a shared host

```bash
htop --readonly
```
Disables F7/F8/F9, the kill menu, and every other state-changing action. Good when several admins share one tmux session.

## Permissions Cheat Sheet

Some htop columns and actions require elevated privileges:

| What you want | Why it needs perms | How to grant |
|---------------|-------------------|--------------|
| See `EXE` for processes you don't own | Needs `CAP_SYS_PTRACE` to read `/proc/[pid]/exe` | `sudo htop`, or `sudo setcap cap_sys_ptrace=ep $(which htop)` (per-binary capability) |
| Disk I/O columns (`IO_RATE`, `IO_READ_RATE`, `IO_WRITE_RATE`) for other users | Reads `/proc/[pid]/io` | Same as above |
| Delay-accounting columns (CPUD% / IOD% / SWAPD%) | Talks to the kernel taskstats netlink interface | `sudo`, or `cap_net_admin` |
| Renice up (`F7`) / autogroup renice (`Shift-F7`) | Lowers nice value below default | `sudo` |
| Kill processes you don't own | Standard Unix permission | `sudo` |

Avoid `sudo htop` when you don't need the extra columns — running interactive UIs as root is a foot-gun.

## htop vs. top, btop, atop, glances

| Tool | When to pick it |
|------|----------------|
| `top` | Available *literally everywhere*, scriptable with `-b -n 1`. Pre-mortem, recovery shells, busybox boxes. |
| **`htop`** | Default for interactive triage. Best ratio of features to ubiquity. |
| `btop` | Modern Rust-y look-and-feel, GPU/network meters out of the box, mouse-first. Heavier. Pick when you want eye candy. |
| `atop` | Records historical samples (`/var/log/atop`), includes per-process disk/network. Use when you need to answer "what was happening at 03:14 last night?" |
| `glances` | Python-based, exposes a REST API and curses UI; nice for embedding into dashboards. |
| `iotop` / `iftop` / `nethogs` | Single-purpose drilldowns for IO / bandwidth / per-process network. Pair with htop. |

## Troubleshooting

### "Memory bar is almost full but MEM% column is small"

This is the canonical htop FAQ. The MEM% column = memory used by the **process itself** (resident set). The header bar = total memory used, which includes **kernel buffers and the page cache** (the blue + brown segments). Linux uses idle RAM for cache; that's healthy. Watch the green segment (and `available` from `free -h`) instead.

### "No SWAP column per process?"

Per-process swap usage cannot be measured exactly on Linux because of shared pages — `top`'s SWAP column fakes it as `VIRT - RES`, which double-counts video memory and shared mappings. htop deliberately omits it. Use `M_SWAP` (`/proc/[pid]/status`'s VmSwap, an approximation) or `M_PSSWP` if you need a number.

### "I changed colors / meters and they didn't persist"

You likely killed htop instead of pressing `q` / `F10`. Setup is only flushed on clean exit. If the terminal closes while htop is running, the in-program changes are lost.

### "Mouse doesn't work over SSH / inside tmux"

- Enable mouse in tmux: `set -g mouse on`.
- Some terminals send a different mouse protocol — try iTerm2, Alacritty, or kitty if your current one stays unresponsive.
- If you genuinely don't want mouse, run `htop -M` to disable it cleanly.

### "Tree view loses my sort"

Selecting any sort column exits tree view (the man page says so explicitly). Re-enter tree view with `t` after sorting; tree view has its own internal ordering by parent → child.

### "htop crashed and now my terminal is broken"

```bash
reset       # or: stty sane; tput reset
```

### "Capability errors / 'Permission denied' on certain columns"

The `EXE`, IO, and delay-accounting columns need `CAP_SYS_PTRACE` and/or `CAP_NET_ADMIN`. Either `sudo htop`, or persist the capability on the binary:

```bash
sudo setcap 'cap_sys_ptrace,cap_net_admin=ep' "$(command -v htop)"
```

Re-run `htop` and verify with `getcap "$(command -v htop)"`.

### "Wrong CPU temperature / no temperatures shown"

htop reads via `libsensors`. Run `sensors-detect` (from `lm-sensors`) once to populate `/etc/sensors.d/` and load the right kernel modules; htop only finds cores named like `Core 0`, `Package id 0` (the `coretemp` driver convention). On VMs you usually won't have any.

### "htop is too old in my distro"

Older htop (`< 3.0`) lacks tree-view sort, mouse-aware menus, and the modern setup screen. Either backport (`apt install -t bookworm-backports htop`), get it from a third-party repo, or [build from source](#build-from-source).

## Bringing It Back to This Box

This Hermes host shipped with `htop 3.3.0` from `/usr/bin/htop`. The latest upstream is `3.5.1` (Apr 2026), so a backport or source build gets you newer columns and bug fixes — but 3.3.0 is plenty for day-to-day triage. The 5 Hermes gateway profiles (each ~300 MB RSS) make a great practice target: launch `htop -t -F gateway` and watch the tree light up.

## Common Pitfalls

1. **`sudo htop` by reflex.** Only escalate when you actually need to act on root-owned processes or read protected `/proc` entries. Running interactive UIs as root invites accidental kills.
2. **Mistaking SIGKILL for SIGTERM.** Always send SIGTERM (15) first; only escalate to SIGKILL (9) if the process refuses to exit. SIGKILL bypasses cleanup and can leave stale lock files / sockets.
3. **Editing `~/.config/htop/htoprc` by hand while htop is running.** htop overwrites the file on clean exit — your edits will vanish. Quit first, edit second, or just use `F2`.
4. **Forgetting that setup persists only on clean exit.** Tmux pane killed? Window closed? Setup lost.
5. **Confusing per-process MEM% with the system memory bar.** They measure different things; see the troubleshooting note above.
6. **Trying to script htop output.** It's not designed for that. Reach for `ps`, `top -b -n 1`, `pidstat`, `procps-ng`, `prometheus-node-exporter`, or `atop -r` instead.
7. **Expecting per-process SWAP to be exact.** It isn't, on any tool. Treat `M_SWAP` / `M_PSSWP` as approximations.
8. **Running with `--drop-capabilities=strict` and being surprised that kill / renice fail.** That's the point of strict mode — read the man page section before flipping it on.

## Verification Checklist

Before considering this skill applied to a host:

- [ ] `htop --version` prints a 3.x version.
- [ ] `script -q -c 'htop -C -n 1' /dev/null` exits cleanly (sanity check that the binary runs and can paint a frame; bare `htop -n 1` fails with `Error opening terminal` in non-interactive shells).
- [ ] `htop --sort-key=help | head -20` lists the available sort columns (catches old binaries that don't support `--sort-key`).
- [ ] If you need IO columns: `getcap "$(command -v htop)"` shows `cap_sys_ptrace` and/or `cap_net_admin` — or you're prepared to run with `sudo`.
- [ ] If on a shared host: confirmed whether `--readonly` should be the default invocation.

## References

- Official site: <https://htop.dev/>
- FAQ: <https://htop.dev/faq.html>
- Source: <https://github.com/htop-dev/htop>
- Mailing list: <https://groups.io/g/htop>
- IRC: `#htop` on Libera.Chat
- `man 1 htop` — authoritative reference (the source of most of this skill)
- Companion: `pcp-htop(1)` and `pcp-htop(5)` for Performance Co-Pilot integration
- This skill's column glossary: [`references/columns.md`](references/columns.md)
