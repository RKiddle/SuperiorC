# 🔍 Bluetooth Device Scanner in C (Windows)

This program scans for discoverable Bluetooth devices on a Windows PC using the Windows Bluetooth API.

---

## 📦 Prerequisites

Before compiling and running this program, ensure the following:

- ✅ **Windows SDK** is installed  
  - You can install it via [Visual Studio Installer](https://visualstudio.microsoft.com/downloads/) → Modify → Individual Components → *Windows 11 SDK*
  - Or download directly from [Microsoft's SDK page](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/)
- ✅ **Compiler**: Use Microsoft Visual Studio or `cl.exe` from the Developer Command Prompt
- ✅ **Link against** `bthprops.lib` (Bluetooth API library)

### 🛠️ Compile Command

```bash
cl bluetooth_scan.c /link bthprops.lib
```

---

## 📄 Code Explanation

```c
#include <windows.h>
#include <bluetoothapis.h>
#include <stdio.h>
```

- `windows.h`: Core Windows API functions
- `bluetoothapis.h`: Bluetooth-specific functions and structures
- `stdio.h`: Standard I/O for printing output

```c
#pragma comment(lib, "bthprops.lib")
```

- Tells the linker to include the Bluetooth library `bthprops.lib`

---

### 🔧 Setup Structures

```c
BLUETOOTH_DEVICE_SEARCH_PARAMS searchParams = { 0 };
BLUETOOTH_DEVICE_INFO deviceInfo = { 0 };
HBLUETOOTH_DEVICE_FIND hFind = NULL;
```

- `searchParams`: Defines how the device search should behave
- `deviceInfo`: Holds details about each found device
- `hFind`: Handle used to iterate through found devices

---

### ⚙️ Configure Search Parameters

```c
searchParams.dwSize = sizeof(BLUETOOTH_DEVICE_SEARCH_PARAMS);
```

- Ensures the structure is correctly sized for the API

```c
searchParams.fReturnAuthenticated = TRUE;
searchParams.fReturnRemembered = TRUE;
searchParams.fReturnUnknown = TRUE;
searchParams.fReturnConnected = TRUE;
searchParams.fIssueInquiry = TRUE;
searchParams.cTimeoutMultiplier = 2;
searchParams.hRadio = NULL;
```

- These flags control which devices are returned:
  - `Authenticated`: Paired devices
  - `Remembered`: Previously seen devices
  - `Unknown`: Devices not categorized
  - `Connected`: Currently connected devices
  - `IssueInquiry`: Actively scan for new devices
- `TimeoutMultiplier`: Inquiry duration (2 × 1.28s = ~2.56s)
- `hRadio`: NULL = use all available Bluetooth radios

---

### 📦 Prepare Device Info Struct

```c
deviceInfo.dwSize = sizeof(BLUETOOTH_DEVICE_INFO);
```

- Sets the size for compatibility with the API

---

### 🔍 Start Device Search

```c
hFind = BluetoothFindFirstDevice(&searchParams, &deviceInfo);
```

- Begins the search and populates `deviceInfo` with the first result

```c
if (hFind == NULL) {
    printf("No Bluetooth devices found or Bluetooth is off.\n");
    return 1;
}
```

- If no devices are found, or Bluetooth is disabled, exit early

---

### 🔁 Iterate Through Devices

```c
do {
    wprintf(L"Found device: %s\n", deviceInfo.szName);
```

- Prints the device name (Unicode string)

```c
    printf("Address: %02X:%02X:%02X:%02X:%02X:%02X\n",
        deviceInfo.Address.rgBytes[5],
        deviceInfo.Address.rgBytes[4],
        deviceInfo.Address.rgBytes[3],
        deviceInfo.Address.rgBytes[2],
        deviceInfo.Address.rgBytes[1],
        deviceInfo.Address.rgBytes[0]);
```

- Formats and prints the Bluetooth MAC address

```c
} while (BluetoothFindNextDevice(hFind, &deviceInfo));
```

- Continues scanning until no more devices are found

---

### 🧹 Cleanup

```c
BluetoothFindDeviceClose(hFind);
```

- Releases the handle and cleans up resources

```c
return 0;
```

- Exit successfully

---

## 📘 Additional Notes

- You can filter devices by class or name prefix for more targeted scanning
- Consider logging results to a file for analysis or classroom use
- For multi-radio systems, use `BluetoothFindFirstRadio()` to target specific adapters

---

Let me know if you'd like this turned into a full GitHub README template or scaffolded for classroom deployment with Thai-English annotations.
