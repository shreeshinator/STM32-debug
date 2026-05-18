# Complete STM32 Development Guide for Beginners

## 📖 What is Everything?

### The Tools You Have
1. **VS Code** - Text editor where you write code
2. **CMake** - Build system that tells the compiler how to build your project
3. **ARM GCC Compiler** - Converts your C code into machine code the STM32 understands
4. **OpenOCD** - Tool that communicates with ST-Link debugger
5. **ST-Link** - USB device that programs and debugs your STM32 board
6. **STM32CubeMX** - GUI tool to configure hardware (pins, clocks, peripherals)
7. **GDB** - Debugger that lets you step through code, set breakpoints
8. **Cortex-Debug Extension** - VS Code plugin that controls the debugger

### The Files in Your Project

```
vscblink/
├── vscblink.ioc              ← STM32CubeMX project (hardware config)
├── CMakeLists.txt            ← Build instructions for CMake
├── CMakePresets.json         ← Debug vs Release build settings
├── STM32F401XX_FLASH.ld      ← Memory layout of your chip
├── Core/
│   ├── Inc/
│   │   ├── main.h            ← Function declarations
│   │   └── stm32f4xx_it.h    ← Interrupt handlers
│   └── Src/
│       ├── main.c            ← YOUR CODE GOES HERE
│       ├── stm32f4xx_it.c    ← Interrupt handlers
│       └── system_stm32f4xx.c ← System startup
├── Drivers/                  ← Hardware abstraction layer (HAL)
├── cmake/
│   ├── gcc-arm-none-eabi.cmake  ← Compiler settings
│   └── stm32cubemx/             ← CubeMX generated build stuff
├── build/
│   └── Debug/
│       ├── vscblink.elf      ← Your compiled program
│       └── vscblink.map      ← Memory map
└── .vscode/
    ├── launch.json           ← Debug configuration
    ├── settings.json         ← Tool paths
    └── tasks.json            ← Build tasks
```

---

## 🚀 STEP-BY-STEP: Basic Development Workflow

### **STEP 1: Open Your Project**

1. Open VS Code
2. Click **File** → **Open Folder**
3. Navigate to: `C:\Users\shreeshrajtripathi\Documents\stm32\vscblink`
4. Click **Select Folder**

**What you'll see:**
- Left sidebar shows file structure
- Explorer panel opens
- Terminal at bottom

---

### **STEP 2: Build Your Project**

**Purpose:** Compile C code → Machine code the STM32 understands

**Steps:**
1. Press **Ctrl+Shift+B** (or Run → Run Build Task)
2. A dropdown appears with task options
3. Select **"cmake: build"**
4. Terminal opens at bottom showing:
   ```
   [0/2] Re-linking...
   [1/2] Linking C executable vscblink.elf
   [2/2] Built target vscblink
   ```

**What happened:**
- CMake ran the compiler
- C files were compiled
- Linker combined them into `build/Debug/vscblink.elf`
- This is your program (machine code)

**Success = No errors + "Built target vscblink"**

---

### **STEP 3: Connect Your Hardware**

**Before debugging, you need:**
1. STM32F401 development board
2. ST-Link debugger (or built-in if your board has one)
3. USB cable

**Connection:**
- ST-Link pins:
  - **SWDIO** → SWDIO on board
  - **SWCLK** → SWCLK on board
  - **GND** → GND on board
  - **3.3V** → 3.3V on board (if powered from ST-Link)
- Plug ST-Link USB into your PC
- Power on the board (if separate power supply)

**Verify connection:**
- Open Terminal → Run: `openocd --version`
- Should show: `xPack Open On-Chip Debugger 0.12.0+dev`

---

### **STEP 4: Start Debugging**

**Purpose:** Upload your program to the board and step through it

**Steps:**
1. Press **F5** (or Run → Start Debugging)
2. A dropdown appears with debug options
3. Select **"Cortex Debug - STM32F401"**
4. VS Code will:
   - Rebuild your code
   - Ask for confirmation
   - Upload to your board via ST-Link
   - Stop at `main()` function (first line of your code)

**What you'll see:**
- Left panel shows: VARIABLES, WATCH, CALL STACK
- Code editor highlights current line (yellow background)
- Debug toolbar appears at top with play/pause buttons

---

### **STEP 5: Debug Your Code**

**Keyboard shortcuts:**

| Action | Key | What it does |
|--------|-----|--------------|
| **Step Over** | F10 | Execute current line, don't enter functions |
| **Step Into** | F11 | Enter the function on current line |
| **Step Out** | Shift+F11 | Exit current function, return to caller |
| **Continue** | F5 | Resume execution until next breakpoint |
| **Pause** | Ctrl+Alt+Break | Pause execution (if running) |
| **Stop** | Shift+F5 | Stop debugging |

