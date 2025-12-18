# **CyberChef – Hoperation Save McSkidy**

# **Task 1 – Introduction**

McSkidy is imprisoned in **King Malhare's Quantum Warren**. Sir BreachBlocker III secured the fortress with multiple access controls to prevent escape.

McSkidy managed to send vital clues using harmless bunny pictures. The message revealed that **five locks** need to be disabled to secure an escape route.

Your mission: Break the locks by examining their logic and leveraging the guard chat system to extract passwords.

## **Learning Objectives**

- Introduction to **encoding/decoding**
- Learn how to use **CyberChef**
- Identify useful information in web applications through **HTTP headers**

---

# **Task 2 – Important Concepts**

## **Encoding and Decoding**

**Encoding** transforms data to ensure compatibility between different systems. It differs from **encryption** in purpose and process.

| Aspect | **Encoding** | **Encryption** |
|--------|-------------|---------------|
| Purpose | Compatibility, Usability | Security, Confidentiality |
| Process | Standardized | Algorithm + Key |
| Security | No | Yes |
| Speed | Fast | Slow |
| Examples | Base64 | TLS |

**Decoding** is the process of converting encoded data back to its original, readable, and usable form.

---

## **CyberChef Overview**

**CyberChef** is also known as the **Cyber Swiss Army Knife**.

| Area | Description |
|------|-------------|
| **Operations** | Repository of diverse CyberChef capabilities |
| **Recipe** | Fine-tune and chain the operations area |
| **Input** | Here you provide the input for your recipe |
| **Output** | Here is the output of your recipe |

---

## **Simple Example**

### **Try Your First Recipe:**

1. Open CyberChef (online version or AttackBox bookmark)
2. Drag and drop **To Base64** operation from Operations area to Recipe area
3. Add `IamRoot` into the Input area
4. Add another operation, **From Base64**, to show the initial input again

**Note:** You can enable/disable an operation in the recipe by toggling the middle button on the right of the operation.

✔ Congratulations! You took the first steps to become a master Chef.

---

## **Inspecting Web Pages**

Besides the rendered content of a web page, your browser receives and can show **additional information**.

For this challenge, you will have a deeper look at that information and put it to good use.

### **Accessing Developer Tools**

| Browser | Menu Path |
|---------|-----------|
| **Chrome** | More tools > Developer tools |
| **Firefox** | Menu (☰) > More tools > Web Developer Tools |
| **Microsoft Edge** | Settings and more (...) > More tools > Developer tools |
| **Opera** | Developer > Developer tools |
| **Safari** | Develop > Show Web Inspector (Requires enabling in Preferences > Advanced) |

**Note:** For a better experience, reposition the console on the right side of the browser. Look for the three dots on the right side of the console.

---

# **Task 3 – First Lock: Outer Gate**

## **Key Information**

Start the target machine and access the web app at `http://MACHINE_IP:8080`

McSkidy revealed vital clues in his message. You must leverage any useful information to break the locks.

### **Key Points to Look For:**

**1. Chat (Base64 Encoded)**
- Decode chat in CyberChef
- Extract useful information from guards
- ⚠ From Lock 3 onwards, guards take longer to respond (~2-3 minutes)

**2. Guard Name**
- This logic persists throughout levels
- Note down the guard's name for each level

**3. Headers**
- Inspect the page → Switch to **'Network' tab**
- Refresh the page → Select the first response

**4. Login Logic**
- Inspect the page → Switch to **'Debugger' tab**
- Match the lock with respective logic
- Find helpful comments explaining what to cook in CyberChef

---

## **First Lock - Outer Gate**

Time to siege the fortress! Follow these steps:

### **Step-by-Step Solution:**

1. **Identify the guard name** and encode it to Base64 → Use as username input

2. **Extract the magic question** from page headers and encode it in Base64

```
Magic Question: "What is the password for this level?"
```

3. **Use the encoded magic question** in the chat → Guard will answer with encoded level password

4. **Switch to 'Debugger' tab** and identify the login logic

```
Login Logic: Password is encoded to Base64
```

5. **Decode the answer** from the guard → You'll have the plaintext password

6. **Log in** with encoded username and plaintext password

✔ Excellent work! One lock is down, and only four remain to be broken.

---

### **What is the password for the first lock?**

<details>
  <summary>Click to reveal the answer</summary>

**Iamsofluffy**

</details>

---

# **Task 4 – Second Lock: Outer Wall**

Excellent job breaking that first level! This level increases the difficulty slightly.

### **Step-by-Step Solution:**

1. **Identify the guard's name** and save the encoded output for later

2. **Extract and encode the magic question** → Retrieve the encoded password from the guard

```
Magic Question: "Did you change the password?"
```

3. **Check the login logic** → Encoding is applied **twice** this time

```
Login Logic: Double Base64 encoding
Decoding: From Base64 → From Base64
```

4. **Log in** with the newfound password and the saved username

✔ You are getting closer to securing an escape route; only three locks remain. Keep up the good work.

---

### **What is the password for the second lock?**

<details>
  <summary>Click to reveal the answer</summary>

**Itoldyoutochangeit!**

</details>

---

# **Task 5 – Third Lock: Guard House**

So far, so good. The login logic now begins to use **chained operations**.

This will be the trend for this and the following levels.

### **Information to Collect:**

✔ Encoded username  
✔ Encoded password from the guard  
✔ XOR key

```
XOR Key: "cyberchef"
```

**Note:** From this lock onwards, there is **no magic question**. You can ask the guard nicely to give you the password. It will still need to be decoded as per the login logic.

⚠ The guard may sometimes fall asleep or take a long time to respond (~2-3 minutes). Keep the message short.

**Example:** _"Password please."_

---

## **Understanding XOR**

### **Theory Time**

