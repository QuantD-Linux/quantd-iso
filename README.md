# Quantd Linux ISO

Quantd Linux is a custom Debian-based live desktop distribution built with [Debian Live Build](https://wiki.debian.org/DebianLive).  
It uses the **MATE Desktop Environment**, **PipeWire** for audio, and includes **Bluetooth** support.  
This project builds a bootable ISO based on **Debian 12 (Bookworm)** for general desktop users.

---

## 🎯 Features

- ✅ Debian 12 (Stable) Base  
- ✅ Full MATE Desktop Environment  
- ✅ PipeWire as audio backend  
- ✅ Bluetooth support (BlueZ + Blueman)  
- ✅ LightDM as Display Manager  
- ✅ Network Manager, Firefox, VLC, GIMP, and more  

---

## 🏗️ Build Environment

You can build Quantd Linux on any Debian-based system (like Debian, Ubuntu, or SparkyLinux).

### 🔧 Required Packages

```
sudo apt update
sudo apt install live-build git curl wget xorriso
```

---

## 📁 Project Structure

```
quantd-linux/
├── config/
│   ├── package-lists/
│   │   └── desktop-full.list.chroot
│   ├── includes.chroot/
│   │   ├── etc/
│   │   │   ├── apt/sources.list
│   │   │   ├── os-release
│   │   │   └── hostname
│   └── hooks/
│       └── 020-pipewire.chroot
```

---

## ⚙️ Build Configuration

Run this inside the `quantd-linux/` directory:

```
lb config \
  --distribution bookworm \
  --debian-installer live \
  --archive-areas "main contrib non-free non-free-firmware" \
  --binary-images iso-hybrid \
  --mirror-bootstrap http://deb.debian.org/debian/ \
  --mirror-binary http://deb.debian.org/debian/ \
  --mirror-chroot http://deb.debian.org/debian/ \
  --mirror-binary-security http://security.debian.org/ \
  --mirror-chroot-security http://security.debian.org/ \
  --bootappend-live "boot=live components quiet splash hostname=quantd user=quantd"
```

---

## 📦 Add Desktop Packages

Create this file:

```
config/package-lists/desktop-full.list.chroot
```

Add the following contents:

```
# MATE Desktop
task-mate-desktop
mate-desktop-environment
mate-desktop-environment-extras

# PipeWire audio
pipewire
pipewire-audio
pipewire-pulse
wireplumber
libspa-0.2-bluetooth

# Bluetooth
bluez
bluetooth
blueman
bluez-tools
pulseaudio-module-bluetooth

# Display/login manager
lightdm

# Default desktop apps
firefox-esr
vlc
gimp
file-roller

# Networking
network-manager
network-manager-gnome

# Utilities
sudo
policykit-1
```

---

## 🌐 Set Debian Stable Repositories

Create:

```
config/includes.chroot/etc/apt/sources.list
```

Paste:

```
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
deb http://security.debian.org/ bookworm-security main contrib non-free non-free-firmware
deb http://deb.debian.org/debian/ bookworm-updates main contrib non-free non-free-firmware
```

---

## 🎨 Branding

Set the hostname:

```
echo "quantd" > config/includes.chroot/etc/hostname
```

Set OS info:

```
config/includes.chroot/etc/os-release
```

Contents:

```
NAME="Quantd Linux"
VERSION="1.0"
ID=quantd
PRETTY_NAME="Quantd Linux 1.0 (Bookworm)"
HOME_URL="https://quantdlinux.example.com"
SUPPORT_URL="https://quantdlinux.example.com/support"
BUG_REPORT_URL="https://quantdlinux.example.com/bugs"
```

---

## 🔊 Enable PipeWire

Create hook:

```
config/hooks/020-pipewire.chroot
```

Make executable:

```
chmod +x config/hooks/020-pipewire.chroot
```

Contents:

```
#!/bin/bash
set -e

systemctl --global disable pulseaudio.service pulseaudio.socket || true
systemctl --global enable pipewire.service pipewire-pulse.service wireplumber.service
```

---

## 🔵 Enable Bluetooth

Create this symlink:

```
mkdir -p config/includes.chroot/etc/systemd/system/bluetooth.target.wants
ln -s /lib/systemd/system/bluetooth.service config/includes.chroot/etc/systemd/system/bluetooth.target.wants/bluetooth.service
```

---

## 🔨 Build the ISO

```
sudo lb clean
sudo lb build
```

The ISO will be generated as:

```
live-image-amd64.hybrid.iso
```

---

## Download the ISO
[https://archive.org/embed/live-image-amd64.hybrid_202508](https://archive.org/embed/live-image-amd64.hybrid_202508)

## 🧪 Test the ISO

### Via QEMU:

```
qemu-system-x86_64 -cdrom live-image-amd64.hybrid.iso -m 2048
```

### Or write to USB:

```
sudo dd if=live-image-amd64.hybrid.iso of=/dev/sdX bs=4M status=progress
```

Replace `/dev/sdX` with your USB device.

---

## 📜 License

GPLv3

---

## 🧩 Credits    

Based on [Debian Live Build](https://wiki.debian.org/DebianLive).  
MATE, PipeWire, Debian — all credits to the respective upstream developers.
