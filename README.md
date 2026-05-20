# Towel
> A towel is just about the most massively useful thing an interstellar hitchhiker can carry.

A thin wrapper around **Distrobox** to keep your host clean while still running tools and apps as if they were locally installed.


## What this project does

Towel installs a `towel` command and a predefined Distrobox container config for `towel`, based on Fedora toolbox. It adds a simple workflow for:

- creating/entering the container,
- installing packages with `dnf` inside the container,
- exporting app launchers/binaries back to the host via managed wrappers.

## Quick start

### 1) Install towel

From this repo root:

```bash
./install
```

- Use `./install --yes` in non-interactive scripts.
- The installer will:
  - copy towel files to `~/.local/share/towel`
  - symlink `~/.local/bin/towel`
  - copy distrobox/container config to `~/.config`
  - install distrobox automatically if missing

### 2) Create the container

```bash
towel create
```

### 3) Enter it

```bash
towel enter
```

### 4) Install software

```bash
towel install <package>
```

By default it prompts whether to export installed apps/binaries to the host.
Use `towel install --yes <package>` to auto-export.

---

## Common commands

```bash
towel                 # same as towel enter
towel create          # create container from ~/.config/distrobox/towel.ini
towel enter           # enter container shell
towel exec <cmd>      # run command inside container
towel install <pkg>   # dnf install inside container (+ optional export)
towel export <pkg>    # export already-installed package commands/apps
towel remove          # remove container and exported wrappers
towel update --check  # check for updates
towel update --apply  # install latest release
```

## How export works

Exported integrations are managed by towel and placed under:

- internal wrappers: `~/.local/share/towel/exports/`
- host-facing symlinks/apps:
  - `~/.local/bin/`
  - `~/.local/share/applications/towel-*.desktop`

Discovery is package-file based (`dnf repoquery`) and currently only exports:

- binaries in `/usr/bin/*`
- desktop entries in `/usr/share/applications/*.desktop`

---

## What applications work well in towel?

### ✅ Good fit

Apps/tools that are:

- regular RPM packages installed via `dnf`
- command-line tools with executables in `/usr/bin`
- desktop apps that ship standard `.desktop` launchers in `/usr/share/applications`
- user-space developer tooling (compilers, linters, formatters, language runtimes, editors)

These are the exact paths towel knows how to export cleanly.

### ⚠️ Usually awkward or unsupported

Apps that require behavior outside towel’s export/runtime model, for example:

- software that needs host-level system services (`systemd` units, daemons, kernel modules)
- packages whose main binaries are not in `/usr/bin` (for example only in `/opt`, custom paths, or generated at runtime)
- GUI apps without a standard `.desktop` launcher under `/usr/share/applications`
- apps needing privileged/root-style host integration beyond normal user-space container access

These can still run manually in some cases, but won’t be reliably auto-exported by `towel export`.

## Notes

- `towel remove` is destructive and unexports managed wrappers first.
- The default container config lives at `~/.config/distrobox/towel.ini` (copied from this repo during install).
- If `towel` isn’t found after install, restart your shell so `~/.local/bin` is on `PATH`.
