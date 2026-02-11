# CachyOS Configuration Notes

Documentation for hardware setup, system configuration, and software solutions for migrating from Windows 11 to CachyOS Linux.

---

## 📁 System

### [Boot Partition Space Management](system/boot-partition-space-management.md)
Diagnosing and resolving boot partition space issues caused by limine-snapper-sync storing historical kernel/initramfs snapshots. Covers reducing `MAX_SNAPSHOT_ENTRIES`, managing snapshot history, and preventing future space problems.

**Key topics:** Boot partition cleanup, limine-snapper-sync configuration, snapshot management

---

## 🔧 Hardware

### [Focusrite Scarlett 2i2 Audio Setup](hardware/focusrite-audio-setup.md)
Complete guide for routing audio from a Focusrite Scarlett 2i2 (with Quad Cortex input) through PipeWire on Linux. Includes low-latency configuration, monitoring setup with qpwgraph, and buffer size tuning for guitar recording.

**Key topics:** PipeWire, qpwgraph, low-latency audio, USB audio interface

### [NVIDIA DisplayPort Hotplug Issue](hardware/nvidia-dp-hotplug-issue.md)
Troubleshooting DisplayPort hotplug detection failure when OLED monitor turns off/on. Documents the issue where NVIDIA+Wayland fails to re-detect displays after OLED burn-in protection power cycles.

**Key topics:** NVIDIA Wayland issues, DisplayPort hotplug, OLED monitor compatibility, HDR troubleshooting

### [NVIDIA Screen Wake-Up Issue](hardware/nvidia-screen-wake-issue.md)
Fix for black screen after waking from DPMS sleep with NVIDIA GPUs on Wayland. Solution involves configuring `NVreg_PreserveVideoMemoryAllocations` and enabling NVIDIA suspend services.

**Key topics:** NVIDIA power management, suspend/resume, DPMS, video memory preservation

---

## 🖥️ Desktop

### [Niri Setup Guide](desktop/niri-setup.md)
Installing and configuring Niri (scrollable-tiling Wayland compositor) alongside KDE Plasma. Covers session switching, essential companion apps (waybar, rofi, mako), keybindings, and auto-start configuration.

**Key topics:** Niri compositor, Wayland, tiling window manager, KDE Plasma coexistence

---

## 🔄 Virtualization

### [Waydroid Setup Guide](virtualization/waydroid-guide.md)
Running Android apps on CachyOS with Waydroid. Includes UFW firewall configuration, ARM translation setup for x86 systems, Google Play certification, and networking troubleshooting. Documents NVIDIA GPU limitations (software rendering only).

**Key topics:** Waydroid, Android containerization, ARM translation, Google Play certification, UFW firewall

### [Windows VM with USB Passthrough](virtualization/windows-vm-usb-passthrough.md)
Creating a Windows 11 VM using virt-manager/QEMU with USB passthrough for the Neural DSP Quad Cortex. Covers UEFI setup, TPM emulation for Windows 11 requirements, and persistent USB device attachment.

**Key topics:** QEMU, virt-manager, USB passthrough, Windows 11 VM, UEFI/TPM setup

---

## 📋 Quick Reference

| Category | Files |
|----------|-------|
| **System** | 1 guide |
| **Hardware** | 3 guides |
| **Desktop** | 1 guide |
| **Virtualization** | 2 guides |
| **Total** | 7 guides |

---

## 🔍 Finding Information

### By Component
- **Audio:** [Focusrite setup](hardware/focusrite-audio-setup.md)
- **Graphics:** [NVIDIA hotplug issue](hardware/nvidia-dp-hotplug-issue.md), [NVIDIA wake issue](hardware/nvidia-screen-wake-issue.md)
- **Android:** [Waydroid guide](virtualization/waydroid-guide.md)
- **Windows:** [Windows VM guide](virtualization/windows-vm-usb-passthrough.md)
- **Storage:** [Boot partition management](system/boot-partition-space-management.md)
- **Window Manager:** [Niri setup](desktop/niri-setup.md)

### By Problem
- **Boot partition full?** → [Boot partition management](system/boot-partition-space-management.md)
- **Screen won't wake?** → [NVIDIA wake issue](hardware/nvidia-screen-wake-issue.md)
- **Monitor doesn't reconnect?** → [NVIDIA hotplug issue](hardware/nvidia-dp-hotplug-issue.md)
- **Need Android apps?** → [Waydroid guide](virtualization/waydroid-guide.md)
- **Need Windows software?** → [Windows VM guide](virtualization/windows-vm-usb-passthrough.md)
- **Guitar monitoring setup?** → [Focusrite setup](hardware/focusrite-audio-setup.md)
- **Want tiling WM?** → [Niri setup](desktop/niri-setup.md)

---

## 📝 Note Format

All guides follow a consistent structure:
- **Problem description** or purpose
- **Prerequisites** and hardware/software requirements
- **Step-by-step solutions** with fish shell commands
- **Configuration details** with explanations
- **Troubleshooting** common issues
- **References** to official documentation

Commands are written for **fish shell** (CachyOS default). Bash/zsh users may need to adapt syntax.
