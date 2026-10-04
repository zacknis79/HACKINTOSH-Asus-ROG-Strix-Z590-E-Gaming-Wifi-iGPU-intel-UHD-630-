# HACKINTOSH-SEQUOIA-Asus-ROG-Strix-Z590-E-Gaming-Wifi-iGPU-intel-UHD-630 

Run macos sequoia on Asus z590 motherboard with intel 10th generation Cpu and Graphic (iGPU)

Create your EFI with a tool like OC Simplifier or ....

Install macos without iGPU Acceleration using NVRAM Boot Arg " -igfxvesa ".

After installing macos you can apply the iGPU Acceleration.  

The config.plist include the framebuffer patch for intel UHD 630 Graphics for intel 10th generation.

Also include all the ACPI tables needed for this Mobo and Kexts except intel wifi and bluetooth because i use ethernet and i don't need bluetooth. 

First you need to generate the EDID from Windows for your Monitor. 

It can be 1 to 3 EDID Connector depending on your desktop and monitor 

For the DP --- to HDMI (AAPL00,override-no-connect)


PowerShell as Administrator :

-----------------------------------------Powershell-------------------------------------------------


Get-ChildItem 'HKLM:\SYSTEM\CurrentControlSet\Enum\DISPLAY' -Recurse -ErrorAction SilentlyContinue |
Where-Object { $_.PSChildName -eq 'Device Parameters' } |
ForEach-Object {
    $p = Get-ItemProperty $_.PSPath -Name EDID -ErrorAction SilentlyContinue
    if ($p.EDID) {
        [PSCustomObject]@{
            Path = $_.PSPath
            EDID = ([BitConverter]::ToString($p.EDID) -replace '-','')
        }
    }
} | Format-List

--------------------------------------------------------------------------------------------------------

Paste all the EDID generated in the config.plist exactly in:

DeviceProperties
    add
      PciRoot(0x0)/Pci(0x2,0x0
           AAPL00,override-no-connect                  data               "your first EDID"
           AAPL01,override-no-connect                  data               "your second EDID (if exist)"
           AAPL02,override-no-connect                  data               "your third EDID (if exist)"
           +
           +
           +
           Frambuffer patch in the config.plist

Apply NVRAM Boot Args in the config.plist. 


BIOS SETTINGS :

- Disable Resizable BAR
- enable VT-D
- enable Internal GPU
- Disable Secure Boot and TPM
- Disbale Fast Boot and Wait for f1 ...
- Disable Memory Protection
- Disable intel virtualisation

* if you have a dGPU disable it in the config.plist (i have an RTX 3070 i've disable it in the config.plist)
