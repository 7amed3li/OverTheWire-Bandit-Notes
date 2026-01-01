### **Bandit Seviye 19 -> 20 (SetUID'nin Gücü)**

Bu seviye, Linux'taki en temel ve en önemli ayrıcalık yükseltme kavramlarından birini öğretir: **SetUID biti**. Bir program bu bite sahip olduğunda, onu çalıştıran kullanıcı kimliğiyle değil, programın **sahibinin** kimliğiyle çalışır. Bu, doğru kullanılmazsa büyük bir güvenlik açığıdır.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[Bandit 19 Challenge.png]] "Bir sonraki seviyeye erişim sağlamak için ev dizinindeki `setuid` yetkisine sahip programı kullanmalısınız. Nasıl kullanılacağını öğrenmek için argümansız çalıştırın."

**Hedefin Zihinsel Analizi:**

1. **Anahtar Delil:** Ev dizininde `setuid` bitine sahip bir program var.
2. **Programın Sahibi:** `ls -la` komutuyla yaptığımız keşif, programın (`bandit20-do`) sahibinin bir sonraki hedefimiz olan `bandit20` kullanıcısı olduğunu gösterdi.
3. **Çalışma Mantığı:** `setuid` biti sayesinde, biz (`bandit19`) bu programı çalıştırdığımızda, program `bandit20` kullanıcısının ayrıcalıklarıyla çalışacaktır.
4. **Kullanım Kılavuzu:** Meydan okuma, programı argümansız çalıştırarak nasıl kullanılacağını öğrenmemizi açıkça söylüyor.
5. **Nihai Amaç:** Bu programı, normalde okuma iznimiz olmayan `/etc/bandit_pass/bandit20` dosyasını okuyacak bir komutu çalıştırmak için kullanmak.

**İlk Plan (Dedektif Planı):**

1. **Giriş:** `bandit19` kullanıcısı olarak SSH ile giriş yap.
2. **Keşif:** `ls -la` komutuyla `setuid` programını ve izinlerini doğrula.
3. **Talimatları Oku:** Programı argümansız çalıştırarak (`./bandit20-do`) yardım mesajını görüntüle.
4. **Saldırıyı Oluştur:** Yardım mesajından anlaşılan mantığa göre, `cat /etc/bandit_pass/bandit20` komutunu program aracılığıyla çalıştır.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Ne Zaman Kullanmayı Düşünmeliyiz?|
|---|---|---|
|**`ls -la`**|**Detaylı Listeleme**|Bir dizindeki dosyaları, sahiplerini, gruplarını ve en önemlisi izinlerini (r, w, x ve **s** gibi özel bitler) görmek için kullanılır. `setuid` bitini tespit etmek için kritik bir adımdır.|
|**`./[program]`**|**Program Çalıştırma**|Mevcut dizindeki bir programı çalıştırmak için kullanılır. Bu seviyede, `bandit20-do` programını hem talimatlarını görmek hem de nihai komutu yürütmek için kullandık.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Bir dosyanın içeriğini standart çıktıya (genellikle terminal ekranı) yazdırmak için kullanılır. Parola dosyasının içeriğini okumak için nihai hedefimizdir.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: Olay Mahaline Giriş ve Keşif**

`bandit19` olarak giriş yaptıktan sonra, `ls -la` komutuyla ortamı inceleriz.

Bash

```
bandit19@bandit:~$ ls -la
total 36
drwxr-xr-x   2 root     root      4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root      4096 Oct 14 09:29 ..
-rwsr-x---   1 bandit20 bandit19 14884 Oct 14 09:26 bandit20-do
...
```

**Analiz:** `bandit20-do` dosyasının izinlerindeki `s` harfi (`rwsr-x---`), `setuid` bitinin ayarlandığını doğrular. Program, `bandit20` kullanıcısına aittir.

##### **Aşama 2: Programın Kendini Tanıtması**

Meydan okumanın önerdiği gibi, programı argümansız çalıştırırız.

Bash

```
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
```

