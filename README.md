# 🚀 Linux Daemon Llano Kernel Builder

Custom high-performance Linux Kernel compilation pipeline built specifically for **AMD A4-3330MX APU (K10.5 Llano)** and **Radeon HD 6480G** with **BORE CPU Scheduler** on Arch Linux.

---

## ⚙️ Hardware-Tuned Specs:
* **Target ISA**: `-march=amdfam10 -O2` (SSE4a, Popcnt, ABM, 3DNow! instructions enabled).
* **CPU Scheduler**: **BORE (Burst-Oriented Response Enhancer)** for silky smooth desktop interactivity on 2 cores.
* **Preemption**: Desktop Low-Latency (`CONFIG_PREEMPT=y`).
* **Timer**: 1000 Hz (`CONFIG_HZ_1000=y`).
* **Memory Optimizer**: LZ4 ZSWAP enabled by default for 3.3 GB RAM systems.

---

## ⚡ 1-Click Install Command on Arch Linux:
```bash
sudo pacman -U https://github.com/liebertjohan-art/daemon-kernel-builder/releases/download/v6.12.16-daemon-llano/linux-daemon-llano-6.12.16-1-x86_64.pkg.tar.zst \
                https://github.com/liebertjohan-art/daemon-kernel-builder/releases/download/v6.12.16-daemon-llano/linux-daemon-llano-headers-6.12.16-1-x86_64.pkg.tar.zst
```
