# xsyscheck

[![Fork](https://img.shields.io/badge/Fork-xsyscheck-0A66C2?logo=github)](https://github.com/leoberbert/xsyscheck) [![Upstream](https://img.shields.io/badge/Upstream-xsos-555?logo=github)](https://github.com/ryran/xsos) [![Shell](https://img.shields.io/badge/Shell-Bash-121011?logo=gnu-bash&logoColor=white)](https://www.gnu.org/software/bash/) [![Platform](https://img.shields.io/badge/Platform-Linux-FCC624?logo=linux&logoColor=black)](https://kernel.org) [![License: GPLv3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

`xsyscheck` is an enterprise-focused fork of `xsos` for fast Linux host triage.
It inspects either a live system or an extracted sosreport and prints an operator-friendly diagnostic report.

## Repository

- Fork repository: `https://github.com/leoberbert/xsyscheck`
- Upstream project: `https://github.com/ryran/xsos`

## Credits and Lineage

This project is a fork of `xsos`, and upstream credit is preserved intentionally.

- Original project author: **Ryan Sawhill Aroha** and contributors
- Original project: `https://github.com/ryran/xsos`
- Fork maintainer: **Leonardo Berbert**

## Highlights

- Works on legacy enterprise environments (including older RHEL families).
- Single-file Bash tool, easy to copy/run in restricted systems.
- Supports both live host inspection and offline sosreport analysis.
- Adds practical production checks for:
  - inode pressure
  - LVM health
  - file descriptor exhaustion risk
  - conntrack saturation/errors
  - mdadm RAID status
  - NUMA mode/topology
  - socket state summary in `--ss`

## Execution Modes

1. **Live system mode**
- Run directly on a host.
- Some modules require root privileges.

2. **sosreport root mode**
- Point to an extracted sosreport root directory.
- `xsyscheck` reads files under that tree instead of local `/proc`, `/sys`, and commands.

3. **Special single-file mode**
- Feed specific dumps using `--B/--C/...` options.
- Useful when you only have partial artifacts.

## Usage

```bash
xsyscheck [DISPLAY OPTIONS] [-6abokcfmdtlerngispSFIN] [SOSREPORT_ROOT]
xsyscheck [DISPLAY OPTIONS] {--B|--C|--F|--M|--D|--T|--L|--R|--N|--G|--I|--P FILE}...
xsyscheck [-?|-h|--help]
```

## Content Modules

- `-a, --all`: run all modules from `XSOS_ALL_VIEW`
- `-b, --bios`: BIOS/firmware/system DMI details
- `-o, --os`: OS/kernel/boot/runtime/subscription context
- `-k, --kdump`: kdump configuration and readiness checks
- `-c, --cpu`: CPU topology/model/flags summary
- `-f, --intrupt`: `/proc/interrupts` activity view
- `-m, --mem`: memory summary with hugepages/THP insights
- `--numa`: NUMA mode, node topology, and `kernel.numa_balancing`
- `-d, --disks`: storage summary (`/proc/partitions`, lsblk, df, multipath context)
- `--inodes`: inode usage summary and risk per mountpoint
- `--lvm`: PV/VG/LV summary with thin usage risk status
- `-t, --mpath`: dm-multipath details
- `-l, --lspci`: PCI summary (network/storage/VGA)
- `-e, --ethtool`: NIC link/driver/ring/error counters
- `-r, --softirq`: softnet budget/backlog checks
- `-n, --netdev`: `/proc/net/dev` RX/TX and error ratios
- `-g, --bonding`: bonding + teaming details
- `-i, --ip`: IP interface inventory (v4 or v6)
- `--net`: convenience alias for major network modules
- `-s, --sysctl`: selected kernel/network/vm sysctl report
- `--filefd`: global file descriptor usage and exhaustion risk
- `-p, --ps`: process/thread behavior and top consumers
- `-S, --ss`: socket deep check (includes connection-state summary)
- `--conntrack`: conntrack occupancy plus key error/drop counters
- `--mdadm`: software RAID state (active/degraded/syncing/inactive)
- `-F, --firewall`: firewall service/rule overview
- `-I, --ifcfg`: ifcfg summary table
- `-N, --netstat`: `/proc/net/netstat` filtered counters

## Display / Behavior Options

- `--scrub`: scrub IP/MAC/hostname/serial/proxy credentials from output
- `-6, --ipv6`: use IPv6 view in IP module
- `-q, --wwid=ID`: filter multipath output to a specific WWID/name/LUN
- `-u, --unit=P`: set unit (`b|k|m|g|t`) for memory/net byte displays
- `--threads`: make process analysis thread-aware (`ps auxm`)
- `-v, --verbose=NUM`: process module verbosity (`0..4`)
- `-w, --width=NUM|w|0`: wrap width, terminal width, or no wrapping
- `-x, --nocolor`: disable colors
- `-y, --less`: pipe output to `less -SR`
- `-z, --more`: pipe output to `more`

## Special Input Options (BASH v4+)

- `--B=FILE`: dmidecode dump
- `--C=FILE`: `/proc/cpuinfo` dump
- `--F=FILE`: `/proc/interrupts` dump
- `--M=FILE`: `/proc/meminfo` dump
- `--D=FILE`: `/proc/partitions` dump
- `--T=FILE`: `multipath -v4 -ll` dump
- `--L=FILE`: `lspci` dump
- `--R=FILE`: `/proc/net/softnet_stat` dump
- `--N=FILE`: `/proc/net/dev` dump
- `--G=FILE`: `/proc/net/bonding/*` dump
- `--I=FILE`: `ip addr` dump
- `--P=FILE`: `ps aux` dump
- `--S=FILE`: `ss -peaonmi` dump

## Environment Variables

`xsyscheck` is highly configurable via `XSOS_*` variables. Core groups:

- **Views**
  - `XSOS_DEFAULT_VIEW`
  - `XSOS_ALL_VIEW`

- **Output / formatting**
  - `XSOS_OUTPUT_HANDLER`
  - `XSOS_FOLD_WIDTH`
  - `XSOS_HEADING_SEPARATOR`
  - `XSOS_COLORS` and color-specific variables

- **Units / verbosity**
  - `XSOS_MEM_UNIT`
  - `XSOS_NET_UNIT`
  - `XSOS_PS_UNIT`
  - `XSOS_PS_LEVEL`
  - `XSOS_PS_THREADS`

- **Network / filtering**
  - `XSOS_IP_VERSION`
  - `XSOS_MULTIPATH_QUERY`
  - `XSOS_ETHTOOL_ERR_REGEX`
  - `XSOS_LSPCI_NET_REGEX`
  - `XSOS_LSPCI_STORAGE_REGEX`
  - `XSOS_SS_CHECK_FIELDS`
  - `XSOS_NETSTAT_FILTER_REGEX`
  - `XSOS_NETSTAT_HIGHLIGHT_REGEX`
  - `XSOS_NETSTAT_IGNORE_ZERO`

- **Scrubbing / privacy**
  - `XSOS_SCRUB_IP_HN`
  - `XSOS_SCRUB_MACADDR`
  - `XSOS_SCRUB_SERIAL`
  - `XSOS_SCRUB_PROXYUSERPASS`

## Examples

```bash
# Default module set
./xsyscheck

# Full report (all modules)
./xsyscheck --all

# Focused production triage bundle
./xsyscheck --numa --mem --filefd --conntrack --mdadm --ss --netstat

# Analyze an extracted sosreport
./xsyscheck --os --cpu --mem --numa --disks /path/to/sosreport-root

# Privacy-safe output for sharing
./xsyscheck --all --scrub --nocolor > triage.txt
```
<img width="792" height="751" alt="image" src="https://github.com/user-attachments/assets/8957a9dc-912a-430b-b1fe-b742f0b0420a" />

## Compatibility Notes

- BASH v4+ is required for some features (associative arrays, special options, advanced modules).
- On very old systems (for example RHEL5-era shells), behavior is reduced as expected.
- Root privileges are recommended for BIOS/multipath/ethtool completeness on live hosts.

## Maintainer

Fork maintainer: **Leonardo Berbert**

Upstream acknowledgment remains part of this fork by design.
