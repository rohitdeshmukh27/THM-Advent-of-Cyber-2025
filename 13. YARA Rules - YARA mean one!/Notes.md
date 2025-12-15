# **YARA – Technical Notes (AoC25 Day 13)**

---

## **Task 1 – Introduction**

### **What’s Happening**

- McSkidy sent **hidden messages inside images**
- Message must be decoded using **YARA**
- Keyword pattern:
  **`TBFC:` + alphanumeric codeword**
- Extract codewords → sort → decode message

### **Why YARA**

- Pattern-based detection
- Works on **files, directories, memory**
- Ideal for **threat hunting & IOC detection**

---

## **Task 2 – YARA Overview**

### **What YARA Is**

- Rule-based pattern matching engine
- Used to **identify malware and artifacts**
- Matches:

  - Text
  - Bytes
  - Regex
  - File properties

### **When to Use YARA**

- Post-incident scanning
- Threat hunting
- Malware classification
- Memory analysis
- IOC validation across systems

---

## **YARA Rule Structure**

```yara
rule Rule_Name
{
    meta:
        author = "Analyst"
        description = "Purpose"
        date = "YYYY-MM-DD"

    strings:
        $s1 = "string"
        $s2 = { 4D 5A }
        $s3 = /regex/

    condition:
        any of them
}
```

### **Core Sections**

| Section   | Purpose            |
| --------- | ------------------ |
| meta      | Rule documentation |
| strings   | Indicators         |
| condition | Trigger logic      |

---

## **Strings in YARA**

### **1. Text Strings**

```yara
$s = "Christmas"
```

**Modifiers**

- `nocase` → ignore case
- `wide` / `ascii` → Unicode vs ASCII
- `xor` → XOR-encoded strings
- `base64`, `base64wide` → encoded payloads

Example:

```yara
$s = "Malhare" xor
```

---

### **2. Hex Strings**

Used for binaries, headers, shellcode

```yara
$mz = { 4D 5A }   // PE header
```

Wildcards supported:

```yara
{ E3 41 ?? C8 }
```

---

### **3. Regex Strings**

Used for flexible patterns (URLs, commands)

```yara
$url = /http:\/\/.*malhare.*/ nocase
```

---

## **Conditions**

### **Basic Logic**

```yara
condition:
    $s1
```

```yara
condition:
    any of them
```

```yara
condition:
    all of them
```

### **Logical Operators**

```yara
condition:
    ($s1 or $s2) and not $benign
```

### **File Properties**

```yara
condition:
    any of them and filesize < 10MB
```

---

## **Practical Detection Example (IcedID Loader)**

```yara
rule TBFC_Simple_MZ_Detect
{
    meta:
        author = "TBFC SOC L2"
        description = "IcedID Loader"
        confidence = "low"

    strings:
        $mz = { 4D 5A }
        $hex = { 48 8B ?? ?? 48 89 }
        $s1 = "malhare" nocase

    condition:
        all of them and filesize < 10485760
}
```

### **Execution**

```bash
yara -r icedid_starter.yar C:\
```

### **Useful Flags**

| Flag | Purpose                  |
| ---- | ------------------------ |
| `-r` | Recursive directory scan |
| `-s` | Print matched strings    |

---

## **YARA Practical Task – Hidden Message**

### **Goal**

- Scan image directory
- Match pattern:
  **`TBFC:` + alphanumeric**
- Extract and decode message

---

## Code and Execution

Create and edit the YARA rule file:

```bash
nano messageextract.yar
```

Add the following content to the file:

```yara
rule TBFC_Simple_MZ_Detect
{
    meta:
        author = "TBFC Blue Team_GingerHacker"
        description = "Extracts TBFC message fragments sent by McSkidy"
        date = "2025-12-13"

    strings:
        $tbfc_msg = /TBFC: [A-Za-z0-9]+/ ascii

    condition:
        $tbfc_msg
}
```

Save and exit the editor:

```
Ctrl + X → Y → Enter
```

Verify whether YARA is installed:

```bash
yara --version
```

If YARA is not installed, install it using:

```bash
sudo apt-get install yara
```

Run the YARA rule against the target files. From the output:

1. **First answer**: Determine it from the **number of images displayed**.
2. **Second answer**: Obtain it directly from the **YARA rule code**.
3. **Third answer**: Extract the detected words and **arrange them in the correct order** to form the final answer.

---

## **Questions**

### **How many images contain the string TBFC?**

<details>
  <summary>Click to reveal the answer</summary>

**5**

</details>

---

### **What regex matches `TBFC:` followed by alphanumeric ASCII characters?**

<details>
  <summary>Click to reveal the answer</summary>

**/TBFC:[A-Za-z0-9]+/**

</details>

---

### **What is the message sent by McSkidy?**

<details>
  <summary>Click to reveal the answer</summary>

**Find me in HopSec Island**

</details>

---

## ✅ **Key Takeaways**

- YARA = **pattern-based detection**
- Strong for **IOC & threat hunting**
- Combine:

  - Strings
  - Logic
  - File properties

- Regex + recursion = powerful for hidden data
