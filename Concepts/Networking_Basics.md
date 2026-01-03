# 🌐 Networking Basics for Hackers

## 🇺🇸 English

Understanding network fundamentals is essential for penetration testing. Key concepts:

### Essential Tools

| Tool | Purpose | Example |
|------|---------|---------|
| `nc` (netcat) | Swiss army knife of networking | `nc localhost 30000` |
| `nmap` | Port scanning and service detection | `nmap -p 31000-32000 localhost` |
| `openssl s_client` | SSL/TLS connections | `openssl s_client -connect host:port` |
| `telnet` | Simple text-based connections | `telnet host port` |

### Key Concepts

- **Ports**: Virtual doors on a computer (0-65535)
- **localhost**: The machine itself (127.0.0.1)
- **SSL/TLS**: Encrypted communication layer
- **Echo Server**: Returns whatever you send (used as decoys)

---

## 🇪🇬 العربية

فهم أساسيات الشبكات ضروري لاختبار الاختراق.

### الأدوات الأساسية

| الأداة | الغرض |
|--------|-------|
| `nc` (netcat) | السكين السويسرية للشبكات - اتصال بسيط بأي منفذ |
| `nmap` | مسح المنافذ واكتشاف الخدمات |
| `openssl s_client` | الاتصال بخدمات مشفرة (SSL/TLS) |

### بالبلدي
- **المنفذ (Port)**: زي باب الشقة، كل خدمة ليها باب خاص
- **localhost**: الجهاز نفسه، زي ما تقول "أنا"
- **SSL/TLS**: تشفير البيانات، زي ما تحط الجواب في ظرف مقفول

---

## 🇹🇷 Türkçe

Ağ temellerini anlamak, sızma testi için çok önemlidir.

### Temel Araçlar

| Araç | Amaç | Örnek |
|------|------|-------|
| `nc` (netcat) | Ağların İsviçre çakısı | `nc localhost 30000` |
| `nmap` | Port tarama ve servis algılama | `nmap -p 31000-32000 localhost` |
| `openssl s_client` | SSL/TLS bağlantıları | `openssl s_client -connect host:port` |

### Temel Kavramlar

- **Portlar**: Bilgisayardaki sanal kapılar (0-65535)
- **localhost**: Makinenin kendisi (127.0.0.1)
- **SSL/TLS**: Şifreli iletişim katmanı

---

### 🛠️ Related Levels

[[Bandit 14 Çözümü]] | [[Bandit 15 Çözümü]] | [[Bandit 16 Çözümü]] | [[Bandit 20 Çözümü]]
