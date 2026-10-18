# R U a Mac?

### A project designed to make Hackintoshing easier — without hours of ProperTree configuration, hunting down kexts, and troubleshooting bootloaders.

> [!WARNING]
> **Work in progress — Woods Studio Labs.** This repository is not a complete, validated installation tool or a substitute for the [Dortania OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/). Instructions and downloads may change. Do not use this project on a computer containing irreplaceable data without verified backups.
>
> Project website: https://projectmac.woodsstudio.dev/  
> Labs website: https://labs.woodsstudio.dev/

## Notices and warnings

> [!IMPORTANT]
> This project targets **Windows-based computers** and currently focuses on preparing installation media and OpenCore configurations from Windows. Linux support is planned. macOS-to-macOS upgrades are outside the project's main scope.

> [!CAUTION]
> **Installing macOS on non-Apple hardware is unsupported by Apple and may conflict with Apple's software license terms.** There is a real risk of data loss, boot failure, or unsupported hardware. Use legally obtained macOS installers and understand the relevant terms before proceeding.

> [!WARNING]
> The **target macOS disk will be erased** when reformatted. Other disks should not be erased, but choosing the wrong disk can destroy data. Disconnect nonessential drives when practical, identify disks by model and capacity, and back up everything important first.

### Suggested starting requirements

| Component | Guidance |
| --- | --- |
| Host operating system | Windows 10 21H2 or newer for the planned Windows workflow |
| Processor | Supported Intel CPU; **generation alone does not guarantee compatibility** |
| Memory | 8 GB recommended; 4 GB may be limiting |
| Installation USB | 16 GB minimum; 32 GB or larger preferred for full offline media |
| Target drive | 128 GB minimum, preferably a SATA SSD |
| Firmware | UEFI firmware with configurable boot options |
| Graphics | Must be checked for compatibility with the **specific macOS release** |
| Installer | Authentic, verified macOS installation media |
| Recovery | A known-working backup EFI and separate recovery/boot option |

> [!NOTE]
> **There is no universally best macOS version.** The project's ThinkCentre M83 reference installation ultimately used **macOS Big Sur 11.7.10**, not Monterey. Monterey 12 may be appropriate for some configurations, but a recommendation must account for CPU, GPU, firmware, and available patches. OpenCore Legacy Patcher (OCLP) is primarily designed for unsupported genuine Macs; it should **not** be presented as a universal Hackintosh upgrade method.

## Reference hardware: Lenovo ThinkCentre M83

The documented reference build used a Lenovo ThinkCentre M83 with:

| Component | Reference configuration |
| --- | --- |
| Model | Lenovo ThinkCentre M83 (SFF chassis reported) |
| CPU | Intel Core i5-4430, 3.00 GHz, 4th-generation Haswell |
| Integrated GPU | Intel HD Graphics 4600 |
| RAM | 8 GB DDR3 |
| macOS drive | Samsung SSD 860 EVO, 250 GB |
| Windows drive | Seagate ST1000LM035, 1 TB |
| Windows installation | Windows 11 on the separate Seagate drive |
| Working macOS | macOS Big Sur 11.7.10 |
| Bootloader | OpenCore 1.0.8 |
| Original boot media | 32 GB SanDisk Cruzer Blade USB |

