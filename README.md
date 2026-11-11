# Using ujust (just) on Debian

This guide explains how to set up the `just` command runner on Debian and replicate the directory structure used by `ujust` (Universal Blue/Fedora) so that recipes and their associated scripts work as intended.

## Overview

On Fedora-based systems, `ujust` is a wrapper that points to a global `justfile`. This `justfile` often calls external shell scripts located in system directories like `/usr/libexec`. To make this work on Debian, you need to install the `just` tool and manually replicate these paths.

## Installation

First, install the `just` command runner:

```bash
sudo apt update
sudo apt install just
```

## System Setup (Replicating Fedora Paths)

To ensure the recipes can find their associated scripts, copy the `justfile` and the scripts folder into the standard system locations.

### 1. Create the directory structure
Create the folders where the recipes and the internal scripts will reside:

```bash
sudo mkdir -p /usr/share/ujust
sudo mkdir -p /usr/libexec/ujust
```

### 2. Copy the files
Clone the repository containing the recipes (e.g., `ublue-os`) and move the files into the directories created above.

```bash
# Clone the source
git clone https://github.com/ublue-os/ublue-os.git /tmp/ublue-os

# Copy the main justfile
sudo cp /tmp/ublue-os/justfile /usr/share/ujust/justfile

# Copy the scripts that the justfile references
# Note: Adjust the source path based on the specific repo structure
sudo cp -r /tmp/ublue-os/scripts/* /usr/libexec/ujust/

# Make sure all scripts are executable
sudo chmod +x /usr/libexec/ujust/*
```

## Creating the `ujust` Command

To avoid typing the full path to the justfile every time, create a system-wide alias or a small wrapper script.

### Option A: Bash Alias (User-level)
Add this to your `~/.bashrc` or `~/.zshrc`:
```bash
alias ujust='just --justfile /usr/share/ujust/justfile'
```
Then run `source ~/.bashrc`.

### Option B: Wrapper Script (System-level)
Create a script in `/usr/local/bin` so all users can use the `ujust` command:
```bash
echo '#!/bin/bash' | sudo tee /usr/local/bin/ujust
echo 'just --justfile /usr/share/ujust/justfile "$@"' | sudo tee -a /usr/local/bin/ujust
sudo chmod +x /usr/local/bin/ujust
```

## Usage

Now you can use the recipes just like on Fedora:

```bash
ujust --list
ujust recipe-name
```

## Important Compatibility Warning

Even with the correct folder structure, **the scripts inside `/usr/libexec/ujust` were written for Fedora.**

* **Package Manager:** Most scripts will call `dnf` or expect `.rpm` packages. You must manually edit the scripts in `/usr/libexec/ujust/` to use `apt` and `.deb` packages.
* **System Paths:** Scripts may look for Fedora-specific config files (e.g., in `/etc/fedora-release`) which do not exist on Debian.
* **Dependencies:** Some recipes rely on Fedora-specific kernel modules or systemd configurations.

**Recommendation:** Always inspect the script in `/usr/libexec/ujust/` before running a recipe to ensure it won't attempt to run incompatible Fedora commands on your Debian system.
