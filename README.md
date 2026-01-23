# Windows 11 to CachyOS Migration Guide

A personal repository documenting my migration from Windows 11 to Linux CachyOS.

## About CachyOS

[CachyOS](https://cachyos.org/) is an Arch-based Linux distribution focused on performance. Key features:

- **Performance-optimized packages** compiled with advanced CPU optimizations
- **Rolling release** - always up-to-date, no major version upgrades
- **Arch ecosystem** - access to official repos and the AUR (Arch User Repository)
- **Multiple desktop environments** available (KDE, GNOME, etc.)

## Essential Commands

### Package Management (pacman)

```bash
# Update system (do this regularly)
sudo pacman -Syu

# Search for a package
pacman -Ss <package-name>

# Install a package
sudo pacman -S <package-name>

# Remove a package
sudo pacman -R <package-name>

# Remove package and its unused dependencies
sudo pacman -Rns <package-name>

# List installed packages
pacman -Q

# Show info about an installed package
pacman -Qi <package-name>
```

### AUR Helper (paru or yay)

The AUR contains community-maintained packages not in official repos. CachyOS typically includes `paru` or `yay`.

```bash
# Install from AUR (same syntax as pacman)
paru -S <package-name>

# Search AUR and official repos
paru -Ss <package-name>
```

## Migration Checklist

- [ ] Back up Windows data
- [ ] Create CachyOS bootable USB
- [ ] Install CachyOS
- [ ] Configure desktop environment
- [ ] Install essential applications
- [ ] Set up development environment
- [ ] Configure hardware (GPU drivers, peripherals)
- [ ] Restore data from backup

## Windows to Linux Equivalents

| Windows | Linux Alternative |
|---------|-------------------|
| File Explorer | Dolphin (KDE) / Nautilus (GNOME) |
| Notepad | Kate / gedit / nano / vim |
| Task Manager | System Monitor / htop / btop |
| Control Panel | System Settings |
| PowerShell/CMD | Terminal (bash/zsh/fish) |
| .exe installers | pacman / AUR |

## Resources

- [CachyOS Wiki](https://wiki.cachyos.org/)
- [Arch Wiki](https://wiki.archlinux.org/) - excellent resource, most applies to CachyOS
- [CachyOS Discord](https://discord.gg/cachyos)
