# 🚩 Security Writeups

> My personal vault of penetration-testing and CTF writeups — TryHackMe, HackTheBox, OverTheWire, HackViser, HackSmarter, and assorted CTFs. Written as I solved them, kept in Obsidian, and published to GitBook.

[![Read on GitBook](https://img.shields.io/badge/Read%20on-GitBook-3884FF?logo=gitbook&logoColor=white)](https://gh0ulh4x.gitbook.io/writeups)
[![Writeups](https://img.shields.io/badge/writeups-231-brightgreen)]()
[![Platforms](https://img.shields.io/badge/platforms-6-blue)]()
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey)](https://creativecommons.org/licenses/by/4.0/)

📖 **Rendered version:** https://YOUR-SPACE.gitbook.io/writeups

---

## 📚 About

This repo is the source for my GitBook site and a mirror of my Obsidian vault. Each writeup documents the full path from recon to root: the scans, the enumeration, the vulnerability, the exploit, and privilege escalation — including the dead ends, because those are where most of the learning happens.

The goal is understanding, not flag-copying. **Passwords, cracked hashes, and flags are deliberately omitted** from every writeup in line with each platform's content-sharing rules.

## 📊 What's Inside

| Platform                                                                       | Writeups | Focus                                            |
| ------------------------------------------------------------------------------ | -------: | ------------------------------------------------ |
| <img src=".gitbook/assets/tryhackme.png" height="20" alt=""> **TryHackMe**     |      155 | Boot2root machines, AD, forensics, seasonal CTFs |
| <img src=".gitbook/assets/hackthebox.png" height="20" alt=""> **HackTheBox**   |       22 | Machines, challenges, Seasons 8–11               |
| <img src=".gitbook/assets/overthewire.png" height="20" alt=""> **OverTheWire** |       35 | Bandit wargame, full level-by-level              |
| <img src=".gitbook/assets/hackviser.png" height="20" alt=""> **HackViser**     |        8 | Warmup range (SQLi, file upload, etc.)           |
| <img src=".gitbook/assets/hacksmarter.png" height="20" alt=""> **HackSmarter** |        4 | Assumed-breach AD red-team labs                  |
| <img src=".gitbook/assets/ctf.png" height="20" alt=""> **CTFs**                |        7 | BSides, Payatu, Hego LLM range, one-offs         |
|                                                                                |  **231** |                                                  |

## 🗂️ Structure

```
Writeups/
├── TryHackMe/
│   ├── Easy/ · Medium/ · Hard/          # by difficulty
│   ├── Active Directory/                # topic rooms
│   ├── AI Security Room/ · Hackify/     # AI / crypto
│   ├── Industrial Intrusion/ · Valentine/  # event CTFs
│   ├── Hacker Holiday/ · Info Room/
│   └── Overflow The Jackpot CTF/
├── HackTheBox/
│   ├── Machines/ · Challenges/
│   └── Season 8/ · 9/ · 10/ · 11/
├── OverTheWire/
│   └── bandit/                          # level-0 → level-34
├── HackViser/Warmup/
├── HackSmarter/                         # AD red-team labs
└── CTFs/
    ├── bsides-2025/ · payatu-ctf/ · hego/
    └── unsorted-challenges/
```

Most sections have a `README.md` acting as a short intro / landing page for that folder.

## 📝 Writeup Format

Writeups follow a consistent shape so they're easy to skim:

```markdown
## Description # official machine/challenge brief

## IP-Address # target(s)

## Enumeration # RustScan / Nmap, service discovery

## Exploitation # foothold — the actual vuln and how it was abused

## Privilege Escalation

## Flag # [flag omitted]
```

> ℹ️ **Notes:** Files are authored in Obsidian, so a few may contain wiki-style
> `[[links]]` or `![[image embeds]]` that render best there. On GitBook these
> resolve as normal links/images once assets are synced.

## 🧰 Tooling You'll See a Lot

Reconnaissance and exploitation lean on a familiar toolkit:

`RustScan` · `Nmap` · `ffuf` / `gobuster` / `feroxbuster` · `Burp Suite` ·
`Hydra` · `WPScan` · `sqlmap` · `Impacket` · `NetExec / nxc` · `BloodHound` ·
`Certipy` · `Kerbrute` · `evil-winrm` · `John` / `Hashcat` · `LinPEAS` · `Ligolo`

## ⚙️ GitBook Sync

The site is kept in sync with this repo using GitBook **Git Sync**.

1. In GitBook: space → **Configure → GitHub/GitLab Sync**, authorize, and select this repo + the `main` branch.
2. Pick **GitHub → GitBook** for the initial sync so the repo is the source of truth.
3. `SUMMARY.md` defines the sidebar — add new writeups there or they won't appear in navigation.

A minimal `.gitbook.yaml`:

```yaml
root: ./
structure:
  readme: README.md
  summary: SUMMARY.md
```

## ➕ Adding a Writeup

1. Create the note under the right platform/difficulty folder.
2. Keep to the format above and **omit all flags, passwords, and hashes.**
3. Add the entry to `SUMMARY.md`.
4. Commit and push:

   ```bash
   git add .
   git commit -m "Add writeup: <platform> - <name>"
   git push
   ```

## ⚠️ Disclaimer

For **educational and ethical-hacking purposes only.** Everything here targets
intentionally vulnerable lab machines or CTF challenges. Event/CTF writeups are
published after the event ends; HTB content only after retirement. Never apply
these techniques to systems you don't have explicit permission to test.

## 📄 License

Writeups: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Scripts and
code snippets: MIT unless noted otherwise.

---

⭐ If a writeup helped you get unstuck, a star is appreciated.