**Analiz:** Programın mesajı son derece açıktır. Kendisinden sonra yazılan herhangi bir komutu başka bir kullanıcı (bu durumda sahibi olan `bandit20`) olarak çalıştırır. `sudo` komutunun çok basit bir versiyonu gibidir.

##### **Aşama 3: Nihai Hamle ve Şifrenin Ele Geçirilmesi**

Artık programın nasıl çalıştığını bildiğimize göre, hedefimiz olan parola dosyasını okumak için `cat` komutunu program aracılığıyla çalıştırırız.

Bash

```
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

**Nihai Karar:** Program, `cat` komutunu `bandit20` kullanıcısının ayrıcalıklarıyla başarıyla çalıştırdı ve normalde erişemeyeceğimiz parola dosyasını okumamızı sağladı.

**`bandit20` seviyesinin şifresi: `GbKksEFF4yrVs6il55v6gwY5aVje5f0j`**

#### **Bölüm 4: Alternatif Çözümler (Farklı Düşünme)**

Bu program herhangi bir komutu çalıştırabildiği için, şifreyi almanın başka yolları da vardır.

1. **Shell (Kabuk) Elde Etme:** Şifre dosyasını okumak yerine, doğrudan `bandit20` kullanıcısına ait bir kabuk oturumu başlatabiliriz. Bu, bize daha fazla kontrol sağlar.
    
    Bash
    
    ```
    bandit19@bandit:~$ ./bandit20-do /bin/bash
    bandit20@bandit:~$ whoami
    bandit20
    bandit20@bandit:~$ cat /etc/bandit_pass/bandit20
    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
    bandit20@bandit:~$ exit
    ```
    
    Bu yöntem, sadece tek bir komut çalıştırmak yerine hedef kullanıcı olarak interaktif bir oturum açmak istediğimizde çok daha güçlüdür.

### **دليل Bandit التعليمي: المستوى 19 -> 20 (قوة SetUID)**

هذا المستوى يعلمنا واحدًا من أهم المفاهيم الأساسية في تصعيد الامتيازات (Privilege Escalation) في نظام لينكس: **بت الـ SetUID**. عندما يمتلك برنامج هذا البت، فإنه لا يعمل بصلاحيات المستخدم الذي يقوم بتشغيله، بل يعمل بصلاحيات **مالك البرنامج** نفسه. هذه ثغرة أمنية كبيرة إذا لم تُستخدم بشكل صحيح.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

![[Bandit 19 Challenge.png]] "للوصول إلى المستوى التالي، يجب عليك استخدام برنامج `setuid` الثنائي في المجلد الرئيسي. قم بتنفيذه بدون وسائط لمعرفة كيفية استخدامه."

**التحليل الذهني للهدف:**

1. **الدليل الرئيسي:** يوجد برنامج يحمل بت `setuid` في المجلد الرئيسي.
2. **مالك البرنامج:** استكشافنا باستخدام `ls -la` أظهر أن مالك البرنامج (`bandit20-do`) هو هدفنا التالي، المستخدم `bandit20`.
3. **منطق العمل:** بفضل بت `setuid`، عندما نقوم نحن (`bandit19`) بتشغيل هذا البرنامج، فإنه سيعمل بامتيازات المستخدم `bandit20`.
4. **دليل الاستخدام:** التحدي يخبرنا صراحةً أن نقوم بتشغيل البرنامج بدون وسائط لمعرفة كيفية استخدامه.
5. **الغاية النهائية:** استخدام هذا البرنامج لتشغيل أمر يقرأ لنا ملف كلمة السر الذي لا نملك صلاحية قراءته مباشرة: `/etc/bandit_pass/bandit20`.

**الخطة المبدئية (خطة المحقق):**

1. **تسجيل الدخول:** سجل الدخول كمستخدم `bandit19`.
2. **الاستكشاف:** تحقق من وجود برنامج `setuid` وصلاحياته باستخدام `ls -la`.
3. **قراءة التعليمات:** قم بتشغيل البرنامج بدون وسائط (`./bandit20-do`) لعرض رسالة المساعدة.
4. **صياغة الهجوم:** بناءً على المنطق المستنتج من رسالة المساعدة، قم بتشغيل الأمر `cat /etc/bandit_pass/bandit20` من خلال البرنامج.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|متى نفكر في استخدامها؟|
|---|---|---|
|**`ls -la`**|**عرض القائمة المفصل**|تُستخدم لعرض الملفات في مجلد ما مع تفاصيل المالكين، المجموعات، والأهم من ذلك، الصلاحيات (مثل r, w, x والبتات الخاصة مثل **s**). هي خطوة حاسمة لاكتشاف بت `setuid`.|
|**`./[program]`**|**تشغيل برنامج**|تُستخدم لتشغيل برنامج موجود في المجلد الحالي. في هذا المستوى، استخدمنا `bandit20-do` لعرض تعليماته وتنفيذ الأمر النهائي.|
|**`cat`**|**عرض محتوى الملفات**|تُستخدم لطباعة محتوى ملف على الخرج القياسي (عادةً شاشة الطرفية). هي هدفنا النهائي لقراءة محتوى ملف كلمة السر.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

##### **المرحلة 1: الدخول إلى مسرح الجريمة والاستكشاف**

بعد الدخول باسم `bandit19`، نقوم بفحص البيئة باستخدام `ls -la`.

Bash

```
bandit19@bandit:~$ ls -la
total 36
drwxr-xr-x   2 root     root      4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root      4096 Oct 14 09:29 ..
-rwsr-x---   1 bandit20 bandit19 14884 Oct 14 09:26 bandit20-do
...
```

**التحليل:** الحرف `s` في صلاحيات الملف `bandit20-do` (`rwsr-x---`) يؤكد أن بت `setuid` مُفعّل. البرنامج مملوك للمستخدم `bandit20`.

##### **المرحلة 2: البرنامج يقدم نفسه**

كما اقترح التحدي، نقوم بتشغيل البرنامج بدون وسائط.

Bash

```
bandit19@bandit:~$ ./bandit20-do
Run a command as another user.
  Example: ./bandit20-do whoami
