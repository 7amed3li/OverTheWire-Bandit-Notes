### **Bandit Seviye 21 -> 22 (Zamanlanmış Görevleri Analiz Etme)**

Bu seviye, Linux sistem yönetiminin temel bir parçası olan `cron` (zamanlanmış görevler) kavramını tanıtır. Bize, otomatik olarak çalışan süreçleri nasıl bulacağımızı, analiz edeceğimizi ve bunlardan nasıl yararlanacağımızı öğretir.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[Bandit 21 Challenge.png]] "`cron` tarafından düzenli aralıklarla otomatik olarak bir program çalıştırılıyor. Yapılandırma için `/etc/cron.d/` dizinine bakın ve hangi komutun yürütüldüğünü görün."

**Hedefin Zihinsel Analizi:**

1. **Anahtar Kavram:** `cron`. Linux'ta komutları veya betikleri belirli zamanlarda otomatik olarak çalıştırmak için kullanılan bir sistemdir.
2. **Belirtilen Konum:** Meydan okuma, bizi doğrudan `/etc/cron.d/` dizinine yönlendirir. Bu dizin, sistem genelindeki `cron` işlerinin yapılandırma dosyalarını barındırır.
3. **Görev Tanımı:** Görevimiz son derece nettir:
    - `/etc/cron.d/` dizinine git.
    - Bandit oyunuyla ilgili görünen `cron` işi dosyasını bul.
    - Bu dosyanın içeriğini okuyarak hangi komutun, ne zaman ve hangi kullanıcı ayrıcalıklarıyla çalıştığını anla.
4. **Beklenti:** Büyük olasılıkla, bir sonraki seviyenin (`bandit22`) ayrıcalıklarıyla çalışan bir betik bulacağız. Bu betik, şifreyi normalde erişemeyeceğimiz bir yerden okuyup, bizim erişebileceğimiz geçici bir dosyaya yazıyor olmalıdır.

**İlk Plan (Dedektif Planı):**

1. **Giriş:** `bandit21` kullanıcısı olarak SSH ile giriş yap.
2. **Olay Mahaline İntikal:** `cd /etc/cron.d/` komutuyla belirtilen dizine geç.
3. **Delilleri İncele:** `ls -la` komutuyla dizindeki dosyaları listele ve `bandit22` ile ilgili olanı bul.
4. **Yapılandırmayı Oku:** `cat` komutuyla ilgili `cron` dosyasının içeriğini oku. Bu, bize çalıştırılan betiğin yolunu verecektir.
5. **Betiği Analiz Et:** Betiğin içeriğini `cat` ile okuyarak ne yaptığını ve şifreyi nereye yazdığını öğren.
6. **Şifreyi Al:** Betiğin şifreyi yazdığı geçici dosyayı oku.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Ne Zaman Kullanmayı Düşünmeliyiz?|
|---|---|---|
|**`cd`**|**Dizin Değiştirme (Change Directory)**|Dosya sisteminde farklı bir dizine geçmek için kullanılır. Bu seviyede `/etc/cron.d/` dizinine gitmek için kullandık.|
|**`ls -la`**|**Detaylı Listeleme**|Bir dizindeki tüm dosyaları (gizli olanlar dahil) ve detaylı bilgilerini (izinler, sahip, boyut vb.) listeler.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Bir veya daha fazla dosyanın içeriğini ekrana yazdırmak için kullanılır. Bu seviyede hem `cron` yapılandırma dosyasını hem de betiğin kendisini ve son olarak şifre dosyasını okumak için kritik bir araçtır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

**Doğru Şifre:** `bandit21` seviyesinin şifresi `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`'dir.

##### **Aşama 1: Olay Mahaline İntikal ve Keşif**

`bandit21` olarak giriş yaptıktan sonra, doğrudan `/etc/cron.d/` dizinine gidip içeriğini listeliyoruz.

Bash

```
bandit21@bandit:~$ ssh bandit21@bandit.labs.overthewire.org -p 2220
# Şifre: EeoULMCra2q0dSkYj561DX7s1CpBuOBt

bandit21@bandit:~$ cd /etc/cron.d/
bandit21@bandit:/etc/cron.d$ ls -la
...
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit22
...
```

**Analiz:** `cronjob_bandit22` adında, bir sonraki seviyeyle doğrudan ilgili görünen bir dosya bulduk.

##### **Aşama 2: Cron İşini Anlamak**

Bu dosyanın içeriğini okuyarak hangi görevin zamanlandığını öğreniyoruz.

