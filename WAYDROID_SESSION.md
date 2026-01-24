# Waydroid Setup Session - Reminder

## Goal
Set up Waydroid to play **Dragon Ball Z Dokkan Battle** on CachyOS with NVIDIA GPU.

## Where We Left Off
- Waydroid init completed (with GAPPS)
- Container service started
- Session started successfully
- UI is working (shows Android screen in fullscreen)
- Discussed window resizing options (not yet applied)

## Important Context (NVIDIA)
- Waydroid uses **software rendering** on NVIDIA (no GPU acceleration)
- This is a Mesa/NVIDIA driver limitation, not fixable
- Dokkan Battle reportedly works despite this, with minor issues:
  - Loading screens may freeze briefly (just wait, game continues)
  - Occasional crashes possible
  - Performance depends on CPU strength

## Next Steps

1. **Resize window** (optional - if fullscreen is annoying):
   ```fish
   waydroid prop set persist.waydroid.width 720
   waydroid prop set persist.waydroid.height 1280
   waydroid session stop
   waydroid session start
   waydroid show-full-ui
   ```
   Or enable multi-window mode:
   ```fish
   waydroid prop set persist.waydroid.multi_windows true
   ```

2. **Sign into Google Play Store** and install Dokkan Battle

3. **If ARM translation is needed** (Dokkan is ARM-only):
   ```fish
   # Install libhoudini for ARM app support (recommended for Intel CPUs)
   # Or libndk (recommended for AMD CPUs)
   # Check: https://wiki.archlinux.org/title/Waydroid#ARM_translation
   ```

4. **Test Dokkan Battle** and troubleshoot any issues

## Starting Waydroid (Quick Reference)
```fish
sudo systemctl start waydroid-container
waydroid session start
waydroid show-full-ui
```

## Stopping Waydroid
```fish
waydroid session stop
sudo systemctl stop waydroid-container
```

## Useful Commands
- Check Waydroid status: `waydroid status`
- Stop session: `waydroid session stop`
- Install APK directly: `waydroid app install /path/to/app.apk`
- List installed apps: `waydroid app list`

## Resources
- ArchWiki: https://wiki.archlinux.org/title/Waydroid
- Waydroid docs: https://docs.waydro.id/
