# 🚰 Piping and Redirection

## 🇺🇸 English

Connect tools together to perform complex tasks like a data pipeline.

### Operators

| Operator | Purpose | Example |
|----------|---------|---------|
| `\|` (Pipe) | Feed output of A to stdin of B | `cat file \| grep pattern` |
| `>` | Redirect output to file (overwrite) | `echo "text" > file` |
| `>>` | Redirect output to file (append) | `echo "text" >> file` |
| `<` | Read input from file | `base64 -d < encoded.txt` |
| `2>` | Redirect errors | `find / 2>/dev/null` |
| `2>&1` | Redirect errors to stdout | `command 2>&1 \| grep error` |

### Common Combinations

```bash
# Sort and find unique lines
sort file | uniq -u

# Search in binary files
strings binary | grep pattern

# Suppress errors
find / -name "file" 2>/dev/null

# Chain multiple operations
cat data | sort | uniq | grep pattern
```

---

## 🇪🇬 العربية

ربط الأوامر ببعضها عشان تنفذ مهام معقدة.

### بالبلدي
- الـ Pipe (`|`) زي ماسورة مياه بتوصل بين حنفيتين
- مخرج الأمر الأول → مدخل الأمر التاني

### الرموز

| الرمز | الغرض |
|-------|-------|
| `\|` | نقل الناتج للأمر التالي |
| `>` | حفظ الناتج في ملف (مسح المحتوى القديم) |
| `>>` | إضافة الناتج لملف (بدون مسح) |
| `<` | قراءة المدخل من ملف |
| `2>/dev/null` | إخفاء الأخطاء (رميها في الفراغ) |

### أمثلة عملية

```bash
# ترتيب وإيجاد السطور الفريدة
sort file | uniq -u

# إخفاء رسائل الخطأ
find / -name "file" 2>/dev/null
```

---

## 🇹🇷 Türkçe

Karmaşık görevleri gerçekleştirmek için araçları birbirine bağlayın.

### Operatörler

| Operatör | Amaç |
|----------|------|
| `\|` (Boru) | Bir komutun çıktısını diğerinin girdisi olarak kullan |
| `>` | Çıktıyı dosyaya yönlendir (üzerine yaz) |
| `>>` | Çıktıyı dosyaya ekle |
| `<` | Girdİyi dosyadan oku |
| `2>/dev/null` | Hataları gizle |

### Yaygın Kombinasyonlar

```bash
# Sırala ve tekil satırları bul
sort dosya | uniq -u

# Hataları gizle
find / -name "dosya" 2>/dev/null
```

---

### 🛠️ Related Levels

[[Bandit 6 Çözümü]] | [[Bandit 7 Çözümü]] | [[Bandit 8 Çözümü]] | [[Bandit 10 Çözümü]] | [[Bandit 14 Çözümü]]

