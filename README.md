# Shadermon

Shadermon is a small Linux system tray utility for monitoring Steam's background Vulkan shader compilation.

Steam can compile Vulkan shaders in the background, but normally provides very little indication of what it is doing or how far along it is. This can result in high CPU usage without an obvious explanation.

Shadermon monitors Steam's `shader_log.txt` and displays the current shader compilation job in the system tray, including:

- Game name
- Compilation percentage
- Compiled/total shader count
- Progress bar
- Desktop notification when compilation finishes

## Multiple Steam libraries

Shadermon supports games installed in secondary Steam libraries.

Steam installations commonly keep the Steam client under:

```text
~/.local/share/Steam
```

while games may be stored on other disks or filesystems.

Shadermon reads Steam's `libraryfolders.vdf` and searches the configured Steam libraries for the appropriate `appmanifest_<appid>.acf`. This allows the shader AppID to be resolved to the correct game name regardless of which configured Steam library contains the game.

## Building

Shadermon is written in Rust and uses GTK3 for its tray interface.

On Arch Linux, install the required native dependencies:

```bash
sudo pacman -S --needed rust gtk3 xdotool
```

Clone the repository and build the release binary:

```bash
git clone https://github.com/AgnosticCrayonEater/shadermon.git
cd shadermon
cargo build --release
```

The resulting binary will be:

```text
target/release/shadermon
```

## Installation

For a per-user installation:

```bash
mkdir -p ~/.local/bin
install -m755 target/release/shadermon ~/.local/bin/shadermon
```

Create a desktop entry at `~/.local/share/applications/shadermon.desktop`:

```ini
[Desktop Entry]
Name=Steam Shader Monitor
Comment=Monitor Steam shader compilation progress
Exec=/home/YOUR_USERNAME/.local/bin/shadermon
Icon=steam
Type=Application
Categories=Utility;
Terminal=false
StartupNotify=false
```

Replace `YOUR_USERNAME` with your Linux username.

## Autostart

Shadermon is designed to run quietly in the system tray, so it can be useful to start it automatically with your desktop session.

For desktops supporting the XDG autostart specification:

```bash
mkdir -p ~/.config/autostart
cp ~/.local/share/applications/shadermon.desktop \
   ~/.config/autostart/shadermon.desktop
```

Shadermon will then start when your graphical desktop session starts.

## How it works

Shadermon monitors:

```text
~/.local/share/Steam/logs/shader_log.txt
```

When Steam reports an active shader compilation job, Shadermon extracts the AppID and progress information.

It then uses Steam's library configuration and application manifests to resolve the AppID to a human-readable game name.

When compilation completes, Shadermon returns to its idle state and sends a desktop notification.

## Tested on

The current fork has been tested on:

- Arch Linux
- KDE Plasma
- Steam with the client and games stored in separate Steam libraries

Other Linux desktop environments may work but have not necessarily been tested.

## Upstream

This repository is a fork of the original [psmon14/shadermon](https://github.com/psmon14/shadermon).

The original project provided the shader-log monitoring, tray progress display, AppID resolution and completion notifications.

This fork adds support for multiple Steam libraries and includes some Linux desktop integration and presentation improvements.

## License

See the upstream repository and repository files for applicable licensing information.
