### **Bandit Seviye 28 -> 29 (Git Geçmişinin Sırları) Kılavuzu**

Bu seviye, Git'in temel bir özelliğini istismar etmeyi öğretir: **kalıcı geçmiş**. Bir geliştirici bir hatayı "düzelttiğinde" veya hassas bir bilgiyi sildiğinde bile, bu bilginin eski versiyonları deponun geçmişinde kalır. Bu seviye, bu dijital izleri nasıl takip edeceğimizi gösterir.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "`ssh://bandit28-git@...` adresindeki Git deposunu klonlayın ve bir sonraki seviyenin şifresini bulun."

**Zihinsel Analiz:**

1. **Görev:** Önceki seviyeye benzer şekilde, bir Git deposunu klonlamamız gerekiyor.
2. **Kritik Nokta:** Bu sefer, şifrenin doğrudan görünür olmayacağını, ancak geçmişte bir yerde "sızdırılmış" ve sonra "düzeltilmiş" olabileceğini varsaymalıyız. Bu, gerçek dünyada sıkça karşılaşılan bir senaryodur.
3. **Strateji:** Sadece mevcut dosyaları incelemek yeterli olmayacak. Deponun tüm değişiklik geçmişini (`commit log`) incelemeli ve şifrenin eklendiği, sonra da kaldırıldığı anı bulmalıyız.

**İlk Plan (Dedektif Planı):**

1. **Hazırlık ve Klonlama (Yerel Makinede):** Önceki seviyede olduğu gibi, yerel makinemizde geçici bir dizin oluştur ve `git clone` komutuyla depoyu klonla. Kimlik doğrulaması için `bandit28`'in şifresini kullan.
2. **Mevcut Durumu İnceleme (Yerel Makinede):** Klonlanan `repo` dizinine gir ve `README` dosyasını oku. Şifrenin yerinde "xxxxxxxxxx" gibi bir şey olduğunu görerek şüphelerimizi doğrula.
3. **Geçmişi Araştırma (Yerel Makinede):** `git log` komutunu kullanarak tüm `commit` geçmişini listele. "fix info leak" (bilgi sızıntısı düzeltmesi) gibi şüpheli `commit` mesajlarını ara.
4. **Kanıtı Ortaya Çıkarma (Yerel Makinede):** Şifrenin sızdırıldığı `commit`'i bulduktan sonra, `git show <commit_hash>` komutunu kullanarak o `commit`'te yapılan spesifik değişiklikleri görüntüle ve şifreyi ortaya çıkar.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Bu Seviyedeki Rolü|
|---|---|---|
|**`git clone`**|**Depo Klonlama**|Uzak depoyu yerel makinemize indirmek için ilk adım.|
|**`git log`**|**Geçmişi Görüntüleme**|Depodaki tüm değişikliklerin (commit'lerin) bir listesini sunar. Bu, zaman çizelgemizdir ve şüpheli aktiviteyi bulmamızı sağlar.|
|**`git show`**|**Commit Detaylarını Gösterme**|Belirli bir `commit`'te tam olarak hangi dosyaların ve hangi satırların değiştirildiğini gösterir. Bu, "silahı" bulduğumuz yerdir.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Başlangıçta `README` dosyasının mevcut durumunu kontrol etmek için kullanılır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye. **Tüm bu komutlar yerel makinenizde (Kali) çalıştırılmalıdır.**

##### **Aşama 1: Klonlama ve İlk İnceleme**

Önce depoyu klonlayıp `README` dosyasının mevcut haline bakıyoruz.

Bash

```
# Kali makinenizde
mkdir /tmp/bandit28 && cd /tmp/bandit28
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
# bandit28'in şifresini girin: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN

cd repo
cat README.md
# Çıktı:
# ...
# - password: xxxxxxxxxx
```

**Analiz:** Beklendiği gibi, şifre mevcut dosyada yok. Şimdi geçmişe dalma zamanı.

##### **Aşama 2: Geçmişi İnceleme**

`git log` komutuyla deponun geçmişini inceliyoruz.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit28/repo]
└─$ git log
commit b5ed... (HEAD -> master)
Author: Morla Porla <morla@overthewire.org>
Date:   ...
    fix info leak

commit 8b7c...
Author: Morla Porla <morla@overthewire.org>
Date:   ...
    add missing data
...
```

**Analiz:** `fix info leak` mesajı, bir önceki `commit`'in (`8b7c...`) şifreyi içerdiğine dair güçlü bir ipucudur.

##### **Aşama 3: Sırrı Ortaya Çıkarma**

Şimdi `git show` komutunu kullanarak `8b7c...` hash'li `commit`'in içeriğini görüntülüyoruz.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit28/repo]
└─$ git show 8b7c651b37ce7a94633b7b7b7c980ded19a16e4f
commit 8b7c...
Author: Morla Porla <morla@overthewire.org>
...
    add missing data

diff --git a/README.md b/README.md
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@
 ## credentials
 
 - username: bandit29
-- password: <TBD>
+- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```

**Nihai Karar:** `git show` komutu, `add missing data` başlıklı `commit`'te `<TBD>` satırının silinip yerine gerçek şifrenin eklendiğini açıkça gösterdi. Bu, Git'in geçmişi asla unutmadığının ve güvenlik açıklarının bu şekilde ortaya çıkarılabileceğinin mükemmel bir örneğidir.

**`bandit29` seviyesinin şifresi: `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`**

### **دليل Bandit التعليمي: المستوى 28 -> 29 (أسرار تاريخ Git)**

