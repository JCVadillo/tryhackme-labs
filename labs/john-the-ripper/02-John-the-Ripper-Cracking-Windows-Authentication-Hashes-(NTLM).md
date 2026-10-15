# 🔐 02 – John the Ripper – Cracking Windows Authentication Hashes (NTLM)
*TryHackMe – John the Ripper: The Basics*
*(Task 05 – Cracking Windows Authentication Hashes)*

---

## 🎯 Lab Summary

This lab moves from generic hash cracking into something you'll actually run into on real engagements: **NTLM hashes** pulled from a Windows machine. NTLM is the hash format used by Windows to store local (SAM) and domain (NTDS.dit) account passwords, typically obtained via tools like Mimikatz or by dumping the SAM/NTDS.dit database.

The main tasks were to:
- Identify the hash type of `ntlm.txt` using `hash-id.py`
- Understand why hash identifiers can be misleading with NTLM hashes
- Select the correct John the Ripper format flag (`nt`)
- Crack the hash using a dictionary attack with `rockyou.txt`

---

## ⚙️ Environment

| Component | Detail |
|---|---|
| **OS** | Kali Linux (ARM64 – UTM on Apple M1) |
| **Tool** | John the Ripper (pre-installed on Kali) |
| **Hash Identifier** | `hash-id.py` |
| **Wordlist** | `rockyou.txt` |
| **Working Directory** | `~/John-the-Ripper-The-Basics/Task05/` |
| **Target File** | `ntlm.txt` |

---

## 🧪 Cracking the NTLM Hash – Step by Step

### Step 1 – Identify the hash type

```bash
cat ntlm.txt | python3 hash-id.py
```

`hash-id.py` returned a list of possible matches, with **MD5** flagged as the top "Possible Hash" and NTLM only showing up further down under "Least Possible Hashes":

```
Possible Hashes:
[+] MD5
[+] Domain Cached Credentials - MD4(MD4(($pass)).(strtolower($username)))

Least Possible Hashes:
[+] RAdmin v2.x
[+] NTLM
[+] MD4
[+] MD2
[+] MD5(HMAC)
```

### Step 2 – Crack it with John the Ripper

Even though the tool's top guess was MD5, the context gives it away — this hash came from a Windows authentication database, so it's almost certainly **NTLM**. The format flag for NTLM in John is `nt`:

```bash
john --format=nt -wordlist=rockyou.txt ntlm.txt
```

**Output:**
```
mushroom         (?)
1g 0:00:00:00 DONE (2026-10-15 15:14) 100.0g/s 819200p/s 819200c/s 819200C/s 123456..whitiger
```

| Field | Value |
|---|---|
| **Hash Type** | NTLM |
| **Format Flag** | `nt` |
| **Result** | `mushroom` |

---

## 📊 Results Summary

| # | File | Hash Type | Format Flag | Cracked Password |
|---|---|---|---|---|
| 1 | `ntlm.txt` | NTLM | `nt` | `mushroom` |

---

## 🧠 Reflections / Notes

- **`hash-id.py` is a guess, not a guarantee.** On this hash it ranked MD5 as the top "Possible Hash" and buried NTLM all the way down in "Least Possible Hashes." Looking at the output alone, you'd be tempted to run `--format=raw-md5` and get nowhere.
- **Context beats the tool's output.** When you're dealing with Windows and service passwords — stuff pulled from a SAM dump or NTDS.dit — chances are you're looking at NTLM, not plain MD5. NTLM and MD5 hashes are structurally similar in length/format, which is exactly why identifiers like `hash-id.py` can mix them up.
- **John handles NTLM with its own flag: `nt`.** It doesn't follow the `raw-` prefix convention used for MD5/SHA family hashes — it's just `--format=nt`.
- This is a good reminder that hash identification tools are a starting point, not the final word. Knowing *where* a hash came from (SAM, NTDS.dit, a web app's user table, etc.) is often more reliable than what an automated identifier spits out.
- `rockyou.txt` cracked it almost instantly, which says more about the weakness of the password than about NTLM itself — NTLM has no salting, so once you have the hash, speed is really only limited by your wordlist and hardware.

---

## 📚 Key Skills Demonstrated

- Recognizing the limitations of automated hash identification tools
- Correlating a hash's **origin/context** (Windows authentication) with its likely type (NTLM)
- Applying the correct John the Ripper format flag (`nt`) for NTLM hashes
- Dictionary-based offline password cracking using **John the Ripper**
- Understanding why NTLM hashes are fast to crack (unsalted)

---

## 🔗 References

- [TryHackMe – John the Ripper: The Basics](https://tryhackme.com/room/johntheripper0)
- [John the Ripper Official Documentation](https://www.openwall.com/john/)
- [Kali Linux – Pre-installed Tools](https://www.kali.org/tools/john/)

---

*This lab is a solid reminder that tooling output needs to be sanity-checked against context. An NTLM hash identified as "MD5" by an automated script would have sent a less careful analyst down the wrong path entirely.*

