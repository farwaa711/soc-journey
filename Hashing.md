# 🔐 Hashing — SOC L1 Notes

## 1. What is Hashing?

**Hashing** converts data into a fixed-length value called a **hash**.

Think of it like a **digital fingerprint** of a file.

```text
File → Hash Function → Hash
```

Example:

```text
malware.exe
    ↓ SHA-256
8f434346648f6b96...
```

---

## 2. Important Hashing Rules

### Same file contents → Same hash

```text
File A: "SOC L1"
File B: "SOC L1"

SHA-256:
Same hash
```

The filenames do **not** matter.

### Change the contents → Different hash

```text
"SOC L1"
     ↓
"SOC L1!"

Different content → Different hash
```

Even a tiny change can produce a completely different hash.

---

## 3. Hashing vs Encryption

| Hashing | Encryption |
|---|---|
| One-way | Reversible |
| No decryption key | Uses a key |
| Used for identification/integrity | Used to protect data |
| Example: SHA-256 | Example: AES |

**Remember:**

> Hashing = fingerprint  
> Encryption = locked box

---

## 4. Hashing vs Encoding

Encoding is **not security**.

Example:

```text
Hello
↓ Base64
SGVsbG8=
```

Base64 can easily be decoded.

Hashing is designed to be one-way.

---

# 5. Common Hash Algorithms

| Algorithm | SOC Knowledge |
|---|---|
| MD5 | Old/weak, but still seen as a file identifier |
| SHA-1 | Old/weak |
| SHA-256 | ⭐ Very commonly used |
| SHA-512 | Strong modern hash |

For SOC work, **SHA-256** is especially important.

---

# 6. What is a Hash Collision?

A **collision** happens when two different inputs produce the same hash.

```text
File A ──→ Hash X
File B ──→ Hash X
```

MD5 and SHA-1 have known collision weaknesses.

For modern file identification, **SHA-256** is commonly preferred.

---

# 7. Why SOC Analysts Use Hashes

Suppose a suspicious file is found:

```text
invoice.exe
```

The analyst calculates its SHA-256:

```text
SHA-256:
abc123...
```

Then the analyst can search for that hash in:

- Threat intelligence
- SIEM
- EDR
- Malware databases
- Other security tools

The goal is to answer:

```text
Is this file known?
↓
Where else did it appear?
↓
Which users/hosts have it?
↓
What processes executed it?
↓
What network connections did it make?
```

---

# 8. Important SOC Limitation

A hash identifies **exact file contents**.

If malware is modified:

```text
Malware A → Hash 111
Modified Malware A → Hash 222
```

The hashes are different even though the malware may be related.

So:

> Different hash ≠ automatically different malware.

Hashing is one **indicator**, not the entire investigation.

---

# 🧪 9. Practical — Create a File and Hash It

### Step 1: Create a file

```bash
echo "SOC L1" > hash-test.txt
```

### Step 2: Calculate SHA-256

```bash
sha256sum hash-test.txt
```

You will get something like:

```text
8f434346648f6b96...  hash-test.txt
```

Your actual hash will be different depending on the exact contents.

---

# 🧪 10. Practical — Change the File

Now change the contents:

```bash
echo "SOC L1!" > hash-test.txt
```

Calculate the hash again:

```bash
sha256sum hash-test.txt
```

You should get a **different hash**.

### Why?

Because:

```text
SOC L1
  ↓
Hash A

SOC L1!
  ↓
Hash B
```

One character changed → different file contents → different hash.

---

# 🧪 11. Practical — Same Contents, Different Filename

Create another file:

```bash
echo "SOC L1" > hash-test2.txt
```

Calculate its hash:

```bash
sha256sum hash-test2.txt
```

Now compare it with:

```bash
echo "SOC L1" > hash-test.txt
sha256sum hash-test.txt
```

If both files contain exactly:

```text
SOC L1
```

their SHA-256 hashes should be **identical**.

This proves:

> Hash depends on the file's contents, not its filename.

---

# 🧪 12. Other Useful Hash Commands

### SHA-256

```bash
sha256sum file.txt
```

### SHA-512

```bash
sha512sum file.txt
```

### MD5

```bash
md5sum file.txt
```

For SOC work, prefer **SHA-256** when possible.

---

# 🛡️ 13. SOC Investigation Example

Alert:

```text
Suspicious file detected:
invoice.exe
Host: PC-042
```

SOC L1 workflow:

```text
1. Identify the file
        ↓
2. Calculate SHA-256
        ↓
3. Search the hash in threat intelligence
        ↓
4. Search SIEM/EDR for the same hash
        ↓
5. Find other affected hosts
        ↓
6. Check process activity
        ↓
7. Check network connections
        ↓
8. Determine scope
```

Example:

```text
SHA-256: abc123...

PC-042   → Found
PC-017   → Found
PC-031   → Found
PC-055   → Found
```

Now the SOC knows the same file appeared on multiple machines and can investigate those systems.

---

# 🎯 14. SOC L1 Hashing Checklist

When you see a suspicious file:

- [ ] Identify the file
- [ ] Calculate SHA-256
- [ ] Search the hash in threat intelligence
- [ ] Search SIEM/EDR for the hash
- [ ] Identify affected hosts
- [ ] Identify affected users
- [ ] Check process activity
- [ ] Check network connections
- [ ] Check file source
- [ ] Check persistence
- [ ] Determine the scope

---

## 🧠 Quick Memory Trick

```text
HASH = FILE FINGERPRINT

Same contents
     ↓
Same hash

Changed contents
     ↓
Different hash

SOC uses hash
     ↓
Identify + Search + Scope
```

### Most important command

```bash
sha256sum suspicious-file
```

### Most important SOC idea

> A file hash helps an analyst identify and track the exact file across systems.
