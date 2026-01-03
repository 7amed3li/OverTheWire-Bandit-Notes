# 🔐 Understanding Linux Permissions

## 🇺🇸 English

Linux uses a permission system based on Owner, Group, and Others.

### Permission Breakdown

```
-rwsr-x--- 1 bandit20 bandit19 14884 Oct 14 bandit20-do
│││││││││
│││││││└─── Others: --- (no access)
││││││└──── Group:  r-x (read + execute)
│││││└───── Owner:  rws (read + write + SUID)
││││└────── Type:   - (regular file)
```

### Numeric Values

| Permission | Value | Meaning |
|------------|-------|---------|
| r | 4 | Read |
| w | 2 | Write |
| x | 1 | Execute |
| s (SUID) | +4000 | Run as owner |

### SUID/SGID Explained

- **SUID (s)**: Program runs with **owner's** privileges
- **SGID (s)**: Program runs with **group's** privileges
- **Sticky bit (t)**: Only owner can delete files in directory

---

## 🇪🇬 العربية

نظام الصلاحيات في لينكس بيحدد مين يقدر يقرأ، يكتب، أو يشغل الملف.

### بالبلدي
- تخيل السيستم عمارة:
  - **Owner** (صاحب الشقة): ليه صلاحيات كاملة
  - **Group** (الساكنين): ليهم صلاحيات محدودة
  - **Others** (أي حد تاني): عادة ملهوش صلاحيات

### صلاحية الـ SUID (الجوكر 🃏)
- لما تشغل برنامج عليه `s`، بتاخد صلاحيات **صاحب البرنامج** مش صلاحياتك أنت
- مثال: `./bandit20-do cat /etc/bandit_pass/bandit20`
  - البرنامج `bandit20-do` مملوك لـ `bandit20`
  - بيتشغل بصلاحيات `bandit20` حتى لو أنت `bandit19`

---

## 🇹🇷 Türkçe

Linux, Sahibi, Grubu ve Diğerleri için bir izin sistemi kullanır.

### Sayısal Değerler

| İzin | Değer | Anlam |
|------|-------|-------|
| r | 4 | Okuma |
| w | 2 | Yazma |
| x | 1 | Çalıştırma |
| s (SUID) | +4000 | Sahibi olarak çalıştır |

### SUID/SGID Açıklaması

- **SUID (s)**: Program **sahibinin** ayrıcalıklarıyla çalışır
- **SGID (s)**: Program **grubun** ayrıcalıklarıyla çalışır

---

### 🛠️ Related Levels

[[Bandit 19 Çözümü]] | [[Bandit 20 Çözümü]] | [[Bandit 26 Çözümü]]

