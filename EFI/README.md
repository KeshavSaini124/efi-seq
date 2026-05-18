# HP EliteBook 745 G6 - Hackintosh EFI
## macOS Sequoia 15.x | OpenCore 1.0.x | AMD Ryzen 5 Pro 3500U

---

## ⚠️ IMPORTANT: THIS IS A TEMPLATE EFI ⚠️
The .kext, .efi, .aml, and .efi files in this folder are PLACEHOLDERS.
You MUST download the real files from the official sources listed below.
The config.plist is complete and ready — only SMBIOS serials need filling in.

---

## Your Hardware
- **Laptop:** HP EliteBook 745 G6
- **CPU:** AMD Ryzen 5 Pro 3500U (Zen+/Picasso, 4 cores / 8 threads)
- **iGPU:** AMD Radeon Vega 8
- **BIOS:** R74 Ver. 01.33.00 (10/31/2025)
- **Target OS:** macOS Sequoia 15.x

---

## STEP 1 — Download Real Kexts (replace placeholders)

| Kext | Download | Notes |
|------|----------|-------|
| Lilu.kext | https://github.com/acidanthera/Lilu/releases | Required first |
| VirtualSMC.kext | https://github.com/acidanthera/VirtualSMC/releases | Includes SMC plugins |
| SMCBatteryManager.kext | (inside VirtualSMC release zip) | |
| SMCAMDProcessor.kext | https://github.com/trulyspinach/SMCAMDProcessor/releases | |
| AMDRyzenCPUPowerManagement.kext | https://github.com/trulyspinach/SMCAMDProcessor/releases | Same zip as above |
| AmdTscSync.kext | https://github.com/ChefKissInc/AmdTscSync/releases | |
| NootedRed.kext | https://github.com/ChefKissInc/NootedRed/releases | Vega 8 iGPU ✅ |
| AppleALC.kext | https://github.com/acidanthera/AppleALC/releases | |
| AppleMCEReporterDisabler.kext | https://github.com/acidanthera/bugtracker/files/3703498/AppleMCEReporterDisabler.kext.zip | |
| ECEnabler.kext | https://github.com/1Revenger1/ECEnabler/releases | |
| NVMeFix.kext | https://github.com/acidanthera/NVMeFix/releases | |
| RestrictEvents.kext | https://github.com/acidanthera/RestrictEvents/releases | |
| VoodooPS2Controller.kext | https://github.com/acidanthera/VoodooPS2/releases | |
| VoodooI2C.kext | https://github.com/VoodooI2C/VoodooI2C/releases | |
| VoodooI2CHID.kext | (inside VoodooI2C release zip) | |
| USBToolBox.kext | https://github.com/USBToolBox/kext/releases | |
| UTBDefault.kext | (inside USBToolBox release zip) | Temp - replace after mapping |
| itlwm.kext | https://github.com/OpenIntelWireless/itlwm/releases | Intel Wi-Fi |
| HeliPort.app | https://github.com/OpenIntelWireless/HeliPort/releases | Wi-Fi menu app |

---

## STEP 2 — Download OpenCore + Drivers

| File | Download |
|------|----------|
| OpenCorePkg (BOOTx64.efi + OpenCore.efi + Drivers) | https://github.com/acidanthera/OpenCorePkg/releases |
| HfsPlus.efi | Inside OpenCorePkg zip → Drivers/HfsPlus.efi |
| OpenRuntime.efi | Inside OpenCorePkg zip |
| OpenCanopy.efi | Inside OpenCorePkg zip |

---

## STEP 3 — Download + Compile ACPI SSDTs

Use SSDTTime to auto-generate your SSDTs from YOUR laptop's ACPI tables.

1. Download SSDTTime: https://github.com/corpnewt/SSDTTime
2. Boot into Windows on the EliteBook
3. Run SSDTTime.bat and choose:
   - Option 4: SSDT-EC
   - Option 5: SSDT-USBX
   - Option 7: SSDT-HPET
   - Option 8: SSDT-PNLF (for brightness)
   - Option A: SSDT-XOSI (for trackpad)
