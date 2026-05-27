# htop Columns Glossary

Reference for the columns selectable in htop's setup screen (`F2` → Columns). Names match the upstream identifiers from `man 1 htop`. Where htop displays a different short label in the header, it is shown in parentheses.

A column showing `-` for every row means it is unsupported on the current platform or unimplemented.

## Identity & Lineage

| Column | Header | Meaning |
|--------|--------|---------|
| `PID` | PID | Process ID. |
| `PPID` | PPID | Parent process ID. |
| `PGRP` | PGRP | Process group ID. |
| `SESSION` | SID | Session ID. |
| `TGID` | TGID | Thread group ID. |
| `TPGID` | TPGID | PID of the foreground process group of the controlling terminal. |
| `TTY` | TTY | Controlling terminal. |
| `ST_UID` | UID | Numeric user ID of the owner. |
| `USER` | USER | Username (highlighted if process holds elevated capabilities). |

## Command Identification

| Column | Header | Meaning |
|--------|--------|---------|
| `Command` | Command | Full command line. With `m` toggled, merges `exe`, `comm`, and `cmdline`. |
| `COMM` | COMM | The `/proc/[pid]/comm` name (Linux 2.6.33+). |
| `EXE` | EXE | Basename of `/proc/[pid]/exe`. Red = executable replaced/deleted on disk; yellow = a loaded library was replaced/deleted. Needs `CAP_SYS_PTRACE`. |

## State, Priority, Time

| Column | Header | Meaning |
|--------|--------|---------|
| `STATE` | S | `S` sleeping, `I` idle, `R` running, `D` uninterruptible sleep, `Z` zombie, `T` stopped/traced, `W` paging. |
| `PRIORITY` | PRI | Kernel internal priority (usually nice + 20; different for RT). |
| `NICE` | NI | Nice value, -20 (high) to 19 (low). |
| `STARTTIME` | START | Time the process started. |
| `TIME` | TIME+ | User + system CPU time consumed (clock ticks). |
| `UTIME` | UTIME+ | User-mode CPU time. |
| `STIME` | STIME+ | Kernel-mode CPU time servicing this process. |
| `CUTIME` | CUTIME+ | User-mode CPU time of waited-for children. |
| `CSTIME` | CSTIME+ | Kernel-mode CPU time of waited-for children. |
| `MINFLT` | MINFLT | Minor (no disk read) page faults. |
| `MAJFLT` | MAJFLT | Major (disk read) page faults. |
| `CMINFLT` | CMINFLT | Minor faults of waited-for children. |
| `CMAJFLT` | CMAJFLT | Major faults of waited-for children. |
| `PROCESSOR` | CPU | The CPU core the process last ran on. |

## Memory

| Column | Header | Meaning |
|--------|--------|---------|
| `M_VIRT` | VIRT | Virtual memory size. |
| `M_RESIDENT` | RES | Resident set size — physical memory in use. |
| `M_SHARE` | SHR | Shared pages. |
| `M_TRS` | CODE | Text-segment resident size (executable instructions). |
| `M_DRS` | DATA | Data + stack resident size. |
| `M_LRS` | LIB | Library size. |
| `M_SWAP` | SWAP | Swapped-out pages (approximate; per-process swap cannot be exact). |
| `M_PSS` | PSS | Proportional set size — shared pages divided across sharers. **Best fairness metric** for shared-library-heavy workloads. |
| `M_M_PSSWP` | PSSWP | Proportional swap share for this mapping. |

## CPU Percentages

| Column | Header | Meaning |
|--------|--------|---------|
| `PERCENT_CPU` | CPU% | CPU % of one core (Linux/Irix mode). 100% = one full core. |
| `PERCENT_NORM_CPU` | NCPU% | CPU % normalized over all cores (Solaris mode). 100% = the whole machine. |
| `PERCENT_MEM` | MEM% | Resident memory as % of physical RAM. |

## I/O (Linux)

| Column | Header | Meaning |
|--------|--------|---------|
| `RCHAR` | RD_CHAR | Bytes read by the process. |
| `WCHAR` | WR_CHAR | Bytes written by the process. |
| `SYSCR` | RD_SYSC | Number of `read(2)` syscalls. |
| `SYSCW` | WR_SYSC | Number of `write(2)` syscalls. |
| `RBYTES` | IO_RBYTES | Bytes of `read(2)` I/O. |
| `WBYTES` | IO_WBYTES | Bytes of `write(2)` I/O. |
| `CNCLWB` | IO_CANCEL | Bytes of cancelled `write(2)` I/O. |
| `IO_READ_RATE` | DISK READ | Read I/O rate, bytes/sec. |
| `IO_WRITE_RATE` | DISK WRITE | Write I/O rate, bytes/sec. |
| `IO_RATE` | DISK R/W | Sum of read + write rates. |
| `IO_PRIORITY` | IO | Scheduling class: `R` realtime / `B` best-effort / `id` idle, with priority where applicable. |

I/O columns require `CAP_SYS_PTRACE` (or root) to read other users' `/proc/[pid]/io`.

## Delay Accounting (Linux, `--enable-delayacct`)

These three are the **diagnostic gold mine** for "why is this thing slow even though CPU is idle":

| Column | Header | Meaning |
|--------|--------|---------|
| `PERCENT_CPU_DELAY` | CPUD% | Time spent runnable but waiting for CPU. High = scheduler contention. |
| `PERCENT_IO_DELAY` | IOD% | Time blocked on synchronous block I/O. High = disk-bound. |
| `PERCENT_SWAP_DELAY` | SWAPD% | Time spent swapping pages in. High = thrashing. |

All three need `CAP_NET_ADMIN` (the kernel's taskstats interface is netlink-based).

## Threading & Containers

| Column | Header | Meaning |
|--------|--------|---------|
| `NLWP` | NLWP | Light-weight processes (threads) in this group. |
| `CTID` | CTID | OpenVZ container ID / virtual environment ID. |
| `VPID` | VPID | OpenVZ process ID. |
| `VXID` | VXID | VServer process ID. |
| `CGROUP` | CGROUP | Full cgroup path. |
| `CCGROUP` | CCGROUP | Shortened cgroup name (`/[S]` system, `/[U]` user, `/[lxc:name]`, `/[SNC:scope]` systemd-nspawn, `/[LXC:name]` LXC monitor, suffix-stripped services/scopes). |
| `AGRP` | AGRP | Autogroup identifier (Linux CFS). |
| `ANI` | ANI | Autogroup nice value. |

## Misc

| Column | Header | Meaning |
|--------|--------|---------|
| `OOM` | OOM | OOM-killer score. The kernel kills high-score processes first under memory pressure. |
| `CTXT` | CTXT | Voluntary + nonvoluntary context switches (cumulative). |

## Quick Recipes

**"Tell me which process is being denied CPU"** → add `PERCENT_CPU_DELAY` (CPUD%), sort by it.

**"Tell me which process is being denied disk"** → add `PERCENT_IO_DELAY` (IOD%), sort by it.

**"Tell me which process is bloating shared memory unfairly"** → add `M_PSS`, compare against `M_RESIDENT`.

**"Tell me which container is loud"** → add `CCGROUP`, then `F4 \[lxc:` to filter to LXC payloads only.

**"Tell me what's about to be OOM-killed"** → add `OOM`, sort descending.