**Set a Breakpoint:**
1. Click on line number (left side of code)
2. Red dot appears
3. When running, code pauses at that line

**View Variables:**
1. Left panel → VARIABLES
2. Expand sections to see values
3. Hover over variable names in code to see values
4. Right-click variable → Add to Watch

**Example Debug Session:**
```c
void main() {
    int x = 5;        ← Click here, set breakpoint (red dot)
    int y = 10;
    int sum = x + y;  ← Press F5, execution pauses here
                      ← Left panel shows: x=5, y=10
    while(1);         ← Press F10, goes to next line
}                     ← Press Shift+F5 to stop debugging
```

---

## 📝 STEP-BY-STEP: Editing Your Code

### **STEP 1: Open main.c**

1. In Explorer (left sidebar)
2. Navigate to: **Core** → **Src** → **main.c**
3. Double-click to open

---

### **STEP 2: Find main() Function**

1. Press **Ctrl+F** to search
2. Type: `void main` or look for `int main`
3. You'll see something like:
   ```c
   int main(void)
   {
     // your code here
     while(1)
     {
     }
   }
   ```

---

### **STEP 3: Add Your Code**

**Example: Blink an LED**

```c
int main(void)
{
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  
  while(1)
  {
    HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);  // Toggle LED
    HAL_Delay(500);                         // Wait 500ms
  }
}
```

**Steps to add:**
1. Click in the `while(1)` loop
2. Type the code above
3. Press **Ctrl+S** to save
4. Press **Ctrl+Shift+B** → select "cmake: build"
5. Wait for "Built target vscblink"
6. Press **F5** to debug
7. Your code runs on the board!

---

## 🔧 STEP-BY-STEP: Change Hardware Configuration

### **STEP 1: Open CubeMX Project**

1. In Explorer, find **vscblink.ioc** file
2. Double-click it
3. STM32CubeMX GUI opens

---

### **STEP 2: Configure Pins**

1. Left sidebar → **Pinout & Configuration**
2. Click the pin you want on the microcontroller diagram
3. Select what it should do (GPIO, UART, I2C, etc.)
4. Configure settings in the right panel

**Example: Enable LED on PA5**
1. Find PA5 on the diagram
2. Click it
3. Select **GPIO_Output**
4. Set label as **LD2**

---

### **STEP 3: Configure Clocks**

1. Left sidebar → **Clock Configuration**
2. Adjust PLL, system clock speed
3. It will show if the configuration is valid

---

### **STEP 4: Generate Code**

1. Click **File** → **Generate Code** (or Ctrl+S)
2. It generates:
   - `Core/Src/main.c` (basic structure)
   - `Core/Src/stm32f4xx_it.c` (interrupt handlers)
   - `CMakeLists.txt` updates
3. Close CubeMX or minimize it

**Important:** CubeMX overwrites the main loop! Don't put your code directly in generated `main()`. Use this pattern:

```c
int main(void) {
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  
  // YOUR CODE HERE (won't be overwritten)
  while(1) {
    // Your loop code
  }
}
```

---

### **STEP 5: Rebuild and Test**

1. Back to VS Code
2. Press **Ctrl+Shift+B** → "cmake: build"
3. If errors, fix them
4. Press **F5** to test on the board

---

## 🛠️ STEP-BY-STEP: Build Different Versions

### **Debug Build (Optimized for Debugging)**

1. Press **Ctrl+Shift+B**
2. Select **"cmake: build"**
3. Creates: `build/Debug/vscblink.elf`
4. Settings: Slow, Large, Full debug info
5. Use this for: Development, debugging

---

### **Release Build (Optimized for Speed)**

**First time only:**
1. Open file: `CMakePresets.json`
2. Change `"default"` section preset from `"Debug"` to `"Release"`
3. Save

**Then:**
1. Press **Ctrl+Shift+B**
2. Select **"cmake: build"**
3. Creates: `build/Release/vscblink.elf`
4. Settings: Fast, Small, Minimal debug info
5. Use this for: Production, deployment

---

### **Clean Build (Remove all old files and rebuild)**

1. Press **Ctrl+Shift+B**
2. Select **"cmake: clean"** (removes build/ folder)
3. Select **"cmake: build"** (rebuilds everything)
4. Use this when: Strange compiler errors, outdated build

---

## 🐛 STEP-BY-STEP: Troubleshooting

### **Problem: "Build Failed"**

**Check the errors in Terminal:**
1. Look at the red error messages
2. Common causes:
   - **Missing semicolon** → Add `;` to end of line
   - **Undefined function** → Check spelling
   - **Wrong header** → Add `#include "something.h"`

