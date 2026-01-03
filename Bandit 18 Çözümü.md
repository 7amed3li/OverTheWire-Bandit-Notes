

### **Bandit Seviye 18 -> 19 (`.bashrc` Tuzağını Atlatma)**

Bu seviye, Linux'un temellerine ve bir kullanıcının oturum açtığında arka planda neler olduğuna dair anlayışımızı test eden zekice bir bulmacadır. Bu sadece bir komut bulmakla ilgili değil, sistemin davranışını alt etmekle ilgilidir.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[27.png]] "Bir sonraki seviyenin şifresi, ev dizinindeki `readme` adlı bir dosyada saklanıyor. Maalesef, birisi `.bashrc` dosyasını SSH ile giriş yaptığınızda sizi dışarı atacak şekilde değiştirmiş."

**Hedefin Zihinsel Analizi:**

1. **Hazine:** Şifre, `readme` adlı bir dosyada.
2. **Tuzak:** SSH ile giriş yaptığımız anda sistem bizi dışarı atıyor.
3. **Suçlu:** Hedef bize açıkça suçlunun `.bashrc` dosyası olduğunu söylüyor.

**`.bashrc` Dosyası Nedir ve Neden Tehlikelidir?**

- `.bashrc` (Bash Run Commands), bir Linux kullanıcısı yeni bir terminal oturumu (shell) başlattığında **otomatik olarak** çalışan bir komut dosyasıdır (script).
- Görevi, kullanıcı için ortamı hazırlamaktır (örneğin, komut takma adları (alias) oluşturmak, komut isteminin (prompt) rengini ayarlamak vb.).
- **Tuzak burada:** Eğer birisi bu dosyanın içine `exit` veya `logout` gibi bir komut koyarsa, yeni bir shell başlattığınız anda bu komut çalışır ve oturumunuz anında kapatılır. Tam olarak bizim başımıza gelen de bu.

**Bu Tuzak Nasıl Aşılır?** SSH protokolü, sadece interaktif (etkileşimli) bir shell başlatmaktan daha fazlasını yapabilir. Uzak sunucuda **tek bir komut çalıştırma** ve sonra bağlantıyı kapatma yeteneğine sahiptir.

- **Anahtar Fikir:** Eğer SSH'a interaktif bir shell başlatmak yerine sadece belirli bir komutu çalıştırmasını söylersek, `.bashrc` dosyası hiç tetiklenmez veya etkisi atlatılır. Çünkü biz bir "shell" istemiyoruz, sadece bir "komutun çıktısını" istiyoruz.

**İlk Plan (Akıllıca Saldırı):**

1. `bandit18` olarak SSH ile bağlan, ancak bağlantı komutunun sonuna, çalıştırmak istediğimiz komutu ekle.
2. Çalıştırmak istediğimiz komut ne? Şifrenin `readme` dosyasında olduğunu bildiğimize göre, en mantıklı komut `cat readme`'dir.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Bu Seviyede Neden Önemlidir?|
|---|---|---|
|**`ssh [user]@[host] "[command]"`**|**Uzaktan Komut Çalıştırma**|**Bu seviyenin anahtarıdır.** SSH'ın bu özelliği, interaktif bir oturum başlatmadan doğrudan uzak sunucuda bir komut çalıştırmamızı sağlar. Bu, `.bashrc` gibi başlangıç script'lerini atlatmanın en temiz yoludur.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Şifrenin içinde bulunduğu `readme` dosyasının içeriğini okumak ve ekrana yazdırmak için kullanacağımız komuttur.|
|**`ls`**|**Dizin İçeriğini Listeleme**|Eğer `.bashrc` tuzağını atlatıp bir şekilde interaktif bir oturum elde etmeyi başarsaydık (alternatif yöntemlerde göreceğiz), `readme` dosyasının gerçekten orada olup olmadığını doğrulamak için `ls` kullanırdık.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte bu seviyeyi çözme yolculuğu, başarısız deneme ve başarılı çözüm dahil.

