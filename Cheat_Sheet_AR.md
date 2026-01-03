# 📚 Bandit Cheat Sheet (العربية)

## 🔧 الأوامر الأساسية

### التنقل وعرض الملفات

| الأمر | الوصف |
|-------|-------|
| `ls -la` | عرض جميع الملفات (مع المخفية والتفاصيل) |
| `cd dirname` | الانتقال للمجلد |
| `cat file` | قراءة محتوى ملف |
| `cat ./-` | قراءة ملف اسمه `-` |
| `file *` | معرفة نوع جميع الملفات |

---

## 🔍 البحث

### البحث عن ملفات (find)
```bash
find . -type f -size 1033c               # بالحجم
find / -user bandit7 -group bandit6      # بالمالك
find . -name "*.txt"                     # بالاسم
```

### البحث داخل الملفات (grep)
```bash
grep "كلمة" file                         # بحث عادي
grep -r "كلمة" /path                     # بحث في كل الملفات
grep -v "كلمة" file                      # السطور بدون الكلمة
```

---

## 🔄 معالجة البيانات

```bash
sort file | uniq -u                       # السطر الفريد
base64 -d < file                          # فك Base64
tr 'A-Za-z' 'N-ZA-Mn-za-m' < file        # فك ROT13
strings file | grep "==="                 # استخراج نصوص من ملف ثنائي
```

### فك الضغط
```bash
gzip -d file.gz
bzip2 -d file.bz2
tar -xf file.tar
xxd -r hexfile > binary                   # عكس hexdump
```

---

## 🌐 الشبكات

### SSH
```bash
ssh user@host -p 2220                     # اتصال عادي
ssh -i key user@host                      # بمفتاح
ssh user@host "cat file"                  # تنفيذ أمر مباشر
```

### Netcat & SSL
```bash
nc localhost 30000                        # اتصال TCP
echo "data" | nc localhost 30000          # إرسال بيانات
openssl s_client -connect host:port       # اتصال SSL
nmap -p 31000-32000 localhost             # مسح المنافذ
```

---

## 🔑 الصلاحيات

### SUID - تشغيل بصلاحيات المالك
```bash
./setuid-program cat /etc/bandit_pass/bandit20
chmod 600 private_key                     # صلاحيات المفتاح
```

---

## 📅 Cron Jobs
```bash
ls /etc/cron.d/                           # عرض المهام
cat /etc/cron.d/cronjob_name              # قراءة مهمة
```

---

## 🌿 Git
```bash
git clone ssh://user@host:port/path       # استنساخ
git log                                   # عرض السجل
git show <hash>                           # عرض commit
git branch -a                             # كل الفروع
git checkout dev                          # تغيير الفرع
git tag && git show secret                # العلامات
git add -f file && git push               # دفع ملف
```

---

## 🎭 تجاوز القيود

```bash
ssh user@host "cat file"                  # تجاوز .bashrc
$0                                        # الهروب من Uppercase Shell
```

---

## 📝 ملخص سريع للمستويات

| المستوى | المفهوم | الأمر الرئيسي |
|:-------:|:--------|:-------------|
| 0 | SSH | `ssh -p 2220` |
| 1-3 | أسماء ملفات خاصة | `cat ./-` |
| 4 | نوع الملف | `file *` |
| 5-6 | البحث | `find . -size 1033c` |
| 7 | grep | `grep "word" file` |
| 8 | فريد | `sort \| uniq -u` |
| 9 | ثنائي | `strings \| grep` |
| 10-11 | تشفير | `base64 -d`, `tr` |
| 12 | ضغط | `gzip/bzip2/tar` |
| 13-16 | شبكات | `ssh -i`, `nc`, `openssl` |
| 17 | مقارنة | `diff` |
| 18 | تجاوز | `ssh ... "command"` |
| 19-20 | SUID | `./suid command` |
| 21-23 | Cron | `/etc/cron.d/` |
| 24 | Brute Force | `for i in {0000..9999}` |
| 27-31 | Git | `git log/branch/tag` |
| 32 | Shell | `$0` |

---

> **💡 نصائح سريعة:**
> - `ls -la` أولاً دائماً
> - `2>/dev/null` لإخفاء الأخطاء
> - `chmod 600` للمفاتيح الخاصة

---

**Made with ❤️ by [Hamed Mohamed](https://github.com/7amed3li)**
