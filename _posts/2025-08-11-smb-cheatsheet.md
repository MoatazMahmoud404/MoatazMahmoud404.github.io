---
title: "SMB (Server Message Block) Cheatsheet"
date: 2025-08-11
categories: ["Cybersecurity", "Penetration Testing", "Network Security"]
tags: ["SMB", "Windows", "Network Security", "Penetration Testing", "Metasploit", "Nmap", "SMBMap", "Enum4Linux", "Cheatsheet"]
author: 0xReDrag0n
author_bio: "Cybersecurity researcher and penetration tester with expertise in network security and Windows exploitation"
image:
  path: "v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-08-11-smb-cheatsheet%2Fbanner.jpg?alt=media&token=5a33332d-6a5c-4018-87e1-712cc60f4683"
seo:
  title: "SMB (Server Message Block) Cheatsheet"
  description: "Master SMB enumeration, exploitation, and security with our comprehensive cheatsheet. Learn Nmap, SMBMap, Metasploit, and more tools for SMB security testing."
  keywords: "SMB, Server Message Block, Windows security, penetration testing, network enumeration, SMBMap, Metasploit, Nmap, cybersecurity cheatsheet"
published: true
---

{% include pageviews.html %}

# SMB (Server Message Block) Cheatsheet

## Table of Contents

