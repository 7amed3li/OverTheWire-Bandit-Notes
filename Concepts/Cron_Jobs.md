# ⏰ Cron Jobs: Scheduled Tasks in Linux

## 🇺🇸 English

Cron is a time-based job scheduler in Unix-like systems. Attackers can exploit poorly configured cron jobs for privilege escalation.

### Cron Syntax

```
* * * * * command
│ │ │ │ │
│ │ │ │ └── Day of week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

**Example**: `* * * * *` = Every minute

### Key Locations

| Path | Description |
|------|-------------|
| `/etc/cron.d/` | System-wide cron jobs |
| `/etc/crontab` | Main cron configuration |
| `/var/spool/cron/` | User-specific cron jobs |

### Security Implications

1. **Writable Scripts**: If the script run by cron is writable, you can inject code
2. **Wildcard Injection**: Misuse of `*` in scripts can be exploited
3. **PATH Hijacking**: Relative command paths can be hijacked

---

## 🇪🇬 العربية

Cron هو مجدول المهام في أنظمة Linux. المهاجمون يستغلون المهام المُعَدَّة بشكل سيء لتصعيد الصلاحيات.

### بالبلدي

- **Cron**: زي المنبه اللي بيشغل برنامج معين في وقت معين
- **`* * * * *`**: يعني "كل دقيقة"
- **الاستغلال**: لو البرنامج اللي الـ cron بيشغله ممكن نكتب فيه، نقدر نحط كود بنا فيه ويتشغل بصلاحيات عالية

### المواقع المهمة

- `/etc/cron.d/`: مكان ملفات الـ cron على مستوى النظام
- تحليل السكريبت اللي بيتشغل هو المفتاح!

---

## 🇹🇷 Türkçe

Cron, Unix benzeri sistemlerde zamana dayalı bir görev zamanlayıcısıdır. Saldırganlar, kötü yapılandırılmış cron işlerini ayrıcalık yükseltme için kullanabilir.

### Cron Sözdizimi

- `* * * * *` = Her dakika çalıştır
- Her `*`, sırasıyla: Dakika, Saat, Ayın günü, Ay, Haftanın günü

### Önemli Konumlar

| Yol | Açıklama |
|-----|----------|
| `/etc/cron.d/` | Sistem genelinde cron işleri |
| `/etc/crontab` | Ana cron yapılandırması |

### Güvenlik Sonuçları

1. **Yazılabilir Betikler**: Cron tarafından çalıştırılan betik yazılabilirse, kod enjekte edebilirsiniz
2. **PATH Hijacking**: Göreceli komut yolları ele geçirilebilir

---

### 🛠️ Related Levels

[[Bandit 21 Çözümü]] | [[Bandit 22 Çözümü]] | [[Bandit 23 Çözümü]]
