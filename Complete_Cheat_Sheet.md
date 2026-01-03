# 📚 Bandit Complete Cheat Sheet
## ملخص شامل | Kapsamlı Özet | Complete Summary

---

## 🔧 الأوامر الأساسية | Basic Commands

### التنقل وعرض الملفات | File Navigation

| Command | Description |
|---------|-------------|
| `ls` | عرض الملفات |
| `ls -la` | عرض جميع الملفات (مع المخفية والتفاصيل) |
| `cd dirname` | الانتقال للمجلد |
| `pwd` | عرض المسار الحالي |
| `cat file` | قراءة محتوى ملف |
| `cat ./-` | قراءة ملف اسمه `-` |
| `cat "file name"` | قراءة ملف فيه مسافات |
| `file *` | معرفة نوع جميع الملفات |

---

## 🔍 البحث | Search Commands

### find - البحث عن الملفات

```bash
# البحث بالاسم
find . -name "filename"

# البحث بالحجم (1033 بايت)
find . -type f -size 1033c

# البحث بالمالك والمجموعة
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null

# البحث بالصلاحيات
find . -perm 644
```

### grep - البحث داخل الملفات

```bash
# البحث عن كلمة
grep "pattern" file

# البحث في عدة ملفات
grep -r "pattern" /path

# البحث بدون حساسية للحروف
grep -i "pattern" file

# عكس البحث (السطور التي لا تحتوي)
grep -v "pattern" file
```

---

## 🔄 معالجة البيانات | Data Processing

### الترتيب والتكرار

```bash
# ترتيب الأسطر
sort file

# إيجاد السطور الفريدة
sort file | uniq -u

# عد التكرارات
sort file | uniq -c
```

### التشفير وفك التشفير

```bash
# Base64
base64 -d < file              # فك التشفير
base64 file                   # تشفير

# ROT13
tr 'A-Za-z' 'N-ZA-Mn-za-m' < file

# استخراج النصوص من ملف ثنائي
strings file | grep "pattern"
```

### فك الضغط

```bash
# gzip
mv file temp.gz && gzip -d temp.gz

# bzip2
mv file temp.bz2 && bzip2 -d temp.bz2

# tar
tar -xf file.tar

# عكس hexdump
xxd -r file > output
```

---

## 🌐 الشبكات | Networking

### SSH - الاتصال الآمن

```bash
# اتصال عادي
ssh user@host -p 2220

# اتصال بمفتاح
ssh -i keyfile user@host -p 2220

# تنفيذ أمر بدون shell (لتجاوز .bashrc)
ssh user@host "cat file"
```

### Netcat - نقل البيانات

```bash
# الاتصال بمنفذ
nc localhost 30000

# إرسال بيانات
echo "data" | nc localhost 30000

# إنشاء خادم مستمع
nc -l -p 8080
```

### OpenSSL - اتصال SSL/TLS

```bash
# اتصال SSL
openssl s_client -connect localhost:30001 -quiet

# إرسال بيانات عبر SSL
echo "data" | openssl s_client -connect localhost:30001 -quiet
```

### Nmap - مسح المنافذ

```bash
# مسح نطاق منافذ
nmap -p 31000-32000 localhost

# معرفة الخدمات
nmap -sV localhost
```

---

## 🔑 الصلاحيات | Permissions

### صلاحيات الملفات

```
-rwxr-x---
 │││ │││
 │││ ││└── Others: --- (لا صلاحيات)
 │││ │└─── Others: --- 
 │││ └──── Group: r-x (قراءة + تنفيذ)
 ││└────── Owner: rwx (كل الصلاحيات)
 │└─────── Owner: rw-
 └──────── Type: - (ملف عادي)
```

### SUID - تشغيل بصلاحيات المالك

```bash
# البحث عن ملفات SUID
find / -perm -4000 2>/dev/null

# استخدام برنامج SUID
./setuid-program cat /etc/bandit_pass/bandit20
```

