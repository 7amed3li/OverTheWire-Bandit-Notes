# 📋 Bandit Quick Reference Card

<div align="center">

## بطاقة المرجع السريع | Hızlı Başvuru Kartı | Quick Reference

</div>

---

## 🗺️ Level Summary Table

| Level | Concept | Key Command | Password Location |
|:-----:|:--------|:------------|:------------------|
| 0 | SSH Connection | `ssh bandit0@... -p 2220` | Given |
| 1 | Special Filename `-` | `cat ./-` or `cat < -` | `readme` |
| 2 | Spaces in Filename | `cat "spaces in filename"` | `spaces in this filename` |
| 3 | Hidden Files | `ls -la`, `cat .hidden` | `.hidden` in `inhere` |
| 4 | File Types | `file ./*` | Human-readable file |
| 5 | Find with Criteria | `find . -type f -size 1033c` | File with specific size |
| 6 | System-wide Find | `find / -user -group -size 2>/dev/null` | `/var/lib/dpkg/info/` |
| 7 | Grep Pattern | `grep "millionth" data.txt` | `data.txt` |
| 8 | Unique Lines | `sort \| uniq -u` | `data.txt` |
| 9 | Strings in Binary | `strings data.txt \| grep ===` | `data.txt` |
| 10 | Base64 Decode | `base64 -d < data.txt` | `data.txt` |
| 11 | ROT13 | `tr 'A-Za-z' 'N-ZA-Mn-za-m'` | `data.txt` |
| 12 | Multi-compression | `file` + `gzip/bzip2/tar` | Hexdump to decompress |
| 13 | SSH Key Auth | `ssh -i keyfile user@host` | Private key in home |
| 14 | Netcat Submit | `nc localhost 30000` | Submit password to port |
| 15 | SSL Connection | `openssl s_client -connect host:port` | Submit over SSL |
| 16 | Port Scanning | `nmap -p 31000-32000 localhost` | Find correct SSL port |
| 17 | File Diff | `diff passwords.old passwords.new` | Changed line |
| 18 | SSH Command Exec | `ssh user@host "cat readme"` | Bypass `.bashrc` |
| 19 | SUID Binary | `./bandit20-do cat /etc/bandit_pass/bandit20` | SUID program |
| 20 | Client-Server | `nc -l -p 8080` + `./suconnect 8080` | Two terminals |
| 21 | Cron Jobs | `cat /etc/cron.d/cronjob_*` | Script copies password |
| 22 | Dynamic Cron | `echo ... \| md5sum \| cut -d ' ' -f 1` | Compute filename |
| 23 | Write Your Script | Create script in spool dir | Execute as `bandit24` |
| 24 | Brute Force | `for i in {0000..9999}; do ...; done \| nc` | 4-digit PIN |
| 25 | Restricted Shell | `ssh ... "cat /etc/bandit_pass/bandit26"` | Skip shell entirely |
| 26 | SUID Again | `./bandit27-do cat /etc/bandit_pass/bandit27` | SUID program |
| 27 | Git Clone | `git clone ssh://...` | `README` in repo |
| 28 | Git History | `git log`, `git show <hash>` | Old commit |
| 29 | Git Branches | `git branch -a`, `git checkout dev` | Dev branch |
| 30 | Git Tags | `git tag`, `git show secret` | Hidden tag |
| 31 | Git Push | Create file and `git push` | Server response |
| 32 | Uppercase Shell | `$0` to escape | Variable expansion |

---

## 🔧 Essential Commands Cheat Sheet

### File Operations
```bash
cat ./- or cat < -          # Read file named "-"
cat "file with spaces"      # Read file with spaces
ls -la                      # List all files including hidden
file *                      # Check file types
```

### Searching
```bash
find / -user X -group Y -size Zc 2>/dev/null
grep "pattern" file
strings binary_file | grep pattern
```

### Text Processing
```bash
sort file | uniq -u         # Find unique lines
base64 -d < file            # Decode base64
tr 'A-Za-z' 'N-ZA-Mn-za-m'  # ROT13
```

### Networking
```bash
nc localhost PORT           # Connect to port
nc -l -p PORT               # Listen on port
openssl s_client -connect host:port
nmap -p START-END host      # Scan ports
```

### Git
```bash
git clone ssh://user@host:port/path
git log                     # View history
git show HASH               # View commit
git branch -a               # List branches
git checkout BRANCH         # Switch branch
git tag                     # List tags
```

### Privilege Escalation
```bash
./setuid_binary command     # Run as owner
ssh user@host "command"     # Execute without shell
```

---

## 🎯 Quick Tips

> [!TIP]
> **Always check these first:**
> 1. `ls -la` - Hidden files and permissions
> 2. File permissions (`rwsr-x---` = SUID)
> 3. `/etc/cron.d/` for scheduled tasks
> 4. Git history, branches, and tags

---

<div align="center">

**Made with ❤️ by [Hamed Ali](https://github.com/7amed3li)**

</div>