1. [SMB Basics](#smb-basics)
2. [SMB Versions & Ports](#smb-versions--ports)
3. [SMB Security Risks & Exploits](#smb-security-risks--exploits)
4. [Mitigations](#mitigations)
5. [Nmap Scanning](#nmap-scanning)
6. [SMBMap](#smbmap)
7. [Metasploit](#metasploit)
8. [Enum4Linux](#enum4linux)
9. [Smbclient](#smbclient)
10. [Rpcclient](#rpcclient)
11. [Hydra](#hydra)
12. [Other Tools](#other-tools)
13. [Miscellaneous Commands](#miscellaneous-commands)
14. [Quick Reference](#quick-reference)

---

> **comprehensive guide for cybersecurity professionals, penetration testers, and security researchers working with SMB protocols**

## SMB Basics

**SMB (Server Message Block)** is a network file-sharing protocol used to allow computers to share files, printers, and other resources over a network. It is primarily used in Windows environments, but also supported on Linux and macOS via Samba.

> **Did You Know**: SMB was originally developed by IBM in the 1980s and later adopted by Microsoft for Windows networking.
{: .prompt-info }

### How SMB Works

- **File Sharing**: Allows users to access shared files and directories on remote computers
- **Printer Sharing**: Enables networked printers to be accessed remotely
- **Authentication**: Uses usernames and passwords to control access to shared resources
- **Interprocess Communication (IPC)**: Supports communication between processes running on different systems

---

## SMB Versions & Ports

| SMB Version | Year | Improvements                                               | Ports Used                        |
| ----------- | ---- | ---------------------------------------------------------- | --------------------------------- |
| SMBv1       | 1983 | Basic file sharing, insecure, vulnerable to attacks        | TCP 445, NetBIOS over TCP 137-139 |
| SMBv2       | 2006 | Performance improvements, reduced commands                 | TCP 445                           |
| SMBv3       | 2012 | Encryption, security enhancements                          | TCP 445                           |
| SMBv3.1.1   | 2015 | Stronger encryption (AES-GCM), authentication improvements | TCP 445                           |

**Ports**:

- **TCP**: 139, 445
- **UDP**: 137, 138 (NetBIOS)
- **Protocols**: SMBv1 (NT LM 0.12), SMBv2, SMBv3

> **Security Warning**: SMBv1 is highly vulnerable and should be disabled on all production systems. It's been exploited by major attacks like WannaCry and EternalBlue.
{: .prompt-warning }

---

## SMB Security Risks & Exploits

- **SMBv1 Vulnerabilities**:
  - EternalBlue (MS17-010): Used in WannaCry ransomware attacks
  - SMB Relay Attacks: Exploit NTLM authentication to gain access
- **SMB Enumeration**: Attackers use tools like:
  - `smbclient` (Linux) – List SMB shares
  - `enum4linux` – Gather SMB and user information
  - `Metasploit` – Exploit SMB vulnerabilities

> **Critical Alert**: EternalBlue (MS17-010) affected over 200,000 systems worldwide and caused billions in damages. Always patch your systems!
{: .prompt-danger }

---

## Mitigations

- Disable SMBv1 (Outdated and insecure)
- Use SMB Signing & Encryption to prevent tampering
- Restrict Access to only authorized users

> **Security Best Practice**: Implement the principle of least privilege and regularly audit SMB access permissions.
{: .prompt-tip }

---

## Nmap Scanning

> **Tool Spotlight**: Nmap is the Swiss Army knife of network scanning and provides extensive SMB enumeration capabilities.
{: .prompt-info }

### General Scanning

```bash
# Basic scan
nmap <TARGET_IP>

# Service version detection
nmap -sV -p 139,445 <TARGET_IP>

# Comprehensive scan
sudo nmap -p445 -sV -sC -O <TARGET_IP>
```

### SMB Protocol Enumeration

```bash
# Enumerate SMB protocols
nmap -p445 --script smb-protocols <TARGET_IP>

# SMB OS discovery
nmap --script smb-os-discovery -p 445 <TARGET_IP>

# SMB security mode
nmap -p445 --script smb-security-mode <TARGET_IP>

# SMB2 support
nmap -p445 --script smb2 <TARGET_IP>
```

> **Pro Tip**: Always run Nmap with sudo when using advanced scripts to ensure proper access to network interfaces.
{: .prompt-tip }

### SMB User & Share Enumeration

```bash
# Enumerate SMB users
nmap -p445 --script smb-enum-users --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>

# Enumerate SMB shares
nmap -p445 --script smb-enum-shares --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>

# Enumerate SMB sessions
nmap -p445 --script smb-enum-sessions --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>

# List files in shared folders
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>
```

### SMB Domain & Group Enumeration

```bash
# Enumerate SMB domains
nmap -p445 --script smb-enum-domains --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>

# Enumerate SMB groups
nmap -p445 --script smb-enum-groups --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>

# Enumerate SMB services
nmap -p445 --script smb-enum-services --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>
```

### SMB Statistics & Information

```bash
# Get SMB server statistics
nmap -p445 --script smb-server-stats <TARGET_IP>
```

---

## SMBMap

> **Tool Spotlight**: SMBMap is a powerful Python-based SMB enumeration tool that provides both command-line and interactive capabilities.
{: .prompt-info }

### Basic Enumeration

```bash
# Enumerate SMB shares with guest access
smbmap -u guest -p "" -H <TARGET_IP>
smbmap -u guest -p "" -d . -H <TARGET_IP>

# Authenticated enumeration
smbmap -u <USER> -p <PASS> -H <TARGET_IP>
smbmap -u <USERNAME> -p <PASSWORD> -d . -H <TARGET_IP>
```

> **Pro Tip**: Use the `-d .` flag to enumerate the current domain context, which often reveals more shares.
{: .prompt-tip }

### File Operations

```bash
# List drives
smbmap -u <USER> -p <PASS> -H <TARGET_IP> -L

# List directory contents
smbmap -u <USER> -p <PASS> -H <TARGET_IP> -r <SHARE_NAME>
smbmap -H <TARGET_IP> -u <USERNAME> -p <PASSWORD> -r '<DRIVE>$'

# Upload file
smbmap -u <USER> -p <PASS> -H <TARGET_IP> --upload <LOCAL_FILE> <REMOTE_PATH>
smbmap -H <TARGET_IP> -u <USERNAME> -p <PASSWORD> --upload '<LOCAL_FILE>' '<REMOTE_PATH>'

# Download file
smbmap -u <USER> -p <PASS> -H <TARGET_IP> --download <REMOTE_FILE>
smbmap -H <TARGET_IP> -u <USERNAME> -p <PASSWORD> --download '<REMOTE_FILE>'
```

### Command Execution

```bash
# Run command on target
smbmap -u <USER> -p <PASS> -H <TARGET_IP> -x '<COMMAND>'
smbmap -H <TARGET_IP> -u <USERNAME> -p <PASSWORD> -x '<COMMAND>'
```

> **Security Warning**: Command execution capabilities should only be used on systems you own or have explicit permission to test.
{: .prompt-warning }

---

## Metasploit

> **Tool Spotlight**: Metasploit Framework provides the most comprehensive SMB exploitation and enumeration modules in the cybersecurity toolkit.
{: .prompt-info }

### SMB Enumeration

```bash
# Enumerate SMB version
use auxiliary/scanner/smb/smb_version
set RHOSTS <TARGET_IP>
exploit

# Enumerate SMB shares
use auxiliary/scanner/smb/smb_enumshares
set RHOSTS <TARGET_IP>
exploit

# Enumerate SMB users
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS <TARGET_IP>
exploit

# Enumerate SMB2 support
use auxiliary/scanner/smb/smb2
set RHOSTS <TARGET_IP>
exploit
```

### SMB Authentication & Exploitation

```bash
# Brute force login
use auxiliary/scanner/smb/smb_login
set PASS_FILE <WORDLIST_PATH>
set SMBUser <USERNAME>
set RHOSTS <TARGET_IP>
exploit

# Enumerate named pipes
use auxiliary/scanner/smb/pipe_auditor
set SMBUser <USERNAME>
set SMBPass <PASSWORD>
set RHOSTS <TARGET_IP>
exploit
```

> **Pro Tip**: Use `set THREADS 10` in Metasploit to speed up enumeration tasks, but be mindful of network performance impact.
{: .prompt-tip }

---

## Enum4Linux

> **Tool Spotlight**: Enum4Linux is a comprehensive SMB enumeration tool that combines multiple techniques into one powerful utility.
{: .prompt-info }

### Basic Enumeration

```bash
# Full enumeration
enum4linux -o <TARGET_IP>

# Enumerate users
enum4linux -U <TARGET_IP>

# Enumerate shares
enum4linux -S <TARGET_IP>

# Enumerate groups
enum4linux -G <TARGET_IP>

# Get machine list
enum4linux -M <TARGET_IP>

# Get name list dump
enum4linux -N <TARGET_IP>

# Get password policy information
enum4linux -P <TARGET_IP>

# Perform full basic enumeration
enum4linux -a <TARGET_IP>
```

### Authenticated Enumeration

```bash
# RID cycling with credentials
enum4linux -r -u <USER> -p <PASS> <TARGET_IP>
enum4linux -r -u <USERNAME> -p <PASSWORD> <TARGET_IP>
```

> **Pro Tip**: The `-a` flag performs a full basic enumeration and is perfect for initial reconnaissance.
{: .prompt-tip }

---

## Smbclient

> **Tool Spotlight**: Smbclient provides an interactive SMB client interface, similar to FTP, for manual exploration of SMB shares.
{: .prompt-info }

### Basic Operations

```bash
# List shares
smbclient -L //<TARGET_IP> -N
smbclient -L <TARGET_IP> -N

# Connect to share
smbclient //<TARGET_IP>/<SHARE_NAME> -U <USER>
smbclient //<TARGET_IP>/<SHARE_NAME> -U <USERNAME>
```

### Navigation Commands

```bash
# Navigate shares
cd <DIRECTORY>
ls
get <FILE>
put <FILE>

# List directory contents
smb: \> ls

# Download file
smb: \> get <FILE_NAME>

# Change directory
smb: \> cd <DIRECTORY_NAME>
```

> **Pro Tip**: Use `help` in smbclient to see all available commands, and `?` for command-specific help.
{: .prompt-tip }

---

## Rpcclient

> **Tool Spotlight**: Rpcclient allows direct interaction with Windows RPC services, providing low-level access to system information.
{: .prompt-info }

### Basic Connection

```bash
# Connect without credentials
rpcclient -U "" -N <TARGET_IP>
```

### Enumeration Commands

```bash
# Enumerate domain users
enumdomusers

# Lookup user SID
lookupnames <USERNAME>

# Enumerate domain groups
enumdomgroups
```

> **Security Warning**: RPC enumeration can generate significant log entries on target systems. Use responsibly.
{: .prompt-warning }

---

## Hydra

> **Tool Spotlight**: Hydra is a fast and flexible password brute-forcing tool that supports multiple protocols including SMB.
{: .prompt-info }

### Brute Force Login

```bash
# Brute force SMB passwords
hydra -l <USER> -P <WORDLIST_PATH> <TARGET_IP> smb
hydra -l <USERNAME> -P <WORDLIST_PATH> <TARGET_IP> smb
```

> **Critical Alert**: Password brute-forcing can trigger account lockouts and security alerts. Always ensure you have proper authorization!
{: .prompt-danger }

---

## Other Tools

> **Additional Tools**: These complementary tools enhance your SMB enumeration and exploitation toolkit.
{: .prompt-info }

### NetBIOS Operations

```bash
# NetBIOS lookup
nmblookup -A <TARGET_IP>
```

### File Operations

```bash
# Extract wordlist
gzip -d /usr/share/wordlists/rockyou.txt.gz

# Extract tar.gz file
tar -xf <FILE_NAME>.tar.gz
```

---

## Miscellaneous Commands

> **Reference Section**: Essential commands and information for comprehensive SMB security testing.
{: .prompt-info }

### Windows SMB Commands

```bash
# Clear stored SMB sessions
net use * /delete

# Map network drive
net use Z: \\<TARGET_IP>\C$ <PASSWORD> /user:<USERNAME>
```

### Named Pipes

Common named pipes: `netlogon`, `lsarpc`, `samr`, `eventlog`, `InitShutdown`, `ntsvcs`, `srvsvc`, `wkssvc`

### Null Session

Use `IPC$` share to enumerate users and shares without authentication

> **Security Warning**: Null sessions are often blocked on modern Windows systems but can still be useful for testing legacy systems.
{: .prompt-warning }

### Default Shares

- `ADMIN$`: Remote admin share
- `C$`, `D$`: Default drive shares
- `IPC$`: Inter-process communication
- `print$`: Printer drivers

---

## Quick Reference

> **Quick Access**: Essential commands and tools for rapid SMB enumeration and testing.
{: .prompt-tip }

### No Credentials Required

- `nmap -p445 --script smb-protocols <TARGET_IP>`
- `nmap -p445 --script smb-security-mode <TARGET_IP>`
- `nmap -p445 --script smb-os-discovery <TARGET_IP>`
- `nmap -p445 --script smb-server-stats <TARGET_IP>`
- `smbclient -L //<TARGET_IP> -N`
- `rpcclient -U "" -N <TARGET_IP>`
- `enum4linux -o <TARGET_IP>`

### Credentials Required

- `nmap -p445 --script smb-enum-users --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>`
- `nmap -p445 --script smb-enum-shares --script-args smbusername=<USER>,smbpassword=<PASS> <TARGET_IP>`
- `smbmap -u <USER> -p <PASS> -H <TARGET_IP>`
- `enum4linux -r -u <USER> -p <PASS> <TARGET_IP>`

### Essential Tools

1. **Nmap** - Port scanning and service enumeration
2. **SMBMap** - Share enumeration and file operations
3. **Metasploit** - Exploitation and advanced enumeration
4. **Enum4Linux** - Comprehensive SMB enumeration
5. **Smbclient** - Interactive SMB client
6. **Rpcclient** - RPC enumeration
7. **Hydra** - Password brute forcing


<!-- comments -->
{% include comments.html %}
{% include analytics.html %}
