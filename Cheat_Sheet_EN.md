# 📚 Bandit Cheat Sheet (English)

## 🔧 Basic Commands

### File Navigation

| Command | Description |
|---------|-------------|
| `ls -la` | List all files (including hidden) |
| `cd dirname` | Change directory |
| `cat file` | Read file content |
| `cat ./-` | Read file named `-` |
| `file *` | Check all file types |

---

## 🔍 Search Commands

### Find Files
```bash
find . -type f -size 1033c               # By size
find / -user bandit7 -group bandit6      # By owner
find . -name "*.txt"                     # By name
```

### Search Inside Files (grep)
```bash
grep "pattern" file                      # Basic search
grep -r "pattern" /path                  # Recursive
grep -v "pattern" file                   # Exclude pattern
```

---

## 🔄 Data Processing

```bash
sort file | uniq -u                       # Unique lines only
base64 -d < file                          # Decode Base64
tr 'A-Za-z' 'N-ZA-Mn-za-m' < file        # ROT13 decode
strings file | grep "==="                 # Extract from binary
```

### Decompression
```bash
gzip -d file.gz
bzip2 -d file.bz2
tar -xf file.tar
xxd -r hexfile > binary                   # Reverse hexdump
```

---

## 🌐 Networking

### SSH
```bash
ssh user@host -p 2220                     # Basic connection
ssh -i key user@host                      # Key-based auth
ssh user@host "cat file"                  # Direct command execution
```

### Netcat & SSL
```bash
nc localhost 30000                        # TCP connection
echo "data" | nc localhost 30000          # Send data
openssl s_client -connect host:port       # SSL connection
nmap -p 31000-32000 localhost             # Port scan
```

---

## 🔑 Permissions

### SUID - Run as Owner
```bash
./setuid-program cat /etc/bandit_pass/bandit20
chmod 600 private_key                     # Key permissions
```

---

## 📅 Cron Jobs
```bash
ls /etc/cron.d/                           # List cron jobs
cat /etc/cron.d/cronjob_name              # Read cron job
```

---

## 🌿 Git
```bash
git clone ssh://user@host:port/path       # Clone repo
git log                                   # View history
git show <hash>                           # View commit
git branch -a                             # All branches
git checkout dev                          # Switch branch
git tag && git show secret                # Tags
git add -f file && git push               # Force push
```

---

## 🎭 Bypassing Restrictions

```bash
ssh user@host "cat file"                  # Bypass .bashrc
$0                                        # Escape Uppercase Shell
```

---

## 📝 Quick Level Summary

| Level | Concept | Key Command |
|:-----:|:--------|:------------|
| 0 | SSH | `ssh -p 2220` |
| 1-3 | Special filenames | `cat ./-` |
| 4 | File type | `file *` |
| 5-6 | Find | `find . -size 1033c` |
| 7 | grep | `grep "word" file` |
| 8 | Unique | `sort \| uniq -u` |
| 9 | Binary | `strings \| grep` |
| 10-11 | Encoding | `base64 -d`, `tr` |
| 12 | Compression | `gzip/bzip2/tar` |
| 13-16 | Networking | `ssh -i`, `nc`, `openssl` |
| 17 | Compare | `diff` |
| 18 | Bypass | `ssh ... "command"` |
| 19-20 | SUID | `./suid command` |
| 21-23 | Cron | `/etc/cron.d/` |
| 24 | Brute Force | `for i in {0000..9999}` |
| 27-31 | Git | `git log/branch/tag` |
| 32 | Shell | `$0` |

---

> **💡 Quick Tips:**
> - Always `ls -la` first
> - Use `2>/dev/null` to hide errors
> - `chmod 600` for private keys

---

**Made with ❤️ by [Hamed Mohamed](https://github.com/7amed3li)**
