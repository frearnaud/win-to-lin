# Boot Partition Space Management

Managing boot partition space on CachyOS with limine-snapper-sync for snapshot rollback capability.

## The Problem

CachyOS uses `limine-snapper-sync` to create bootable snapshot entries in the Limine bootloader. Each snapshot stores historical kernel and initramfs files in `/boot/.../limine_history/` for rollback purposes. Over time, these can fill up the boot partition.

### Warning Signs

You'll receive a notification from `limine-snapper-notify` when the boot partition approaches capacity:

```
Stopping snapshot boot entry creation: Boot partition usage will increase by XXX MiB
from XX.X% to XX.X% and will exceed the 85.0% limit.
```

This means:
- System updates will still work
- New snapshot boot entries won't be created (no rollback capability for new updates)
- Boot partition is at risk of filling completely

## Diagnosing Boot Partition Usage

### Check Boot Partition Size

```fish
df -h /boot
```

Typical CachyOS boot partition: **2 GB** (can be tight with multiple kernels + snapshots)

### See What's Using Space

```fish
sudo du -ah /boot/ | sort -h | tail -20
```

Look for the `limine_history` directory - this is usually the largest consumer.

### Understanding limine_history

The `limine_history` directory stores:
- **Kernel images** (`vmlinuz-*`) - ~15-17 MB each
- **Initramfs images** (`initramfs-*`) - **150-220 MB each** (the main culprit)
- Multiple versions for each kernel you have installed

**Example:** With 2 kernels (latest + LTS) and 8 snapshot entries, you might store:
- 3-4 versions of each kernel = 6-8 kernel images (~120 MB)
- 3-4 versions of each initramfs = 6-8 initramfs files (~1.2-1.6 GB)
- **Total: ~1.2-1.7 GB just for snapshot history**

## Solution 1: Reduce Snapshot Entries (Recommended)

The most effective solution is to reduce how many snapshot boot entries are kept.

### Edit Configuration

```fish
sudo nano /etc/limine-snapper-sync.conf
```

Find and change:
```
MAX_SNAPSHOT_ENTRIES=8
```

To:
```
MAX_SNAPSHOT_ENTRIES=3
```

**Recommendation:** 3 entries is plenty for rollback protection. Most people never need more than 1-2.

### Apply Changes

```fish
sudo limine-snapper-sync
```

This will:
1. Remove old snapshot boot entries
2. Delete unused historical kernel/initramfs files
3. Free 400-600 MB of space (depending on how many kernels you have)

### Verify

```fish
df -h /boot
```

You should now be well under the 85% threshold.

## Solution 2: Remove Unused Kernel Versions

If you have both latest and LTS kernels but only use one, removing the unused kernel frees significant space.

### Check Installed Kernels

```fish
pacman -Q | grep linux-cachyos
```

You might see:
- `linux-cachyos` - Latest kernel (bleeding edge)
- `linux-cachyos-lts` - Long Term Support kernel (more stable)

### Remove LTS Kernel (If Unused)

**Only do this if you're comfortable using just the latest kernel:**

```fish
sudo pacman -Rns linux-cachyos-lts linux-cachyos-lts-headers linux-cachyos-lts-nvidia-open
```

This frees ~500 MB from `limine_history`.

### Or Remove Latest Kernel (If You Prefer LTS)

```fish
sudo pacman -Rns linux-cachyos linux-cachyos-headers linux-cachyos-nvidia-open
```

**Warning:** Always keep at least one bootable kernel!

## Solution 3: Delete Old Snapshots

Reducing snapper snapshots also reduces boot partition usage.

### List Snapshots

```fish
sudo snapper -c root list
```

### Delete Old Snapshots

Keep only recent ones (e.g., delete everything before snapshot #70):

```fish
sudo snapper -c root delete 1-69
```

### Sync to Update Boot Entries

```fish
sudo limine-snapper-sync
```

**Note:** This only helps if you also reduce `MAX_SNAPSHOT_ENTRIES` - otherwise, the system will keep historical kernels for the remaining snapshots.

## Understanding the Configuration

Key settings in `/etc/limine-snapper-sync.conf`:

```bash
# Maximum snapshot entries in boot menu
MAX_SNAPSHOT_ENTRIES=3

# Boot partition usage limit (as percentage)
LIMIT_USAGE_PERCENT=85

# How snapshots appear in boot menu
SNAPSHOT_FORMAT_CHOICE=2

# Restore method for your Btrfs layout
RESTORE_METHOD=replace
```

## Prevention Tips

### Keep Boot Partition Healthy

- **Monitor regularly:** Run `df -h /boot` occasionally
- **Lean toward fewer snapshots:** 2-3 entries is usually sufficient
- **Consider kernel needs:** Do you really need both latest + LTS?
- **Run cleanup after major updates:** `sudo limine-snapper-sync` to prune old history

### If Space Issues Persist

1. **Further reduce MAX_SNAPSHOT_ENTRIES to 2**
2. **Increase boot partition size** (requires repartitioning - advanced)
3. **Disable snapshot boot entries entirely** (loses rollback capability - not recommended)

## Related Commands

```fish
# Check boot partition usage
df -h /boot

# Manually trigger snapshot sync and cleanup
sudo limine-snapper-sync

# List snapshot entries info
sudo limine-snapper-info

# View current snapshot entries
sudo limine-snapper-list

# Clean old package cache (different location, but helpful for overall space)
sudo paccache -r
```

## Why This Matters

A full boot partition can:
- **Prevent kernel updates** from installing
- **Block snapshot creation** (lose rollback protection)
- **Potentially prevent booting** if critical files can't be written

Keeping it below 70% usage provides a comfortable buffer for updates.

## Typical Boot Partition Usage Breakdown

On a healthy 2 GB boot partition:

| Item | Size | Notes |
|------|------|-------|
| Current kernels (2) | ~400 MB | vmlinuz + initramfs for latest and LTS |
| limine_history (3 entries) | ~600 MB | Historical versions for rollback |
| Intel microcode | ~15 MB | CPU firmware updates |
| Limine bootloader | ~1 MB | Boot splash, configs, binaries |
| **Total** | **~1.0 GB** | **~50-60% usage** ✅ |

With 8 snapshot entries, limine_history grows to ~1.2-1.6 GB, pushing total usage to 80-90%.
