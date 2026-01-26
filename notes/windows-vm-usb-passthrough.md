# Windows VM with USB Passthrough for Cortex Control

Run Cortex Control in a Windows VM while passing the Quad Cortex USB device directly to the VM.

## Prerequisites

### Install Required Packages

```fish
sudo pacman -S qemu-full virt-manager libvirt edk2-ovmf dnsmasq iptables-nft
```

**What these are:**
- `qemu-full` - The VM emulator/hypervisor
- `virt-manager` - GUI for managing VMs
- `libvirt` - VM management daemon
- `edk2-ovmf` - UEFI firmware for VMs (needed for modern Windows)
- `dnsmasq` - Network services for VM networking
- `iptables-nft` - Firewall backend for VM networking

### Enable Services

```fish
sudo systemctl enable --now libvirtd
sudo systemctl enable --now virtlogd
```

### Add User to Groups

```fish
sudo usermod -aG libvirt (whoami)
sudo usermod -aG kvm (whoami)
```

**Log out and back in** for group changes to take effect.

### Verify KVM Support

```fish
# Check if CPU supports virtualization
LC_ALL=C lscpu | grep Virtualization

# Should show: Virtualization: VT-x (Intel) or AMD-V (AMD)
```

If nothing shows, enable virtualization in BIOS/UEFI settings (usually called "Intel VT-x" or "AMD-V" / "SVM Mode").

## Download Windows ISO

1. Go to [Microsoft's Windows 11 download page](https://www.microsoft.com/software-download/windows11)
2. Download the Windows 11 ISO (or Windows 10 if preferred)
3. Save it somewhere accessible (e.g., `~/Downloads/`)

## Create the Virtual Machine

### Launch virt-manager

```fish
virt-manager
```

### Create New VM

1. Click **"Create a new virtual machine"** (or File → New Virtual Machine)

2. **Step 1 - Connection & Media:**
   - Select "Local install media (ISO image)"
   - Click "Forward"

3. **Step 2 - ISO Selection:**
   - Click "Browse" → "Browse Local" → select your Windows ISO
   - It should auto-detect "Microsoft Windows 11" (or 10)
   - Click "Forward"

4. **Step 3 - Memory & CPU:**
   - Memory: **4096 MB** minimum (8192 MB recommended)
   - CPUs: **2** minimum (4 recommended)
   - Click "Forward"

5. **Step 4 - Storage:**
   - Enable "Create a disk image for the virtual machine"
   - Size: **64 GB** minimum (Cortex Control + Windows needs space)
   - Click "Forward"

6. **Step 5 - Final Settings:**
   - Name: `Windows-CortexControl` (or whatever you prefer)
   - **Check "Customize configuration before install"** ← Important!
   - Click "Finish"

### Pre-Install Configuration

The configuration window opens. Make these changes:

1. **Overview:**
   - Firmware: Change to `UEFI x86_64: /usr/share/edk2/x64/OVMF_CODE.secboot.4m.fd`
   - This enables UEFI boot (required for Windows 11)

2. **CPUs:**
   - Check "Copy host CPU configuration" for best performance

3. **Add USB Controller (if not present):**
   - Click "Add Hardware" → "Controller"
   - Type: USB
   - Model: USB 3

4. Click **"Begin Installation"**

### Install Windows

Follow the Windows installer. During installation:
- Choose "Custom: Install Windows only" for clean install
- Select the virtual disk
- Complete the setup wizard

**Tip:** If Windows 11 complains about TPM/SecureBoot:
- Shut down the VM
- In virt-manager, add: "Add Hardware" → "TPM" → Type: Emulated, Model: TIS, Version: 2.0

## USB Passthrough Setup

Once Windows is installed and running:

### Add Quad Cortex USB Device

1. **Plug in your Quad Cortex** via USB
2. In virt-manager with the VM running, go to **Virtual Machine → Redirect USB device**
3. Select **"Neural DSP Quad Cortex"** from the list
4. The device is now available inside Windows

### Persistent USB Passthrough (Auto-connect)

For automatic passthrough when the VM starts:

1. Shut down the VM
2. Open VM details → Click "Add Hardware"
3. Select **"USB Host Device"**
4. Find and select the Quad Cortex
5. Click "Finish"

Now the Quad Cortex will automatically attach to the VM when it starts (and the device is connected).

**Note:** When passed to the VM, the Quad Cortex won't be available to Linux until you disconnect it from the VM or shut down the VM.

## Install Cortex Control

Inside the Windows VM:

1. Open Edge/browser
2. Go to [neuraldsp.com/downloads](https://neuraldsp.com/downloads)
3. Download Cortex Control for Windows
4. Install and run it
5. Your Quad Cortex should appear and connect

## Performance Tips

### Enable Virtio Drivers (Better Disk/Network Performance)

1. Download [virtio-win ISO](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso)
2. In VM settings, add a second CD-ROM with the virtio-win ISO
3. Inside Windows, open Device Manager
4. Update drivers for any unknown devices using the virtio-win disc

### Reduce VM Resource Usage

Since you only need this for Cortex Control occasionally:
- Lower RAM to 4GB if system is tight
- Set CPUs to 2
- Shut down VM when not in use

## Troubleshooting

### USB Device Not Showing

```fish
# List USB devices
lsusb | grep -i neural

# Check if device is bound to a driver that prevents passthrough
sudo dmesg | grep -i neural
```

### Permission Denied Errors

```fish
# Ensure you're in the right groups
groups

# Should include: libvirt kvm
# If not, log out and back in after usermod commands
```

### VM Won't Start (UEFI errors)

Make sure `edk2-ovmf` is installed and you selected the correct firmware path in VM settings.

### Windows 11 Requirements Not Met

Add TPM emulation:
1. Shut down VM
2. Add Hardware → TPM → Emulated TIS 2.0
3. Start VM again

## Quick Reference

```fish
# Start virt-manager
virt-manager

# List running VMs
virsh list

# Start VM from command line
virsh start Windows-CortexControl

# Shut down VM gracefully
virsh shutdown Windows-CortexControl

# Force stop VM
virsh destroy Windows-CortexControl

# List USB devices
lsusb
```