Bash

```
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

**Analiz:** Bu satır bize her dakika (`* * * * *`), `bandit22` kullanıcısı olarak `/usr/bin/cronjob_bandit22.sh` betiğinin çalıştırıldığını söylüyor.

##### **Aşama 3: Betiğin Sırlarını Açığa Çıkarma**

Şimdi, betiğin kendisinin ne yaptığını anlamak için içeriğini okuyoruz.

Bash

```
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

**Analiz:** Betik son derece basit:

1. `bandit22` şifresini `/etc/bandit_pass/bandit22` dosyasından okur.
2. Bu şifreyi `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv` adlı bir dosyaya yazar.
3. Bu geçici dosyanın izinlerini `644` olarak ayarlar, bu da "herkesin okuyabileceği" anlamına gelir.

##### **Aşama 4: Şifreyi Ele Geçirme**

Artık şifrenin her dakika nereye kopyalandığını bildiğimize göre, tek yapmamız gereken o dosyayı okumak.

Bash

```
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

**Nihai Karar:** `cron` işini analiz ederek, çalıştırılan betiği bularak ve betiğin mantığını anlayarak, bir sonraki seviyenin şifresinin geçici olarak kopyalandığı dosyayı tespit ettik ve okuduk.

**`bandit22` seviyesinin şifresi: `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`**

#### **Bölüm 4: Alternatif Yöntemler Neden İşe Yaramaz?**

Bu özel ortamda, `/tmp` dizinini `ls` veya `find` gibi komutlarla doğrudan listeleme iznimiz yoktur. Bu, OverTheWire'ın oyuncuların birbirlerinin geçici dosyalarını görmesini engellemek için aldığı bir güvenlik önlemidir. Bu nedenle, `/tmp` dizinini değişiklikler için izlemek (`watch`) veya belirli kriterlere uyan dosyaları aramak (`find`) gibi yöntemler "Permission denied" hatası verecektir.

Bu durum, betiği analiz ederek dosyanın **tam adını** öğrenmenin bu seviyeyi çözmek için **tek geçerli yöntem** olduğunu göstermektedir.

### **دليل Bandit التعليمي: المستوى 21 -> 22 (تحليل المهام المجدولة)**

هذا المستوى يقدم لنا مفهومًا أساسيًا في إدارة أنظمة لينكس وهو `cron` (المهام المجدولة). إنه يعلمنا كيف نجد العمليات التي تعمل تلقائيًا، وكيف نحللها، وكيف نستفيد منها.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

![[Bandit 21 Challenge.png]] "برنامج يعمل تلقائيًا على فترات منتظمة من `cron`. ابحث في `/etc/cron.d/` عن الإعدادات وانظر ما هو الأمر الذي يتم تنفيذه."

**التحليل الذهني للهدف:**

1. **المفهوم الرئيسي:** `cron`. هو نظام في لينكس لتشغيل الأوامر أو البرامج النصية تلقائيًا في أوقات محددة.
2. **الموقع المحدد:** التحدي يوجهنا مباشرة إلى المسار `/etc/cron.d/`. هذا المجلد يحتوي على ملفات إعدادات مهام `cron` على مستوى النظام.
3. **وصف المهمة:** مهمتنا واضحة جدًا:
    - اذهب إلى المجلد `/etc/cron.d/`.
    - ابحث عن ملف مهمة `cron` الذي يبدو مرتبطًا بلعبة Bandit.
    - اقرأ محتوى هذا الملف لفهم ما هو الأمر الذي يتم تشغيله، ومتى، وبأي صلاحيات مستخدم.
4. **التوقع:** على الأرجح، سنجد برنامجًا نصيًا (script) يعمل بصلاحيات المستوى التالي (`bandit22`). هذا البرنامج النصي يجب أن يكون يقوم بنسخ كلمة السر من مكان لا يمكننا الوصول إليه إلى ملف مؤقت في مكان يمكننا الوصول إليه.

**الخطة المبدئية (خطة المحقق):**

1. **تسجيل الدخول:** سجل الدخول كمستخدم `bandit21`.
2. **الانتقال لمسرح الجريمة:** انتقل إلى المجلد المحدد `cd /etc/cron.d/`.
3. **فحص الأدلة:** اعرض محتويات المجلد باستخدام `ls -la` وابحث عن الملف المتعلق بـ `bandit22`.
4. **قراءة الإعدادات:** استخدم `cat` لقراءة محتوى ملف `cron` ذي الصلة. هذا سيعطينا مسار البرنامج النصي الذي يتم تشغيله.
5. **تحليل البرنامج النصي:** اقرأ محتوى البرنامج النصي باستخدام `cat` لفهم ما يفعله وأين يكتب كلمة السر.
6. **الحصول على كلمة السر:** اقرأ الملف المؤقت الذي يكتب فيه البرنامج النصي كلمة السر.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|متى نفكر في استخدامها؟|
|---|---|---|
|**`cd`**|**تغيير المجلد (Change Directory)**|تُستخدم للانتقال إلى مجلد مختلف في نظام الملفات. استخدمناها للذهاب إلى `/etc/cron.d/`.|
|**`ls -la`**|**عرض القائمة المفصل**|تعرض جميع الملفات (بما في ذلك المخفية) في مجلد مع معلومات مفصلة (الصلاحيات، المالك، الحجم، إلخ).|
|**`cat`**|**عرض محتوى الملفات**|تُستخدم لطباعة محتوى ملف أو أكثر على الشاشة. هي أداة حاسمة في هذا المستوى لقراءة ملف إعدادات `cron`، والبرنامج النصي نفسه، وأخيرًا ملف كلمة السر.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

**كلمة السر الصحيحة:** كلمة سر المستوى `bandit21` هي `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`.

##### **المرحلة 1: الانتقال لمسرح الجريمة والاستكشاف**

بعد الدخول باسم `bandit21`، نذهب مباشرة إلى مجلد `/etc/cron.d/` ونعرض محتوياته.

Bash

```
bandit21@bandit:~$ ssh bandit21@bandit.labs.overthewire.org -p 2220
# كلمة السر: EeoULMCra2q0dSkYj561DX7s1CpBuOBt

