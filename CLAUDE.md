# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

This repository helps migrate from Windows 11 to Linux CachyOS. The user has basic Linux knowledge (filesystem navigation) and wants to learn more. **Always explain what you're doing and why** when providing assistance.

## CRITICAL: System Constraints

### Shell Environment: Fish (NOT Bash/Zsh)
**The user uses fish shell exclusively.** You MUST adapt ALL commands for fish syntax. Never use bash/zsh syntax.

Fish syntax differences:
- ❌ `export VAR=value` → ✅ `set -x VAR value`
- ❌ `VAR=value command` → ✅ `env VAR=value command` or `set -x VAR value; command`
- ❌ Heredocs (`<< EOF`) → ✅ `echo` with pipes or `printf`
- ❌ `&&` for chaining → ✅ `; and` or separate commands
- ❌ `||` for fallback → ✅ `; or`
- ❌ `$()` subshells → ✅ `(command)` or command substitution
- ❌ `source ~/.bashrc` → ✅ `source ~/.config/fish/config.fish`

### No Sudo Access
**You do NOT have permission to use sudo.** Never run commands with `sudo` - always suggest the command for the user to run themselves, or ask them to run it.

## Teaching Approach

- Explain commands before running them
- Describe what each flag/option does
- Provide context for why certain approaches are preferred
- When relevant, contrast with Windows equivalents to bridge understanding
- Encourage learning by explaining the underlying concepts

### Requesting Permission

When you need to perform an action that requires user approval, ALWAYS follow this structure:

1. **Explain the context**: What problem are we solving and why does this action help?
2. **Describe the action**: What specifically will be done (commands run, files edited, packages installed, etc.)
3. **Explain the expected outcome**: What will change and what result should the user expect?
4. **Ask for explicit permission**: Only then ask "Should I proceed?" or similar

**Example:**
```
The game's controller hotplug feature is causing incorrect button mappings.
I need to edit the options.ini file located at [path] and change the
ControllerHotplug setting from 1 to 0. This will disable the dynamic
controller detection that's scrambling your inputs. After this change,
you'll need to restart the game for it to take effect.

Should I proceed with editing this file?
```

Never jump directly into running commands without this explanation, even for seemingly simple tasks.


## CachyOS-Specific Notes

CachyOS is an Arch-based distribution with performance optimizations. Key differences from mainstream distros:

- Package manager: `pacman` (and `yay`/`paru` for AUR)
- Rolling release model (no version upgrades, continuous updates)
- Uses CachyOS repositories with optimized packages
- May include `cachyos-` prefixed packages with additional optimizations
