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
| [Wargames](docker-labs/wargames/) | Easy | Linux | WOPR telnet, Prompt injection, SHA-256 hash lookup, SUID godmode binary |

## TryHackMe

| Máquina | Dificultad | SO | Técnicas |
|---------|------------|-----|----------|
| [RootMe](try-hack-me/rootme/) | Easy | Linux | File Upload Extension Bypass (.phtml), SUID Python |
| [Game Zone](try-hack-me/game-zone/) | Easy | Linux | SQLi, sqlmap, John SHA-256, SSH Port Forwarding, Webmin RCE |
| [Basic Pentesting](try-hack-me/basic-pentest/) | Easy | Linux | gobuster, SMB, hydra SSH, SSH key cracking, sudo |
| [Network Services](try-hack-me/network-services/) | Easy | Linux | SMB null session, enum4linux, credential discovery |
| [Wreath](try-hack-me/wreath/) | Medium | Linux | Webmin CVE-2019-15107 RCE, SSH key extraction, pivoting |
| [TakeOver](try-hack-me/takeover/) | Easy | Linux | Subdomain fuzzing — pending |

## VulnHub

| Máquina | Dificultad | SO | Técnicas |
|---------|------------|-----|----------|
| [Kioptrix Level 1](vuln-hub/kioptrix-1/) | Easy | Linux | Samba 2.2.1a trans2open RCE, searchsploit, gcc |
| [Kioptrix Level 1.1](vuln-hub/kioptrix-1.1/) | Easy | Linux | SQLi auth bypass, Command Injection, Kernel exploit 2.6.9 |
| [Kioptrix Level 1.2](vuln-hub/kioptrix-1.2/) | Easy | Linux | gobuster, SQLi, sqlmap, hash cracking, sudo ht, /etc/sudoers |

## INE Labs

| Lab | Dificultad | SO | Técnicas |
|-----|------------|-----|----------|
| [Exploitation CTF 1](labs-ine/exploitation-ctf-1/) | Easy | Linux | Flatcore CVE-2021-39608, WordPress Duplicator CVE-2020-11738, hydra SSH |
| [Exploitation CTF 2](labs-ine/exploitation-ctf-2/) | Easy | Windows | SMB brute force, NTLM hash reuse, FTP, msfvenom ASPX shell |
| [Windows SMB PSexec](labs-ine/windows-smb-psexec/) | Easy | Windows | WinRM, CrackMapExec brute force, Evil-WinRM |
| [LAB SMB](labs-ine/lab-smb/) | Easy | Linux | Samba 4.1 is_known_pipename RCE |
| [LAB Pivoting](labs-ine/lab-pivoting/) | Medium | Windows | HFS 2.3 RCE, autoroute, SOCKS proxy, BadBlue 2.7 bind_tcp |
