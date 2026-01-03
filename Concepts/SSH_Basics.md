# 🌐 SSH: The Gateway to Remote Linux

## 🇺🇸 English

**Secure Shell (SSH)** is a cryptographic network protocol for operating network services securely over an unsecured network.

### Essential Commands

| Command | Purpose |
|---------|---------|
| `ssh user@host -p port` | Basic connection |
| `ssh -i keyfile user@host` | Key-based authentication |
| `ssh user@host "command"` | Execute command without shell |

### Key Concepts

- **Port**: Default is 22, Bandit uses 2220
- **Key-based Auth**: More secure than passwords
- **Known Hosts**: `~/.ssh/known_hosts` stores server fingerprints

### Security Tips

1. Always use `chmod 600` on private keys
2. Use `-i` flag for key authentication
3. SSH can bypass `.bashrc` by executing commands directly

---

## 🇪🇬 العربية

الـ SSH هو "نفق" مؤمن بيخليك تتحكم في سيرفر بعيد كأنك قاعد قدامه بالظبط.

### بالبلدي
- تخيله زي الـ Remote Desktop بس للـ Terminal
- محتاج العنوان (Host)، الباب (Port)، والمفتاح (Password/Key)

### الأوامر الأساسية

| الأمر | الغرض |
|-------|-------|
| `ssh user@host -p 2220` | اتصال عادي |
| `ssh -i keyfile user@host` | اتصال بمفتاح خاص |
| `ssh user@host "cat file"` | تنفيذ أمر بدون فتح shell |

### نصائح مهمة

1. لازم تعمل `chmod 600` على المفتاح الخاص
2. لو الـ `.bashrc` فيها `exit`، استخدم `ssh ... "command"` لتجاوزها

---

## 🇹🇷 Türkçe

**SSH (Secure Shell)**, güvenli olmayan bir ağ üzerinden ağ hizmetlerini güvenli bir şekilde çalıştırmak için kullanılan kriptografik bir ağ protokolüdür.

### Temel Komutlar

| Komut | Amaç |
|-------|------|
| `ssh kullanıcı@sunucu -p port` | Temel bağlantı |
| `ssh -i anahtar kullanıcı@sunucu` | Anahtar tabanlı kimlik doğrulama |
| `ssh kullanıcı@sunucu "komut"` | Shell olmadan komut çalıştır |

### Güvenlik İpuçları

1. Özel anahtarlarda her zaman `chmod 600` kullanın
2. `.bashrc` tuzağını atlamak için doğrudan komut çalıştırın

---

### 🛠️ Related Levels

[[Bandit 0 Çözümü]] | [[Bandit 13 Çözümü]] | [[Bandit 18 Çözümü]] | [[Bandit 25 Çözümü]]