4. For SSDT-PLUG-ALT: manually get from https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PLUG-ALT.aml
5. Place all .aml files in EFI/OC/ACPI/

---

## STEP 4 — Generate SMBIOS (REQUIRED)

Open config.plist and fill in these fields:

1. Download GenSMBIOS: https://github.com/corpnewt/GenSMBIOS
2. Run: python3 GenSMBIOS.py → Choose option 3 → Type: iMac20,1
3. Copy the generated values into config.plist:
   - SystemSerialNumber → replace REPLACE_WITH_YOUR_SERIAL
   - MLB              → replace REPLACE_WITH_YOUR_MLB
   - SystemUUID       → replace REPLACE_WITH_YOUR_UUID
   - ROM              → your Ethernet MAC address (no colons, hex encoded)

Use ProperTree to edit plist: https://github.com/corpnewt/ProperTree

---

## STEP 5 — BIOS Settings (HP EliteBook 745 G6)

Press F10 at boot to enter BIOS:

```
Security → Secure Boot → DISABLED
Advanced → Boot Options → Fast Boot → DISABLED
Advanced → Boot Options → UEFI Boot Order → USB first
Advanced → System Options → Virtualization Technology → can leave ON
Security → TPM → DISABLED
Advanced → Device Configurations → Wake on LAN → DISABLED
```

---

## STEP 6 — Install macOS Sequoia

1. Download macOS Sequoia using gibMacOS: https://github.com/corpnewt/gibMacOS
2. Create USB installer with createinstallmedia
3. Copy this EFI folder to the USB EFI partition (mount with MountEFI)
4. **IMPORTANT:** Keep NootedRed.kext DISABLED (Enabled=false) during installation
5. Boot from USB, select "Install macOS Sequoia"
6. After installation completes: mount EFI, set NootedRed Enabled=true
7. Reboot and enjoy GPU acceleration ✅

---

## STEP 7 — USB Mapping (do this after install)

1. Boot into Windows
2. Download USBToolBox: https://github.com/USBToolBox/tool
3. Run USBToolBox.exe and map all ports
4. Export UTBMap.kext
5. Replace UTBDefault.kext with your generated UTBMap.kext in EFI/OC/Kexts/
6. Update config.plist to reference UTBMap.kext instead of UTBDefault.kext

---

## Working / Not Working

| Feature | Status |
|---------|--------|
| CPU (Ryzen 5 Pro 3500U) | ✅ Works |
| Vega 8 GPU acceleration | ✅ Works (NootedRed) |
| Battery | ✅ Works |
| Keyboard | ✅ Works |
| Trackpad (I2C) | ✅ Works |
| Audio (ALC285) | ✅ Try layout-id 13, 21, or 66 |
| Intel AX200 Wi-Fi | ✅ Works (itlwm + HeliPort) |
| Bluetooth | ⚠️ Partial |
| Sleep/Wake | ⚠️ May need tweaking |
| HDMI Out | ⚠️ Test with NootedRed |
| Fingerprint Reader | ❌ Never works on hackintosh |
| AirDrop / Handoff | ❌ Intel Wi-Fi limitation |

---

## Audio Fix
If no audio after install, try different layout-ids in boot-args:
Change `alcid=13` to `alcid=21`, or `alcid=66`, or `alcid=3`

---

## If Black Screen on Boot
- Make sure NootedRed is DISABLED during install
- Use iMac20,1 SMBIOS (already set)
- Add `-nootedredinit` to boot-args (already set)

---

## Resources
- Dortania AMD Guide: https://dortania.github.io/OpenCore-Install-Guide/AMD/zen.html
- AMD-OSX Forum: https://forum.amd-osx.com
- NootedRed Guide: https://chefkiss.dev/applehax/nootedred/
- AMD Vanilla Patches: https://github.com/AMD-OSX/AMD_Vanilla
