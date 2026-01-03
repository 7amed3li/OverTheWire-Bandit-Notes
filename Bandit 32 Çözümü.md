### **Bandit Seviye 32 -> 33 (Kısıtlı Kabuktan Kaçış) Kılavuzu**

Bu son seviye, Git'in karmaşıklığından uzaklaşıp siber güvenliğin temel bir konseptine odaklanır: **kısıtlı kabuklardan (restricted shells) kaçış**. Bu tür kabuklar, bir kullanıcının sistem üzerindeki yeteneklerini sınırlamak için tasarlanmıştır. Bu seviyedeki "Uppercase Shell", yazdığımız her komutu büyük harfe çevirerek Linux'un küçük harfe duyarlı yapısını bize karşı kullanır ve standart komutları işlevsiz hale getirir.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "Bir sonraki seviyenin şifresini bulun."

**Zihinsel Analiz:**

1. **Görev:** Sisteme bağlandığımızda bizi standart bir `bash` kabuğu yerine, `WELCOME TO THE UPPERCASE SHELL` mesajıyla özel bir kabuk karşılıyor.
2. **Kritik Nokta:** `ls` yazdığımızda, kabuk bunu `LS` olarak çalıştırıyor ve Linux'ta `LS` adında bir komut olmadığı için "Command not found" veya "Permission denied" hatası alıyoruz. Bu, `cat`, `cd`, `whoami` gibi tüm temel komutlar için geçerlidir.
3. **Strateji:** Doğrudan komut yazmak işe yaramayacak. Kabuğun kendisini veya ortam değişkenlerini (environment variables) manipüle etmenin bir yolunu bulmalıyız. Kabuğun, komutları büyük harfe çevirmeden önce değişkenleri yorumlama olasılığı yüksektir. Bu, kaçış noktamız olabilir.

**İlk Plan (Dedektif Planı):**

1. **Bağlan ve Doğrula:** `bandit32` olarak SSH ile bağlan ve `ls` gibi bir komut yazarak kabuğun gerçekten de her şeyi büyük harfe çevirdiğini ve komutların çalışmadığını doğrula.
2. **Değişkenleri Kullanarak Kaç:** Komut yazmak yerine, çalışan kabuğun kendisini temsil eden bir değişkeni çağır. `$0` veya `$SHELL` gibi değişkenler, mevcut kabuk programının (`/bin/sh` veya `/bin/bash`) yolunu içerir. Bu değişkeni çağırmak, büyük harf dönüştürme katmanını atlayarak yeni ve kısıtlamasız bir kabuk başlatabilir.
3. **Şifreyi Al:** Normal bir kabuğa (`$`) düştükten sonra, standart `cat` komutunu kullanarak şifre dosyasını oku.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç / Kavram|Türkçe Açıklama|Bu Seviyedeki Rolü|
|---|---|---|
|**Kısıtlı Kabuk**|**Restricted Shell**|Kullanıcının çalıştırabileceği komutları veya gerçekleştirebileceği eylemleri sınırlayan özel bir kabuk ortamı.|
|**`$0`**|**Kabuk Değişkeni**|Çalışan betiğin veya kabuğun adını içeren özel bir değişkendir. Bu, kaçış anahtarımızdır.|
|**`$SHELL`**|**Kabuk Değişkeni**|Kullanıcının varsayılan kabuğunun yolunu tutan bir ortam değişkenidir. `$0` gibi bu da kaçış için kullanılabilir.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Kısıtlı kabuktan kaçtıktan sonra şifre dosyasını okumak için kullandığımız standart komut.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: Kısıtlı Kabuğa Giriş ve Test**

Önce bağlanıp tuzağı kendi gözlerimizle görüyoruz.

Bash

```
ssh bandit32@bandit.labs.overthewire.org -p 2220
# bandit32'nin şifresini girin: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K

WELCOME TO THE UPPERCASE SHELL
>> ls
sh: 1: LS: not found
>> whoami
sh: 1: WHOAMI: not found
```

**Analiz:** Kabuk beklendiği gibi çalışıyor; komutlarımız işe yaramaz hale geliyor.

##### **Aşama 2: Kabuktan Kaçış**

Şimdi, komut yazmak yerine, kabuğun kendisini çağırmak için `$0` değişkenini kullanıyoruz.

Bash

```
>> $0
$
```

**Analiz:** Başardık! Komut istemi `>>` yerine `$` oldu. Bu, artık kısıtlamasız, normal bir `sh` veya `bash` kabuğunda olduğumuzun kesin kanıtıdır. Artık komutlarımız olması gerektiği gibi çalışacak.

##### **Aşama 3: Zafer ve Şifreyi Alma**

Normal bir kabukta olduğumuza göre, son şifreyi alabiliriz.

Bash

```
$ ls -la
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
$ exit
```

**Nihai Karar:** Bu seviye, bir sistemin kurallarını doğrudan çiğnemek yerine, bu kuralların içindeki boşlukları ve istisnaları kullanarak nasıl aşılabileceğini gösteren klasik bir "hacker zihniyeti" dersiydi. Değişkenlerin komutlardan önce işlendiği gerçeğini kullanarak, kısıtlayıcı katmanı tamamen atladık.

**`bandit33` seviyesinin şifresi: `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`**

### **دليل Bandit التعليمي: المستوى 32 -> 33 (الهروب من الصدفة المقيدة)**

هذا المستوى الأخير يبتعد عن تعقيدات Git ويركز على مفهوم أساسي في الأمن السيبراني: **الهروب من الأصداف المقيدة (Restricted Shells)**. تم تصميم هذه الأصداف للحد من قدرات المستخدم على النظام. "الصدفة الكبيرة" في هذا المستوى تستغل حساسية Linux لحالة الأحرف ضدنا عن طريق تحويل كل أمر نكتبه إلى أحرف كبيرة، مما يجعل الأوامر القياسية عديمة الفائدة.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "ابحث عن كلمة السر للمستوى التالي."

