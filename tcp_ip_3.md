Here’s the **binary method for subnetting** explained the way interviewers expect: step-by-step, visual, and precise. This method removes guesswork and works for **any subnetting problem**.

---

# 🧠 BINARY METHOD FOR SUBNETTING (IN-DEPTH)

## 📌 1. Why Binary Method?

Most students memorize shortcuts—but interviewers often ask:

> “Can you explain subnetting using binary?”

Because:

* IP addresses are **actually binary (0s and 1s)**
* Subnetting = **bit manipulation**

---

# 🔢 2. IPv4 in Binary

Each octet = **8 bits**

| Decimal | Binary   |
| ------- | -------- |
| 0       | 00000000 |
| 1       | 00000001 |
| 64      | 01000000 |
| 128     | 10000000 |
| 192     | 11000000 |
| 255     | 11111111 |

---

## 📍 Example IP:

```
192.168.1.10
```

### Binary:

```
192 → 11000000
168 → 10101000
1   → 00000001
10  → 00001010
```

---

# 🧱 3. Subnet Mask in Binary

Example:

```
/26 = 255.255.255.192
```

### Binary:

```
11111111.11111111.11111111.11000000
```

👉 Key idea:

* **1s = Network bits**
* **0s = Host bits**

---

# ⚙️ 4. Core Concept

Subnetting = **Borrowing bits from host part**

```
Original (/24):
11111111.11111111.11111111.00000000

After subnetting (/26):
11111111.11111111.11111111.11000000
                              ↑↑
                        Borrowed bits
```

---

# 🧮 5. Binary Subnetting Table (VERY IMPORTANT)

### For last octet

| Bit Position | Value |
| ------------ | ----- |
| 1st          | 128   |
| 2nd          | 64    |
| 3rd          | 32    |
| 4th          | 16    |
| 5th          | 8     |
| 6th          | 4     |
| 7th          | 2     |
| 8th          | 1     |

---

# 📊 6. Subnet Mask Table (Binary → Decimal)

| CIDR | Binary (Last Octet) | Decimal |
| ---- | ------------------- | ------- |
| /25  | 10000000            | 128     |
| /26  | 11000000            | 192     |
| /27  | 11100000            | 224     |
| /28  | 11110000            | 240     |
| /29  | 11111000            | 248     |
| /30  | 11111100            | 252     |

---

# 🔍 7. Step-by-Step Binary Method

---

## ✅ Example 1:

### Find subnet details of:

```
IP: 192.168.1.130/26
```

---

### 🔹 Step 1: Convert last octet to binary

```
130 = 10000010
```

---

### 🔹 Step 2: Subnet mask (/26)

```
Mask:
11111111.11111111.11111111.11000000
```

---

### 🔹 Step 3: Identify network & host bits

```
IP:   10000010
Mask: 11000000
      --------
Net   Host
```

* First **2 bits → subnet**
* Remaining **6 bits → hosts**

---

### 🔹 Step 4: Find subnet (IMPORTANT)

Take first **network bits only**:

```
IP last octet: 10|000010
               ↑↑
```

Now zero out host bits:

```
10 000000 = 128
```

---

### 🔹 Step 5: Network Address

```
192.168.1.128
```

---

### 🔹 Step 6: Broadcast Address

Set all host bits = 1:

```
10 111111 = 191
```

```
Broadcast = 192.168.1.191
```

---

### 🔹 Step 7: Host Range

```
Start = 192.168.1.129
End   = 192.168.1.190
```

---

# 📊 8. Full Summary Table (Example 1)

| Field         | Value         |
| ------------- | ------------- |
| IP Address    | 192.168.1.130 |
| CIDR          | /26           |
| Binary IP     | 10000010      |
| Mask (binary) | 11000000      |
| Network       | 192.168.1.128 |
| Broadcast     | 192.168.1.191 |
| Host Range    | 129 – 190     |
| Hosts         | 62            |

---

# 🧮 9. Example 2 (Interview Level)

### Problem:

```
IP: 10.0.0.75/27
```

---

### Step 1: Convert

```
75 = 01001011
```

---

### Step 2: Mask (/27)

```
11100000
```

---

### Step 3: Split

```
IP:   010|01011
Mask: 111|00000
```

---

### Step 4: Network

```
01000000 = 64
```

---

### Step 5: Broadcast

```
01011111 = 95
```

---

### Final:

| Field     | Value     |
| --------- | --------- |
| Network   | 10.0.0.64 |
| Broadcast | 10.0.0.95 |
| Range     | 65 – 94   |
| Hosts     | 30        |

---

# 🔥 10. Binary Shortcut Trick

Instead of full binary:

👉 Just focus on **last octet**

Example:

```
/27 → 11100000
```

* First 3 bits = subnet
* Remaining 5 bits = hosts

---

# ⚡ 11. Key Interview Points

You should be able to say:

* “Subnetting is bitwise separation of network and host portions.”
* “Mask determines boundary using binary 1s and 0s.”
* “Network = IP AND Mask”
* “Broadcast = Network OR inverted mask”

---

# 🧠 12. AND Operation (Important Concept)

### Example:

```
IP:   10000010
Mask: 11000000
----------------
AND:  10000000
```

👉 Result = Network address

---

# 🧠 13. OR Operation (Broadcast)

```
Network: 10000000
Host bits: 00111111
-------------------
Broadcast: 10111111
```

---

# 📌 14. Practice Table (Quick Reference)

| CIDR | Host Bits | Hosts | Block Size |
| ---- | --------- | ----- | ---------- |
| /24  | 8         | 254   | 256        |
| /25  | 7         | 126   | 128        |
| /26  | 6         | 62    | 64         |
| /27  | 5         | 30    | 32         |
| /28  | 4         | 14    | 16         |
| /29  | 3         | 6     | 8          |
| /30  | 2         | 2     | 4          |

---

# 🚀 Final Understanding

Binary method gives you:

* Exact control
* No memorization dependency
* Strong interview confidence

---

## 👍 If you want next:

I can give:

* **Hard interview questions (Google/Amazon level)**
* **VLSM using binary (very important)**
* **Tricks to solve in under 10 seconds**
* **Mock interview Q&A on networking**

Just tell me what level you want next.
