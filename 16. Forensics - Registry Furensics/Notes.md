# **Registry Furensics**

---

## **What is the Windows Registry**

* Central **configuration database** for Windows
* Stores:

  * OS settings
  * Installed software
  * User activity
  * Startup programs
  * Hardware & drivers
* Critical artefact in **Windows forensic investigations**

---

## **Registry Hives (On Disk)**

Registry is split into **binary hive files**.

| Hive             | Stores                                   | Location                                                       |
| ---------------- | ---------------------------------------- | -------------------------------------------------------------- |
| **SYSTEM**       | Services, drivers, hardware, boot config | `C:\Windows\System32\config\SYSTEM`                            |
| **SOFTWARE**     | Installed apps, autostarts, OS info      | `C:\Windows\System32\config\SOFTWARE`                          |
| **SECURITY**     | Local security policies, audit config    | `C:\Windows\System32\config\SECURITY`                          |
| **SAM**          | User accounts, password hashes, groups   | `C:\Windows\System32\config\SAM`                               |
| **NTUSER.DAT**   | User activity, preferences               | `C:\Users\<user>\NTUSER.DAT`                                   |
| **USRCLASS.DAT** | Shellbags, Jump Lists                    | `C:\Users\<user>\AppData\Local\Microsoft\Windows\USRCLASS.DAT` |

---

## **Registry Root Keys (Live View)**

Hives are **mapped** into logical root keys:

| Hive         | Registry Editor Path         |
| ------------ | ---------------------------- |
| SYSTEM       | `HKLM\SYSTEM`                |
| SOFTWARE     | `HKLM\SOFTWARE`              |
| SECURITY     | `HKLM\SECURITY`              |
| SAM          | `HKLM\SAM`                   |
| NTUSER.DAT   | `HKU\<SID>` & `HKCU`         |
| USRCLASS.DAT | `HKU\<SID>\Software\Classes` |

> `HKCR` and `HKCC` are **dynamic**, not real hives.

---

## **Why Registry Matters in Forensics**

Registry reveals:

* Program execution
* Persistence mechanisms
* User actions
* USB history
* Installed software
* System identity

Used with:

* Logs
* Memory
* File system
  to build **attack timelines**

---

## **Important Forensic Registry Keys**

### **User Activity**

| Key                       | Evidence                   |
| ------------------------- | -------------------------- |
| `HKCU\...\UserAssist`     | GUI-launched applications  |
| `HKCU\...\RunMRU`         | Commands via Win+R         |
| `HKCU\...\TypedPaths`     | Explorer address bar paths |
| `HKCU\...\WordWheelQuery` | Explorer search terms      |
| `HKCU\...\RecentDocs`     | Recently opened files      |

---

### **Persistence / Autostart**

| Key            | Purpose                     |
| -------------- | --------------------------- |
| `HKLM\...\Run` | Startup apps (all users)    |
| `HKCU\...\Run` | Startup apps (current user) |

---

### **System & Software**

| Key                            | Purpose                |
| ------------------------------ | ---------------------- |
| `HKLM\...\Uninstall`           | Installed applications |
| `HKLM\SYSTEM\...\ComputerName` | Hostname               |
| `HKLM\SYSTEM\...\Enum\USBSTOR` | USB devices            |

---

## **Registry Editor vs Registry Forensics**

### ❌ Registry Editor

* Live system only
* Modifies evidence
* Cannot open offline hives
* Binary values unreadable

### ✅ Registry Explorer

* Opens **offline hives**
* Safe forensic analysis
* Parses binary data
* Supports transaction logs
* Bookmarking & searching

---

## **Handling Offline Hives (Best Practice)**

* Hives may be **dirty** (incomplete writes)
* Always:

  * Hold **SHIFT + Open**
  * Replay transaction logs
* Ensures **consistent state**

---

## **Registry Explorer Workflow**

1. Launch **Registry Explorer**
2. `File → Load Hive`
3. Select hive (SYSTEM, SOFTWARE, NTUSER, etc.)
4. **SHIFT + Open** → replay logs
5. Use:

   * Search bar
   * Bookmarks
   * Known forensic keys

---

## **Key Investigation Example**

### **Find Computer Name**

```text
SYSTEM → ControlSet001 →
Control → ComputerName → ComputerName
```

Value reveals hostname
Example: `DISPATCH-SRV01`

---

## **Persistence Detection Example**

Startup entry found in:

```text
HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

Value:

```text
"C:\Program Files\DroneManager\dronehelper.exe" --background
```

➡️ **Malware persistence**

---

## **Installed Application Tracing**

From:

```text
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
```

Correlated with:

* Execution paths
* Downloads folder
* NTUSER execution artefacts

---

## **Key Takeaways**

* Registry = **Windows behavioral timeline**
* Always analyze **offline**
* NTUSER.DAT = **user actions**
* SOFTWARE = **installed tools**
* SYSTEM = **hardware & identity**
* `Run` keys = **persistence**
* Registry Explorer > Registry Editor for forensics

---
