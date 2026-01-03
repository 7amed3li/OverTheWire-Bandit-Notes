### **Bandit Seviye 22 -> 23 (Dinamik Analiz ve Mantık Taklidi)**

Bu seviye, bir önceki seviyenin üzerine inşa edilmiştir ancak önemli bir farkla: **öngörülemezlik**. Bize, sadece betikleri okumanın değil, aynı zamanda onların mantığını anlayıp taklit etmenin de ne kadar önemli olduğunu öğretir.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[Bandit 22 Challenge.png]] "`cron` tarafından düzenli aralıklarla otomatik olarak bir program çalıştırılıyor. Yapılandırma için `/etc/cron.d/` dizinine bakın... Eğer ne yaptığını anlamakta zorlanıyorsanız, hata ayıklama bilgilerini görmek için onu çalıştırmayı deneyin."

**Hedefin Zihinsel Analizi:**

1. **Başlangıç Noktası:** Bir önceki seviyeyle aynı: `/etc/cron.d/` dizinini kontrol et.
2. **İpucu:** Meydan okuma, betiğin "kasıtlı olarak kolay okunur" yapıldığını ve onu **çalıştırarak** hata ayıklama bilgilerini görebileceğimizi belirtiyor. Bu, betiğin davranışının onu çalıştıran kullanıcıya göre değişebileceğine dair güçlü bir işarettir.
3. **Temel Fark:** Bir önceki seviyede, parola sabit bir dosyaya kopyalanıyordu. Bu seviyede ise, bir "dinamik" veya "değişken" isme sahip bir dosyaya kopyalanması muhtemeldir.
4. **Nihai Amaç:** `cron` işinin çalıştırdığı betiğin mantığını çözmek, şifrenin kopyalandığı dosyanın adını nasıl hesapladığını anlamak ve bu adı kendimiz hesaplayarak dosyayı okumak.

**İlk Plan (Dedektif Planı):**

1. **Giriş:** `bandit22` kullanıcısı olarak SSH ile giriş yap.
2. **Keşif:** `/etc/cron.d/` dizinine git ve `cronjob_bandit23` dosyasını bul.
3. **Yapılandırmayı Oku:** `cat cronjob_bandit23` komutuyla çalıştırılan betiğin tam yolunu öğren.
4. **Betiği Analiz Et:** Betiğin içeriğini `cat` ile oku. Betiğin, dosya adını nasıl oluşturduğunu anlamaya odaklan.
5. **Mantığı Taklit Et:** Betiğin dosya adını oluşturmak için kullandığı komutları, `bandit23` kullanıcısı için manuel olarak çalıştırarak dosya adını kendin hesapla.
6. **Şifreyi Al:** Hesapladığın dosya adını kullanarak `/tmp` dizinindeki dosyayı oku.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

Bu seviyede kullanılan komutlar bir öncekiyle aynıdır, ancak `echo`, `md5sum` ve `cut` gibi yeni araçlar betiğin içinde karşımıza çıkar.

|Araç|Türkçe Açıklama|Ne Zaman Kullanmayı Düşünmeliyiz?|
|---|---|---|
|**`echo`**|**Metin Yazdırma**|Kendisine verilen metni veya değişkenleri ekrana yazdırmak için kullanılır. Betikte, `md5sum` komutuna girdi sağlamak için kullanılmıştır.|
|**`md5sum`**|**MD5 Hash Hesaplayıcı**|Verilen bir girdinin (metin veya dosya) MD5 özetini (hash) hesaplar. Betikte, dinamik dosya adını oluşturmak için kullanılmıştır.|
|**`cut`**|**Metin Kesme Aracı**|Bir metin satırının belirli bölümlerini kesmek için kullanılır. Betikte, `md5sum` çıktısının sadece hash kısmını almak için kullanılmıştır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

**Doğru Şifre:** `bandit22` seviyesinin şifresi `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`'dur.

##### **Aşama 1: Cron İşini Bulma**

`bandit22` olarak giriş yaptıktan sonra, `cronjob_bandit23` dosyasını inceleriz.

Bash

