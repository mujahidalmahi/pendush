# Pendush Safety Architecture & Fail-Closed Model

**Author:** PinkDotHash / Akruno  
**Specification Level:** Production-Grade / Zero-Accidental-Write Standard  

---

## Executive Overview

Pendush operates under a strict **Fail-Closed Safety Architecture**. Disk utilities carry inherent danger because a write to the wrong sector or storage target can destroy user data or make an operating system unbootable. Pendush enforces 5 distinct verification boundaries before writing a single byte to physical hardware.

```
+-------------------------------------------------------------------------+
|                         PENDUSH SAFETY PIPELINE                         |
+-------------------------------------------------------------------------+
                                    |
  [Layer 1: Device Enumeration & Bus Verification]
  - Filter out SATA, NVMe, IDE, SCSI, RAID fixed system storage
  - Tag removable USB bus targets only
                                    |
  [Layer 2: Fail-Closed Device Identity Matching]
  - Query PNP Instance ID, Serial Number, Storage Unique ID
  - Reject ambiguous devices lacking strong hardware IDs
                                    |
  [Layer 3: Volume-to-Disk Extent Resolution]
  - Resolve Volume drive letter (e.g. E:) to PhysicalDrive index & byte offsets
  - Verify extent strictly belongs to target disk index
                                    |
  [Layer 4: TargetSession Handle Locking & Dismount]
  - Acquire FSCTL_LOCK_VOLUME and FSCTL_DISMOUNT_VOLUME
  - Prevent Windows shell caching and concurrent file access
                                    |
  [Layer 5: Safe Partitioning & Alignments]
  - 1MB alignment standard
  - Dynamic MBR types: 0x0C (FAT32) vs 0x07 (NTFS/exFAT)
  - GPT GUIDs and CRC32 verification
```

---

## Layer-by-Layer Verification Specifications

### Layer 1: Hardware-backed Bus Filtering
- **API**: `IOCTL_STORAGE_QUERY_PROPERTY` with `StorageDeviceProperty`.
- **Criteria**:
  - `BusType` must be `BusTypeUsb` (`0x07`).
  - `RemovableMedia` flag must be set to `TRUE` (unless the user explicitly enables fixed disk mode in advanced settings).
  - Devices matching `IsSystemDiskIndex` are completely excluded from selection and destructive pipelines.

### Layer 2: Fail-Closed Device Identity (`DeviceIdentity`)
- When the user selects a target in the UI, a cryptographic and hardware descriptor fingerprint is captured:
  - Physical disk index (`diskIndex`)
  - Target device path (`\\\\.\\PhysicalDriveX`)
  - Total capacity within $\pm 1\,\text{MB}$ tolerance
  - Physical sector size (512 vs 4096 bytes)
  - Verified strong identifier: `pnpInstanceId` or `uniqueId` or `serialNumber`.
- **Fail-Closed Rule**: If the live physical disk cannot produce a verified hardware identifier, or if any attribute mismatches during execution, the operation **immediately aborts without touching the disk**.

### Layer 3: Volume-to-Disk Extent Resolution
- Before operating on a drive letter (e.g., formatting, checking filesystem, or creating bootable files), `DeviceEngine::ResolvePhysicalDiskForVolume()` executes `IOCTL_VOLUME_GET_VOLUME_DISK_EXTENTS`.
- The returned physical disk index, starting offset, and length must match the selected target device.

### Layer 4: Volume & Handle Locking
- Before raw streaming or partitioning:
  1. `CreateFileW` with `GENERIC_READ | GENERIC_WRITE`.
  2. `FSCTL_LOCK_VOLUME` is issued to evict OS file handles.
  3. `FSCTL_DISMOUNT_VOLUME` is issued to invalidate cached metadata.
  4. Write operations execute with 64KB sector-aligned buffers.
  5. `FlushFileBuffers` is called and verified before releasing handles.

### Layer 5: Partition Scheme & Alignment
- **MBR Mode**:
  - Sector 0 contains standard Windows bootstrap code (`s_windowsMbrBootstrapCode`) and `0x55AA` signature.
  - Partition entry 1 is set to Active/Bootable (`0x80`).
  - Partition type is set dynamically: `PARTITION_FAT32_XINT13` (`0x0C`) for FAT32 or `PARTITION_IFS` (`0x07`) for NTFS/exFAT.
- **GPT Mode**:
  - Sector 0 contains Protective MBR (Type `0xEE`).
  - Primary GPT header at LBA 1 with Basic Data Partition GUID (`EBD0A0A2-B9E5-4433-87C0-68B6B72699C7`).
  - Validated CRC32 checksums on disk and in memory.
