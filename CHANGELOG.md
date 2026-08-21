# Changelog

All notable changes to **Pendush** will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.1] - 2026-08-21

### Security & Safety Remediation (70 Audit Findings Fixed)

#### Core Safety Model & Identity Verification
- **Fail-Closed DeviceIdentity**: Implemented strict validation requiring verified hardware identifiers (PNP Instance ID, Unique Storage ID, or Hardware Serial Number). Missing identifiers strictly fail closed to prevent identical USB device swap race conditions.
- **Unified `TargetSession` Architecture**: Refactored all destructive and modifying operations (DD streaming, ISO extraction, Reinitialization, Format, CHKDSK, Vaccine, Benchmark, Fake-NAND probe) to acquire exclusive hardware validation, drive letter extent verification, and live handle locking.
- **System OS Drive Immunity**: Triple-layer checks against Windows installation directory, partition boot markers, NVMe/SATA fixed bus flags, and system disk indices.

#### ISO & UDF Engine Hardening
- **Global Memory & Traversal Budgets**: Capped directory recursion depth to 16 levels, total parsed entries to 500,000, and cumulative directory metadata read to 128 MB.
- **Extent Bounds Validation**: Added checked arithmetic (`SafeMultiply64`, `SafeAdd64`) to prevent 64-bit integer overflows during sector math. All UDF extents are bounded within the image file size.
- **El Torito Platform IDs**: Full support for both BIOS (Platform ID `0x00`) and UEFI (Platform ID `0xEF`) catalog entries.

#### Storage Repair, Diagnostics & Format
- **Dynamic MBR Partition Types**: MBR layouts now assign `0x0C` (`PARTITION_FAT32_XINT13`) for FAT32 and `0x07` (`PARTITION_IFS`) for NTFS/exFAT.
- **CHKDSK Watchdog**: Replaced fragile 120s timeout with a generous 30-minute watchdog and responsive cooperative cancellation.
- **Sector Size Sanity**: Validated sector sizes ($512 \le S \le 4096$, power of 2) before attempting partition table queries.

#### Security & Update Engines
- **Atomic File Cleanup**: Implemented handle-based deletion via `FILE_FLAG_DELETE_ON_CLOSE` to eliminate TOCTOU race windows.
- **BOM & UTF-16 Autorun Parsing**: Autorun scanner now recognizes UTF-16LE, UTF-16BE, UTF-8 with BOM, and ANSI scripts up to 64 KB.
- **Vaccine Rollback**: Fully verified vaccine creation flow with atomic rollback on creation or lock failure.
- **Update Parser**: Added recursive Unicode JSON decoder (`\uXXXX`), exact `Pendush_Setup_x64.exe` asset matching, and 1 MB response cap.

#### Win32 UI & Threading
- **Async Operations**: All heavy tasks (ISO parsing, device refresh, benchmarks) run on dedicated worker threads with non-blocking progress messaging.
- **Cancellation Logging**: Cooperative cancellation is now published with explicit `[WARN]` entries to the live UI log and persisted to `%APPDATA%\PinkDotHash\Pendush\logs\pendush.log`.
- **Log Management & Truncation**: Bounded in-memory log edit control (2,000 lines FIFO cap) and physical log file truncation on "Clear Logs".
- **Safe Exit UX**: Prompts confirmation before closing when an active operation is underway, executing graceful cancellation.
- **Repository Architecture**: Mapped source repository to `mujahidalmahi/pendush-code` and release distribution to `mujahidalmahi/pendush`.

---

## [1.0.0] - 2026-08-20

### Initial Release
- Native C++20 Win32 desktop application with modern dark UI.
- Bootable USB creation supporting GPT (UEFI) and MBR (BIOS/CSM).
- Windows 11 TPM 2.0 / Secure Boot / RAM requirement bypass via `autounattend.xml`.
- DD raw image streamer with block-aligned I/O and SHA-256 verification.
- Diagnostic suite, USB Reinitialization, Speed Benchmark, and Fake-NAND probe.