```
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

**Analiz:** Her dakika, `bandit23` kullanıcısı `/usr/bin/cronjob_bandit23.sh` betiğini çalıştırıyor.

##### **Aşama 2: Betiğin Mantığını Çözme**

Şimdi, betiğin kendisini okuyarak sırrını ortaya çıkarıyoruz.

Bash

```
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

**Analiz:**

- `myname` değişkeni, betiği çalıştıran kullanıcının adını alır (`cron` çalıştırdığında bu `bandit23` olur).
- `mytarget` değişkeni, "I am user [kullanıcı_adı]" cümlesinin MD5 hash'i olarak ayarlanır.
- Son olarak, `bandit23` şifresi, adı bu hash olan bir dosyaya (`/tmp/[hash_değeri]`) kopyalanır.

**Problem:** Eğer betiği biz (`bandit22`) çalıştırırsak, `myname` "bandit22" olur ve yanlış dosya adı hesaplanır. Bu yüzden betiğin mantığını taklit etmeliyiz.

##### **Aşama 3: Dosya Adını Hesaplama**

Betiğin `bandit23` olarak çalışırken ne yapacağını tam olarak taklit ediyoruz.

Bash

```
bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```

**Analiz:** İşte bu, `cron` işinin her dakika oluşturduğu dosyanın adıdır.

##### **Aşama 4: Şifreyi Ele Geçirme**

Artık dosyanın tam adını bildiğimize göre, onu okumak çok kolay.

Bash

```
bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

**Nihai Karar:** Betiğin dinamik mantığını anlayıp manuel olarak taklit ederek, şifrenin kopyalandığı dosyanın adını başarıyla belirledik ve içeriğini okuduk.

**`bandit23` seviyesinin şifresi: `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`**

### **دليل Bandit التعليمي: المستوى 22 -> 23 (التحليل الديناميكي ومحاكاة المنطق)**

هذا المستوى مبني على المستوى السابق، ولكنه يضيف لمسة مهمة: **عدم القدرة على التنبؤ**. إنه يعلمنا أهمية ليس فقط قراءة البرامج النصية، بل فهم منطقها ومحاكاته.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

![[Bandit 22 Challenge.png]] "برنامج يعمل تلقائيًا على فترات منتظمة من `cron`. ابحث في `/etc/cron.d/`... إذا واجهت مشاكل في فهم ما يفعله، حاول تنفيذه لترى معلومات التصحيح التي يطبعها."

**التحليل الذهني للهدف:**

1. **نقطة البداية:** نفس المستوى السابق: تحقق من المجلد `/etc/cron.d/`.
2. **التلميح:** التحدي يشير إلى أن البرنامج النصي "سهل القراءة عن قصد" وأننا يمكننا **تشغيله** لرؤية معلومات التصحيح. هذه إشارة قوية إلى أن سلوك البرنامج النصي قد يتغير اعتمادًا على المستخدم الذي يقوم بتشغيله.
3. **الاختلاف الجوهري:** في المستوى السابق، تم نسخ كلمة السر إلى ملف باسم ثابت. في هذا المستوى، من المحتمل أن يتم نسخها إلى ملف باسم "ديناميكي" أو "متغير".
4. **الغاية النهائية:** فك شفرة منطق البرنامج النصي الذي تشغله مهمة `cron`، وفهم كيف يقوم بحساب اسم الملف الذي يتم نسخ كلمة السر إليه، ثم حساب هذا الاسم بأنفسنا لقراءة الملف.

**الخطة المبدئية (خطة المحقق):**

1. **تسجيل الدخول:** سجل الدخول كمستخدم `bandit22`.
2. **الاستكشاف:** انتقل إلى `/etc/cron.d/` وابحث عن الملف `cronjob_bandit23`.
3. **قراءة الإعدادات:** استخدم `cat cronjob_bandit23` لمعرفة المسار الكامل للبرنامج النصي الذي يتم تشغيله.
4. **تحليل البرنامج النصي:** اقرأ محتوى البرنامج النصي باستخدام `cat`. ركز على فهم كيفية إنشاء اسم الملف.
5. **محاكاة المنطق:** قم بتنفيذ الأوامر التي يستخدمها البرنامج النصي لإنشاء اسم الملف يدويًا، ولكن للمستخدم `bandit23`، لحساب اسم الملف بنفسك.
6. **الحصول على كلمة السر:** اقرأ الملف الموجود في `/tmp` باستخدام الاسم الذي قمت بحسابه.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

الأوامر المستخدمة في هذا المستوى هي نفسها السابقة، لكننا نرى أدوات جديدة داخل البرنامج النصي نفسه.

|الأداة|الشرح بالعربية|متى نفكر في استخدامها؟|
|---|---|---|
|**`echo`**|**طباعة نص**|تُستخدم لطباعة النص أو المتغيرات المعطاة لها على الشاشة. في البرنامج النصي، تم استخدامها لتوفير مدخل لأمر `md5sum`.|
|**`md5sum`**|**حساب بصمة MD5**|تقوم بحساب بصمة MD5 (hash) لمدخل معين (نص أو ملف). في البرنامج النصي، تم استخدامها لإنشاء اسم الملف الديناميكي.|
|**`cut`**|**أداة قص النصوص**|تُستخدم لقص أجزاء معينة من سطر نصي. في البرنامج النصي، تم استخدامها لأخذ جزء الـ hash فقط من ناتج `md5sum`.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

**كلمة السر الصحيحة:** كلمة سر المستوى `bandit22` هي `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`.

##### **المرحلة 1: العثور على مهمة Cron**

بعد الدخول باسم `bandit22`، نفحص الملف `cronjob_bandit23`.

Bash

```
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
```

**التحليل:** كل دقيقة، يقوم المستخدم `bandit23` بتشغيل البرنامج النصي `/usr/bin/cronjob_bandit23.sh`.

##### **المرحلة 2: فك منطق البرنامج النصي**

الآن، نكشف سر البرنامج النصي بقراءة محتواه.

Bash

```
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

