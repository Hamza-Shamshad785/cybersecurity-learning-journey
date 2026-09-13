# Day 4 — SMB Enumeration & Exploitation

## Target

* **Target:** Metasploitable 2
* **IP:** 172.16.141.128
* **Environment:** Isolated lab

## Objective

Practice SMB enumeration, identify a version-specific vulnerability, and understand the complete penetration-testing workflow from enumeration to exploitation.

## 1. SMB Enumeration

Started by enumerating SMB with:

```bash
enum4linux -a 172.16.141.128
```

### Key findings

* Workgroup: `WORKGROUP`
* Hostname: `METASPLOITABLE`
* Samba version: `3.0.20-Debian`
* Anonymous/null SMB session was permitted
* Multiple SMB shares were discovered
* Multiple usernames were enumerated

### Discovered shares

```text
print$
tmp
opt
IPC$
ADMIN$
```

The `tmp` share was accessible anonymously and could be listed.

## 2. Share Enumeration

Connected to the accessible share:

```bash
smbclient //172.16.141.128/tmp -N
```

The share contained runtime/system files such as:

```text
.ICE-unix
.X11-unix
.X0-lock
5161.jsvc_up
```

No obvious credentials were discovered from the share.

## 3. Vulnerability Research

The identified Samba version was:

```text
Samba 3.0.20
```

Version-specific research identified the **Samba username map script command execution vulnerability**, which affects vulnerable Samba versions in this range.

## 4. Exploitation

The vulnerability was exploited in the isolated Metasploitable 2 lab using the corresponding Metasploit module.

Result:

```text
Remote command shell obtained
```

The resulting shell had **root privileges**, so privilege escalation was not required for this attack path.

## 5. Attack Chain

```text
SMB discovery
      ↓
Anonymous SMB enumeration
      ↓
Samba 3.0.20 identified
      ↓
Version-specific vulnerability research
      ↓
Username map script command execution
      ↓
Remote shell
      ↓
Root access
```

## 6. Lessons Learned

The main lesson from this exercise was the importance of enumeration before exploitation.

The workflow followed was:

**Enumeration → Version Identification → Vulnerability Research → Exploitation → Privilege Verification**

I also learned that obtaining a root shell directly means privilege escalation is unnecessary for that particular attack path.

## Tools Used

* Nmap
* enum4linux
* smbclient
* Metasploit Framework

## Lab Note

This exercise was performed against an intentionally vulnerable Metasploitable 2 machine in an isolated lab environment.
