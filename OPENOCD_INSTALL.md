# OpenOCD Installation Guide for Windows

## Option 1: Pre-built Binary (Easiest)

### Step 1: Download OpenOCD
Visit: https://github.com/openocd-org/openocd/releases

Download the latest Windows binary (look for a file like):
- `openocd-x.xx.x-xxx-win64.zip` or
- `OpenOCD-x.xx.x-xxx.exe` (installer)

### Step 2: Extract or Install
If downloaded as ZIP:
1. Extract to a location, e.g.: `C:\tools\openocd`
2. The executable will be in `C:\tools\openocd\bin\openocd.exe`

If downloaded as EXE:
1. Run the installer and follow the prompts
2. Installer will place `openocd.exe` in PATH automatically

### Step 3: Add to PATH (if manual extraction)
If you extracted the ZIP file manually, add to Windows PATH:

**PowerShell (Admin):**
```powershell
[Environment]::SetEnvironmentVariable(
    "Path",
    "$env:Path;C:\tools\openocd\bin",
    [EnvironmentVariableTarget]::User
)
```

Then restart PowerShell for changes to take effect.

### Step 4: Verify Installation
```powershell
openocd --version
```

Expected output: `Open On-Chip Debugger 0.xx.x`

---

## Option 2: Pre-built from Xpack

Alternative source with simpler setup:
https://github.com/xpack-dev-tools/openocd-xpack/releases

Download: `xpack-openocd-x.xx.x-x-win32-x64.zip`

Extract to `C:\tools\xpack-openocd` and add `C:\tools\xpack-openocd\bin` to PATH.

---

## Option 3: Build from Source (Advanced)

Requirements:
- MinGW-w64
- Autotools
- LibUsb

Not recommended unless you need custom features.

---

## Troubleshooting

### "openocd: command not found"
- Restart VS Code and PowerShell after adding to PATH
- Check `$env:Path` includes the OpenOCD bin directory:
  ```powershell
  $env:Path -split ';' | grep -i openocd
  ```

### ST-Link not detected in OpenOCD
- Install libusb drivers from: https://github.com/libusb/libusb/wiki/windows_backend
- Or use ST-Link USB driver from: https://www.st.com/en/development-tools/stsw-stlink009.html

### Cannot find interface/stlink.cfg
The config files are bundled with OpenOCD. If missing, they're at:
`<OpenOCD-dir>/share/openocd/scripts/interface/stlink.cfg`

---

## Next Steps

After installing:
1. Restart VS Code
2. Connect ST-Link debugger to your STM32 board
3. Press F5 to start debugging
