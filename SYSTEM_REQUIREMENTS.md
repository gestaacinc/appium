# 📱 Appium Day 1: Windows 11 Setup Guide
> **Target:** Windows 11 | **Node:** v20.19.5+ | **IDE:** VS Code | **Background:** Java/Selenium
> **Goal:** Zero to "Hello World" on an Android Emulator in under 1 hour.

---

## 🛑 Phase 0: Pre-Flight Check
Before starting, verify your package manager is active. Open **PowerShell** and run:

```powershell
winget --version
```

*   **If you see a version number:** Proceed to **Phase 1 (Preferred Method)**.
*   **If you see an error:** Skip to **Phase 1 (Manual Method)**.

---

## 📦 Phase 1: Installation (Preferred Method)
We use `winget` to automate the heavy lifting. Open **PowerShell as Administrator**:

### 1. Install OpenJDK 17
Appium works best with Java 17.
```powershell
winget install -e --id Microsoft.OpenJDK.17
```

### 2. Install Android Studio
Required for SDK management and Emulators.
```powershell
winget install -e --id Google.AndroidStudio
```

### 3. Install VS Code Java Extensions
Required for Maven/Gradle support.
```powershell
code --install-extension vscjava.vscode-java-pack
```

---

## 🛠️ Phase 1: Installation (Manual Method - Plan B)
*Only use this if winget failed above.*

*   **Java JDK 17:** Download the `.msi` from [Microsoft Build of OpenJDK 17](https://learn.microsoft.com/en-us/java/openjdk/download).
*   **Android Studio:** Download from [developer.android.com/studio](https://developer.android.com/studio). Run the installer and keep all defaults.

---

## ⚙️ Phase 2: Android SDK Configuration
1.  **Open Android Studio.**
2.  On the Welcome screen, click **More Actions > SDK Manager**.
3.  **Note the Android SDK Location** path at the top.
    *   *Default:* `C:\Users\YourUsername\AppData\Local\Android\Sdk`
    *   📌 **Copy this path.** You will need it for Phase 3.
4.  In the **SDK Tools** tab, check the following:
    *   [x] Android SDK Build-Tools
    *   [x] Android SDK Command-line Tools (latest)
    *   [x] Android Emulator
    *   [x] Android SDK Platform-Tools
5.  Click **Apply** and wait for the download to finish.

---

## 🔗 Phase 3: Environment Variables (Smart Method)
This script automatically detects your paths to prevent typos. Run this in **PowerShell (Admin)**:

```powershell
# 1. Auto-Detect Paths
$User = $env:USERNAME
$SdkPath = "C:\Users\$User\AppData\Local\Android\Sdk"
$JavaFolder = Get-ChildItem "C:\Program Files\Microsoft" -Filter "jdk-17*" | Select-Object -First 1

if (-not $JavaFolder) {
    Write-Host "❌ CRITICAL: Java 17 not found. Check Phase 1." -ForegroundColor Red
    return
}
$JavaPath = $JavaFolder.FullName

# 2. Set Environment Variables
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", $JavaPath, "User")
if (Test-Path $SdkPath) {
    [System.Environment]::SetEnvironmentVariable("ANDROID_HOME", $SdkPath, "User")
}

# 3. Update System Path
$CurrentPath = [System.Environment]::GetEnvironmentVariable("Path", "User")
$AddPaths = "$JavaPath\bin;$SdkPath\platform-tools;$SdkPath\emulator;$SdkPath\cmdline-tools\latest\bin"

if ($CurrentPath -notmatch "platform-tools") {
    $NewPath = "$AddPaths;" + $CurrentPath
    [System.Environment]::SetEnvironmentVariable("Path", $NewPath, "User")
    Write-Host "✅ SYSTEM PATH UPDATED." -ForegroundColor Green
}
```

> [!IMPORTANT]
> **Restart your Terminal** after running the script above.

---

## 🚀 Phase 4: Appium 2.0 Server & Drivers

### 1. Install Appium Server
```powershell
npm install -g appium
```

### 2. Install the Android Driver
```powershell
appium driver install uiautomator2
```

### 3. Install Appium Doctor (Diagnostic Tool)
```powershell
npm install -g @appium/doctor
```

### 4. Install Appium Inspector
1.  Go to [Appium Inspector Releases](https://github.com/appium/appium-inspector/releases).
2.  Download the `-windows-x64.exe` file.
3.  **Tip:** If you get a "Missing Shortcut" popup, click Cancel. The app is still installed—just search for "Appium Inspector" in your Start Menu.

---

## 🩺 Phase 5: Final Health Check
Run the doctor to verify your setup:
```bash
appium-doctor --android
```
**Success Criteria:**
*   ✅ `ANDROID_HOME` is set.
*   ✅ `JAVA_HOME` is set.
*   ✅ `adb` and `android` exist.

---

## 📱 Phase 6: Launch Your Emulator
1.  Open **Android Studio > Virtual Device Manager**.
2.  Create a new device (e.g., **Pixel 6**).
3.  Select **API 34** (Android 14) and download it.
4.  Press the **Play (▶)** button to boot the phone.

---

## 🏁 Phase 7: The "Hello World" Test
1.  Start the Appium Server in your terminal: `appium`
2.  Open **Appium Inspector**.
3.  Set **Remote Host** to `127.0.0.1` and **Remote Port** to `4723`.
4.  Paste this into the **JSON Representation**:
```json
{
  "platformName": "Android",
  "appium:automationName": "UiAutomator2",
  "appium:deviceName": "emulator-5554"
}
```
5.  Click **Start Session**. If you see your phone screen, you are ready! 🎉

---

## 🔧 Troubleshooting: "The Nuclear Option"
If `adb` still fails, force the path into the Registry:
```powershell
$AdbPath = "C:\Users\$env:USERNAME\AppData\Local\Android\Sdk\platform-tools"
$CurrentRegPath = [System.Environment]::GetEnvironmentVariable("Path", "User")
[System.Environment]::SetEnvironmentVariable("Path", "$AdbPath;" + $CurrentRegPath, "User")
```
*(Requires PC Restart after running)*
