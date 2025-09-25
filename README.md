# ToolBox-Assignment


# Intro to Hashcat — Practical Password Cracking for Defenders

---


**Intro to Hashcat**
Practical password cracking for defensive security
**Presented by:** *Maggie Friend*
**Course:** CYB301 — Immaculata University

**Note:**
Quick intro: what students will learn in 1–2 weeks: installing Hashcat, basic workflows, wordlist + mask + rule attacks, GPU usage, demo commands, and ethics/safe lab setup.

---

## What is Hashcat?

* Industry-standard password recovery / cracking tool
* Supports GPU-accelerated attacks (massive speed)
* Handles many hash types (MD5, NTLM, bcrypt\*, SHA variants, etc.)
* Primarily used for auditing and recovery

**Note:**
Hashcat is for legitimate uses: penetration testing with authorization, password recovery, and defensive auditing. Note some modern slow hashes (bcrypt, argon2) are intentionally slow — Hashcat supports some but may be less effective.

---

## Safety, legality & lab setup

* Only test on systems you own or explicitly authorized for testing
* Use isolated lab VMs and non-production data
* Store cracked passwords securely and responsibly
* Recommended lab: Kali VM (Hashcat installed), target VM with sample hashes

**Note:**
Policy and ethics: unauthorized cracking is illegal. Show instructor-approved lab workflow: generate sample hashes on a throwaway VM, use offline files, and log results.

---

## Install & prerequisites

* Requirements: compatible GPU (NVIDIA/AMD) or CPU-only fallback
* OS: Linux recommended (Ubuntu/Kali); Windows supported
* Install steps (summary):

  * On Ubuntu: `sudo apt install hashcat` or download from hashcat.net
  * GPU drivers + OpenCL/CUDA drivers required for GPU acceleration
* Useful files: rockyou.txt (wordlist), mask files, sample hash file

**Note:**
GPU drivers matter most. On Linux, install NVIDIA driver + CUDA or AMD ROCm/OpenCL. If no GPU, Hashcat runs CPU-only but much slower.

---

## Hashcat basics (workflow & format)

* Basic workflow: get hash(es) → identify hash type → choose attack mode → run Hashcat → analyze results
* Key files: `hashes.txt`, `wordlist.txt`
* Important options: `-m` (hash type), `-a` (attack mode), `-o` (output file), `--username`
* Common attack modes (`-a`):

  * 0 = straight (wordlist)
  * 1 = combination
  * 3 = mask (brute-force masks)
  * 6 = hybrid wordlist + mask
  * 7 = hybrid mask + wordlist


---

## Quick demo commands (copy/paste)

**1) Identify hash type (example):**
(Use `hashid` or `hash-identifier` tools — manual mapping shown in notes.)

**2) Straight (wordlist) attack:**

```
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
```

**3) Mask attack (example: 8-char lowercase):**

```
hashcat -m 0 -a 3 hashes.txt ?l?l?l?l?l?l?l?l
```

**4) Rule-based (apply rules):**

```
hashcat -m 0 -a 0 hashes.txt wordlist.txt -r rules/best64.rule
```

**5) Hybrid (wordlist + digit mask):**

```
hashcat -m 0 -a 6 hashes.txt wordlist.txt ?d?d?d
```



## Example: create a demo hash & crack it

**Create an MD5 hash (demo):**

```
echo -n "Password123" | md5sum | awk '{print $1}' > demo_hash.txt
```

**Crack with rockyou wordlist:**

```
hashcat -m 0 -a 0 demo_hash.txt /usr/share/wordlists/rockyou.txt --show
```

**Expected:** `Password123` cracked quickly (if in wordlist)

**Note:**
use `echo -n` to avoid newline. This demo shows the full pipeline: generate hash file, run Hashcat, examine cracked output. Stress that real-world passwords may require more advanced masks/rules.

---

## Speed tuning & GPU tips

* Use `--benchmark` to test performance: `hashcat -b`
* Use appropriate `-O` (optimized kernel) for faster speed (but beware max password length limits)
* Monitor GPU usage and temps (nvidia-smi)
* Use incremental masks, start with targeted masks (year suffixes, leetspeak)
* Use rulesets like `rules/best64.rule`, `rules/d3ad0ne.rule`

**Note:**
`-O` is faster but has limits. Always monitor temps and power. Tuning attack strategies (wordlists, targeted masks) yields better results than naive brute force.

---

## Advanced strategies & defenses

* Combine sources: breached password lists, custom company wordlists, mangling rules
* Use masks to target known patterns (e.g., `?u?l?l?l?d?d?d` for capital + letters + digits)
* Defense: enforce strong password policies, multi-factor auth, rate-limiting, detect leaked credentials, password blacklists
* For pentesters: capture hashes ethically and report remediation steps

**Note:**
Offensive knowledge informs defense: password complexity, password rotation is insufficient without MFA, and monitoring for credential stuffing.

---

## Resources & next steps

* Official: [https://hashcat.net/hashcat/](https://hashcat.net/hashcat/)
* Wordlists: `rockyou.txt`, SecLists (GitHub)
* Rulesets shipped with Hashcat (`rules/`)


---

```
### Hashcat — contributed by <Maggie Friend>
**Tool:** Hashcat — https://hashcat.net/hashcat  
**Format:** Documentation + demo commands (Markdown + notes)  
**Repo or Page:** <Your GitHub repo or page link>  
**Short description:** Intro to Hashcat install, basic attacks (wordlist, mask, rules), speed tuning, demo commands, and ethical lab setup.
**Notes:** Includes demo hashes and commands; GPU recommended for full performance.
```
Sources: Hashcat, Chatgpt
---

