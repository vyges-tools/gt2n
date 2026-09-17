# Vyges mirror notes

This repository is the Vyges mirror of [azadnaeemi/GT2N](https://github.com/azadnaeemi/GT2N) —
GT2N, the open-source 2nm GAA nanosheet PDK from Prof. Azad Naeemi's group at Georgia Tech.
Upstream content is unmodified. Everything Vyges adds is listed below.

## The pin is a commit, not a release

Upstream carries **no git tags**, so there is no release to pin to. The descriptor's
`upstream.ref` is therefore `main@<commit>` — the branch records where the pin came from,
the commit **is** the pin. Hand git the commit; `main@<commit>` is not a git revision.

`versions[]` / `latest` in `vyges-tools/pdk-catalog` stay **hand-maintained** for this entry:
`gen_index.py` derives versions from a mirror's tags, and tags that do not exist cannot
describe the tree being served.

## What is in the tree

| path | what it is |
| --- | --- |
| `techlib/gt2_tech.lef` | technology LEF with **BPR + the backside stack** (BPR/BV0/BM1..BM4/BRDL) over frontside M0..M6 |
| `techlib/gt2_fspdn_tech.lef` | the **frontside-PDN alternative** of the same process — not extra layers of one stack; pick one per design |
| `lib/tt/`, `lef/tt/`, `gds/`, `cdl/` | 10 std-cell libraries = 2 sheet widths (13/31 nm) x 5 Vt (HVT>SVT>LVT>ULVT>ELVT), ~69 characterized cells on a 6-track 0.144 um row (`SITE gt2_6t`) |
| `device/tt/` | thermal-calibrated 3-stack nanosheet model cards (NFIN=3, Lg 14 nm), **per width/Vt as well as per corner** |
| `icv_runset/` | DRC + LVS runsets for **Synopsys IC Validator** |
| `qrc/`, `nxtgrd/` | parasitic tech for **QRC** (`.ict`/`.tch`) and **StarRC** (`.itf`/`.nxtgrd`) |
| `std_cells/` | Custom Compiler layouts/schematics/abstracts (OpenAccess) |
| `Openroad_example/` | upstream's OpenROAD run-script example (tarball) |

⚠️ **One corner only.** The initial release characterizes `tt` at 0.7 V / 25 C. Upstream's
README says 3 process corners are under development. No behavioral Verilog, and no SRAM —
a compiler/library is announced for a future release.

⚠️ **`qrc/GT2_qrc.tch` is 62 MB**, over GitHub's recommended 50 MB (under the 100 MB hard
limit). Pushes warn; nothing breaks. Do **not** convert it to LFS — a mirror that needs
`git lfs` to check out is a mirror that fails closed on machines that do not have it.

## What Vyges adds

| path | what it is |
| --- | --- |
| `gt2n.vyges-pdk.json` | PDK descriptor, pinned to this tree — the copy of record is `vyges-tools/pdk-catalog` |
| `upstream.yaml` | upstream tracking manifest |
| `.github/workflows/upstream-sync.yml` | daily merge of `upstream/main` |
| `VYGES-MIRROR.md` | this file |

## `.github/workflows/` is pinned, deliberately

The sync job makes this directory byte-identical to what is already on `origin/main` after
every sync, so its push never contains a workflow diff. **`GITHUB_TOKEN` may not create,
update or delete a workflow file**, so a sync that carried an upstream CI change would be
rejected outright. Upstream ships no `.github/` today; this pin is what keeps that from
becoming a silent sync failure the day it does.

A merge conflict fails the run **red** rather than leaving the mirror stale behind a green
badge.