##### **Aşama 1: Normal Giriş Denemesi ve Tuzağa Düşme**

Herhangi bir aceminin yapacağı ilk şey, normal bir şekilde SSH ile giriş yapmayı denemektir. İşte bu denemenin sonucu:

Bash

```
┌──(root㉿kali)-[~]
└─# ssh bandit18@bandit.labs.overthewire.org -p 2220
...
bandit18@bandit.labs.overthewire.org's password: (şifre girilir)
...
Welcome to OverTheWire!
...
Byebye !
Connection to bandit.labs.overthewire.org closed.
```

**Analiz:**

- Giriş başarılı oldu, şifre doğruydu.
- Hoş geldiniz mesajını gördük.
- Ancak hemen ardından "Byebye !" mesajını gördük ve bağlantı kapandı.
- **Sonuç:** `.bashrc` tuzağı tam olarak beklendiği gibi çalıştı. Bize herhangi bir komut yazma fırsatı vermeden bizi dışarı attı.

##### **Aşama 2: Zeki Saldırı ve Başarılı Çözüm**

Şimdi, `.bashrc`'yi atlatmak için planımızı uyguluyoruz: SSH ile bağlanırken doğrudan çalıştırmak istediğimiz komutu belirtiyoruz.

Bash

```
┌──(root㉿kali)-[~]
└─# ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
bandit18@bandit.labs.overthewire.org's password: (şifre girilir)
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

**Analiz:**

1. SSH, `bandit18` olarak kimlik doğrulaması yaptı.
2. İnteraktif bir shell başlatmak **yerine**, doğrudan `"cat readme"` komutunu uzak sunucuda çalıştırdı.
3. `cat readme` komutunun çıktısını (şifrenin kendisi) terminalimize geri gönderdi.
4. Komut bittiğinde, SSH bağlantıyı otomatik olarak kapattı.
5. Tüm bunlar, `.bashrc`'nin bizi dışarı atma fırsatı bulamadan gerçekleşti.

**`bandit19` seviyesinin şifresi: `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`**

##### **Aşama 3: Alternatif Çözüm Yolları (Daha Fazla Bilgi İçin)**

Bu bulmacayı çözmenin başka yolları da vardır, hepsi aynı prensibe dayanır: interaktif olmayan bir shell kullanmak.

- **Alternatif 1: `bash --noprofile --norc` Kullanımı** Eğer bir şekilde interaktif bir shell'e ihtiyacınız olsaydı, `bash`'e başlangıç dosyalarını (`.bash_profile`, `.bashrc` vb.) yüklememesini söyleyebilirdiniz.
    
    Bash
    
    ```
    ssh bandit18@... -p 2220 'bash --noprofile --norc'
    ```
    
    Bu komut size, hiçbir başlangıç script'ini çalıştırmayan "temiz" bir shell verir ve böylece `exit` komutu tetiklenmez.
    
- **Alternatif 2: SFTP Kullanımı** SFTP (SSH File Transfer Protocol), dosyaları güvenli bir şekilde aktarmak için SSH'ı kullanır ancak bir shell çalıştırmaz. Dolayısıyla `.bashrc`'den etkilenmez. Dosyayı doğrudan indirebilirdik.
    
    Bash
    
    ```
    # 1. SFTP ile bağlan
    sftp -P 2220 bandit18@bandit.labs.overthewire.org
    
    # 2. SFTP istemcisinin içinde, dosyayı indir
    sftp> get readme
    
    # 3. SFTP'den çık
    sftp> exit
    
    # 4. Yerel makinede dosyayı oku
    cat readme
    ```
    

### **دليل Bandit التعليمي: المستوى 18 -> 19 (التحايل على فخ `.bashrc`)**

هذا المستوى هو لغز ذكي يختبر فهمنا لأساسيات لينكس وما يحدث في الكواليس عند تسجيل دخول المستخدم. الأمر لا يتعلق فقط بإيجاد أمر، بل يتعلق بالتحايل على سلوك النظام نفسه.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**
![[27.png]]
 "كلمة السر للمستوى التالي مخزنة في ملف يسمى `readme` في المجلد الرئيسي. لسوء الحظ، قام شخص ما بتعديل ملف `.bashrc` ليقوم بتسجيل خروجك عند تسجيل الدخول باستخدام SSH."

**التحليل الذهني للهدف:**

1. **الكنز:** كلمة السر موجودة في ملف `readme`.
2. **الفخ:** بمجرد تسجيل الدخول عبر SSH، يتم طردنا على الفور.
3. **المتهم:** الهدف يخبرنا بصراحة أن المتهم هو ملف `.bashrc`.

**ما هو ملف `.bashrc` ولماذا هو خطير؟**

- `.bashrc` (Bash Run Commands) هو سكربت (ملف أوامر) يعمل **تلقائيًا** في كل مرة يبدأ فيها مستخدم لينكس جلسة طرفية (shell) جديدة.
- وظيفته هي تهيئة البيئة للمستخدم (على سبيل المثال، إنشاء اختصارات للأوامر (aliases)، ضبط لون موجه الأوامر (prompt)، إلخ).
- **الفخ هنا:** إذا وضع شخص ما أمرًا مثل `exit` أو `logout` داخل هذا الملف، فبمجرد أن تبدأ جلسة shell جديدة، سيتم تنفيذ هذا الأمر وسيتم إغلاق جلستك على الفور. وهذا بالضبط ما يحدث لنا.

**كيف نتجاوز هذا الفخ؟** يمكن لبروتوكول SSH أن يفعل أكثر من مجرد بدء جلسة طرفية تفاعلية. لديه القدرة على **تنفيذ أمر واحد** على الخادم البعيد ثم إغلاق الاتصال.

- **الفكرة الرئيسية:** إذا طلبنا من SSH تنفيذ أمر معين فقط بدلاً من بدء جلسة تفاعلية، فلن يتم تشغيل ملف `.bashrc` أو سيتم تجاوز تأثيره. لأننا لا نطلب "shell"، بل نطلب فقط "ناتج أمر".

**الخطة المبدئية (الهجوم الذكي):**

1. اتصل بـ `bandit18` عبر SSH، ولكن أضف الأمر الذي نريد تنفيذه في نهاية أمر الاتصال.
2. ما هو الأمر الذي نريد تنفيذه؟ بما أننا نعرف أن كلمة السر موجودة في ملف `readme`، فإن الأمر الأكثر منطقية هو `cat readme`.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|لماذا هي مهمة في هذا المستوى؟|
|---|---|---|
|**`ssh [user]@[host] "[command]"`**|**تنفيذ الأوامر عن بعد**|**هذا هو مفتاح هذا المستوى.** تتيح لنا هذه الميزة في SSH تنفيذ أمر مباشرة على الخادم البعيد دون بدء جلسة تفاعلية. هذه هي أنظف طريقة لتجاوز سكربتات بدء التشغيل مثل `.bashrc`.|
|**`cat`**|**عرض محتوى الملفات**|هو الأمر الذي سنستخدمه لقراءة محتوى ملف `readme` الذي يحتوي على كلمة السر وطباعته على الشاشة.|
|**`ls`**|**عرض محتويات المجلد**|إذا تمكنا بطريقة ما من تجاوز فخ `.bashrc` والحصول على جلسة تفاعلية (سنرى ذلك في الطرق البديلة)، فسنستخدم `ls` للتأكد من وجود ملف `readme` بالفعل.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

إليك رحلة حل هذا المستوى، بما في ذلك المحاولة الفاشلة والحل الناجح.

##### **المرحلة 1: محاولة الدخول العادية والوقوع في الفخ**

أول شيء سيجربه أي مبتدئ هو محاولة تسجيل الدخول بشكل طبيعي عبر SSH. إليك نتيجة هذه المحاولة:

Bash

```
┌──(root㉿kali)-[~]
└─# ssh bandit18@bandit.labs.overthewire.org -p 2220
...
bandit18@bandit.labs.overthewire.org's password: (يتم إدخال كلمة المرور)
...
Welcome to OverTheWire!
...
Byebye !
Connection to bandit.labs.overthewire.org closed.
```

**التحليل:**

- تم تسجيل الدخول بنجاح، وكانت كلمة المرور صحيحة.
- رأينا رسالة الترحيب.
- ولكن على الفور، رأينا رسالة "Byebye !" وأُغلق الاتصال.
- **النتيجة:** فخ `.bashrc` عمل تمامًا كما هو متوقع. لم يمنحنا أي فرصة لكتابة أي أمر وقام بطردنا.

##### **المرحلة 2: الهجوم الذكي والحل الناجح**

الآن، نطبق خطتنا لتجاوز `.bashrc`: نحدد الأمر الذي نريد تنفيذه مباشرة أثناء الاتصال بـ SSH.

Bash

```
┌──(root㉿kali)-[~]
└─# ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
bandit18@bandit.labs.overthewire.org's password: (يتم إدخال كلمة المرور)
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

