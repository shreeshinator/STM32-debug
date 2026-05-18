# STM32 Debug Environment Setup Guide

## System Configuration

### ARM Toolchain Location
- **Found at:** `C:\Users\shreeshrajtripathi\.platformio\packages\toolchain-gccarmnoneeabi\bin`
- **Already configured** in VS Code settings

### Required Tools
- ✅ **ARM GCC Compiler** - Located in PlatformIO
- ✅ **CMake** - Installed at `C:\Program Files\CMake`
- ✅ **Ninja** - Should be installed (used by CMakePresets.json)
- ❌ **OpenOCD** - **Needs to be installed** for debugging

### Environment Variables
Create these system environment variables:

1. **TOOLCHAIN_PATH** (Optional, already hardcoded)
   - Value: `C:\Users\shreeshrajtripathi\.platformio\packages\toolchain-gccarmnoneeabi`

2. **CUBE_BUNDLE_PATH** (Optional)
   - Value: Path to STM32CubeIDE installation (if using IDE clangd)

## OpenOCD Installation

OpenOCD is required for ST-Link debugging. Install it using one of:

### Option 1: Using Chocolatey (Recommended)
```powershell
choco install openocd
```

### Option 2: Download Binary
- Download from: https://openocd.org/
- Extract to a location in your PATH

### Option 3: Build from Source
```bash
git clone https://github.com/openocd-org/openocd.git
cd openocd
./bootstrap
./configure --enable-stlink
make
make install
```

After installation, verify with:
```powershell
openocd --version
```

## Verify Setup

Run the build task to test:
1. Press `Ctrl+Shift+B` → Select "cmake: build"
2. Build should succeed and create `build/Debug/vscblink.elf`

## Debug Session

1. Connect ST-Link debugger to your board
2. Press `F5` or go to Run → Start Debugging
3. Select "Cortex Debug - STM32F401" configuration
4. Debugging should start automatically

## Notes

- The debug configuration uses OpenOCD with ST-Link interface
- OpenOCD config files: `interface/stlink.cfg` and `target/stm32f4x.cfg`
- The SVD file path is empty - add it for peripheral register viewing
- Build artifacts: `build/Debug/vscblink.elf` (executable)
