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

Many Android apps are ARM-only. For x86 systems, install ARM translation using waydroid_script:

```fish
# Clone the helper script
git clone https://github.com/casualsnek/waydroid_script
cd waydroid_script

# Set up Python venv and install dependencies
python -m venv venv
source venv/bin/activate.fish
pip install -r requirements.txt

# Install libndk ARM translation (recommended for games)
sudo venv/bin/python main.py install libndk
```

After installing, restart Waydroid:
```fish
waydroid session stop
sudo systemctl restart waydroid-container
waydroid session start
```

## Google Play Device Certification

Some apps show "this app won't work for your device" because Waydroid isn't certified by Google. To fix:

**1. Get your Android ID:**
```fish
sudo waydroid shell -- sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = 'android_id';"
```
This outputs something like `android_id|1234567890123456789`. Copy just the number.

**2. Register the device:**
- Go to https://www.google.com/android/uncertified/
- Sign in with the same Google account used in Waydroid
- Paste the Android ID number and click Register

**3. Clear Play Store data and restart:**
```fish
sudo waydroid shell -- pm clear com.android.vending
waydroid session stop
waydroid session start
waydroid show-full-ui
```

Sign back into Google Play. Certification can take 10-20 minutes to propagate.

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
