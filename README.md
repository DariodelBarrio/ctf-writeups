# CTF Writeups

Writeups de máquinas resueltas en plataformas de CTF y pentesting.

## HackTheBox

| Máquina | Dificultad | SO | Técnicas |
|---------|------------|-----|----------|
| [Headless](hack-the-box/easy/headless/) | Easy | Linux | Blind XSS, Command Injection, Sudo Path Hijack |
| [Active](hack-the-box/easy/active/) | Easy | Windows | SMB Null Session, GPP Credentials, Kerberoasting |
| [Forest](hack-the-box/easy/forest/) | Easy | Windows | AS-REP Roasting, WriteDACL, DCSync, Pass-the-Hash |
| [Expressway](hack-the-box/easy/expressway/) | Easy | Linux | IKE/IPsec, PSK Hash Crack, Custom Sudo -h, Log Analysis |
| [GoodGames](hack-the-box/easy/goodgames/) | Easy | Linux | SQLi, SSTI/Jinja2 RCE, Docker Escape, SUID Bash |
| [CodePartTwo](hack-the-box/easy/codeparttwo/) | Easy | Linux | js2py Sandbox Escape, SQLite Credential Dump, sudo Binary Abuse |
| [Escape Two](hack-the-box/easy/escape-two/) | Easy | Windows | SMB, XLSX Credential Extraction, MSSQL xp_cmdshell, WinRM, AD CS |

## DockerLabs

| Máquina | Dificultad | SO | Técnicas |
|---------|------------|-----|----------|
| [WalkingCMS](docker-labs/walkingcms/) | Easy | Linux | WordPress, wpscan XML-RPC brute force, PHP reverse shell, SUID env |
| [Inyection](docker-labs/inyection/) | Easy | Linux | SQL Injection auth bypass, SSH, SUID env |
| [Move](docker-labs/move/) | Easy | Linux | Grafana CVE-2021-43798 Path Traversal, SSH, sudo writable Python script |
| [Domain](docker-labs/domain/) | Easy | Linux | SMB, enum4linux, netexec brute force, PHP webshell, SUID nano |
| [Trust](docker-labs/trust/) | Easy | Linux | gobuster, SSH, hydra brute force, sudo vim, /etc/passwd |
| [Upload](docker-labs/upload/) | Easy | Linux | Arbitrary File Upload, PHP reverse shell, sudo env |
| [Dance Samba](docker-labs/dance-samba/) | Easy | Linux | FTP anonymous, SMB, SSH key injection, Base32/Base64, sudo |
| [Little Pivoting](docker-labs/little-pivoting/) | Medium | Linux | Metasploit, autoroute, SOCKS proxy, port forwarding, Path Traversal |
| [Pressenter](docker-labs/pressenter/) | Easy | Linux | WordPress, malicious plugin, wp-config.php, MySQL, credential reuse |
| [Agua de Mayo](docker-labs/agua-de-mayo/) | Easy | Linux | Brainfuck in HTML, gobuster, SSH, sudo bettercap, SUID bash |
| [Wargames](docker-labs/wargames/) | Easy | Linux | FTP, SSH, HTTP — incomplete notes |
