# Using `ujust` Recipes on Debian

`ujust` is commonly associated with Fedora-based systems such as Universal Blue and Bazzite. Debian can run the `just` command runner, but that does **not** make every `ujust` recipe compatible.

Many recipes assume Fedora-specific tools—often `rpm`, `dnf`, or `rpm-ostree`—or expect files and system configurations that exist only on Universal Blue images. Debian does not provide the same environment. Installing the `rpm` utility alone will not make `rpm-ostree` commands or Fedora-specific recipes work.

## Install `just`

```bash
sudo apt update
sudo apt install just
```

## Run a recipe file

Obtain the relevant `.just` file from the project that provides the recipe. Then list its recipes and run one:

```bash
just --justfile /path/to/justfile --list
just --justfile /path/to/justfile RECIPE_NAME
```

Replace `/path/to/justfile` and `RECIPE_NAME` with the actual file path and recipe name.

## Check compatibility first

Before running a recipe, inspect its commands for Fedora-specific dependencies:

```bash
grep -nE 'rpm|rpm-ostree|dnf|ostree' /path/to/justfile
```

You can also check whether common tools are available:

```bash
command -v rpm
command -v rpm-ostree
command -v dnf
```

A missing command usually means the recipe will fail. Even if `rpm` is installed, that does not provide `rpm-ostree` or reproduce Fedora's package-management and system-image setup.

## Recommended approach

- Use `just` to run only recipes that are compatible with Debian.
- Read each recipe before running it, especially if it uses `sudo`.
- Replace Fedora-specific package or system-management commands with Debian equivalents where appropriate.
- Do not run recipes that modify an immutable system image or rely on `rpm-ostree` unless you are actually using a compatible system.

In short: Debian can use the `just` runner, but `ujust` recipes often need to be adapted before they will work.