**التحليل:**

1. قام SSH بالمصادقة كمستخدم `bandit18`.
2. **بدلاً من** بدء جلسة shell تفاعلية، قام مباشرة بتنفيذ الأمر `"cat readme"` على الخادم البعيد.
3. أعاد ناتج الأمر `cat readme` (وهو كلمة السر نفسها) إلى الطرفية الخاصة بنا.
4. عندما انتهى الأمر، أغلق SSH الاتصال تلقائيًا.
5. كل هذا حدث قبل أن يجد `.bashrc` فرصة لطردنا.

**كلمة السر للمستوى `bandit19` هي: `cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8`**

##### **المرحلة 3: طرق الحل البديلة (لمزيد من المعرفة)**

هناك طرق أخرى لحل هذا اللغز، وكلها تعتمد على نفس المبدأ: استخدام جلسة غير تفاعلية.

- **الطريقة البديلة 1: استخدام `bash --noprofile --norc`** إذا كنت بحاجة إلى جلسة تفاعلية لسبب ما، يمكنك أن تطلب من `bash` عدم تحميل ملفات بدء التشغيل (`.bash_profile`, `.bashrc`, إلخ).
    
    Bash
    
    ```
    ssh bandit18@... -p 2220 'bash --noprofile --norc'
    ```
    
    يمنحك هذا الأمر جلسة shell "نظيفة" لا تقوم بتشغيل أي سكربتات بدء، وبالتالي لا يتم تشغيل أمر `exit`.
    
