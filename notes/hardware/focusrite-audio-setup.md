# Focusrite Scarlett 2i2 Audio Setup on Linux

Guide for monitoring guitar through the Focusrite Scarlett 2i2 while also hearing computer audio.

## Hardware Setup

- Focusrite Scarlett 2i2 (2nd Gen) connected via USB
- Neural DSP Quad Cortex → Focusrite inputs 1 & 2
- Headphones → Focusrite headphone output

## Prerequisites

PipeWire is the audio system on CachyOS. Install the graphical patchbay:

```fish
sudo pacman -S qpwgraph
```

## Audio Routing with qpwgraph

1. Launch `qpwgraph`
2. Set Focusrite as default output (so computer audio goes there):
   ```fish
   wpctl set-default (wpctl status | grep -i "focusrite.*sink" | grep -oP '\d+' | head -1)
   ```
   Or find the sink ID manually with `wpctl status` and run `wpctl set-default <ID>`

3. In qpwgraph, connect:
   - **Scarlett 2i2 capture_FL** → **Scarlett 2i2 playback_FL** (left channel)
   - **Scarlett 2i2 capture_FR** → **Scarlett 2i2 playback_FR** (right channel)

This creates a monitoring loopback - you hear your guitar input through the headphones.

## Low-Latency Configuration

Default PipeWire buffer (1024 samples) adds ~21ms latency. For guitar monitoring, lower is better.

### Latency Reference

| Quantum (samples) | Latency @ 48kHz |
|-------------------|-----------------|
| 1024              | ~21ms           |
| 512               | ~10.7ms         |
| 256               | ~5.3ms          |
| 128               | ~2.7ms          |
| 64                | ~1.3ms          |

### Persistent Configuration

Config file: `~/.config/pipewire/pipewire.conf.d/low-latency.conf`

```
context.properties = {
    default.clock.quantum = 128
    default.clock.min-quantum = 64
    default.clock.max-quantum = 1024
}
```

Apply changes:
```fish
systemctl --user restart pipewire pipewire-pulse wireplumber
```

### Change Buffer Size On-the-Fly

```fish
# Set to 256 samples
pw-metadata -n settings 0 clock.force-quantum 256

# Reset to default (let PipeWire decide)
pw-metadata -n settings 0 clock.force-quantum 0
```

### Check Current Settings

```fish
pw-metadata -n settings 0 | grep -E 'clock.quantum|clock.rate'
```

## Troubleshooting

### Audio Crackles/Glitches (XRUNs)

Buffer size is too low for your system. Increase quantum to 256 or 512.

Monitor in real-time:
```fish
pw-top
```
Shows "XRUN" when glitches occur.

### Connections Reset After Reboot

qpwgraph connections don't persist by default. Options:
- Reconnect manually in qpwgraph after boot
- Use qpwgraph's "Patchbay" feature to save and auto-restore connections

### Device Not Detected

Check if Focusrite is recognized:
```fish
wpctl status          # PipeWire devices
aplay -l              # ALSA devices
lsusb | grep -i focusrite
```
