# NVIDIA DisplayPort Hotplug Issue (OLED Monitor)

## Problem

When the OLED monitor turns off (either manually or via automatic OLED protection), the display cannot be restored. The screen stays black even though the system is running.

**This is different from the suspend/wake issue** in `nvidia-screen-wake-issue.md`. This is a DisplayPort hotplug detection problem.

## Hardware Setup

- **GPU**: NVIDIA RTX 4070 Ti
- **Driver**: 590.48.01
- **Monitor**: 3440x1440 ultrawide OLED @ 175Hz
- **Connection**: DisplayPort (DP-3)
- **HDR**: Enabled
- **Session**: Wayland (KDE Plasma)

## Root Cause

When an OLED monitor turns off (for burn-in protection or manual power off), the DisplayPort connection drops entirely. When the monitor turns back on, NVIDIA + KWin on Wayland often fail to properly re-detect and reinitialize the display.

This is a known issue with NVIDIA proprietary drivers on Wayland.

## What We've Verified

- `NVreg_PreserveVideoMemoryAllocations=1` is set (CachyOS default) - not relevant to this issue
- `nvidia-suspend.service`, `nvidia-resume.service`, `nvidia-hibernate.service` are enabled - not relevant to this issue
- Initramfs was rebuilt after config changes

## Solutions to Try

### 1. Disable HDR (Testing)
HDR can cause additional hotplug issues. Test with HDR disabled:
**System Settings → Display and Monitor → HDR → Disable**

### 2. Switch TTY to Force Re-detection
When screen is black after monitor wakes:
- Press **Ctrl+Alt+F2** (switch to TTY2)
- Then **Ctrl+Alt+F1** (switch back to Wayland)
This may force display reinitialization.

### 3. Kernel Parameters (If HDR fix doesn't work)
Try adding to kernel command line:
```
nvidia-drm.modeset=1 nvidia-drm.fbdev=1
```

### 4. Check for KWin/Plasma Updates
This may be fixed in newer KDE Plasma versions. Check:
- https://bugs.kde.org (search for "NVIDIA hotplug" or "DisplayPort hotplug")
- https://forums.developer.nvidia.com

### 5. Alternative: X11 Session
As a last resort, X11 handles DisplayPort hotplug more reliably than Wayland with NVIDIA. Could test with an X11 session to confirm.

## Why DPMS Isn't an Option

OLED monitors need to fully turn off pixels to prevent burn-in. The monitor's automatic protection feature cuts the DisplayPort signal, which is the correct behavior for OLED longevity. Using software DPMS (screen blanking) would keep the connection alive but wouldn't provide the same OLED protection.

## Status

**Current**: Testing HDR disabled to see if it resolves the hotplug detection issue.

## References

- [NVIDIA Linux Forums](https://forums.developer.nvidia.com/c/gpu-graphics/linux/)
- [KDE Bug Tracker](https://bugs.kde.org)
- Related file: `nvidia-screen-wake-issue.md` (different issue - suspend/wake)