- **الطريقة البديلة 2: استخدام SFTP** يستخدم SFTP (بروتوكول نقل الملفات عبر SSH) بروتوكول SSH لنقل الملفات بأمان ولكنه لا يقوم بتشغيل shell. لذلك، لا يتأثر بملف `.bashrc`. كان بإمكاننا تنزيل الملف مباشرة.
    
    Bash
    
    ```
    # 1. اتصل باستخدام SFTP
    sftp -P 2220 bandit18@bandit.labs.overthewire.org
    
    # 2. داخل عميل SFTP، قم بتنزيل الملف
    sftp> get readme
    
    # 3. اخرج من SFTP
    sftp> exit
    
    # 4. اقرأ الملف على جهازك المحلي
    cat readme
    ```

---

## 🇺🇸 English

### Objective
Bypass `.bashrc` which logs you out immediately upon SSH login.

### Command
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"
```

### Explanation
- When SSH connects with a command, it executes that command directly without starting an interactive shell
- The `.bashrc` file (which contains `exit`) is not executed
- This bypasses the logout trap

### Alternative Methods
- `ssh ... -t /bin/sh`: Start a different shell
- `sftp`: File transfer protocol doesn't trigger `.bashrc`

### Key Takeaways
- **SSH Command Execution:** `ssh user@host "command"` runs command directly
- **Restricted Shell Bypass:** Avoid malicious `.bashrc` by not starting interactive shell