هذا المستوى يعلمنا درسًا قيمًا جدًا في استغلال ميزة أساسية في Git: **التاريخ الدائم**. حتى عندما يقوم مطور "بإصلاح" خطأ أو حذف معلومة حساسة، فإن الإصدارات القديمة من هذه المعلومة تظل موجودة في تاريخ المستودع. هذا المستوى يوضح لنا كيفية تتبع هذه الآثار الرقمية.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "قم باستنساخ مستودع Git على العنوان `ssh://bandit28-git@...` وابحث عن كلمة السر للمستوى التالي."

**التحليل الذهني للهدف:**

1. **المهمة:** بشكل مشابه للمستوى السابق، نحتاج إلى استنساخ مستودع Git.
2. **النقطة الحاسمة:** هذه المرة، يجب أن نفترض أن كلمة السر لن تكون مرئية بشكل مباشر، ولكنها قد تكون قد "تم تسريبها" في الماضي ثم "تم إصلاحها". هذا سيناريو شائع جدًا في العالم الحقيقي.
3. **الاستراتيجية:** لن يكون فحص الملفات الحالية كافيًا. يجب علينا فحص سجل التغييرات الكامل للمستودع (`commit log`) وتحديد اللحظة التي تمت فيها إضافة كلمة السر، ثم إزالتها.

**الخطة المبدئية (خطة المحقق):**

1. **التحضير والاستنساخ (على الجهاز المحلي):** كما في المستوى السابق، قم بإنشاء مجلد مؤقت على جهازك المحلي واستنسخ المستودع باستخدام `git clone`. استخدم كلمة مرور `bandit28` للمصادقة.
2. **فحص الوضع الحالي (على الجهاز المحلي):** ادخل إلى مجلد `repo` المستنسخ واقرأ ملف `README`. تحقق من شكوكك عندما ترى شيئًا مثل "xxxxxxxxxx" بدلاً من كلمة السر.
3. **التحقيق في التاريخ (على الجهاز المحلي):** استخدم الأمر `git log` لسرد كل تاريخ الـ `commits`. ابحث عن رسائل `commit` مشبوهة مثل "fix info leak" (إصلاح تسريب معلومات).
4. **كشف الدليل (على الجهاز المحلي):** بعد تحديد الـ `commit` الذي تم فيه تسريب كلمة السر، استخدم الأمر `git show <commit_hash>` لعرض التغييرات المحددة التي حدثت في ذلك الـ `commit` والكشف عن كلمة السر.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|دورها في هذا المستوى|
|---|---|---|
|**`git clone`**|**استنساخ المستودع**|الخطوة الأولى لتنزيل المستودع البعيد إلى جهازنا المحلي.|
|**`git log`**|**عرض التاريخ**|يقدم قائمة بجميع التغييرات (commits) في المستودع. هذا هو خطنا الزمني الذي يسمح لنا بتحديد النشاط المشبوه.|
|**`git show`**|**عرض تفاصيل الـ commit**|يعرض بالضبط أي الملفات والأسطر التي تم تغييرها في `commit` معين. هذا هو المكان الذي نجد فيه "السلاح".|
|**`cat`**|**عرض محتوى الملفات**|يستخدم في البداية للتحقق من الوضع الحالي لملف `README`.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط. **يجب تنفيذ كل هذه الأوامر على جهازك المحلي (كالي).**

##### **المرحلة 1: الاستنساخ والفحص الأولي**

أولاً، نستنسخ المستودع ونلقي نظرة على الحالة الحالية لملف `README`.

Bash

```
# على جهاز كالي
mkdir /tmp/bandit28 && cd /tmp/bandit28
git clone ssh://bandit28-git@bandit.labs.overthewire.org:2220/home/bandit28-git/repo
# أدخل كلمة مرور bandit28: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN

cd repo
cat README.md
# الناتج:
# ...
# - password: xxxxxxxxxx
```

**التحليل:** كما هو متوقع، كلمة السر غير موجودة في الملف الحالي. حان الوقت للغوص في الماضي.

##### **المرحلة 2: فحص التاريخ**

نفحص تاريخ المستودع باستخدام الأمر `git log`.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit28/repo]
└─$ git log
commit b5ed... (HEAD -> master)
Author: Morla Porla <morla@overthewire.org>
Date:   ...
    fix info leak

commit 8b7c...
Author: Morla Porla <morla@overthewire.org>
Date:   ...
    add missing data
...
```

**التحليل:** رسالة `fix info leak` هي دليل قوي على أن الـ `commit` السابق (`8b7c...`) يحتوي على كلمة السر.

##### **المرحلة 3: كشف السر**

الآن نستخدم الأمر `git show` لعرض محتويات الـ `commit` الذي يحمل الهاش `8b7c...`.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit28/repo]
└─$ git show 8b7c651b37ce7a94633b7b7b7c980ded19a16e4f
commit 8b7c...
Author: Morla Porla <morla@overthewire.org>
...
    add missing data

diff --git a/README.md b/README.md
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@
 ## credentials
 
 - username: bandit29
-- password: <TBD>
+- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```

**الحكم النهائي:** أظهر الأمر `git show` بوضوح أنه في الـ `commit` الذي يحمل عنوان `add missing data`، تم حذف السطر `<TBD>` وإضافة كلمة السر الحقيقية مكانه. هذا مثال مثالي على أن Git لا ينسى التاريخ أبدًا، وكيف يمكن الكشف عن الثغرات الأمنية بهذه الطريقة.

**كلمة السر للمستوى `bandit29` هي: `4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7`**

---

## 🇺🇸 English

### Objective
Find password in git history that was removed in later commits.

### Commands
```bash
git clone ssh://...
cd repo
git log                    # View commit history
git show <commit-hash>     # View specific commit details
```

### Key Insight
The password was added in an earlier commit with message "add missing data", then replaced with `<TBD>`.

### Key Takeaways
- **Git History Never Forgets:** Deleted content remains in commit history
- **`git log`:** View all commits
- **`git show`:** Examine specific commit changes