# Porting ujust-style Recipes to Debian

This guide explains how to set up a `ujust` environment on Debian. This is useful for any project that uses the `ujust` pattern—a combination of the `just` command runner, a global `justfile`, and associated scripts stored in system directories.

## Overview

The `ujust` pattern typically relies on two main directories:
1. `/usr/share/ujust/`: Stores the `justfile` (the list of recipes).
2. `/usr/libexec/ujust/`: Stores the actual shell scripts that the recipes execute.

Since Debian does not have these by default, you must create the structure and populate it with the files from your chosen project.

## 1. Installation

First, install the `just` command runner:

```bash
sudo apt update
sudo apt install just
```

## 2. System Structure Setup

You need to create the standard directories so that the `justfile` can find the scripts it references.

```bash
# Create the directory for the justfile
sudo mkdir -p /usr/share/ujust

# Create the directory for the executable scripts
sudo mkdir -p /usr/libexec/ujust
```

## 3. Populating the Files

You must now copy the `justfile` and the scripts from the project you wish to use. Replace `<project-repo>` with the actual path or URL of the repository.

### Step A: Copy the justfile
The `justfile` must be placed exactly here for the wrapper to work:
```bash
# Example if you have the repo cloned locally:
sudo cp /path/to/<project-repo>/justfile /usr/share/ujust/justfile
```

### Step B: Copy the scripts
Most `ujust` setups reference scripts located in a `scripts` or `libexec` folder within the repo. These must be moved to `/usr/libexec/ujust/`:
```bash
# Copy all scripts to the libexec folder
sudo cp -r /path/to/<project-repo>/scripts/* /usr/libexec/ujust/

# Ensure all copied scripts are executable
sudo chmod +x /usr/libexec/ujust/*
```

## 4. Creating the `ujust` Command

To make the recipes accessible via a simple `ujust` command, create a system-wide wrapper script.

```bash
echo '#!/bin/bash' | sudo tee /usr/local/bin/ujust
echo 'just --justfile /usr/share/ujust/justfile "$@"' | sudo tee -a /usr/local/bin/ujust
sudo chmod +x /usr/local/bin/ujust
```

## 5. Usage

You can now list and run recipes from any project you have installed in the directories above:

```bash
# List all available recipes
ujust --list

# Run a specific recipe
ujust recipe-name
```

## ⚠️ Important Compatibility Warning

Most projects using the `ujust` pattern are designed for Fedora/RPM-based systems. **They will not work on Debian without modification.**

* **Package Managers:** Look for `dnf` or `rpm` commands in the scripts located in `/usr/libexec/ujust/` and change them to `apt` or `dpkg`.
* **Paths:** Fedora-specific paths (e.g., `/etc/fedora-release`) must be updated to Debian equivalents.
* **Dependencies:** Ensure any tools the scripts rely on are installed via `apt`.

**Recommendation:** Always open the script in `/usr/libexec/ujust/` and verify the commands before running them to avoid system errors.
