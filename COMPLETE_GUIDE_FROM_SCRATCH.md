# Complete STM32 Development Guide: From CubeMX to Debug

## 📋 Table of Contents
1. Create Project in STM32CubeMX
2. Generate Code with CMake Support
3. Open in VS Code with STM32CubeIDE Extension
4. Create Launch Configuration
5. Install and Configure OpenOCD
6. Set Up Cortex-Debug Extension
7. Your First Debug Session

---

## PART 1: Create Project in STM32CubeMX

### Step 1.1: Open STM32CubeMX

1. Open STM32CubeMX (installed on your computer)
2. Click **File** → **New Project**

### Step 1.2: Select Your Microcontroller

1. In the search box, search for your microcontroller
2. **Example:** Type `STM32F401RE` or `STM32F401CC`
3. Click on the microcontroller from the list
4. Click **Start Project**

**A new project window opens with a chip diagram**

### Step 1.3: Configure Your Hardware (Optional)

This is where you:
- Set which pins are GPIO, UART, SPI, I2C, ADC, etc.
- Configure clock frequencies
- Enable interrupts
- Set up timers

**For now:** Just use defaults (we'll skip complex hardware config)

### Step 1.4: Configure Project Settings

1. Click **Project** (top menu) → **Settings**

**In the Project Settings window:**

#### **Name Your Project**
- **Project Name:** Enter a name (e.g., `my_stm32_project`)
- **Project Location:** Choose where to save (e.g., `C:\Projects\STM32`)

#### **Select Toolchain (CRITICAL)**

Under "Toolchain / IDE", you have options:
- ❌ **STM32CubeIDE** - Uses built-in IDE (don't select this if using VS Code)
- ❌ **Keil uVision** - Different IDE
- ❌ **IAR EW ARM** - Different IDE
- ✅ **Makefile** - Generic makefile (use this)
- ✅ **CMake** - Modern build system (use this for VS Code)
- ✅ **STM32CubeIDE with CMake** - Hybrid (good option)

**SELECT: "CMake"** (or "STM32CubeIDE with CMake")

**Why?**
- Works perfectly with VS Code
- Industry standard build system
- Easy to customize
- Portable across platforms

#### **Select Code Generator**

Under "Code Generator" section:
- **Project Structure:** Select `Standard` or `Flat` (doesn't matter much)
- **Copy only the necessary library files** → Check this (saves space)
- **Generate peripheral initialization as a pair of '.c/.h' files** → Check this
- **Delete previous generated files when not re-generated** → Uncheck (safer)
- **Use HAL or LL drivers** → Select **HAL** (easier for beginners)

#### **Click OK**

---

## PART 2: Generate Code in CubeMX

### Step 2.1: Configure Your Hardware (If Needed)

In the main CubeMX window:

**Left sidebar options:**
- **System Core** → Configure clocks, system setup
- **Analog** → ADC, DAC configuration
- **Connectivity** → UART, SPI, I2C, USB
- **Timers** → PWM, Timer setup
- **Additional Software** → Middleware, libraries

**For this guide:** Leave everything as default

### Step 2.2: Generate Code

1. **Project** (top menu) → **Generate Code** (or Ctrl+S)
2. A dialog appears: "Do you want to generate code?"
3. Click **YES**
4. Wait... (generates all files)

**What was generated:**
```
my_stm32_project/
├── Core/
│   ├── Inc/          ← Header files (.h)
│   └── Src/          ← Source code (.c)
├── Drivers/          ← HAL library
├── cmake/            ← CMake build scripts
├── CMakeLists.txt    ← Build instructions
├── CMakePresets.json ← Debug/Release config
├── startup_*.s       ← Startup code
├── *_FLASH.ld        ← Linker script (memory layout)
└── my_stm32_project.ioc ← CubeMX project file
```

### Step 2.3: Close CubeMX

1. **File** → **Exit** (or close the window)
2. Your project folder now has all the files

---

## PART 3: Install VS Code Extensions

### Step 3.1: Install Cortex-Debug Extension

1. Open **VS Code**
2. Click **Extensions** icon (left sidebar, looks like 4 squares)
3. Search: `Cortex-Debug`
4. Click the one by **mcu-debug**
5. Click **Install**

### Step 3.2: Install STM32CubeIDE Extension (Optional but Recommended)

1. Search: `STM32`
2. Install **STM32CubeIDE** by STMicroelectronics
3. This adds syntax highlighting and IntelliSense

### Step 3.3: Install CMake Tools Extension (Recommended)

1. Search: `CMake Tools`
2. Install by **Microsoft**
3. This helps with building

### Step 3.4: Install C/C++ Extension (Recommended)

1. Search: `C/C++`
2. Install by **Microsoft**
3. This adds code editing features

---

## PART 4: Open Project in VS Code

### Step 4.1: Open Your Project

1. Open **VS Code**
2. **File** → **Open Folder**
3. Navigate to your project folder (e.g., `C:\Projects\STM32\my_stm32_project`)
4. Click **Select Folder**

**VS Code opens the project:**
- Left sidebar shows file tree
- You can see `CMakeLists.txt`, `Core/`, `Drivers/`, etc.

### Step 4.2: Configure CMake (First Time Only)

1. **VS Code command palette:** Press **Ctrl+Shift+P**
2. Type: `CMake: Select a Kit`
3. Select your ARM toolchain:
   - Look for `arm-none-eabi` in the list
   - If not found, you may need to install ARM GCC
4. Select **GCC arm-none-eabi** (or similar)

### Step 4.3: Configure Build Type

1. **Command palette:** Ctrl+Shift+P
2. Type: `CMake: Select Variant`
3. Choose **Debug** (for development/debugging)
4. Or **Release** (for production)

### Step 4.4: Build the Project

1. **Command palette:** Ctrl+Shift+P
2. Type: `CMake: Build`
3. A terminal opens and shows:
   ```
   [0/X] Re-linking...
   [1/X] Linking C executable ...
   [X/X] Built target my_stm32_project
   ```

**Success = No errors + "Built target my_stm32_project"**

---

## PART 5: Install and Configure OpenOCD

### Step 5.1: Download OpenOCD

OpenOCD is the tool that communicates with your ST-Link debugger.

**Download:**
1. Visit: https://github.com/xpack-dev-tools/openocd-xpack/releases
2. Download the latest Windows x64 ZIP file
   - Example: `xpack-openocd-0.12.0-7-win32-x64.zip` (about 3 MB)
3. Save to: `C:\tools\`

### Step 5.2: Extract OpenOCD

1. Right-click the ZIP file
2. **Extract All** → **Extract**
3. You get a folder: `xpack-openocd-0.12.0-7`
4. Inside this folder is: `bin\openocd.exe`

### Step 5.3: Add OpenOCD to Windows PATH

**Why?** So you can run `openocd` from anywhere in the terminal

**Steps (Administrator PowerShell):**
1. Right-click **PowerShell**
2. Select **Run as Administrator**
3. Paste this command (all one line):
   ```powershell
   [Environment]::SetEnvironmentVariable("Path", "$env:Path;C:\tools\xpack-openocd-0.12.0-7\bin", "User")
   ```
4. Press Enter
5. Close PowerShell

### Step 5.4: Verify OpenOCD Installation

1. Open a new **PowerShell** (normal user, not admin)
2. Type: `openocd --version`
3. You should see:
   ```
   xPack Open On-Chip Debugger 0.12.0+dev
   Licensed under GNU GPL v2
   ```

**If not found:**
- Restart VS Code and PowerShell
- The PATH changes take time to apply

---

## PART 6: Install ARM Toolchain (If Not Already Installed)

The ARM compiler converts your C code to machine code.

### Option A: Via PlatformIO (Easiest)

If you already have PlatformIO installed with an ARM project, it's already there:
- Location: `C:\Users\<YourName>\.platformio\packages\toolchain-gccarmnoneeabi\bin`

### Option B: Install Standalone

1. Download from: https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads
2. Choose: `arm-gnu-toolchain-13.2.Rel1-mingw-w64-i686-arm-none-eabi.exe`
3. Run installer
4. Select **Add to PATH** during installation
5. Installation adds `arm-none-eabi-gcc` to PATH

### Step 6.5: Verify ARM Toolchain

1. Open **PowerShell**
2. Type: `arm-none-eabi-gcc --version`
3. You should see version info

---

## PART 7: Create Launch Configuration

The launch configuration tells VS Code how to debug (which debugger to use, how to flash, etc.)

### Step 7.1: Create .vscode Folder

1. In your project folder (in VS Code)
2. If `.vscode` folder doesn't exist, create it:
   - Right-click project name
   - **New Folder**
   - Name it `.vscode`

### Step 7.2: Create launch.json

1. Press **Ctrl+Shift+D** (or click Debug icon on left)
2. Click **Create a launch.json file**
3. Select **Cortex-Debug** from the dropdown
4. A file opens: `.vscode/launch.json`

### Step 7.3: Configure launch.json

Replace the entire contents with:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Cortex Debug",
            "type": "cortex-debug",
            "request": "launch",
            "servertype": "openocd",
            "cwd": "${workspaceFolder}",
            "executable": "${workspaceFolder}/build/Debug/<YOUR_PROJECT_NAME>",
            "preLaunchTask": "build",
            "armToolchainPath": "${config:cortex-debug.armToolchainPath}",
            "openocdPath": "${config:cortex-debug.openocdPath}",
            "searchDir": [
                "${workspaceFolder}"
            ],
            "configFiles": [
                "interface/stlink.cfg",
                "target/stm32f4x.cfg"
            ],
            "device": "STM32F401xE",
            "runToEntryPoint": "main",
            "showDevDebugOutput": false,
            "logging": {
                "level": "warn",
                "showTimestamps": true
            }
        }
    ]
}
```

**IMPORTANT: Change these parts:**
- Replace `<YOUR_PROJECT_NAME>` with your actual project name
  - Example: If project is `my_stm32_project`, put `my_stm32_project`
  - Look in `build/Debug/` to find the executable name
- Replace `STM32F401xE` with your chip:
  - `STM32F4` for STM32F4 series
  - `STM32L0` for STM32L0 series
  - etc.
- Replace `stm32f4x.cfg` with correct target config:
  - `stm32f1x.cfg` for F1 series
  - `stm32l0.cfg` for L0 series
  - etc.

### Step 7.4: Create settings.json

1. In `.vscode` folder, create file: `settings.json`
2. Add:

```json
{
    "cortex-debug.openocdPath": "C:\\tools\\xpack-openocd-0.12.0-7\\bin\\openocd.exe",
    "cortex-debug.armToolchainPath": "C:\\<PATH_TO_ARM_GCC>\\bin",
    "cortex-debug.searchDir": [
        "${workspaceFolder}"
    ],
    "cmake.cmakePath": "cmake",
    "cmake.preferredGenerators": [
        "Ninja"
    ]
}
```

**Change:**
- `C:\\<PATH_TO_ARM_GCC>\\bin` to your actual ARM GCC path
  - Example: `C:\\Users\\shreeshrajtripathi\\.platformio\\packages\\toolchain-gccarmnoneeabi\\bin`

### Step 7.5: Create tasks.json

1. In `.vscode` folder, create file: `tasks.json`
2. Add:

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "cmake",
            "args": [
                "--build",
                "build/Debug"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "problemMatcher": ["$gcc"]
        }
    ]
}
```

---

## PART 8: Use OpenOCD (Manual Testing)

OpenOCD is used automatically during debugging, but you can test it manually.

### Step 8.1: Test OpenOCD Connection

1. Open **PowerShell**
2. Navigate to project folder: `cd C:\Projects\STM32\my_stm32_project`
3. Type:
   ```powershell
   openocd -f interface/stlink.cfg -f target/stm32f4x.cfg
   ```
4. You should see:
   ```
   Open On-Chip Debugger 0.12.0
   ...
   stm32f4x.cpu: hardware has 6 breakpoints, 4 watchpoints
   Info : Listening on port 3333 for gdb connections
   ```

**What this means:**
- OpenOCD connected to ST-Link
- OpenOCD is listening on port 3333 for GDB debugger
- Your board is detected

### Step 8.2: Stop OpenOCD

Press **Ctrl+C** in the PowerShell

---

## PART 9: Set Up Cortex-Debug Extension

The Cortex-Debug extension controls the debugger from VS Code.

### Step 9.1: Configuration Already Done

If you created `launch.json` and `settings.json` (Part 7), you're done!

### Step 9.2: Verify Settings

Open `.vscode/settings.json` and check:
1. `openocdPath` points to `openocd.exe`
2. `armToolchainPath` points to ARM GCC `bin` folder

---

## PART 10: Your First Debug Session

### Step 10.1: Connect Hardware

1. Plug **ST-Link USB** into your PC
2. Connect **ST-Link cables** to your board:
   - SWDIO → SWDIO on board
   - SWCLK → SWCLK on board
   - GND → GND on board
   - 3.3V → 3.3V on board (if powered from ST-Link)
3. Power on your board

### Step 10.2: Build Project

1. **Command palette:** Ctrl+Shift+P
2. Type: `CMake: Build`
3. Wait for: `Built target my_stm32_project`

### Step 10.3: Start Debugging

1. Press **F5** (or Debug icon → Start Debugging)
2. VS Code will:
   - Start OpenOCD in background
   - Upload your program to the board
   - Stop at `main()` function (first line of your code)
3. Left panel shows: **VARIABLES**, **WATCH**, **CALL STACK**

### Step 10.4: Debug Your Code

**Basic commands:**

| Action | Key |
|--------|-----|
| Step Over (execute line) | F10 |
| Step Into (enter function) | F11 |
| Step Out (exit function) | Shift+F11 |
| Continue | F5 |
| Stop Debug | Shift+F5 |

**Set Breakpoint:**
1. Click on line number (left side)
2. Red dot appears
3. Execution pauses there

**View Variables:**
- Left panel → VARIABLES
- Expand sections to see values
- Hover over variables in code

### Step 10.5: Stop Debugging

1. Press **Shift+F5**
2. Debugger stops
3. OpenOCD closes automatically

---

## PART 11: Typical Development Workflow

**This is what you'll do every time:**

1. **Edit Code**
   - Open `Core/Src/main.c`
   - Make changes
   - Ctrl+S to save

2. **Build**
   - Ctrl+Shift+P → CMake: Build
   - Or Ctrl+Shift+B (if tasks.json configured)

3. **Debug**
   - Press F5
   - Step through with F10/F11
   - Check variables

4. **If You Change Hardware**
   - Double-click `.ioc` file
   - Configure in CubeMX GUI
   - Generate code
   - Back to VS Code (auto-reloads)
   - Build and debug

---

## PART 12: Troubleshooting

### Problem: "Cannot find executable"

**Error:** Launch config can't find the compiled binary

**Solution:**
1. Check `build/Debug/` folder exists
2. Check executable name in `launch.json` matches actual file
3. Example actual file: `my_stm32_project.elf`
4. Your config should say: `"executable": "${workspaceFolder}/build/Debug/my_stm32_project.elf"`

### Problem: "Debugger won't connect"

**Steps to fix:**
1. Verify ST-Link plugged into USB
2. Check cables connected (SWDIO, SWCLK, GND)
3. Check board is powered
4. Run in terminal: `openocd --version` (should work)
5. Run in terminal: `arm-none-eabi-gcc --version` (should work)

### Problem: "CMake: build failed"

**Check errors in terminal:**
1. Look for red error messages
2. Common: Missing semicolon, undefined variable, wrong #include
3. Fix the error in your C code
4. Try building again

### Problem: "Port 3333 already in use"

OpenOCD from previous session still running.

**Solution:**
1. Open PowerShell
2. Type: `taskkill /IM openocd.exe /F`
3. Try debugging again

### Problem: "Breakpoints not working"

**Checklist:**
1. Built in Debug mode (not Release)?
2. Red dot appears when you click line number?
3. Does it say "Breakpoint" in left panel?
4. Try stopping (Shift+F5) and starting (F5) again

---

## Quick Reference: File Locations

```
C:\tools\xpack-openocd-0.12.0-7\bin\openocd.exe
    ↑ OpenOCD executable
    
C:\Users\<YourName>\.platformio\packages\toolchain-gccarmnoneeabi\bin\
    ↑ ARM GCC compiler
    
C:\Projects\STM32\my_stm32_project\
├── .vscode\
│   ├── launch.json     ← Debug configuration
│   ├── settings.json   ← Tool paths
│   └── tasks.json      ← Build tasks
├── Core\Src\main.c     ← Your code
├── CMakeLists.txt      ← Build instructions
├── CMakePresets.json   ← Debug/Release modes
└── build\Debug\
    └── my_stm32_project.elf  ← Compiled executable
```

---

## What Each Tool Does (Summary)

| Tool | Purpose | How It Works |
|------|---------|-------------|
| **STM32CubeMX** | Hardware config GUI | You configure → generates CMakeLists.txt + source code |
| **CMake** | Build system | Reads CMakeLists.txt → runs compiler → creates .elf file |
| **ARM GCC** | C compiler | Converts your C code → machine code for STM32 |
| **OpenOCD** | Debugger bridge | Talks to ST-Link → uploads program → stops at breakpoints |
| **ST-Link** | USB dongle | Physically connects PC → your board |
| **GDB** | Debugger engine | Debugs the program (runs in background) |
| **Cortex-Debug** | VS Code plugin | Controls OpenOCD + GDB from VS Code |

---

## You're Ready!

You now have:
- ✅ Project created in CubeMX with CMake
- ✅ Project opened in VS Code
- ✅ OpenOCD installed and configured
- ✅ ARM toolchain installed
- ✅ launch.json created
- ✅ First debug session completed

**Next time: Just press F5 and debug!**
