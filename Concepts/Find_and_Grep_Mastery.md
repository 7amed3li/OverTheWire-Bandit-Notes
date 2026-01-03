# 🔍 Find and Grep Mastery

## 🇺🇸 English

Two essential search tools with different purposes:

| Tool | Searches For | Example |
|------|-------------|---------|
| `find` | **Files** by name/attributes | `find / -name "file"` |
| `grep` | **Text** inside files | `grep "pattern" file` |

### Find Command Options

| Option | Purpose | Example |
|--------|---------|---------|
| `-name` | Search by filename | `find . -name "*.txt"` |
| `-type f` | Regular files only | `find . -type f` |
| `-type d` | Directories only | `find . -type d` |
| `-size` | Search by size | `find . -size 1033c` (bytes) |
| `-user` | Search by owner | `find / -user bandit7` |
| `-group` | Search by group | `find / -group bandit6` |
| `-perm` | Search by permissions | `find . -perm 644` |

### Grep Command Options

| Option | Purpose | Example |
|--------|---------|---------|
| `-r` | Recursive search | `grep -r "pattern" /dir` |
| `-i` | Case insensitive | `grep -i "Pattern" file` |
| `-v` | Invert match | `grep -v "exclude" file` |
| `-n` | Show line numbers | `grep -n "pattern" file` |

---

## 🇪🇬 العربية

**Find** و **Grep** أداتان أساسيتان للبحث بأغراض مختلفة:

### بالبلدي
- **Find**: ده "المساح" اللي بيدور على **مكان الملف** في الهارد
- **Grep**: ده "كشاف" بيدور على **كلمة معينة** جوه الملفات
- **تشبيه**: `find` بيدور على شنطة الهدوم، `grep` بيدور على التيشرت الأحمر جوه الشنطة

### خيارات find المهمة

| الخيار | الغرض |
|--------|-------|
| `-name` | البحث بالاسم |
| `-size 1033c` | البحث بالحجم (بالبايت) |
| `-user bandit7` | البحث بالمالك |
| `-type f` | ملفات عادية فقط |

### خيارات grep المهمة

| الخيار | الغرض |
|--------|-------|
| `-i` | تجاهل حالة الأحرف |
| `-v` | عكس النتيجة (اللي مش فيه الكلمة) |

---

## 🇹🇷 Türkçe

Farklı amaçlara sahip iki temel arama aracı.

| Araç | Aradığı Şey |
|------|-------------|
| `find` | Dosyaları ada/özniteliklere göre arar |
| `grep` | Dosyaların içindeki metinleri arar |

### Find Seçenekleri

| Seçenek | Amaç |
|---------|------|
| `-name` | Ada göre ara |
| `-size` | Boyuta göre ara |
| `-user` | Sahibine göre ara |
| `-type f` | Sadece dosyalar |

### Grep Seçenekleri

| Seçenek | Amaç |
|---------|------|
| `-r` | Rekürsif arama |
| `-i` | Büyük/küçük harf duyarsız |
| `-v` | Eşleşmeyenleri göster |

---

### 🛠️ Related Levels

[[Bandit 4 Çözümü]] | [[Bandit 5 Çözümü]] | [[Bandit 6 Çözümü]] | [[Bandit 7 Çözümü]] | [[Bandit 9 Çözümü]]

