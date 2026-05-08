# BizHawk Installation Guide for Ubuntu Linux

> **BizHawk** is a multi-system emulator written in C# by TASEmulators. It supports dozens of consoles (NES, SNES, Genesis, N64, Game Boy, PlayStation, and many more) and is the go-to tool for Tool-Assisted Speedrunning (TAS).
>
> This guide covers installing the **pre-built Linux release** on Ubuntu (x86_64 only).

---

## 1 — Prerequisites

BizHawk on Linux runs on **Mono** (not .NET Core / .NET 8). You need five runtime dependencies installed before launching.

### Update your package list

```bash
sudo apt update
```

### Install all dependencies in one command

```bash
sudo apt install -y \
  mono-complete \
  libopenal1 \
  lua5.4 \
  lsb-release
```

> **glibc** (`libc6`) is already present on every Ubuntu install — you don't need to install it explicitly.

### What each package provides

| Package | Purpose |
|---|---|
| `mono-complete` | Full Mono runtime, compiler, and all libraries — required to run BizHawk's C# code on Linux |
| `libopenal1` | OpenAL Soft shared library — provides the audio backend |
| `lua5.4` | Lua 5.4 interpreter and shared library — powers BizHawk's Lua scripting console |
| `lsb-release` | Provides `lsb_release` — used by BizHawk's launch script to identify the distro |
| `libc6` (glibc) | GNU C Library — already installed on all Ubuntu systems |

### Verify the installs

```bash
mono --version        # should print Mono JIT compiler version 6.x
lua5.4 -v             # should print Lua 5.4.x
lsb_release -a        # should print your Ubuntu release info
```

---

## 2 — Download BizHawk

Go to the latest release page:

**https://github.com/TASEmulators/BizHawk/releases/latest**

Download the Linux archive. As of this writing the latest release is **2.11.1**. The file you want is:

```
BizHawk-2.11.1-linux-x64.tar.gz
```

Or grab it directly from the terminal:

```bash
cd ~/Downloads
wget https://github.com/TASEmulators/BizHawk/releases/download/2.11.1/BizHawk-2.11.1-linux-x64.tar.gz
```

> **Tip:** If a newer version is available when you read this, adjust the version number accordingly.

---

## 3 — Extract

Create a dedicated folder and extract into it. The BizHawk team recommends keeping each version in its own folder — **don't mix different versions**.

```bash
mkdir -p ~/BizHawk
tar -xzf ~/Downloads/BizHawk-*-linux-x64.tar.gz -C ~/BizHawk
```

Check the contents:

```bash
ls ~/BizHawk
```

You should see `EmuHawkMono.sh`, various DLLs, and supporting files.

---

## 4 — Launch BizHawk

Run the provided shell script:

```bash
cd ~/BizHawk
./EmuHawkMono.sh
```

The script will print an error if something is wrong (like a missing dependency). Otherwise you can ignore console output — EmuHawk's window should appear.

### Optional: pass command-line arguments

```bash
# Launch directly into a ROM with a Lua script
./EmuHawkMono.sh --lua=/absolute/path/to/script.lua /absolute/path/to/rom.sfc
```

> **Important:** File paths passed to `EmuHawkMono.sh` must be **absolute**, or relative to the BizHawk install directory (the script changes the working directory there).

---

## 5 — Create a Desktop Shortcut (optional)

You can launch BizHawk from anywhere since `EmuHawkMono.sh` handles the working directory. Create a `.desktop` file for convenience:

```bash
cat > ~/.local/share/applications/bizhawk.desktop << 'EOF'
[Desktop Entry]
Name=BizHawk
Comment=Multi-system emulator for TAS
Exec=/home/YOUR_USERNAME/BizHawk/EmuHawkMono.sh
Icon=/home/YOUR_USERNAME/BizHawk/Assets/icon_bizhawk.ico
Terminal=false
Type=Application
Categories=Game;Emulator;
EOF
```

Replace `YOUR_USERNAME` with your actual username (or use `$HOME` expansion). You may also want to convert the `.ico` to `.png` for better desktop integration.

---

## 6 — Firmware Setup

Many emulated systems require firmware / BIOS files that **cannot** be distributed with BizHawk (they're copyrighted). You need to provide your own legally-obtained dumps.

1. Create (or locate) the `Firmware` folder inside your BizHawk directory.
2. Drop all your firmware files in there — filenames and subfolders don't matter, BizHawk auto-detects them by hash.
3. When you try to load a game that needs firmware, BizHawk will tell you if anything is missing.
4. You can change the firmware search path via **Config → Paths → Global tab → Firmware**.

---

## 7 — Known Limitations on Linux

- **N64 (Mupen64Plus core)** does not work on Linux. The Ares64 core is available as an alternative.
- Some features may lag behind the Windows version — the project is primarily Windows-focused.
- **Wine is not needed and not supported** — BizHawk runs natively via Mono.
- **.NET 8 / .NET Core is not a dependency** — only Mono.
- Only **x86_64** is fully supported. AArch64 may partially work with missing features (see GitHub issue #4052).

---

## 8 — Troubleshooting

### "EmuHawkMono.sh: Permission denied"

```bash
chmod +x ~/BizHawk/EmuHawkMono.sh
```

### Missing shared library errors

If you see errors about missing `.so` files, double-check that all dependencies are installed:

```bash
dpkg -l mono-complete libopenal1 lua5.4 lsb-release
```

All four should show `ii` (installed) in the status column.

### Audio not working

Make sure PulseAudio or PipeWire is running. OpenAL Soft should pick up your system audio server automatically. You can check the OpenAL config with:

```bash
cat /etc/openal/alsoft.conf   # if it exists
```

### Script or Lua errors

Ensure `lua5.4` is the version installed (not 5.1 or 5.3). BizHawk specifically requires **5.4**.

---

## Quick Reference

```bash
# Full install sequence (copy-paste friendly)
sudo apt update
sudo apt install -y mono-complete libopenal1 lua5.4 lsb-release

cd ~/Downloads
wget https://github.com/TASEmulators/BizHawk/releases/download/2.11.1/BizHawk-2.11.1-linux-x64.tar.gz

mkdir -p ~/BizHawk
tar -xzf BizHawk-*-linux-x64.tar.gz -C ~/BizHawk

cd ~/BizHawk
chmod +x EmuHawkMono.sh
./EmuHawkMono.sh
```

---

## Resources

- **GitHub:** https://github.com/TASEmulators/BizHawk
- **Releases:** https://github.com/TASEmulators/BizHawk/releases
- **FAQ:** https://tasvideos.org/Bizhawk/FAQ
- **Changelog:** https://tasvideos.org/Bizhawk/ReleaseHistory
- **Linux issues thread:** https://github.com/TASEmulators/BizHawk/issues/1430
- **Discord:** `#bizhawk` on the TASVideos Discord