Lenovo reference: [ThinkCentre M83 specification sheet](https://psref.lenovo.com/syspool/Sys/PDF/ThinkCentre/ThinkCentre_M83_Tower/ThinkCentre_M83_Tower_Spec.PDF). Check the exact chassis and motherboard revision before applying model-specific advice; the linked PDF is labeled **Tower**, while the reference system was described as **SFF**.

> [!TIP]
> Keeping Windows and macOS on **separate physical disks** makes it easier to isolate installations and troubleshoot boot problems. It does **not** remove the need for full backups.

## What was completed on the reference build

The following describes the **observed outcome** of the project, rather than claiming that every intermediate command or BIOS setting was recorded.

1. A Windows 11 installation was retained on the separate 1 TB Seagate disk.
2. A 250 GB Samsung SATA SSD was used as the macOS target.
3. macOS Big Sur 11.7.10 was installed and successfully booted on the Lenovo ThinkCentre M83.
4. An OpenCore 1.0.8 EFI initially booted from a 32 GB USB drive.
5. The **working USB EFI folder** was copied to the **Samsung SSD's EFI system partition** from Windows 11 using PowerShell.
6. The computer subsequently booted macOS **without requiring the USB drive**.
7. EFI backups and test variants were kept separately during later experimentation.

> [!IMPORTANT]
> The successful USB-to-SSD migration is a confirmed result. The full original installer-creation sequence, exact firmware settings, and all boot arguments were **not preserved as a complete reproducible log**. The sections below therefore explain how to build a safe repeatable workflow, not a verbatim transcript of commands already performed.

## Getting started

### 1. Inventory the hardware

Before downloading or writing anything, record:

- Exact PC model, chassis, motherboard and firmware version.
- CPU model and generation.
- Integrated and discrete GPU models.
- Storage controller and disk model numbers.
- USB controllers and available ports.
- Audio controller, Ethernet controller and other peripherals required for the build.
- Current Windows boot mode (UEFI or Legacy/CSM).

On Windows, useful built-in tools include **System Information** (`msinfo32`), **Device Manager**, **Disk Management**, and PowerShell:

```powershell
Get-CimInstance Win32_Processor | Select-Object Name
Get-CimInstance Win32_VideoController | Select-Object Name
Get-PhysicalDisk | Select-Object FriendlyName, MediaType, Size
Get-Disk | Select-Object Number, FriendlyName, Size, PartitionStyle
```

> [!CAUTION]
> Never select a disk for formatting by its disk number alone. Disk numbers can change between boots. Verify the **model, capacity and existing partitions**.

### 2. Select a compatible macOS release

Use the [Dortania hardware limitations](https://dortania.github.io/OpenCore-Install-Guide/macos-limits.html) and [Dortania installation guide](https://dortania.github.io/OpenCore-Install-Guide/) to check CPU and GPU support. For the documented M83 build, the working target was **Big Sur 11.7.10**.

> [!WARNING]
> Do not assume a newer macOS version is better. Graphics acceleration, legacy hardware support, security updates, and application compatibility all vary by release. Big Sur is an older operating system and should not be described as currently security-supported.

### 3. Obtain installation media and OpenCore

Use verified, preferably official sources:

- [Dortania OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [OpenCorePkg releases](https://github.com/acidanthera/OpenCorePkg/releases)
- [Apple: download and install macOS](https://support.apple.com/102662)
- [ProperTree](https://github.com/corpnewt/ProperTree)
- [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)

The original project draft referenced a third-party Monterey `.raw` image, a Woods Studio Simplify archive, and a long GitHub release-asset URL. These are **not verified or recommended here**. GitHub release-asset URLs containing expiring signed query parameters should be replaced with stable release-page links.

> [!NOTE]
> A raw image obtained from an unofficial mirror may be modified, corrupted, outdated, or unauthorized. Validate provenance and checksums before writing it to removable media. This README does not certify any third-party installer image.

### 4. Prepare the installer USB

1. Back up the USB drive: it will be erased.
2. Obtain a suitable macOS installer using the procedure appropriate to the chosen release.
3. Create the installation medium following Dortania's Windows instructions.
4. Prepare the USB's EFI system partition with the **correct** OpenCore files and machine-specific configuration.
5. Verify that `EFI/BOOT/BOOTx64.efi` and `EFI/OC/OpenCore.efi` are present, along with the configured drivers, ACPI files and kexts.
6. Keep a complete copy of the USB EFI folder outside the USB before first boot.

> [!IMPORTANT]
> A bootable OpenCore EFI is **not** the same thing as a full offline macOS installer. The installer files must also be present or retrievable through the supported installation workflow.

### 5. Configure OpenCore for the exact machine

A typical OpenCore folder has this layout:

```text
EFI/
├── BOOT/
│   └── BOOTx64.efi
└── OC/
    ├── OpenCore.efi
    ├── ACPI/
    ├── Drivers/
    ├── Kexts/
    ├── Resources/
    ├── Tools/
    └── config.plist
```

`config.plist` controls the boot environment. Its entries must agree with the actual files under `ACPI`, `Drivers` and `Kexts`.

The reference EFI variants contained components such as **Lilu**, **VirtualSMC**, **WhateverGreen**, **AppleALC**, **SMCProcessor**, **SMCSuperIO**, **USBToolBox**, **UTBDefault**, and **IntelMausi**. Some variants also contained **CtlnaAHCIPort**. **Presence in a ZIP does not prove that a component was enabled, necessary, or working**; consult the actual configuration and hardware before reusing it.

Recommended configuration checks:

- Use the **Sample.plist** that matches the exact OpenCore version.
- Apply only ACPI tables appropriate to the machine.
- Include the drivers actually required by the firmware and filesystem setup.
- Check kext dependencies and load order; for example, dependent plugins should not precede their parent kexts.
- Configure platform information and SMBIOS for the chosen macOS version and hardware.
- Generate **unique** platform identifiers; do not publish real serial numbers or other machine-specific identifiers in a public repository.
- Validate the completed configuration using the matching `ocvalidate` executable.
- Save a known-good EFI copy **before every change**.

> [!CAUTION]
> Do not distribute a single universal `config.plist` as if it were guaranteed to work across all Intel systems. Incorrect graphics, ACPI, USB, or platform settings can cause boot failures or unstable operation.

### 6. Check firmware settings

The following are **items to investigate**, not verified settings from the reference installation:

- Boot mode: UEFI, not an accidental Legacy-only configuration.
- SATA controller mode: normally AHCI when appropriate to the storage configuration.
- Secure Boot and other firmware security features: follow the current OpenCore guidance for the specific system.
- Fast Boot and CSM: adjust only if the installation procedure requires it.
- VT-d, CFG Lock and related options: inspect the motherboard's capabilities and the selected OpenCore quirks before changing them.
- Boot priority: ensure the intended USB or SSD EFI can be selected from the firmware boot menu.

Record original firmware settings before changing anything. Firmware option names differ between models and revisions.

### 7. Install macOS to the intended disk

1. Boot the prepared installer through OpenCore.
2. Open Disk Utility and enable **View → Show All Devices**.
3. Identify the target **Samsung SSD by model and capacity**, not merely by a volume name.
4. Erase **only the intended target drive**, using a GUID partition map and the filesystem recommended for the chosen macOS installer (normally APFS for Big Sur).
5. Start installation and allow the expected restarts.
6. At each restart, select the appropriate installer or macOS entry through OpenCore until Setup Assistant appears.
7. Complete initial setup, then verify macOS version and the target disk.

> [!CAUTION]
> Disk Utility's **Erase** operation destroys the selected disk's existing data. Do not erase the separate Windows disk. If the target cannot be identified confidently, stop.

### 8. Boot successfully from USB first

Before migrating the bootloader, confirm that:

- The installer has finished and the macOS desktop is reachable.
- The system can restart and boot again using the USB OpenCore installation.
- The internal macOS SSD is recognized and stable.
- Basic graphics, keyboard, mouse, audio and storage behavior are checked.
- A backup of the exact working USB EFI exists on another drive.

Do not treat a single successful boot as proof that sleep, power management, USB mapping, and all peripherals are fully configured.

## Moving OpenCore from USB to the macOS SSD

This migration **was successfully completed** on the reference build using Windows 11. It removes the requirement to leave the USB plugged in for ordinary macOS boots.

### 9. Understand the EFI system partition

On a GPT disk, the **EFI System Partition (ESP)** is a small FAT32 partition used for UEFI boot files. It is distinct from the macOS APFS data/system volumes.

The relevant destination is the **Samsung SSD's ESP**, not the Seagate Windows disk's ESP.

### 10. Back up both the working EFI and Windows boot configuration

From Windows:

1. Copy the complete known-working USB `EFI` folder to a safe location.
2. Export or copy the current SSD EFI folder if one already exists.
3. Save these backups somewhere **other than the EFI partition being edited**.
4. Confirm Windows can still boot independently, or prepare suitable Windows recovery media.

Suggested backup names:

```text
Backups/
├── EFI-USB-known-good/
├── EFI-SSD-before-migration/
└── EFI-SSD-after-migration/
```

> [!WARNING]
> Never replace the only bootable EFI without a tested recovery path. Keep a bootable USB or another way to restore the original EFI.

### 11. Identify and mount the Samsung SSD's ESP in Windows

Use an **Administrator PowerShell** or Windows Terminal session:

```powershell
Get-Disk | Select-Object Number, FriendlyName, Size, PartitionStyle
Get-Partition | Select-Object DiskNumber, PartitionNumber, DriveLetter, Type, Size
```

Identify the Samsung SSD using its **actual current disk number**. The ESP is usually a small system partition, but do not rely on size alone.

For a partition that has been positively identified, Windows can assign a temporary drive letter:

```powershell
# EXAMPLE ONLY — replace these values after checking Get-Partition.
Get-Partition -DiskNumber <SAMSUNG_DISK_NUMBER> -PartitionNumber <EFI_PARTITION_NUMBER> |
    Add-PartitionAccessPath -AccessPath 'S:\'
```

> [!NOTE]
> If Windows refuses to mount the partition, do not format it or force ownership changes. Check the partition type, permissions, and whether a drive letter is already assigned. Other supported mounting methods may be required on some systems.

### 12. Copy the working EFI to the SSD

After mounting and checking the destination:

```powershell
# Replace the source with the actual location of the KNOWN-WORKING EFI.
# The source directory must itself contain BOOT and OC subfolders.
Copy-Item -Path 'C:\EFI-USB-known-good' -Destination 'C:\EFI-Backup' -Recurse -Force

# Example: copy the BOOT and OC folders into the mounted ESP's EFI directory.
New-Item -ItemType Directory -Path 'S:\EFI' -Force | Out-Null
Copy-Item -Path 'C:\EFI-USB-known-good\*' -Destination 'S:\EFI' -Recurse -Force
```

**Important:** These commands are illustrative and assume the source folder contains `BOOT` and `OC`. They do not delete stale destination files. If the SSD already has an EFI folder, inspect and back it up first rather than merging configurations blindly.

Check the resulting structure:

```text
S:\EFI\BOOT\BOOTx64.efi
S:\EFI\OC\OpenCore.efi
S:\EFI\OC\config.plist
```

Compare the copied files with the known-working source. A file-by-file or cryptographic-hash comparison is preferable to assuming that a successful copy operation means everything is correct.

### 13. Boot from the SSD

1. Shut down Windows.
2. Remove the installation USB.
3. Open the PC's firmware boot menu.
4. Select the Samsung SSD's UEFI/OpenCore entry, if available.
5. Confirm that OpenCore starts and macOS boots.
6. Restart once more to check repeatability.

If no SSD boot entry appears, inspect the ESP layout and firmware boot entries. Do not erase or replace the Windows Boot Manager to make OpenCore appear.

**Reference result:** The M83 successfully booted Big Sur without the USB after the working EFI was copied to the Samsung SSD.

## Windows and macOS dual-boot considerations

Because Windows and macOS occupy separate physical drives, either OS can often be selected from the motherboard's firmware boot menu. OpenCore may also display Windows, depending on configuration.

> [!IMPORTANT]
> Do not assume that OpenCore is the only way to boot Windows. Test direct Windows Boot Manager access before making risky changes to the macOS SSD EFI.

Recommended safeguards:

- Keep the Windows drive's own EFI and Windows Boot Manager intact.
- Do not run generic “repair EFI” scripts without inspecting which physical disk they modify.
- Avoid deleting firmware boot entries merely to change the default OS.
- Document which disk contains each EFI partition.
- After major changes, test both operating systems.

## Troubleshooting

### OpenCore does not appear

Check whether the firmware is using UEFI boot mode, whether the correct physical drive was selected, and whether `EFI/BOOT/BOOTx64.efi` exists. If the USB boots but the SSD does not, compare the two ESP folder structures and firmware boot entries.

### OpenCore appears, but macOS does not boot

Return to the last known-good EFI. Check the latest changed kexts, drivers, ACPI entries, boot arguments, and `config.plist` syntax. Enable OpenCore logging only as needed and avoid publishing logs containing unique platform identifiers.

### Boot stops during installation

Confirm the installer is complete, the target disk is prepared correctly, and the correct installer-stage entry is selected after each restart. A changing entry name across restarts can be normal.

### Black screen or unusable graphics

Check the GPU against the target macOS release and review framebuffer, connector and graphics-related configuration. Do not assume that a display signal proves hardware acceleration is active.

### Keyboard or mouse stops working

Inspect USB port mapping, USB controller behavior and the relevant input drivers. Keep an alternative input device or port available during testing.

### SSD is missing from Disk Utility

Check physical cabling, firmware detection, controller mode, and storage-controller compatibility. Do not format the Windows drive simply because it is the only visible disk.

### Windows no longer appears as a boot option

Use the firmware boot menu to look for **Windows Boot Manager** on the Seagate drive. Restore the known-good EFI if a recent change caused the problem. Avoid rewriting the Windows disk until its actual boot state has been assessed.

## Post-install checklist

A working desktop is the beginning of validation, not the end:

- [ ] macOS boots from the internal SSD without the installation USB.
- [ ] Windows still boots from its separate disk.
- [ ] Both OS drives are identified correctly.
- [ ] The macOS version and bootloader version are recorded.
- [ ] Graphics acceleration and display outputs are verified.
- [ ] Audio input and output are tested.
- [ ] All intended USB ports are mapped and tested at appropriate speeds.
- [ ] Sleep, wake, restart and shutdown are tested.
- [ ] CPU power management and temperatures are checked.
- [ ] System clock and time-zone behavior are checked across both operating systems.
- [ ] The EFI configuration passes the matching `ocvalidate` check.
- [ ] A known-good EFI backup and recovery procedure are stored safely.
- [ ] Machine-specific identifiers and logs are removed from anything published publicly.

## Beginner walkthrough: decisions before clicking anything

> [!TIP]
> **Rule of three:** Identify the physical disk, confirm the partition, and make a backup **before** changing boot files or erasing a volume.

### A. Identify the PC in Windows

Open **Start → System Information** (`msinfo32`). Record **System Model**, **BIOS Version/Date**, and **BIOS Mode**. BIOS Mode should report `UEFI` for the UEFI workflow described here. Open **Device Manager** to identify display and storage controllers; use **Task Manager → Performance** to cross-check memory and disk capacity.

Run these commands in **PowerShell** (they only read information):

```powershell
Get-CimInstance Win32_Processor | Select-Object Name
Get-CimInstance Win32_BaseBoard | Select-Object Manufacturer,Product
Get-CimInstance Win32_VideoController | Select-Object Name,DriverVersion
Get-PhysicalDisk | Select-Object FriendlyName,MediaType,Size
Get-Disk | Format-Table Number,FriendlyName,PartitionStyle,Size
```

Write the results into a worksheet **without sharing device serial numbers publicly**:

| Question | Example from reference build | Why it matters |
| --- | --- | --- |
| Which PC? | ThinkCentre M83 | Firmware and ACPI vary by model |
| Which CPU? | Intel Core i5-4430 (Haswell) | Determines OpenCore generation guidance |
| Which GPU? | Intel HD Graphics 4600 | Determines macOS graphics compatibility |
| Which target disk? | Samsung SSD 860 EVO, 250 GB | **Only this disk** is the intended erase target |
| Which disk runs Windows? | Seagate ST1000LM035, 1 TB | Must be preserved |
| How much RAM? | 8 GB | Impacts installation and responsiveness |
| Which firmware mode? | **Verify on the target machine** | Prevents UEFI/Legacy confusion |

> [!WARNING]
> The hardware table is an **example, not a supported-hardware guarantee**. The Lenovo model's firmware revision, graphics output, storage controller and installed cards can differ.

### B. Choose the right installation path

| Situation | Action |
| --- | --- |
| PC has a supported Intel CPU/GPU and UEFI firmware | Continue to the relevant Dortania platform guide |
| CPU looks supported, but GPU compatibility is uncertain | Stop and verify graphics **for the exact macOS version** |
| Windows and macOS will be on separate disks | Identify each disk by **model, size and disk number**; protect Windows disk |
| macOS will share a disk with Windows | **Do not follow this reference disk-erasure workflow**; use a separately validated partitioning guide |
| No tested EFI backup or recovery method | Create one before changing the internal ESP |
| The macOS installer is from an unofficial mirror | Verify origin/integrity or use a trusted acquisition method |

### C. What the installer USB actually contains

An OpenCore bootloader alone does **not** install macOS. A successful workflow needs **both** a suitable macOS installer/recovery environment and a compatible OpenCore EFI. On Windows, the official Dortania guide documents a recovery-based installer workflow, which may require network access during installation. A full offline installer is a separate workflow and should not be assumed from a 16 GB USB capacity alone.

> [!IMPORTANT]
> The project's future tool must explicitly label media as **Online Recovery** or **Full Offline Installer**, show its download size and prerequisites, and refuse to call an EFI-only USB a completed installer.

## OpenCore files explained — no guessing

| Path | Purpose | Common failure |
| --- | --- | --- |
| `EFI/BOOT/BOOTx64.efi` | Standard UEFI fallback boot entry | Firmware cannot locate bootloader |
| `EFI/OC/OpenCore.efi` | Main OpenCore bootloader | Boot fails before picker |
| `EFI/OC/config.plist` | Machine-specific boot settings | Incorrect path, invalid entry, mismatched version |
| `EFI/OC/Drivers/` | UEFI drivers referenced by `UEFI → Drivers` | Missing referenced driver |
| `EFI/OC/ACPI/` | ACPI tables referenced by `ACPI → Add` | Invalid or inappropriate SSDT |
| `EFI/OC/Kexts/` | Kernel extensions referenced by `Kernel → Add` | Missing dependencies or wrong kext order |
| `EFI/OC/Resources/` | Picker resources, when configured | Picker artwork/icons missing |
| `EFI/OC/Tools/` | Optional utilities | Tool not listed in configuration |

> [!NOTE]
> Copying a `.kext` into `Kexts` **does not enable it**. It must have a valid `Kernel → Add` entry. Similarly, a `.efi` file in `Drivers` requires an appropriate `UEFI → Drivers` entry. Use ProperTree's **OC Snapshot** on the correct `OC` directory, then review the result; snapshotting does not make an incompatible driver compatible.

### Exact configuration verification

1. Extract the OpenCore release package for the chosen version.
2. Begin with its matching sample configuration, not an unrelated internet EFI.
3. Populate ACPI, kext and driver folders **for the actual hardware**.
4. Use ProperTree to snapshot the `OC` directory and inspect all resulting entries.
5. Confirm `Lilu.kext` appears before kexts that depend on it, and `VirtualSMC.kext` before its plugins.
6. Validate using `Utilities/ocvalidate` from the **same OpenCore release**.
7. Inspect and fix every reported error. A successful validation checks configuration structure; it does **not** prove hardware compatibility.
8. Store the exact working configuration in a private backup before testing a change.

**Windows PowerShell example** (adjust the two paths to the extracted OpenCore package and EFI):

```powershell
$OpenCoreRoot = 'C:\OpenCore-1.0.8-RELEASE'
$Config = 'C:\EFI-Staging\EFI\OC\config.plist'
$Validator = Join-Path $OpenCoreRoot 'Utilities\ocvalidate\ocvalidate.exe'
if (!(Test-Path $Validator)) { throw "ocvalidate not found: $Validator" }
if (!(Test-Path $Config)) { throw "config.plist not found: $Config" }
& $Validator $Config
if ($LASTEXITCODE -ne 0) { throw 'OpenCore validation failed; do not deploy.' }
```

> [!TIP]
> OpenCore release ZIP layouts can vary. If `ocvalidate.exe` is elsewhere, locate it within the extracted release and update `$Validator`. Do not substitute a validator from a different version.

## SSD EFI migration — extra-safe Windows procedure

The following is a **recommended repeatable method**, not a verbatim record of the reference machine's earlier PowerShell commands.

### 1. Identify the disks without writing to them

In **Administrator PowerShell**:

```powershell
Get-Disk | Format-Table Number,FriendlyName,PartitionStyle,Size
Get-Partition | Format-Table DiskNumber,PartitionNumber,DriveLetter,Type,Size
```

Locate the **Samsung 250 GB SSD** by name and size. Note its disk number. Locate its **EFI System Partition** using partition type and disk association; do not guess from partition number or drive letter. Verify that the **Seagate 1 TB Windows disk** is a different physical disk.

### 2. Mount only the verified Samsung EFI partition

```powershell
# REPLACE both values with numbers verified above.
$MacDiskNumber = 1
$MacEspPartitionNumber = 1

$disk = Get-Disk -Number $MacDiskNumber
$part = Get-Partition -DiskNumber $MacDiskNumber -PartitionNumber $MacEspPartitionNumber
$disk | Format-List Number,FriendlyName,Size
$part | Format-List DiskNumber,PartitionNumber,GptType,Type,Size
```

**Do not run the next command unless the displayed disk and partition are correct.** The numeric values above are placeholders, **not** the reference machine's confirmed numbers.

```powershell
# S: must be free. This adds a drive-letter access path; it does not format the ESP.
Add-PartitionAccessPath -DiskNumber $MacDiskNumber `
  -PartitionNumber $MacEspPartitionNumber -AccessPath 'S:\'
```

If Windows refuses access, **stop** and inspect the error. Do not run `format`, `clean`, `convert`, or `delete partition` to solve a mount problem.

### 3. Back up before replacing anything

```powershell
New-Item -ItemType Directory -Path 'C:\Hackintosh-Backups' -Force | Out-Null
Copy-Item 'S:\EFI' 'C:\Hackintosh-Backups\EFI-before-change' -Recurse -Force
Test-Path 'C:\Hackintosh-Backups\EFI-before-change\OC\config.plist'
```

The final command should return `True` when the existing SSD EFI has OpenCore's configuration. If it returns `False`, inspect the source folder and backup; **do not continue**. If the SSD EFI has a different layout, preserve it exactly and investigate before changing anything.

### 4. Stage and inspect the replacement first

Extract the new EFI ZIP into `C:\EFI-Staging` and check:

```powershell
$Staged = 'C:\EFI-Staging\EFI'
@('BOOT\BOOTx64.efi','OC\OpenCore.efi','OC\config.plist') |
  ForEach-Object { '{0}: {1}' -f $_, (Test-Path (Join-Path $Staged $_)) }
```

**All three checks must be `True`.** Run `ocvalidate` against the staged configuration before deployment. Keep the original backup outside the ESP. If the ESP contains unrelated boot folders, preserve them; never delete the Windows boot folder from the Windows disk.

### 5. Deploy and verify the copy

Only after validation and backup, and **only if the target `S:` is the Samsung ESP**:

```powershell
# Back up the existing folder first (previous step), then replace only the
# Samsung ESP's OpenCore-related files, preserving any unrelated boot files.
Copy-Item 'C:\EFI-Staging\EFI\BOOT\*' 'S:\EFI\BOOT' -Recurse -Force
Copy-Item 'C:\EFI-Staging\EFI\OC\*' 'S:\EFI\OC' -Recurse -Force

@('BOOT\BOOTx64.efi','OC\OpenCore.efi','OC\config.plist') |
  ForEach-Object { '{0}: {1}' -f $_, (Test-Path (Join-Path 'S:\EFI' $_)) }
```

> [!CAUTION]
> This copy procedure **merges** into existing folders. Stale files can remain. For a controlled release, compare file manifests and remove obsolete files **only after verifying that they belong to OpenCore and are backed up**. Never indiscriminately erase an ESP.

### 6. Unmount and boot-test

```powershell
Remove-PartitionAccessPath -DiskNumber $MacDiskNumber `
  -PartitionNumber $MacEspPartitionNumber -AccessPath 'S:\'
```

Restart, use the motherboard's one-time boot menu, select the SSD's UEFI entry, and verify **two successful macOS boots**. Then test **direct Windows Boot Manager**. If the system fails, use the separate recovery path to restore `EFI-before-change` rather than improvising further edits on the only bootable EFI.

## Troubleshooting by symptom

| Symptom | First check | Avoid |
| --- | --- | --- |
| USB missing from firmware boot menu | UEFI mode, correct USB media layout, `BOOTx64.efi` | Reformatting internal disks |
| OpenCore picker missing | `EFI/BOOT` and `EFI/OC` layout, UEFI boot selection | Deleting Windows boot files |
| `OC: Configuration requires vault but no vault provided` | `Misc → Security → Vault` and matching vault files | Disabling protections at random |
| `OC: Failed to load configuration` | `config.plist` location and validity | Replacing whole EFI with unknown download |
| Kext not loading | `Kernel → Add`, bundle paths, dependencies, OS version compatibility | Assuming folder presence means active driver |
| Apple logo hangs or restarts | Verbose logs, ACPI, graphics, kernel quirks | Repeated blind BIOS changes |
| Installer returns to first screen | Correct macOS installer/target entry after restart | Erasing SSD again immediately |
| Disk absent in installer | Controller mode, physical connection, storage support | Formatting another disk to test |
| macOS boots but display is black | Correct iGPU framebuffer/connector and macOS compatibility | Random graphics patches |
| Windows boot option disappears | Firmware boot order and Windows disk ESP | Rebuilding Windows boot files on macOS SSD |

### Collect useful evidence before asking for help

Provide **model, CPU, GPU, macOS release, OpenCore release, exact error text, and the stage at which boot stops**. Capture a photo of the **last visible verbose lines** rather than only saying “it doesn't work.” Never publicly post an unredacted `config.plist` containing SMBIOS serials, UUIDs, MLB values or ROM data. Share a sanitized config and a list of enabled kexts instead.

## Post-install verification — commands and expected results

After the first successful boot, use **macOS Terminal**:

```bash
sw_vers                    # ProductVersion and BuildVersion
system_profiler SPHardwareDataType
system_profiler SPDisplaysDataType
system_profiler SPStorageDataType
```

Then test: keyboard and mouse on every needed USB port, graphics acceleration, sound output, sleep/wake, restart/shutdown, correct system time, and a cold boot **without the installer USB**. Record each as `PASS`, `FAIL`, or `NOT TESTED`. **A successful desktop boot is not proof that all of these work.**

## R U a Mac? tool design — make the easiest path the safest path

### Proposed beginner wizard

1. **Welcome / risks:** Explain unsupported hardware, data loss, licensing and backup requirements in plain language.
2. **Scan hardware:** Read CPU generation, GPU device ID, RAM, firmware mode, disk models and partition maps without making changes.
3. **Compatibility report:** Show `Supported`, `Needs manual verification`, or `Unsupported` **for the selected macOS release**, with links to evidence. Never promise a working install from CPU model alone.
4. **Choose target:** Display disks by **brand + capacity + Windows/macOS labels**. Require explicit confirmation of the intended erase target.
5. **Select installer type:** Online recovery vs full offline media, with internet/storage requirements and source verification.
6. **Build EFI:** Select version-matched OpenCore files and hardware-appropriate kexts/ACPI. Generate unique SMBIOS data locally, never in public logs.
7. **Validate:** Run matching `ocvalidate`, verify every referenced file, detect missing dependencies and refuse deployment on errors.
8. **Write USB:** Display the **exact removable device** and require a final confirmation before erasing it. Verify written content afterward.
9. **Guided BIOS screen:** Show model-specific steps **only when verified**; otherwise show a checklist and link to manufacturer documentation.
10. **Install companion guide:** Provide screenshots and restart-by-restart instructions. Never automate destructive APFS formatting without unambiguous confirmation.
11. **Migrate EFI:** Identify the target disk's ESP, back it up, stage changes, validate, copy and verify. Preserve Windows boot entries.
12. **Health check:** Produce a redacted diagnostics bundle, checklist, and rollback instructions.

### Safety requirements for the app

- Default to **read-only scanning**. Separate scanning from writing.
- Never choose a target disk solely from its disk number; Windows disk numbering can change.
- Detect BitLocker/device encryption and warn about recovery-key implications before firmware or boot changes.
- Make a backup of every modified ESP before applying changes, and confirm the backup is readable.
- Never write a sample SMBIOS identity unchanged to multiple machines.
- Keep an audit log of file operations **without personal identifiers**.
- Support dry-run mode that shows exactly which files would be added, replaced or deleted.
- Provide a tested rollback route **before** the first write.
- Make unsupported hardware and unverified patches clearly visible; do not hide them behind a “Fix All” button.

### Example repository content to add

```text
docs/
  00-before-you-start.md
  01-hardware-check.md
  02-macos-compatibility.md
  03-create-installer.md
  04-build-opencore-efi.md
  05-bios-and-first-boot.md
  06-install-macos.md
  07-migrate-efi-from-windows.md
  08-post-install-tests.md
  09-recovery-and-rollback.md
  troubleshooting/
  hardware/
    lenovo-thinkcentre-m83.md
examples/
  hardware-report.REDACTED.json
  install-checklist.md
```

> [!IMPORTANT]
> These are **proposed features and procedures**, not claims that the Woods Studio tool already implements them. The reference installation confirms a successful Big Sur boot and SSD EFI migration, not universal hardware compatibility.

## Roadmap: what R U a Mac? still needs

These are **proposed product features**, not features confirmed to exist in the current Woods Studio tool.

### Phase 1 — Hardware detection and compatibility

- [ ] Collect hardware information from Windows without requiring users to type device IDs manually.
- [ ] Detect CPU generation, GPU, firmware boot mode, storage controllers and USB controllers.
- [ ] Provide a per-component compatibility report for each supported macOS release.
- [ ] Explain **why** a release is recommended, unsupported, or requires additional work.
- [ ] Distinguish verified compatibility from community reports and experimental configurations.

### Phase 2 — Installer creation

- [ ] Use trustworthy installer acquisition methods with checksum verification.
- [ ] Warn before erasing a USB or target disk and display its model and capacity.
- [ ] Create online-recovery or full offline media where supported.
- [ ] Detect and validate GPT, FAT32 ESP and installation-media layout.
- [ ] Provide a clear progress indicator, detailed log and recovery guidance.

### Phase 3 — OpenCore configuration

- [ ] Download pinned OpenCore releases from stable release pages.
- [ ] Select required drivers, ACPI files and kexts based on detected hardware.
- [ ] Resolve dependencies and correct load order automatically.
- [ ] Generate and validate `config.plist` against the matching OpenCore schema.
- [ ] Generate unique platform identifiers locally and keep them private.
- [ ] Produce a machine-readable compatibility report and an editable EFI manifest.
- [ ] Refuse to overwrite a working EFI without a verified backup.

### Phase 4 — Guided installation and SSD migration

- [ ] Explain firmware options with model-specific warnings.
- [ ] Walk through Disk Utility selection with explicit disk-identity checks.
- [ ] Detect when macOS has completed setup.
- [ ] Provide a Windows-based **copy EFI to SSD** workflow.
- [ ] Verify copied files and preserve existing Windows boot entries.
- [ ] Offer a rollback option for every bootloader change.

### Phase 5 — Validation and support

- [ ] Run a post-install health checklist.
- [ ] Capture sanitized diagnostic logs with explicit consent.
- [ ] Provide targeted troubleshooting based on the last boot stage.
- [ ] Track which hardware/macOS combinations have been tested successfully.
- [ ] Publish reproducible test results, limitations and known issues.
- [ ] Document the upgrade path for OpenCore and individual kexts without implying automatic compatibility.

## Repository layout (proposed)

```text
R-U-a-Mac/
├── README.md
├── LICENSE
├── SECURITY.md
├── CONTRIBUTING.md
├── docs/
│   ├── hardware-compatibility.md
│   ├── installation-windows.md
│   ├── firmware-settings.md
│   ├── opencore-configuration.md
│   ├── migrate-efi-to-ssd.md
│   ├── dual-boot.md
│   ├── troubleshooting.md
│   └── tested-hardware/
│       └── lenovo-thinkcentre-m83.md
├── manifests/
│   ├── releases.json
│   └── compatibility.json
├── scripts/
│   └── windows/
└── tests/
```

> [!WARNING]
> **Do not commit private SMBIOS identifiers, firmware dumps, disk images containing personal data, or unlicensed installer binaries.** Example EFIs should be clearly marked as hardware-specific and stripped of private identifiers.

## Reference links

- [Woods Studio Labs](https://labs.woodsstudio.dev/)
- [R U a Mac? project website](https://projectmac.woodsstudio.dev/)
- [Dortania OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [OpenCorePkg releases](https://github.com/acidanthera/OpenCorePkg/releases)
- [OpenCore configuration documentation](https://github.com/acidanthera/OpenCorePkg/tree/master/Docs)
- [Apple macOS download guidance](https://support.apple.com/102662)
- [ProperTree](https://github.com/corpnewt/ProperTree)
- [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)
- [Lenovo ThinkCentre M83 specification sheet](https://psref.lenovo.com/syspool/Sys/PDF/ThinkCentre/ThinkCentre_M83_Tower/ThinkCentre_M83_Tower_Spec.PDF)

---

**Project status:** Experimental documentation and tooling under development. The reference M83 reached a working Big Sur installation and SSD-based OpenCore boot, but the end-to-end automated workflow has **not** yet been validated.