**XOR** is a popular operation that uses both input data and a key. The process involves a bitwise exclusive OR between the data and key.

**How to Reverse XOR:**

XOR has a magic property: when you XOR the result with the key again, the new result will be the initial data.

```
Data XOR Key = Result
Result XOR Key = Data
```

✔ Try this in CyberChef: Put two XOR operations one after another, use the same key for both, and the output should be identical.

---

### **Step-by-Step Solution:**

1. **Check the login logic** → Password is first XOR'ed with a key, then encoded to Base64

```
Login Logic: XOR with key → Base64 encoding
```

2. **Build the reverse recipe** in CyberChef to find the plaintext password

```
Reverse Recipe:
1. From Base64
2. XOR (with key: "cyberchef")
```

3. **Use the credentials** and unlock the next level

---

### **What is the password for the third lock?**

<details>
  <summary>Click to reveal the answer</summary>

**BugsBunny**

</details>

---

# **Task 6 – Fourth Lock: Inner Castle**

We are almost there. In this level, Sir BreachBlocker III throws you a curveball.

### **Step-by-Step Solution:**

1. **Check the login logic** as before → We will not be needing header information for this one

```
Login Logic: MD5 hash
```

2. **Ask the guard for the password** → The reply seems a bit odd

⚠ After decoding, the guard's answer reveals a **hash string**

---

### **Theory Time**

**MD5** (Message-Digest Algorithm 5) is a cryptographic algorithm that produces a fixed-size hash value.

- This is supposed to be a **one-way function** (you cannot reverse it)
- However, **precomputed hashes** can be leveraged to identify the input

---

3. **Putting it together:**
   - The plaintext password is passed through MD5
   - You have the hash
   - This looks like a job for **CrackStation**

4. **Open CrackStation** and paste the hash to retrieve the password

5. **Use the credentials** and advance to the final level

✔ Fantastic. One more lock and you will ensure McSkidy has safe passage and escapes.

---

### **What is the password for the fourth lock?**

<details>
  <summary>Click to reveal the answer</summary>

**passw0rd1**

</details>

---

# **Task 7 – Fifth Lock: Prison Tower**

Ready for the final hurdle?

### **Hidden Message from McSkidy:**

> _"I can see you are ready to break the last lock. Be aware that Sir BreachBlocker III implemented **different mechanisms** for the last lock, which change occasionally. Make sure you match the correct approach when decoding the password."_

⚠ That sounds tricky, but do not despair. You will find a way.

---

### **Step-by-Step Solution:**

1. **Extract the information** as before, noting down the encoded guard name

2. **Note the recipe ID** from the header and match the corresponding login logic

### **Recipe Decoding Cheat Sheet:**

| Recipe ID | Reverse Logic |
|-----------|---------------|
| **1** | From Base64 ⇒ Reverse ⇒ ROT13 |
| **2** | From Base64 ⇒ From Hex ⇒ Reverse |
| **3** | ROT13 ⇒ From Base64 ⇒ XOR(extracted key) |
| **4** | ROT13 ⇒ From Base64 ⇒ ROT47 |

3. **Build the reverse recipe** with CyberChef and extract the final password

✔ Finally, the last lock has been breached, and you provided a safe path for McSkidy to escape.

---

### **What is the password for the fifth lock?**

<details>
  <summary>Click to reveal the answer</summary>

**51rBr34chBl0ck3r**

</details>

---

### **What is the retrieved flag?**

<details>
  <summary>Click to reveal the answer</summary>

**THM{M3D13V4L_D3C0D3R_4D3P7}**

</details>

---

# **Task 8 – Epilogue**

As McSkidy passed by the Inner Castle, she heard a thunderous voice:

> _"Why should Christmas have all the fun?"_

McSkidy managed to get back to **Wareville** just in time as TBFC was about to be hit by another disaster.

---

## **Additional Resources**

If you found decoding secrets interesting, check out:

🔗 [Introduction to Cryptography](https://tryhackme.com/room/cryptographyintro)

---

## **Side Quest 3**

Looking for the key to **Side Quest 3**? Hopper has left us a [CyberChef link](https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B/%3D')Label('encoder1')ROT13(true,true,false,7)Split('H0','H0%5C%5Cn')Jump('encoder1',8)Fork('%5C%5Cn','%5C%5Cn',false)Zlib_Deflate('Dynamic%20Huffman%20Coding')XOR(%7B'option':'UTF8','string':'h0pp3r'%7D,'Standard',false)To_Base32('A-Z2-7%3D')Merge(true)Generate_Image('Greyscale',1,512)&input=SG9wcGVyIG1hbmFnZWQgdG8gdXNlIEN5YmVyQ2hlZiB0byBzY3JhbWJsZSB0aGUgZWFzdGVyIGVnZyBrZXkgaW1hZ2UuIEhlIHVzZWQgdGhpcyB2ZXJ5IHJlY2lwZSB0byBkbyBpdC4gVGhlIHNjcmFtYmxlZCB2ZXJzaW9uIG9mIHRoZSBlZ2cgY2FuIGJlIGRvd25sb2FkZWQgZnJvbTogCgpodHRwczovL3RyeWhhY2ttZS1pbWFnZXMuczMuYW1hem9uYXdzLmNvbS91c2VyLXVwbG9hZHMvNWVkNTk2MWM2Mjc2ZGY1Njg4OTFjM2VhL3Jvb20tY29udGVudC81ZWQ1OTYxYzYyNzZkZjU2ODg5MWMzZWEtMTc2NTk1NTA3NTkyMC5wbmcKClJldmVyc2UgdGhlIGFsZ29yaXRobSB0byBnZXQgaXQgYmFjayE) as a lead.

**Challenge:** Reverse the algorithm to recover the key and access the corresponding challenge in the Side Quest Hub!

---