bandit21@bandit:~$ cd /etc/cron.d/
bandit21@bandit:/etc/cron.d$ ls -la
...
-rw-r--r--   1 root root   120 Oct 14 09:26 cronjob_bandit22
...
```

**التحليل:** وجدنا ملفًا اسمه `cronjob_bandit22`، والذي يبدو مرتبطًا بشكل مباشر بالمستوى التالي.

##### **المرحلة 2: فهم مهمة Cron**

نقرأ محتوى هذا الملف لنعرف ما هي المهمة المجدولة.

Bash

```
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
```

**التحليل:** هذا السطر يخبرنا أنه كل دقيقة (`* * * * *`)، يقوم المستخدم `bandit22` بتشغيل البرنامج النصي `/usr/bin/cronjob_bandit22.sh`.

##### **المرحلة 3: كشف أسرار البرنامج النصي**

الآن، نقرأ محتوى البرنامج النصي نفسه لنفهم ما يفعله.

Bash

```
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

**التحليل:** البرنامج النصي بسيط للغاية:

1. يقرأ كلمة سر `bandit22` من ملف `/etc/bandit_pass/bandit22`.
2. يكتب كلمة السر هذه في ملف جديد اسمه `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv`.
3. يغير صلاحيات هذا الملف المؤقت إلى `644`، مما يعني أنه "قابل للقراءة من قبل الجميع".

##### **المرحلة 4: الحصول على كلمة السر**

بما أننا نعرف الآن أين يتم نسخ كلمة السر كل دقيقة، كل ما علينا فعله هو قراءة ذلك الملف.

Bash

```
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

**الحكم النهائي:** من خلال تحليل مهمة `cron`، وجدنا البرنامج النصي الذي يتم تشغيله، وبفهم منطق البرنامج النصي، حددنا الملف الذي يتم نسخ كلمة السر إليه وقمنا بقراءته.

**كلمة السر للمستوى `bandit22` هي: `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`**

#### **الجزء الرابع: لماذا لا تعمل الطرق البديلة؟**

في هذه البيئة تحديدًا، لا نملك صلاحية عرض محتويات المجلد `/tmp` مباشرة باستخدام أوامر مثل `ls` أو `find`. هذه حماية أمنية وضعتها OverTheWire لمنع اللاعبين من رؤية ملفات بعضهم البعض المؤقتة. لهذا السبب، تفشل طرق مثل مراقبة المجلد (`watch`) أو البحث فيه عن ملفات تم تعديلها حديثًا (`find`) وتعطي خطأ "Permission denied".

هذا يوضح أن الطريقة الوحيدة الصالحة لحل هذا المستوى هي تحليل البرنامج النصي لمعرفة **الاسم الكامل** للملف، ثم قراءته مباشرة.