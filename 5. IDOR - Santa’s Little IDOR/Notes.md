# 🎄 IDOR - Santa’s Little IDOR

## 🧩 Task 1: Introduction

### 🔐 What is IDOR (Insecure Direct Object Reference)

IDOR is a web vulnerability where an application exposes internal object identifiers (e.g., user ID, order ID, file ID, note ID) directly in a request **without verifying whether the user is authorized** to access that object.

If an attacker changes the identifier in a request and gains access to another user’s data, it is an IDOR.

**Example**

```

[https://example.com/profile?user_id=123](https://example.com/profile?user_id=123)
→ Change to:
[https://example.com/profile?user_id=124](https://example.com/profile?user_id=124)

```

---

### 🔼 Privilege Escalation Types

**Vertical Privilege Escalation**

- Gaining higher-level privileges (e.g., a normal user performing admin actions).

**Horizontal Privilege Escalation**

- Accessing data belonging to another user with the same privilege level.
- Most IDOR vulnerabilities fall under this category.

IDORs occur when a user can perform an allowed action (e.g., viewing a package) but can change the identifier to access someone else’s data.

---

## 🧩 Task 2: IDOR on the Shelf

### Obvious Identifier Manipulation

A URL such as:

`(https://awesome.website.thm/TrackPackage?packageID=1001)`

may reveal data for other users if the attacker changes the `packageID` value.

If the server does not confirm whether the requesting user owns the package, the application is vulnerable.

---

### Why IDOR Happens

Applications often use identifiers in database queries:

```

SELECT person, address, status FROM Packages WHERE packageID = value;

```

If `packageID` is predictable and there is **no authorization check**, an attacker can simply iterate through IDs and retrieve other users’ data.

IDOR vulnerabilities are often due to missing or weak authorization checks.

---

## 🔑 Authentication vs Authorization

**Authentication**

- Verifies _who you are_ (e.g., username/password).
- Session cookies/tokens store this information for each request.

**Authorization**

- Determines _what you are allowed to access_.

Authorization **cannot** happen without authentication.  
If an IDOR endpoint does not require authentication, both issues must be fixed.

---

## 🧩 Privilege Escalation

- **Vertical:** Gaining higher privileges.
- **Horizontal:** Accessing another user’s data.
- **IDOR typically results in horizontal privilege escalation.**

---

## 🔍 Identifying the Vulnerability

### 1. Changing Stored Identifiers

Modifying values like `user_id` in local storage or cookies can reveal other users’ accounts.

### 2. Encoded IDs (Base64)

Example:  
`Mg==` → Base64 for `2`  
Still exploitable by encoding alternative IDs.

### 3. Hashed IDs (MD5/SHA1)

If the hashing method is known and predictable, an attacker can reproduce hashes to target different objects.

### 4. UUIDv1 Predictability

UUIDv1 includes timestamp data.  
If the generation time window is known, valid UUIDs can be brute-forced.

---

## 🛠️ Preventing IDOR

- Always perform **server-side authorization checks** for every request.
- Do not rely on techniques like:
  - Base64 encoding
  - Hashing identifiers
  - Random-looking IDs
- Validate that the authenticated user **owns or has permission** to access the referenced object.
- Monitor unauthorized access attempts.
- Ensure authentication is required for sensitive endpoints.

---

# Questions & Answers

### **Question: What does IDOR stand for?**

<details>
  <summary>Click to reveal the answer</summary>

**The answer:** Insecure Direct Object Reference

</details>

---

### **Question: What type of privilege escalation are most IDOR cases?**

<details>
  <summary>Click to reveal the answer</summary>

**The answer:** Horizontal

</details>

---

### **Question: Exploiting the view_accounts IDOR, what is the user_id of the parent that has 10 children?**

Change the user_id manually (in Application/Local Storage) from 12 upward. After each change, refresh the page, then check:

Network → view_account_info → Preview → children

The children list shows entries from 0 to n.
When the last index is 9, it means that account has 10 children.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:** 15

</details>

---

### **Bonus Task: Using the base64 or md5 child endpoint, find the id_number of the child born on 2019-04-17**

This can also be solved using Burp Suite.
The method is explained clearly in this YouTube video:
[https://youtu.be/geNAA2g-ZnY?t=1840](https://youtu.be/geNAA2g-ZnY?t=1840)

<details>
  <summary>Click to reveal the answer</summary>

**The answer:** 19

</details>

---

### **Bonus Task: Find the valid voucher for 20 November 2025 using the /parents/vouchers/claim endpoint**

This part is explained well by the same person in the video linked above, but I’ll also explain the UUID generation process using AI. If anyone has a better approach, feel free to share.
[https://youtu.be/geNAA2g-ZnY?t=2405](https://youtu.be/geNAA2g-ZnY?t=2405)

At first, I generated UUIDv1 values for the time window 20 November 2025, 20:00–24:00 UTC using an arbitrary, random clock sequence and node value, since the real values were unknown. These UUIDs were only timestamp-based samples and could not match the actual voucher. Later, once I provided the real clock sequence (11417) and node (02:6c:cd:f7:d7:69) used by the system(I got this from the first valid voucher and used the UUID Decoder website to obtain the values.), I regenerated the entire set—this time producing accurate UUIDv1 candidates for each minute in the window, resulting in a correct brute-force list of 240 UUIDs that follow the same structure the real voucher generator would produce.

<details>
  <summary>Click to reveal the answer</summary>

**The answer:** 22643e00-c655-11f0-ac99-026ccdf7d769

</details>

---

# Thank you
