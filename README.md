<div align="center">

<img src="assets/logo.png" alt="Pendush Logo" width="120" />

# Pendush — USB Utility
**A simple, fast, and rock-solid native Windows USB utility.**

[![Latest Release](https://img.shields.io/github/v/release/mujahidalmahi/pendush-releases?style=for-the-badge&color=D94B73&label=Latest%20Release)](https://github.com/mujahidalmahi/pendush-releases/releases/latest)
[![Platform](https://img.shields.io/badge/Platform-Windows%20x64-0078D6?style=for-the-badge&logo=windows)](https://github.com/mujahidalmahi/pendush-releases)
[![License](https://img.shields.io/badge/License-Apache%202.0-272624?style=for-the-badge)](LICENSE)

*Developed by **PinkDotHash**, a software tools brand by **Akruno**.*

[📥 Download Latest Installer (v1.0.1)](https://github.com/mujahidalmahi/pendush-releases/releases/latest) • [📖 User Guide](#-complete-user-guide) • [🛡️ Safety Architecture](#-safety-first-architecture) • [💬 Report an Issue](https://github.com/mujahidalmahi/pendush-releases/issues)

---

</div>

## 💡 Guiding Philosophy

> **"Build small. Solve real problems. Make excellent tools."**

Pendush is engineered in pure C++20 and Win32 for maximum reliability, speed, and safety. Unlike heavy Electron-based or bloated disk tools, Pendush has **zero third-party UI dependencies**, starts instantly, and performs real hardware-level diagnostics and formatting with total precision.

---

## ✨ Features at a Glance

| Feature | Description |
| :--- | :--- |
| 🚀 **Bootable USB Creator** | Native ISO9660 & UDF analysis. Seamlessly create bootable Windows & Linux USB drives with automated Windows 11 TPM/SecureBoot/RAM bypass and byte-exact raw DD streaming. |
| 🛡️ **Zero-Tolerance Safety** | Dynamic OS disk detection unconditionally protects internal Windows drives, recovery partitions, and pagefiles from accidental erasure. |
| 🔍 **Hardware Diagnostics** | Real GPT Header CRC32 validation, protective MBR verification, filesystem dirty bit detection, and S.M.A.R.T. predictive failure alerts. |
| 🛠️ **Storage Repair & Formatting** | Quick fixes for RAW drives, missing drive letters, software write-protection attributes, and low-level FAT32, NTFS, and exFAT formatting. |
| 🧪 **Speed Benchmark & NAND Probe** | Deterministic pseudo-random vector benchmark with unbuffered I/O + memory wrap-around fake capacity detection to expose fraudulent flash drives. |
| 🧼 **USB Security & Vaccine** | Heuristic scanner for disguised `.exe` malware and shortcut worms, plus built-in write-locked autorun immunization. |
| 🎨 **Warm Paper Design System** | Clean, distraction-free warm light aesthetic (`#F4F1EA`) with pixel-perfect High-DPI scaling on Windows 10 and 11. |
| 🔄 **1-Click Auto-Updates** | Built-in GitHub update checker that alerts you when a new release is available and upgrades with one click. |

---

## 📥 Installation & Setup

### System Requirements:
- **Operating System**: Windows 10 / Windows 11 (64-bit x64)
- **Privileges**: Administrator Rights (required for physical disk IOCTL operations)
- **RAM / Storage**: < 20 MB RAM, < 10 MB disk space

### Quick Install:
1. Download the latest installer: [**`Pendush_Setup_v1.0.1.exe`**](https://github.com/mujahidalmahi/pendush-releases/releases/latest).
2. Run the installer and follow the prompt.
3. Launch **Pendush** from your Desktop or Start Menu.

---

## 📖 Complete User Guide

### 1. Creating a Bootable USB Drive
1. Insert your USB flash drive (minimum 8 GB recommended for modern OS images).
2. Open Pendush and select your USB drive from the top **Target Device** dropdown.
3. Click the **Boot Creator** tab on the left sidebar.
4. Click **Browse...** to select your ISO file (`.iso`, `.img`, or `.raw`).
   - Pendush automatically inspects the image structure, determines whether it is Windows or Linux, and configures the optimal partition scheme (GPT for UEFI or MBR for Legacy BIOS).
   - If Windows 11 is detected, you can check **"Bypass Windows 11 Hardware Checks"** to automatically skip TPM 2.0, Secure Boot, and RAM limits.
5. Click **Create Bootable USB** and confirm the safety prompt.

---

### 2. Testing for Fake / Fraudulent USB Drives (Authenticity Probe)
Counterfeit flash drives spoof their internal controller firmware to report a fake capacity (e.g. reporting 128 GB when only 16 GB of physical NAND exists), causing silent file corruption.
1. Select your USB drive in Pendush.
2. Navigate to **Speed & Capacity Test** in the sidebar.
3. Click **Run Fake Capacity Probe**.
4. Pendush tests physical memory address boundaries using deterministic test vectors. If address wrap-around is detected, Pendush will alert you with the real physical memory limit.

---

### 3. Repairing Write-Protected, RAW, or Corrupted USB Drives
If Windows says your drive is "Write Protected", "RAW", or refuses to format:
1. Go to the **Repair & Reinitialize** tab.
2. Try individual quick repairs:
   - **Clear Read-Only Attribute**: Clears software write-protection flags via disk attributes.
   - **Mount / Repair Drive Letter**: Mounts missing volume letters so Explorer can see your drive.
   - **Filesystem Check (`chkdsk`)**: Performs a non-destructive filesystem scan and repairs cluster chains.
3. For severely corrupted or stubborn drives, click **Full USB Reinitialization**:
   - Pendush cleans the partition headers, re-writes a fresh MBR/GPT partition layout with 1MB alignment, and creates a clean FAT32 filesystem.

---

### 4. USB Security & Immunization Vaccine
1. Go to the **Security Hygiene** tab.
2. Click **Scan USB Drive** to detect hidden shortcut worms (`.lnk`), double-extension executables (e.g. `document.pdf.exe`), or malicious `autorun.inf` files.
3. Click **Apply USB Vaccine**:
   - Creates an un-deletable, read-only directory lock on your USB drive (`autorun.inf\lpt1.pendush_lock`), preventing malware from creating autorun scripts when plugged into public or shared computers.

---

## 🛡️ Safety-First Architecture

Pendush is built with strict multi-layer safeguards to prevent data loss:

1. **Dynamic Windows System Detection**: Queries kernel storage drivers (`IOCTL_STORAGE_GET_DEVICE_NUMBER`, `IOCTL_VOLUME_GET_VOLUME_DISK_EXTENTS`, `GetSystemWindowsDirectoryW`) to identify active OS drives, system partitions, crash dumps, and page files.
2. **Fixed Internal Disk Protection**: Non-removable internal NVMe/SATA SSDs are blocked from destructive format commands.
3. **Pre-Flight Identity Verification**: Validates serial numbers, disk indices, and capacity before executing raw block writes.
4. **Clean Volume Locking**: Unmounts and locks volume handles exclusively during operations to prevent file conflicts.

---

## 🔄 Automatic Updates

Pendush includes a native, lightweight update client powered by the Windows WinHTTP subsystem:
- When enabled in **Logs & Settings**, Pendush periodically queries the official release repository for new versions.
- You can manually check anytime by clicking **"Check for updates now"** in the Settings tab.
- Updates are non-intrusive and never install anything without your explicit consent.

---

## 📄 License & Credits

- **Product**: Pendush
- **Brand**: PinkDotHash
- **Parent Company**: Akruno
- **License**: [Apache License 2.0](LICENSE)
- **Official Repository**: [github.com/mujahidalmahi/pendush-releases](https://github.com/mujahidalmahi/pendush-releases)