### تغيير الصلاحيات

```bash
# صلاحيات المفتاح الخاص (مطلوب لـ SSH)
chmod 600 private_key
```

---

## 📅 Cron Jobs - المهام المجدولة

```bash
# مكان ملفات cron
ls /etc/cron.d/

# قراءة مهمة cron
cat /etc/cron.d/cronjob_name

# قراءة السكريبت المُنفَّذ
cat /usr/bin/script.sh
```

---

## 🌿 Git - التحكم في الإصدارات

### الأساسيات

```bash
# استنساخ مستودع
git clone ssh://user@host:port/path

# عرض السجل
git log

# عرض تفاصيل commit
git show <hash>
```

### الفروع والعلامات

```bash
# عرض جميع الفروع
git branch -a

# التبديل لفرع
git checkout dev

# عرض العلامات
git tag

# عرض محتوى علامة
git show secret
```

### الدفع (Push)

```bash
# تجاوز .gitignore
git add -f file

# إعداد الهوية
git config --global user.email "email@example.com"
git config --global user.name "Name"

# Commit و Push
git commit -m "message"
git push origin master
```

---

## 🎭 تجاوز القيود | Bypassing Restrictions

### تجاوز .bashrc

```bash
# تنفيذ أمر مباشر بدون shell
ssh user@host "cat /etc/bandit_pass/user"

# استخدام shell مختلف
ssh user@host -t /bin/sh
```

### تجاوز الـ Uppercase Shell

```bash
# استخدام متغير $0
>> $0
$ cat /etc/bandit_pass/bandit33
```

---

## 📝 ملخص المستويات | Levels Summary

| Level | Concept | Key Command |
|:-----:|:--------|:------------|
| 0 | SSH | `ssh -p 2220` |
| 1 | Filename `-` | `cat ./-` |
| 2 | Spaces | `cat "file name"` |
| 3 | Hidden files | `ls -la` |
| 4 | File type | `file *` |
| 5 | Find + size | `find -size 1033c` |
| 6 | Find + user/group | `find / -user -group` |
| 7 | Grep | `grep "word" file` |
| 8 | Unique line | `sort \| uniq -u` |
| 9 | Strings | `strings \| grep` |
| 10 | Base64 | `base64 -d` |
| 11 | ROT13 | `tr 'A-Za-z' 'N-ZA-Mn-za-m'` |
| 12 | Compression | `gzip/bzip2/tar` |
| 13 | SSH Key | `ssh -i key` |
| 14 | Netcat | `nc localhost 30000` |
| 15 | SSL | `openssl s_client` |
| 16 | Port scan | `nmap -p` |
| 17 | Diff | `diff file1 file2` |
| 18 | SSH command | `ssh ... "cat file"` |
| 19 | SUID | `./suid cat file` |
| 20 | Client-Server | `nc -l -p` |
| 21-23 | Cron | `/etc/cron.d/` |
| 24 | Brute force | `for i in {0000..9999}` |
| 25-26 | SUID again | Same as 19 |
| 27 | Git clone | `git clone` |
| 28 | Git history | `git log/show` |
| 29 | Git branches | `git branch -a` |
| 30 | Git tags | `git tag/show` |
| 31 | Git push | `git push -f` |
| 32 | Shell escape | `$0` |

---

## 💡 نصائح سريعة | Quick Tips

> **دائماً افحص:**
> 1. `ls -la` - الملفات المخفية والصلاحيات
> 2. `file *` - أنواع الملفات
> 3. `/etc/cron.d/` - المهام المجدولة
> 4. SUID bit (`s` في الصلاحيات)
> 5. Git: log, branches, tags

> **لإخفاء الأخطاء:**
> `2>/dev/null`

> **للهروب من Shell مقيدة:**
> `$0` أو `ssh ... "command"`

---

<div align="center">

**Made with ❤️ by [Hamed Mohamed](https://github.com/7amed3li)**

</div>
