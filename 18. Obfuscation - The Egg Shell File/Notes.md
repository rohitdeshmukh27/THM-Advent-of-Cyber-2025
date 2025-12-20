# **Obfuscation – The Egg Shell File**

# **Task 1 – Introduction**

WareVille has not felt right since the wormhole appeared. Everyone in TBFC is on high alert: Systems are going haywire, dashboards are spiking, and SOC alerts have been firing nonstop.

Amid the chaos, McSkidy keeps her focus on a particular alert that caught her interest: an email posing as **northpole-hr**. It's littered with carrot emojis, but the weird thing is this: **there is no North Pole human resources department**. TBFC's HR is at the South Pole.

Digging further, she found a tiny **PowerShell file** from that email was downloaded. Among the code are random strings of characters, all gibberish, nothing readable at a glance.

McSkidy knows malicious actors often hide code and data using a technique called **obfuscation**. But what is it, really? And how can we decipher it?

## **Learning Objectives**

- Learn about **obfuscation**, why and where it is used
- Learn the difference between **encoding, encryption, and obfuscation**
- Learn about obfuscation and the **common techniques**
- Use **CyberChef** to recover plaintext safely

---

# **Task 2 – Obfuscation & Deobfuscation**

## **Understanding the Gibberish**

**Obfuscation** is the practice of making data hard to read and analyze. Attackers use it to evade basic detection and delay investigations.

### **Example: ROT1 Cipher**

Let's say a security tool will automatically block any file that contains the word **"the bandit yeti"**. To evade this, attackers can use a simple cipher technique called **"ROT1"** to hide the string.

**ROT1** is a cipher technique that shifts each letter forward by 1 in the alphabet:
- The letter `a` becomes `b`
- The letter `b` becomes `c`
- And so on...

Using this cipher:
```
Original:   carrot coins go brr
ROT1:       dbsspu dpjot hp css
```

✔ Visual: `CARROT` → `DBBSPU`

This can be further complicated by using **ROT13**, which moves the characters forward by 13 spaces.

---

## **Obfuscation in the Real World**

So far, we've kept it simple. In the real world, you'll encounter a wider range of techniques.

### **XOR Obfuscation**

One slightly more complicated technique is utilizing **XOR**:
- Each character is represented as a byte
- Each byte is combined with a key using the XOR mathematical operation
- This changes the bytes into different bytes
- The resulting text often contains uncommon symbols or non-readable characters

⚠ Obfuscating with XOR is not something that can be easily done manually. But thankfully, there are tools available that can help us with this. One such widely used tool is called **CyberChef**.

### **XOR Obfuscation with CyberChef**

Let's try to obfuscate a string using XOR:

**Step-by-Step:**

1. **Open CyberChef** by visiting the link in your browser
2. At the top right, you'll see an **Input box** → Paste `carrot supremacy`
3. In the left pane under **Operations**, search for **"XOR"** → Drag it into the middle under **Recipe**
4. Set the **Key** to `a` → Make sure the dropdown next to Key is set to **HEX**
5. The obfuscated result should automatically appear in the **Output** section. If it doesn't, click **BAKE!**

**Result:**
```
Original:     carrot supremacy
XOR (key a):  ikxxe~*yzxogkis!
```

---

## **Detecting Patterns**

The good thing about well-known obfuscation techniques is that **it's easy to reverse it once you figure out the technique used**.

### **Visual Clues for Common Obfuscation Techniques**

| Technique | Visual Clues |
|-----------|-------------|
| **ROT1** | Common words look "one letter off", spaces stay the same. Easy to detect. |
| **ROT13** | Look for three-letter words. `the` becomes `gur`, `and` becomes `naq`. Spaces stay the same. |
| **Base64** | Long strings containing mostly alphanumeric characters (A-Z, a-z, 0–9), sometimes with `+` or `/`, often ending in `=` or `==`. |
| **XOR** | Looks like random symbols but stays the same length as the original. If a short key was reused, you may notice a tiny repeat every few characters. |

---

### **Reversing the Obfuscation**

