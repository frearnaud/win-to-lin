# Waydroid Setup Guide

Guide for running Android apps on CachyOS with Waydroid.

## Initial Setup

Waydroid was initialized with Google Apps (GAPPS):
```fish
sudo waydroid init -s GAPPS
```

## UFW Firewall Fix (Required)

By default, UFW blocks Waydroid networking. Run these once to fix:

```fish
sudo ufw allow 67    # DHCP - allows container to get IP address
sudo ufw allow 53    # DNS - allows domain name resolution
sudo ufw default allow FORWARD  # allows packet forwarding between networks
```

Also add a NAT rule (needed after each reboot, or make persistent - see below):
```fish
sudo iptables -t nat -A POSTROUTING -s 192.168.240.0/24 -o enp4s0 -j MASQUERADE
```

### Making NAT Rule Persistent

To avoid running the iptables command after each reboot:
```fish
sudo pacman -S iptables-persistent
# Or add the command to /etc/rc.local or a systemd service
```

## Starting Waydroid

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

## Window Sizing Options

**Set fixed dimensions** (phone-like):
```fish
waydroid prop set persist.waydroid.width 720
waydroid prop set persist.waydroid.height 1280
waydroid session stop
waydroid session start
waydroid show-full-ui
```

**Enable multi-window mode** (resizable window):
```fish
waydroid prop set persist.waydroid.multi_windows true
```

## NVIDIA GPU Limitations

Waydroid uses **software rendering** on NVIDIA GPUs (no GPU acceleration). This is a Mesa/NVIDIA driver limitation. Games may:
- Have brief loading screen freezes (just wait)
- Crash occasionally
- Depend on CPU performance

## ARM App Support

Many Android apps are ARM-only. For x86 systems, install ARM translation:
```fish
# Check the Arch Wiki for current instructions:
# https://wiki.archlinux.org/title/Waydroid#ARM_translation
```

## Useful Commands

| Command | Description |
|---------|-------------|
| `waydroid status` | Check session and container status |
| `waydroid app install /path/to.apk` | Install APK directly |
| `waydroid app list` | List installed apps |
| `sudo waydroid shell` | Access Android shell |
| `waydroid log` | View Waydroid logs |

## Troubleshooting Network Issues

If networking stops working, check:

1. **Container has IP**: `sudo waydroid shell -- ip addr show eth0`
   - Should show `inet 192.168.240.x/24`

2. **DHCP server running**: `ps aux | grep "dnsmasq.*waydroid"`

3. **NAT rule exists**: `sudo iptables -t nat -L POSTROUTING -n`
   - Should show MASQUERADE rule for 192.168.240.0/24

4. **Forwarding allowed**: `sudo iptables -L FORWARD -n`
   - Policy should be ACCEPT or have allow rules for waydroid0

## Resources

- ArchWiki: https://wiki.archlinux.org/title/Waydroid
- Waydroid docs: https://docs.waydro.id/
