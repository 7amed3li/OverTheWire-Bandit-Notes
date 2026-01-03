# 📚 Bandit Kopya Kağıdı (Türkçe)

## 🔧 Temel Komutlar

### Dosya Gezinmesi

| Komut | Açıklama |
|-------|----------|
| `ls -la` | Tüm dosyaları listele (gizliler dahil) |
| `cd dizin` | Dizin değiştir |
| `cat dosya` | Dosya içeriğini oku |
| `cat ./-` | `-` adlı dosyayı oku |
| `file *` | Tüm dosya türlerini kontrol et |

---

## 🔍 Arama Komutları

### Dosya Bulma (find)
```bash
find . -type f -size 1033c               # Boyuta göre
find / -user bandit7 -group bandit6      # Sahibine göre
find . -name "*.txt"                     # Ada göre
```

### Dosya İçinde Arama (grep)
```bash
grep "kelime" dosya                      # Temel arama
grep -r "kelime" /yol                    # Rekürsif
grep -v "kelime" dosya                   # Deseni hariç tut
```

---

## 🔄 Veri İşleme

```bash
sort dosya | uniq -u                      # Sadece tekil satırlar
base64 -d < dosya                         # Base64 çöz
tr 'A-Za-z' 'N-ZA-Mn-za-m' < dosya       # ROT13 çöz
strings dosya | grep "==="                # İkili dosyadan çıkar
```

### Sıkıştırma Açma
```bash
gzip -d dosya.gz
bzip2 -d dosya.bz2
tar -xf dosya.tar
xxd -r hexdosya > ikili                   # Hexdump tersine çevir
```

---

## 🌐 Ağ İletişimi

### SSH
```bash
ssh kullanici@sunucu -p 2220              # Temel bağlantı
ssh -i anahtar kullanici@sunucu           # Anahtar ile kimlik doğrulama
ssh kullanici@sunucu "cat dosya"          # Doğrudan komut çalıştır
```

### Netcat & SSL
```bash
nc localhost 30000                        # TCP bağlantısı
echo "veri" | nc localhost 30000          # Veri gönder
openssl s_client -connect sunucu:port     # SSL bağlantısı
nmap -p 31000-32000 localhost             # Port taraması
```

---

## 🔑 İzinler

### SUID - Sahip Olarak Çalıştır
```bash
./setuid-program cat /etc/bandit_pass/bandit20
chmod 600 ozel_anahtar                    # Anahtar izinleri
```

---

## 📅 Cron İşleri
```bash
ls /etc/cron.d/                           # Cron işlerini listele
cat /etc/cron.d/cronjob_adi               # Cron işini oku
```

---

## 🌿 Git
```bash
git clone ssh://kullanici@sunucu:port/yol # Depoyu klonla
git log                                   # Geçmişi görüntüle
git show <hash>                           # Commit'i görüntüle
git branch -a                             # Tüm dallar
git checkout dev                          # Dal değiştir
git tag && git show secret                # Etiketler
git add -f dosya && git push              # Zorla gönder
```

---

## 🎭 Kısıtlamaları Atlama

```bash
ssh kullanici@sunucu "cat dosya"          # .bashrc'yi atla
$0                                        # Uppercase Shell'den kaç
```

---

## 📝 Hızlı Seviye Özeti

| Seviye | Konsept | Ana Komut |
|:------:|:--------|:----------|
| 0 | SSH | `ssh -p 2220` |
| 1-3 | Özel dosya adları | `cat ./-` |
| 4 | Dosya türü | `file *` |
| 5-6 | Bulma | `find . -size 1033c` |
| 7 | grep | `grep "kelime" dosya` |
| 8 | Tekil | `sort \| uniq -u` |
| 9 | İkili | `strings \| grep` |
| 10-11 | Kodlama | `base64 -d`, `tr` |
| 12 | Sıkıştırma | `gzip/bzip2/tar` |
| 13-16 | Ağ | `ssh -i`, `nc`, `openssl` |
| 17 | Karşılaştırma | `diff` |
| 18 | Atlama | `ssh ... "komut"` |
| 19-20 | SUID | `./suid komut` |
| 21-23 | Cron | `/etc/cron.d/` |
| 24 | Kaba Kuvvet | `for i in {0000..9999}` |
| 27-31 | Git | `git log/branch/tag` |
| 32 | Shell | `$0` |

---

> **💡 Hızlı İpuçları:**
> - Her zaman önce `ls -la`
> - Hataları gizlemek için `2>/dev/null`
> - Özel anahtarlar için `chmod 600`

---

**Made with ❤️ by [Hamed Mohamed](https://github.com/7amed3li)**
