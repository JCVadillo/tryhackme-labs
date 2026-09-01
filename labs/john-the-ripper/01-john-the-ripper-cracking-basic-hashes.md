# 🔐 01 – John the Ripper – Cracking Basic Hashes
*TryHackMe – John the Ripper: The Basics*
*(Task 4 – Cracking Basic Hashes)*

---

## 🎯 Lab Summary

This lab covers the fundamentals of hash identification and offline password cracking using **John the Ripper** on Kali Linux. The objective was to identify the hash type of four unknown hashes and crack each one using a dictionary attack with the `rockyou.txt` wordlist.

The main tasks were to:
- Transfer working files into the Kali Linux environment
- Use `hash-id.py` to identify each hash algorithm
- Determine the correct John the Ripper format flag for each hash type
- Crack all four hashes using dictionary-based attacks

---

## ⚙️ Environment

| Component | Detail |
|---|---|
| **OS** | Kali Linux (ARM64 – UTM on Apple M1) |
| **Tool** | John the Ripper (pre-installed on Kali) |
| **Hash Identifier** | `hash-id.py` |
| **Wordlist** | `rockyou.txt` (`/usr/share/wordlists/`) |
| **Working Directory** | `/usr/share/wordlists/` |

---

## 📁 File Setup

The hash files and `hash-id.py` were transferred to the Kali VM. To keep everything in one working directory alongside `rockyou.txt`, `hash-id.py` was moved to `/usr/share/wordlists/`:

```bash
sudo mv hash-id.py /usr/share/wordlists/
```

---

## 🧪 Hash Cracking – Step by Step

### Hash 1

**File:** `hash1.txt`
**Hash value:** `2e728dd31fb5949bc39cac5a9f066498`

**Step 1 – Identify the hash type:**
```bash
cat John-the-Ripper-The-Basics/Task04/hash1.txt | python3 hash-id.py
```
> Result: **MD5**

**Step 2 – Crack it:**

MD5 requires the `raw-` prefix in John the Ripper to distinguish it from salted variants:
```bash
john --format=raw-md5 --wordlist=rockyou.txt John-the-Ripper-The-Basics/Task04/hash1.txt
```

| Field | Value |
|---|---|
| **Hash Type** | MD5 |
| **Format Flag** | `raw-md5` |
| **Result** | `biscuit` |

---

### Hash 2

**File:** `hash2.txt`
**Hash value:** `1A732667F3917C0F4AA98BB13011B9090C6F8065`

**Step 1 – Identify the hash type:**
```bash
cat John-the-Ripper-The-Basics/Task04/hash2.txt | python3 hash-id.py
```
> Result: **SHA-1**

**Step 2 – Verify available format name:**
```bash
john --list=formats | grep -iF "sha1"
```

**Step 3 – Crack it:**
```bash
john --format=raw-sha1 --wordlist=rockyou.txt John-the-Ripper-The-Basics/Task04/hash2.txt
```

| Field | Value |
|---|---|
| **Hash Type** | SHA-1 |
| **Format Flag** | `raw-sha1` |
| **Result** | `kangeroo` |

---

### Hash 3

**File:** `hash3.txt`
**Hash value:** `D7F4D3CCEE7ACD3DD7FAD3AC2BE2AAE9C44F4E9B7FB802D73136D4C53920140A`

**Step 1 – Identify the hash type:**
```bash
cat John-the-Ripper-The-Basics/Task04/hash3.txt | python3 hash-id.py
```
> Result: **SHA-256**

**Step 2 – Crack it:**
```bash
john --format=raw-sha256 --wordlist=rockyou.txt John-the-Ripper-The-Basics/Task04/hash3.txt
```

| Field | Value |
|---|---|
| **Hash Type** | SHA-256 |
| **Format Flag** | `raw-sha256` |
| **Result** | `microphone` |

---

### Hash 4

**File:** `hash4.txt`
**Hash value:** `c5a60cc6bbba781c601c5402755ae1044bbf45b78d1183cbf2ca1c865b6c792cf3c6b87791344986c8a832a0f9ca8d0b4afd3d9421a149d57075e1b4e93f90bf`

**Step 1 – Identify the hash type:**
```bash
cat John-the-Ripper-The-Basics/Task04/hash4.txt | python3 hash-id.py
```
> Result: **Whirlpool**

**Step 2 – Verify available format name:**
```bash
john --list=formats | grep -iF "Whirlpool"
```

**Step 3 – Crack it:**

Unlike MD5 and SHA variants, Whirlpool does **not** require a `raw-` prefix in John the Ripper:
```bash
john --format=whirlpool --wordlist=rockyou.txt John-the-Ripper-The-Basics/Task04/hash4.txt
```

| Field | Value |
|---|---|
| **Hash Type** | Whirlpool |
| **Format Flag** | `whirlpool` |
| **Result** | `colossall` |

---

## 📊 Results Summary

| # | File | Hash Type | Format Flag | Cracked Password |
|---|---|---|---|---|
| 1 | `hash1.txt` | MD5 | `raw-md5` | `biscuit` |
| 2 | `hash2.txt` | SHA-1 | `raw-sha1` | `kangeroo` |
| 3 | `hash3.txt` | SHA-256 | `raw-sha256` | `microphone` |
| 4 | `hash4.txt` | Whirlpool | `whirlpool` | `colossall` |

---

## 🧠 Reflections / Notes

- **Hash identification is the critical first step** — throwing the wrong format flag at John the Ripper either produces no results or silently uses the wrong algorithm
- **The `raw-` prefix convention** applies to unsalted MD5, SHA-1, and SHA-256 in John the Ripper — it distinguishes them from salted or application-specific variants (e.g., `md5crypt`, `sha1-django`)
- **Whirlpool is the exception** — it does not follow the `raw-` prefix pattern; the format flag is simply `whirlpool`
- **`--list=formats | grep`** is an essential habit when unsure of the exact flag name — John the Ripper's format naming is not always intuitive
- **`rockyou.txt` remains devastatingly effective** against weak passwords — all four hashes were cracked because the plaintext values were common dictionary words
- Moving `hash-id.py` into `/usr/share/wordlists/` was a practical workaround to keep the working directory clean, but a better long-term approach would be to add custom scripts to `/usr/local/bin/` for system-wide access

---

## 📚 Key Skills Demonstrated

- Hash type identification using `hash-id.py`
- Dictionary-based offline password cracking using **John the Ripper**
- Correct application of **format flags** for MD5, SHA-1, SHA-256, and Whirlpool
- Use of `--list=formats` to verify and discover format names
- Understanding of the `raw-` prefix convention in John the Ripper
- Practical file management and tool navigation within **Kali Linux CLI**

---

## 🔗 References

- [TryHackMe – John the Ripper: The Basics](https://tryhackme.com/room/johntheripper0)
- [John the Ripper Official Documentation](https://www.openwall.com/john/)
- [Kali Linux – Pre-installed Tools](https://www.kali.org/tools/john/)

---

*All hashes were successfully cracked using dictionary attacks against rockyou.txt. This lab reinforces why weak or commonly-used passwords remain a critical vulnerability regardless of the hashing algorithm applied.*