```

**التحليل:** رسالة البرنامج واضحة للغاية. يقوم بتشغيل أي أمر يُكتب بعده كمستخدم آخر (في هذه الحالة، مالكه `bandit20`). إنه يعمل كنسخة مبسطة جدًا من الأمر `sudo`.

##### **المرحلة 3: الضربة القاضية والحصول على كلمة السر**

الآن بعد أن عرفنا كيف يعمل البرنامج، نستخدمه لتشغيل أمر `cat` لقراءة ملف كلمة السر المستهدف.

Bash

```
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

**الحكم النهائي:** نجح البرنامج في تشغيل أمر `cat` بامتيازات `bandit20`، مما سمح لنا بقراءة ملف كلمة السر الذي لا يمكننا الوصول إليه عادةً.

**كلمة السر للمستوى `bandit20` هي: `GbKksEFF4yrVs6il55v6gwY5aVje5f0j`**

#### **الجزء الرابع: الحلول البديلة (التفكير خارج الصندوق)**

بما أن هذا البرنامج يمكنه تشغيل أي أمر، فهناك طرق أخرى للحصول على كلمة السر.

1. **الحصول على جلسة Shell:** بدلاً من مجرد قراءة ملف كلمة السر، يمكننا بدء جلسة shell تفاعلية بصلاحيات المستخدم `bandit20`. هذا يمنحنا تحكمًا أكبر.
    
    Bash
    
    ```
    bandit19@bandit:~$ ./bandit20-do /bin/bash
    bandit20@bandit:~$ whoami
    bandit20
    bandit20@bandit:~$ cat /etc/bandit_pass/bandit20
    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
    bandit20@bandit:~$ exit
    ```
    
    هذه الطريقة أقوى بكثير عندما نريد فتح جلسة تفاعلية كمستخدم مستهدف بدلاً من مجرد تنفيذ أمر واحد.