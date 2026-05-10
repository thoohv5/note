---
title: CPU架构
date: 2026-04-07
tags:
  - 基础设施
  - Linux
type: note
status: complete
---

## CPU架构

## 分类

### ARM架构

### aarch64（或称为arm64）

- `aarch64` 是 ARM 64 位架构的官方术语，由 ARM 公司推出。"AArch64" 一词强调 ARM 架构的 64 位版本。在这个架构中，寄存器宽度和指令集都扩展到了 64 位。
- `arm64` 是通用术语，用于表示 ARM 64 位指令集。这个术语更加简洁，被广泛使用，尤其是在开发者和用户社区中。
- "arm64" 这个术语常常用于描述在 Linux 操作系统上运行的 ARM 64 位架构。

### arm6/arm7

- `arm` 是一种 RISC（Reduced Instruction Set Computing）架构，广泛应用于移动设备、嵌入式系统和其他领域。ARM 架构的不同版本用于不同的用途。
- `arm6` 和 `arm7` 是 ARMv6 和 ARMv7 指令集的版本，分别属于 ARM 公司的早期架构。这些版本的 ARM 架构通常用于低功耗和嵌入式系统，例如早期的智能手机和嵌入式设备。

### X86架构

### x86（或称为 386）

- `x86` 是一种基于 Intel 的 32 位指令集架构。最初，Intel 的 80386 处理器引入了这个架构，因此它也被称为 "386"。后来，`x86` 成为个人计算机（PC）领域中最为流行的 CPU 架构，支持许多操作系统和软件。

### x64（或称为 x86_64）

- `x64` 是 `x86_64` 的简写，用于表示 64 位 x86 指令集。这是一个通用的术语，表示支持 64 位计算的 x86 架构。
- `x86_64` 是对 64 位 x86 指令集架构的一种通用命名。这种架构最初由 Intel 和 AMD 开发，并在市场上广泛使用。
- `x86` 表示原始的 32 位 x86 指令集（如 80386、80486 等），而 `x86_64` 表示对该指令集的扩展，使其支持 64 位操作系统和应用程序。

### amd64

`amd64` 是由 AMD（Advanced Micro Devices）命名的 64 位 x86 指令集。虽然最初是由 AMD 开发，但由于其广泛的采用，它成为了 x86_64 架构的一种通用称呼。

### MIPS架构

mips

### PPC64架构

IBM PowerPC 64-bit 架构，它是一种用于 64 位 PowerPC 处理器的架构。PowerPC 是由 IBM、Apple 和 Motorola 共同开发的一种处理器架构，广泛用于服务器、工作站和嵌入式系统。PowerPC 处理器在过去曾经是 Apple Macintosh 计算机的主要架构，后来转向了基于 Intel 架构的处理器。

在 Linux 中，`ppc64` 和 `ppc64le` 分别表示大端和小端的 PowerPC 64 位系统，因为 PowerPC 架构可以以不同的字节顺序运行。

## 命令

### 查询系统CPU机构

```bash
Arch命令

[root@localhost ～]# arch
x86_64

```

```bash
uname -a命令

[root@localhost ～]# uname -a
Linux localhost.localdomain 3.10.0-1127.el7.x86_64 #1 SMP Tue Mar 31 23:36:51 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

```

```bash
lscpu

[root@localhost ～]# lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    2
Socket(s):             2
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 63
Model name:            Intel(R) Xeon(R) CPU E5-2678 v3 @ 2.50GHz
Stepping:              2
CPU MHz:               2499.998
BogoMIPS:              4999.99
Hypervisor vendor:     VMware
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              30720K
NUMA node0 CPU(s):     0-3
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts mmx fxsr sse sse2 ss ht syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts nopl xtopology tsc_reliable nonstop_tsc aperfmperf eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm epb invpcid_single fsgsbase tsc_adjust bmi1 avx2 smep bmi2 invpcid xsaveopt dtherm ida arat pln pts

```