**التحليل الذهني للهدف:**

1. **المهمة:** عند الاتصال بالنظام، بدلاً من صدفة `bash` القياسية، تستقبلنا صدفة مخصصة برسالة `WELCOME TO THE UPPERCASE SHELL`.
2. **النقطة الحاسمة:** عندما نكتب `ls`، تقوم الصدفة بتشغيله كـ `LS`، وبما أنه لا يوجد أمر بهذا الاسم في Linux، نحصل على خطأ "Command not found" أو "Permission denied". هذا ينطبق على جميع الأوامر الأساسية مثل `cat`، `cd`، `whoami`.
3. **الاستراتيجية:** كتابة الأوامر مباشرة لن تنجح. يجب أن نجد طريقة للتلاعب بالصدفة نفسها أو بمتغيرات البيئة الخاصة بها. من المحتمل جدًا أن تقوم الصدفة بتفسير المتغيرات قبل تحويل الأوامر إلى أحرف كبيرة. قد تكون هذه هي نقطة الهروب لدينا.

**الخطة المبدئية (خطة المحقق):**

1. **الاتصال والتحقق:** اتصل بـ `bandit32` عبر SSH واكتب أمرًا مثل `ls` للتحقق من أن الصدفة تقوم بالفعل بتحويل كل شيء إلى أحرف كبيرة وأن الأوامر لا تعمل.
2. **الهروب باستخدام المتغيرات:** بدلاً من كتابة أمر، قم باستدعاء متغير يمثل الصدفة قيد التشغيل نفسها. متغيرات مثل `$0` أو `$SHELL` تحتوي على مسار برنامج الصدفة الحالي (`/bin/sh` أو `/bin/bash`). استدعاء هذا المتغير قد يبدأ صدفة جديدة غير مقيدة، متجاوزًا طبقة التحويل إلى الأحرف الكبيرة.
3. **الحصول على كلمة السر:** بمجرد أن تسقط في صدفة عادية (`$`)، استخدم أمر `cat` القياسي لقراءة ملف كلمة المرور.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة / المفهوم|الشرح بالعربية|دورها في هذا المستوى|
|---|---|---|
|**الصدفة المقيدة**|**Restricted Shell**|بيئة صدفة خاصة تقيد الأوامر التي يمكن للمستخدم تشغيلها أو الإجراءات التي يمكنه القيام بها.|
|**`$0`**|**متغير الصدفة**|متغير خاص يحتوي على اسم السكربت أو الصدفة قيد التشغيل. هذا هو مفتاح الهروب لدينا.|
|**`$SHELL`**|**متغير الصدفة**|متغير بيئة يحمل مسار الصدفة الافتراضية للمستخدم. يمكن استخدامه للهروب مثل `$0`.|
|**`cat`**|**عرض محتوى الملفات**|الأمر القياسي الذي نستخدمه لقراءة ملف كلمة المرور بعد الهروب من الصدفة المقيدة.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

##### **المرحلة 1: الدخول إلى الصدفة المقيدة واختبارها**

أولاً، نتصل ونرى الفخ بأعيننا.

Bash

```
ssh bandit32@bandit.labs.overthewire.org -p 2220
# أدخل كلمة مرور bandit32: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K

WELCOME TO THE UPPERCASE SHELL
>> ls
sh: 1: LS: not found
>> whoami
sh: 1: WHOAMI: not found
```

**التحليل:** الصدفة تعمل كما هو متوقع؛ أوامرنا أصبحت عديمة الفائدة.

##### **المرحلة 2: الهروب من الصدفة**

الآن، بدلاً من كتابة أمر، نستخدم المتغير `$0` لاستدعاء الصدفة نفسها.

Bash

```
>> $0
$
```

**التحليل:** لقد نجحنا! تغير موجه الأوامر من `>>` إلى `$`. هذا دليل قاطع على أننا الآن في صدفة `sh` أو `bash` عادية وغير مقيدة. الآن ستعمل أوامرنا كما ينبغي.

##### **المرحلة 3: النصر والحصول على كلمة السر**

بما أننا في صدفة عادية، يمكننا الحصول على كلمة السر النهائية.

Bash

```
$ ls -la
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
$ exit
```

**الحكم النهائي:** كان هذا المستوى درسًا كلاسيكيًا في "عقلية الهاكر"، حيث يوضح كيف يمكن تجاوز قواعد النظام ليس عن طريق كسرها مباشرة، ولكن باستخدام الثغرات والاستثناءات داخل تلك القواعد. من خلال استغلال حقيقة أن المتغيرات تتم معالجتها قبل الأوامر، تجاوزنا الطبقة المقيدة تمامًا.

**كلمة السر للمستوى `bandit33` هي: `tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0`**

---

## 🇺🇸 English

### Objective
Escape from the "Uppercase Shell" that converts all commands to uppercase.

### The Challenge
All commands typed are converted to UPPERCASE, making them fail:
- `ls` becomes `LS` → "not found"
- `cat` becomes `CAT` → "not found"

### Solution
```bash
>> $0
$
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```

### Explanation
- `$0` is a special variable containing the current shell
- Variables are processed BEFORE the uppercase conversion
- Calling `$0` spawns a new unrestricted shell

### Key Takeaways
- **`$0` Variable:** Contains the running shell's path
- **Variable Expansion:** Happens before command processing
- **Restricted Shell Escape:** Exploit processing order to bypass restrictions