**Solution:**
1. Fix the error in the code
2. Save (**Ctrl+S**)
3. Rebuild (**Ctrl+Shift+B** → "cmake: build")

---

### **Problem: "Debugger won't connect"**

**Check:**
1. Is ST-Link plugged into USB? (Check Device Manager)
2. Are cables connected? (SWDIO, SWCLK, GND)
3. Is board powered? (Check LED)
4. Is OpenOCD installed? (Open terminal → `openocd --version`)

**Fix:**
1. Unplug ST-Link
2. Check all cables
3. Plug in ST-Link again
4. Press F5 again

---

### **Problem: "Port already in use"**

OpenOCD is still running from a previous debug session.

**Solution:**
1. Open Terminal
2. Run: `taskkill /IM openocd.exe /F`
3. Try debugging again (F5)

---

### **Problem: Code compiles but doesn't upload**

**Check:**
1. Breakpoint at main? (Yellow pause icon)
2. Does Variables panel show values?
3. Are you clicking "Debug Anyway" when prompted?

**If it built but didn't upload:**
1. Stop debug (Shift+F5)
2. Disconnect ST-Link
3. Wait 2 seconds
4. Plug ST-Link back in
5. Try F5 again

---

## 📚 STEP-BY-STEP: Common Tasks

### **Task: Read a Sensor Value**

```c
int main(void) {
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  MX_ADC1_Init();  // Enable ADC in CubeMX
  
  while(1) {
    uint32_t adc_value = HAL_ADC_GetValue(&hadc1);
    // Now adc_value contains sensor reading (0-4095 for 12-bit)
  }
}
```

**Steps:**
1. In CubeMX: Enable ADC1, configure pin
2. Generate code
3. Add the code above
4. Build and debug
5. In debug: Check `adc_value` in Variables panel

---

### **Task: Print Debug Messages**

```c
#include <stdio.h>

int main(void) {
  // ... init code ...
  
  while(1) {
    printf("Hello from STM32!\n");
    HAL_Delay(1000);
  }
}
```

**To see output:**
1. In Debug Console (bottom panel)
2. Messages appear as program runs
3. Useful for: Checking logic, no breakpoints needed

---

### **Task: Toggle LED**

```c
int main(void) {
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  
  while(1) {
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);   // LED ON
    HAL_Delay(500);
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET); // LED OFF
    HAL_Delay(500);
  }
}
```

**Steps:**
1. In CubeMX: Configure PA5 as GPIO_Output
2. Generate code
3. Add code above
4. Build and debug
5. Watch LED blink every 500ms

---

## 📋 Quick Reference: Buttons & Shortcuts

| What | How |
|------|-----|
| Build | Ctrl+Shift+B |
| Debug | F5 |
| Step Over | F10 |
| Step Into | F11 |
| Step Out | Shift+F11 |
| Continue | F5 |
| Stop Debug | Shift+F5 |
| Pause | Ctrl+Alt+Break |
| Open File | Ctrl+P |
| Search | Ctrl+F |
| Save | Ctrl+S |
| Undo | Ctrl+Z |
| Redo | Ctrl+Y |

---

## 🎯 Your First Complete Debug Session

**Follow this exactly:**

1. **Open VS Code** → File → Open Folder → Select vscblink
2. **Edit code** → Open Core/Src/main.c
3. **Find main()** → Ctrl+F → type "void main" or "int main"
4. **Add code inside while(1):**
   ```c
   while(1) {
     HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
     HAL_Delay(500);
   }
   ```
5. **Save** → Ctrl+S
6. **Build** → Ctrl+Shift+B → Select "cmake: build" → Wait for "Built target"
7. **Connect ST-Link USB** to PC
8. **Connect ST-Link to board** (SWDIO, SWCLK, GND, 3.3V)
9. **Debug** → Press F5 → Select "Cortex Debug - STM32F401"
10. **Watch:** Code stops at main(), left panel shows variables
11. **Step through:** Press F10 repeatedly to execute line by line
12. **Stop:** Press Shift+F5

**Done!** You've debugged code on the STM32! 🎉

---

## 📖 When You Get Stuck

1. **Check the error message** - Read it carefully, it usually tells you the problem
2. **Google the error** - Most errors have been solved before
3. **Check the file** - Does the file exist? Is the path correct?
4. **Restart VS Code** - Fixes weird issues 90% of the time
5. **Rebuild** - Ctrl+Shift+B → "cmake: rebuild"
6. **Check cables** - 50% of debug issues are cable problems

---

**YOU'RE READY! Start with your first debug session above.** 🚀