Once you have a guess on what cipher was used, you can go back to **CyberChef**, but this time use an operation that does the **reverse effect**.

**Example:**
- To deobfuscate Base64, use **From Base64** instead of **To Base64**

---

## **Unfamiliar Patterns**

Not sure what you're looking at? It's fine. Even if you don't have an idea of what cipher was used, it is easy enough to keep on trying different operations just to see if the text becomes readable.

### **The Magic Operation**

CyberChef includes an operation called **Magic** that automatically guesses and tries common decoders for you.

**How to Use:**
1. Add the **Magic** operation to your recipe
2. Look at the results → It will display multiple possibilities
3. Check which one makes sense
4. You can enable **"Intensive mode"** to test more possibilities

⚠ **Note:** This won't catch everything (especially custom XOR keys or unusual layers)! Use it as a hint to help you figure it out. Some challenges below cannot be fully solved with the Magic operation alone.

---

## **Layered Obfuscation**

To make deobfuscation harder, threat actors often combine **multiple techniques**, almost like a layered approach.

### **Example: Multi-Layer Obfuscation**

A script that threat actors want hidden can be:
1. Compressed with **gzip**
2. **XOR'ed** with a key (e.g., `x`)
3. Finally **Base64-encoded**

**Result:**
```
H4sIADKZ42gA/32PT2sqQRDE7/MpitGDgrPEJJcXyOGha1xwVaLwyLvI6rbuhP3HTHswm/3uzmggIQfrUD3VzI/u7iDXljepNkFth6KDmYsWnBF2R2OoZOyrPCUDXSKB1UWdEzjZ5hQI8c9oJrU4cn1kyPXbMgRW0X/nF8WLcTSJwvFX9Jr/jUP5i1NOgPeLfjxvtKQQL8RqlOk8jZgKfGJSmTDZZWqxfacdoxFAl0814Rl6j153EyxXkR1VJSe6JNNHAzmOXiHRgnJLPk+iWeiyR63+uIm6Hb5B92NG5YGzK5sm7FnfTSxfzl3rgoJ1tWKjy0NPnpxUHKs0xXT6VBSy7zjZ3A3UY4tmOPjTAs29t4dWQu2vpwyua7niJ7iyCeZJQaIVZ/xwdy/JAQAA
```

### **Reversing Layered Obfuscation**

To reverse this kind of layered obfuscation, apply the operations in the **opposite order**:

**Deobfuscation Steps:**
1. **Base64-decode**
2. **XOR** with `x`
3. **Decompress gzip**

✔ Chain these Operations in CyberChef to recover the original content.

---

## **Unwrapping the Easter Egg**

Luckily, McSkidy detected something phishy in the email we received. She extracted a **PowerShell script** from the PDF and put it on an isolated VM to figure out what it's doing.

There is some obfuscation in this script. Can you help her understand what it is?

### **Challenge Instructions**

**Part 1: Deobfuscate the C2 URL**

1. Open **SantaStealer.ps1**, located on the VM's Desktop in Visual Studio (double-click it)
2. Navigate to the **"Start here"** section of the script
3. Follow the instructions in the code's comments and **save**
4. Run the script from a PowerShell terminal to get your first flag

**Opening PowerShell:**
- Click **Start** (or press the Windows key)
- Type **"PowerShell"** and press Enter

**Commands to Run:**
```powershell
PS C:\Users\Administrator> cd .\Desktop\
PS C:\Users\Administrator\Desktop> .\SantaStealer.ps1
```

---

**Part 2: Obfuscate the API Key**

There is a second challenge waiting for you. This time, you must **obfuscate the malicious actor's API key using XOR**.

1. Follow the **Part 2 instructions** in the code's comments
2. Run the script again to get your last flag

---

### **What is the first flag you get after deobfuscating the C2 URL and running the script?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{C2_De0bfuscation_29838}**

</details>

---

### **What is the second flag you get after obfuscating the API key and running the script again?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{API_Obfusc4tion_ftw_0283}**

</details>

---
