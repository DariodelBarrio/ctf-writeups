# Agua de Mayo — DockerLabs

| Field      | Value              |
|------------|--------------------|
| Platform   | DockerLabs         |
| OS         | Linux              |
| Difficulty | Easy               |
| Tags       | gobuster, Brainfuck steganography, SSH, sudo bettercap, SUID bash |

---

## 1. Reconnaissance

```bash
nmap -sV -sC <IP>
```

<img width="718" height="198" alt="nmap scan" src="https://github.com/user-attachments/assets/a2203fbd-6567-4f1b-b5b8-5933e0e81cc1" />

Ports open: 22 (SSH) and 80 (HTTP).

---

## 2. Web Enumeration

Accessed port 80 — the page appeared empty at first glance.

<img width="958" height="502" alt="Web page — blank" src="https://github.com/user-attachments/assets/378fda4f-0155-4194-ac5f-6803dfce8523" />

Inspected the HTTP response with curl instead of relying on the browser render:

```bash
curl http://<IP>/
```

<img width="1847" height="165" alt="curl — Brainfuck code in HTML comment" src="https://github.com/user-attachments/assets/e9039437-b23d-491a-9b54-9650cde00a06" />

Hidden inside an HTML comment (`<!-- -->`) was a block of **Brainfuck** code. Brainfuck is an esoteric programming language that uses only 8 characters (`+`, `-`, `>`, `<`, `[`, `]`, `.`, `,`). It's occasionally used in CTFs to obscure simple strings.

<img width="966" height="141" alt="Brainfuck code identified" src="https://github.com/user-attachments/assets/c9da437e-1b7d-4e6b-8bca-33e92ea417c9" />

> **Important:** To decode correctly, strip the HTML comment delimiters first and pass only the raw Brainfuck characters to an online decoder.

<img width="967" height="223" alt="Brainfuck decoded — password" src="https://github.com/user-attachments/assets/3f57d5f8-e848-4a8a-b0ec-b2eb0aa3a581" />

Decoded output was a password.

---

## 3. Directory Fuzzing

```bash
gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirb/common.txt
```

<img width="870" height="483" alt="gobuster results" src="https://github.com/user-attachments/assets/b7eef1e7-0833-44ad-ad9c-142d28a42003" />

Found a directory containing an image file.

---

## 4. Image Analysis — Username from Filename

Downloaded the image and analyzed it for steganographic content with multiple tools. No hidden data was found in the file itself.

<img width="608" height="305" alt="Image found" src="https://github.com/user-attachments/assets/3e476cb2-2451-4517-bedc-0f71d759d5b3" />

<img width="631" height="463" alt="Steganography tools — no data found" src="https://github.com/user-attachments/assets/1c98de91-924e-4f1c-98cb-029c657b0671" />

<img width="623" height="76" alt="Image filename: agua" src="https://github.com/user-attachments/assets/4da354ad-61f7-437b-bf16-ddbdee559f30" />

The filename was `agua` — matching the machine name. In CTFs, this kind of naming hint is intentional. Used `agua` as the SSH username with the Brainfuck-decoded password.

---

## 5. SSH Access

```bash
ssh agua@<IP>
```

<img width="742" height="327" alt="SSH login as agua" src="https://github.com/user-attachments/assets/c87c8053-c4f8-4894-a1e6-78c69a229b72" />

---

## 6. Privilege Escalation — sudo bettercap

```bash
sudo -l
```

<img width="1155" height="122" alt="sudo -l — bettercap as root" src="https://github.com/user-attachments/assets/b030f7db-10bb-4141-afa1-1364e7d35add" />

`agua` could run `bettercap` as root with no password. Bettercap is a network analysis and attack framework. Less obviously, it also supports an interactive REPL that can execute OS commands using the `!` prefix.

<img width="1081" height="110" alt="bettercap — ! command execution" src="https://github.com/user-attachments/assets/010861ce-3ecf-41f3-8c74-45bd4db80b30" />

<img width="1282" height="267" alt="bettercap — chmod +s /bin/bash" src="https://github.com/user-attachments/assets/d7b27154-073b-4f04-a2a1-edbc94016cbc" />

From inside the bettercap REPL:

```
!chmod +s /bin/bash
```

<img width="547" height="87" alt="SUID set on /bin/bash" src="https://github.com/user-attachments/assets/22d0ddad-e655-4618-8261-2e05f73b878b" />

This set the SUID bit on `/bin/bash`. Exited bettercap and ran:

```bash
/bin/bash -p
```

<img width="392" height="97" alt="Root shell" src="https://github.com/user-attachments/assets/db5718e1-7725-4645-af08-6cf0b85ae31a" />

---

## Key Takeaways

**Always read page source, not just the rendered view.** Brainfuck in an HTML comment is invisible in a browser but trivial to find with `curl` or DevTools. Automated scanners often miss this.

**Image metadata and filenames are both worth checking.** When steganographic analysis turns up nothing, the filename itself can be the clue. Naming an image after the machine or a user is a common CTF convention.

**Bettercap's `!` operator executes OS commands as the running user.** Any tool with built-in shell access (bettercap, vim, less, man, python, etc.) is a privilege escalation vector when run with elevated permissions.
