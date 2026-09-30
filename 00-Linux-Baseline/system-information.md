# System Information Baseline

## Endpoint Identity

| Field | Value |
|---|---|
| Hostname | `soc-linux` |
| Endpoint IP | `192.168.1.16` |
| Endpoint Role | Linux SOC Monitoring Endpoint |
| SIEM Hostname | `elastic-siem` |
| SIEM IP | `192.168.1.11` |
| Virtualization | VMware |
| Hardware Vendor | VMware, Inc. |
| Hardware Model | `VMware20,1` |
| Chassis | Virtual Machine |

## Operating System

| Field | Value |
|---|---|
| Operating System | Ubuntu 24.04.4 LTS |
| Distribution | Ubuntu |
| Version ID | `24.04` |
| Codename | `Noble Numbat` |
| Kernel | `6.8.0-142-generic` |
| Architecture | `arm64 / aarch64` |
| CPU Operating Mode | 64-bit |
| Byte Order | Little Endian |

## Virtualization and Firmware

| Field | Value |
|---|---|
| Virtualization Platform | VMware |
| Hardware Vendor | VMware, Inc. |
| Hardware Model | `VMware20,1` |
| Firmware Version | `VMW201.00V.25275966.BA64.2603102050` |
| Firmware Date | `2026-03-10` |

## CPU

| Field | Value |
|---|---|
| CPU Architecture | `aarch64` |
| CPU Count | `4` |
| Online CPU List | `0-3` |
| Vendor ID | `Apple` |
| Model Name | Not reported |
| CPU Model | `0` |
| Threads per Core | `1` |
| Cores per Socket | `4` |
| Sockets | `1` |
| NUMA Nodes | `1` |
| NUMA Node 0 CPUs | `0-3` |
| Stepping | `0x0` |
| BogoMIPS | `48.00` |

## CPU Security Features

| Vulnerability | Status |
|---|---|
| Gather Data Sampling | Not affected |
| Indirect Target Selection | Not affected |
| ITLB Multihit | Not affected |
| L1TF | Not affected |
| MDS | Not affected |
| Meltdown | Not affected |
| MMIO Stale Data | Not affected |
| Retbleed | Not affected |
| Reg File Data Sampling | Not affected |
| Spectre Rstack Overflow | Not affected |
| Spectre Store Bypass | Vulnerable |
| Spectre v1 | Mitigation: `__user pointer sanitization` |
| Spectre v2 | Mitigation: `CSV2, but not BHB` |
| SRBDS | Not affected |
| TSA | Not affected |
| TSX Async Abort | Not affected |
| VMSCAPE | Not affected |

## CPU Flags

```text
fp
asimd
evtstrm
aes
pmull
sha1
sha2
crc32
atomics
fphp
asimdhp
cpuid
asimdrdm
jscvt
fcma
lrcpc
dcpop
sha3
asimddp
sha512
asimdf
hm
dit
uscat
ilrcpc
flagm
sb
paca
pacg
dcpodp
flagm2
frint
```

## Memory

| Resource |   Total |    Used |    Free |  Shared | Buff/Cache | Available |
| -------- | ------: | ------: | ------: | ------: | ---------: | --------: |
| RAM      | 3.8 GiB | 665 MiB | 126 MiB | 1.2 MiB |    3.2 GiB |   3.2 GiB |
| Swap     | 3.8 GiB | 256 KiB | 3.8 GiB |       - |          - |   3.8 GiB |

## Storage

| Filesystem                          | Type     |    Size |    Used | Available | Usage | Mount Point                 |
| ----------------------------------- | -------- | ------: | ------: | --------: | ----: | --------------------------- |
| `/dev/mapper/ubuntu--vg-ubuntu--lv` | ext4     |  33 GiB | 9.8 GiB |    22 GiB |   32% | `/`                         |
| `/dev/nvme0n1p2`                    | ext4     | 2.0 GiB | 198 MiB |   1.6 GiB |   11% | `/boot`                     |
| `/dev/nvme0n1p1`                    | vfat     | 1.1 GiB | 6.4 MiB |   1.1 GiB |    1% | `/boot/efi`                 |
| `tmpfs`                             | tmpfs    | 392 MiB | 1.3 MiB |   390 MiB |    1% | `/run`                      |
| `tmpfs`                             | tmpfs    | 2.0 GiB |       0 |   2.0 GiB |    0% | `/dev/shm`                  |
| `tmpfs`                             | tmpfs    | 5.0 MiB |       0 |   5.0 MiB |    0% | `/run/lock`                 |
| `tmpfs`                             | tmpfs    | 392 MiB |  12 KiB |   392 MiB |    1% | `/run/user/1000`            |
| `efivarfs`                          | efivarfs | 256 KiB |  34 KiB |   223 KiB |   14% | `/sys/firmware/efi/efivars` |

## Runtime State

| Field                | Value                |
| -------------------- | -------------------- |
| Uptime at Collection | `3 hours 47 minutes` |
| Logged-in Users      | `3`                  |
| Load Average         | `0.07, 0.02, 0.00`   |

## Kernel Information

```text
Linux soc-linux 6.8.0-142-generic #142-Ubuntu SMP PREEMPT_DYNAMIC
Wed Sep 2 14:30:29 UTC 2026
aarch64 aarch64 aarch64 GNU/Linux
```

## System Monitoring Context

```text
                 CatchMe Linux SOC

                 elastic-siem
                 192.168.1.11
                       |
                 Elastic / Fleet
                       |
                 Elastic Agent
                       |
                       v
                   soc-linux
                 192.168.1.16
                       |
          +------------+------------+
          |            |            |
       System        Auditd       SSH/Auth
        Logs         Events        Logs
```
