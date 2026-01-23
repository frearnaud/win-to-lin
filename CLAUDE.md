# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

This repository helps migrate from Windows 11 to Linux CachyOS. The user has basic Linux knowledge (filesystem navigation) and wants to learn more. **Always explain what you're doing and why** when providing assistance.

## Teaching Approach

- Explain commands before running them
- Describe what each flag/option does
- Provide context for why certain approaches are preferred
- When relevant, contrast with Windows equivalents to bridge understanding
- Encourage learning by explaining the underlying concepts

## Shell Environment

The user uses **fish** as their default shell. Always adapt commands for fish syntax:

- No `export VAR=value` → use `set -x VAR value`
- No heredocs (`<< EOF`) → use `echo` with pipes or `printf`
- No `&&` for chaining → use `; and` or separate commands
- No `$()` subshells in some contexts → use `(command)`
- No `source ~/.bashrc` → use `source ~/.config/fish/config.fish`

## CachyOS-Specific Notes

CachyOS is an Arch-based distribution with performance optimizations. Key differences from mainstream distros:

- Package manager: `pacman` (and `yay`/`paru` for AUR)
- Rolling release model (no version upgrades, continuous updates)
- Uses CachyOS repositories with optimized packages
- May include `cachyos-` prefixed packages with additional optimizations
