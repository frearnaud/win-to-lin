# NVIDIA Screen Wake-Up Issue on Linux

## Problem

After the screen goes to sleep (DPMS), the display cannot wake up. Symptoms:
- Screen stays black
- System is still running (fans spinning, keyboard LEDs work)
- Mouse/keyboard input doesn't wake the display
- Only fix is holding power button to force shutdown

This is a known issue with NVIDIA proprietary drivers on Linux, especially on Wayland.

## Affected Hardware

- NVIDIA RTX 40 series (confirmed on RTX 4070 Ti)
- Likely affects other modern NVIDIA GPUs
- More common on Wayland than X11

## Solution

### 1. Create NVIDIA Power Management Config

```bash
# For bash/zsh:
sudo tee /etc/modprobe.d/nvidia-power.conf << 'EOF'
options nvidia NVreg_PreserveVideoMemoryAllocations=1 NVreg_TemporaryFilePath=/var/tmp
EOF

# For fish:
echo 'options nvidia NVreg_PreserveVideoMemoryAllocations=1 NVreg_TemporaryFilePath=/var/tmp' | sudo tee /etc/modprobe.d/nvidia-power.conf
```

**What this does:** Tells the NVIDIA driver to preserve GPU video memory state during power transitions, allowing proper display restoration on wake.

### 2. Enable NVIDIA Suspend Services

```bash
sudo systemctl enable nvidia-suspend.service nvidia-resume.service nvidia-hibernate.service
```

**What this does:** Enables NVIDIA's services that handle saving/restoring GPU state during sleep/wake cycles.

### 3. Rebuild Initramfs

```bash
# On Arch/CachyOS:
sudo mkinitcpio -P

# On Ubuntu/Debian:
sudo update-initramfs -u

# On Fedora:
sudo dracut --force
```

### 4. Reboot

```bash
reboot
```

## Alternative: Disable Screen Sleep Entirely

If the above doesn't work, you can prevent the screen from sleeping at all.

### KDE Plasma

Go to **System Settings → Power Management → Energy Saving** and set:
- "Screen Energy Saving" to **OFF** or timeout to **Never**

Or edit `~/.config/powerdevilrc`:

```ini
[AC][Display]
DimDisplayWhenIdle=false
TurnOffDisplayWhenIdle=false
TurnOffDisplayIdleTimeoutSec=-1
DimDisplayIdleTimeoutSec=-1

[AC][SuspendAndShutdown]
AutoSuspendAction=0
```

### Nuclear Option: Mask Sleep Targets

To completely prevent any form of system sleep:

```bash
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

To undo:

```bash
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

## References

- [NVIDIA Driver Documentation - Power Management](https://download.nvidia.com/XFree86/Linux-x86_64/latest/README/powermanagement.html)
- [Arch Wiki - NVIDIA/Tips and tricks](https://wiki.archlinux.org/title/NVIDIA/Tips_and_tricks#Preserve_video_memory_after_suspend)