**التحليل:**

- المتغير `myname` يأخذ اسم المستخدم الذي يشغل البرنامج (سيكون `bandit23` عندما يشغله `cron`).
- المتغير `mytarget` يتم تعيينه ليكون بصمة MD5 لجملة "I am user [username]".
- أخيرًا، يتم نسخ كلمة سر `bandit23` إلى ملف في `/tmp/` اسمه هو قيمة هذا الـ hash.

**المشكلة:** إذا قمنا نحن (`bandit22`) بتشغيل البرنامج النصي، فإن `myname` ستكون "bandit22" وسيتم حساب اسم ملف خاطئ. لذلك، يجب أن نحاكي منطق البرنامج.

##### **المرحلة 3: حساب اسم الملف**

نحن نحاكي بالضبط ما سيفعله البرنامج النصي عندما يعمل كمستخدم `bandit23`.

Bash

```
bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
```

**التحليل:** هذا هو اسم الملف الذي تقوم مهمة `cron` بإنشائه كل دقيقة.

##### **المرحلة 4: الحصول على كلمة السر**

بما أننا نعرف الآن الاسم الكامل للملف، فإن قراءته بسيطة.

Bash

```
bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

**الحكم النهائي:** من خلال فهم المنطق الديناميكي للبرنامج النصي ومحاكاته يدويًا، نجحنا في تحديد اسم الملف الذي تم نسخ كلمة السر إليه وقمنا بقراءة محتواه.

**كلمة السر للمستوى `bandit23` هي: `0Zf11ioIjMVN551jX3CmStKLYqjk54Ga`**

---

## 🇺🇸 English

### Objective
Analyze a cron script that uses dynamic filenames based on username.

### Key Insight
The script generates filename using: `echo I am user $myname | md5sum | cut -d ' ' -f 1`

### Process
1. Simulate the script for target user:
   ```bash
   echo I am user bandit23 | md5sum | cut -d ' ' -f 1
   8ca319486bfbbc3663ea0fbe81326349
   ```
2. Read the password: `cat /tmp/8ca319486bfbbc3663ea0fbe81326349`

### Key Takeaways
- **Dynamic Analysis:** Simulate script logic with target variables
- **md5sum:** Creates unique hash from input string
- **cut:** Extracts specific fields from output