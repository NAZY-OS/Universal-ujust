# Using ujust (just) on Debian

This guide explains how to use the `just` command runner on Debian to execute recipes typically associated with `ujust` (Universal Blue/Fedora).

## Overview

`ujust` is essentially a wrapper for `just` that points to a specific set of recipes used in Fedora-based distributions like Bazzite or Universal Blue. While the `just` tool itself is platform-agnostic, the recipes (the scripts inside the justfile) are often written specifically for Fedora.

## Installation

To run these recipes, you first need to install the `just` command runner:

```bash
sudo apt update
sudo apt install just
```

## Usage

Since you are not on a Fedora-based system where `ujust` is pre-configured, you must manually specify the path to the project's `justfile`.

To list available recipes:
```bash
just --justfile /path/to/justfile --list
```

To run a specific recipe:
```bash
just --justfile /path/to/justfile recipe-name
```

## Important Compatibility Warning

Most `ujust` recipes are designed for the Fedora ecosystem. When running them on Debian, you will likely encounter the following issues:

* **Package Management:** Many recipes call `dnf` or attempt to install `.rpm` packages. These will fail on Debian, which uses `apt` and `.deb` packages.
* **System Paths:** Recipes may reference file paths (e.g., `/etc/fedora-release`) or system configurations that do not exist on Debian.
* **Dependencies:** Some recipes rely on Fedora-specific tools or kernel configurations.

**Recommendation:** Always inspect the content of the recipe in the `justfile` before executing it. You will likely need to manually edit the recipe to replace Fedora-specific commands with their Debian equivalents.
