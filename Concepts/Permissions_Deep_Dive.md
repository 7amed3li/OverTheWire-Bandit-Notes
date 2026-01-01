# 🔐 Understanding Linux Permissions

## 🇺🇸 English

Linux uses a permission system based on Owner, Group, and Others.

- **r** (4), **w** (2), **x** (1)
- **Setuid (s)**: Runs with the owner's identity.

## 🇪🇬 العربية

نظام الصلاحيات في لينكس بيحدد مين يقدر يقرأ، يكتب، أو يشغل الملف.

- **بالبلدي**: تخيل السيستم عمارة. صاحب الشقة (Owner) ليه صلاحيات كاملة، الساكن (Group) ليه صلاحيات أقل، والبواب (Others) ملوش إنه يتدخل في اللي جوه.
- **صلاحية الـ s**: دي "الجوكر"، بتخلي اللي يشغل البرنامج ياخد صلاحيات صاحب البرنامج نفسه.

## 🇹🇷 Türkçe

Linux, Sahibi, Grubu ve Diğerleri için bir izin sistemi kullanır.

- **r** (Okuma), **w** (Yazma), **x** (Çalıştırma)
- **Setuid (s)**: Programın sahibinin kimliğiyle çalışmasını sağlar.

---

### 🛠️ Related Levels

[[Bandit 19 Çözümü]] | [[Bandit 26 Çözümü]]
