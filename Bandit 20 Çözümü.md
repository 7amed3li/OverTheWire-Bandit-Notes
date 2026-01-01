### **Bandit Seviye 20 -> 21 (Ağ ve Süreç Kontrolü Sanatı)**

Bu seviye, ağ temellerini (istemci/sunucu modeli) ve Linux süreç kontrolünü (job control) birleştiren zekice bir bulmacadır. Bizi aynı anda hem dinleyici (sunucu) hem de bağlanan (istemci) olmaya zorlar.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[Bandit 20 Challenge.png]] "Ev dizininde, argüman olarak belirttiğiniz porta `localhost` üzerinden bağlanan bir `setuid` programı var. Bağlantıdan bir satır metin okur ve bunu bir önceki seviyenin (bandit20) şifresiyle karşılaştırır. Şifre doğruysa, bir sonraki seviyenin (bandit21) şifresini iletir."

**Hedefin Zihinsel Analizi:**

1. **Anahtar Program:** `suconnect` adında bir `setuid` programımız var. `ls -la` ile baktığımızda, programın sahibinin `bandit21` olduğunu ve bizim (`bandit20`) onu çalıştırma iznimiz olduğunu görürüz. Bu, programın `bandit21` ayrıcalıklarıyla çalıştığı anlamına gelir.
2. **Programın Davranışı:**
    - Bir port numarası argümanı alır (örn: `./suconnect 8080`).
    - Bu porta **bağlanır** (yani bir **istemci** gibi davranır).
    - Bağlantıdan bir satır metin **okur** (mevcut şifreyi bekler).
    - Okuduğu metni `bandit20` şifresiyle karşılaştırır.
    - Doğruysa, `bandit21` şifresini aynı bağlantı üzerinden geri **gönderir**.
3. **Temel Zorluk:** Program bir istemcidir. Bizim ise ona şifreyi gönderecek ve yeni şifreyi alacak bir **sunucu** olmamız gerekiyor. Tek bir terminalde aynı anda hem istemci hem de sunucu olamayız.
4. **İpucu:** Oyun, `nc`, `screen`, `tmux` ve "Unix 'job control'" gibi araçları önererek, birden fazla işlemi aynı anda yönetmemiz gerektiğine işaret eder.

**İlk Plan (Dedektif Planı):**

En net yöntem, iki ayrı terminal penceresi kullanmaktır.

1. **Terminal 1 (Sunucu Rolü):**
    - `bandit20` olarak giriş yap.
    - `nc` (netcat) kullanarak belirli bir portta (örn: 8080) dinleyen bir sunucu başlat. Bu sunucu bir bağlantı bekleyecektir.
2. **Terminal 2 (İstemci Rolü):**
    - `bandit20` olarak giriş yap.
    - `suconnect` programını, Terminal 1'deki sunucunun dinlediği porta bağlanacak şekilde çalıştır.
3. **Etkileşim:**
    - `suconnect` bağlandığında, Terminal 1'e geri dön.
    - `bandit20` şifresini Terminal 1'e yaz ve Enter'a bas.
    - `suconnect` şifreyi doğrulayacak ve yeni şifreyi geri gönderecektir. Yeni şifre Terminal 1'de görünecektir.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Ne Zaman Kullanmayı Düşünmeliyiz?|
|---|---|---|
|**`nc` (netcat)**|**Ağların İsviçre Çakısı**|TCP/UDP bağlantıları okumak ve yazmak için kullanılır. `-l` (listen) bayrağı ile kolayca bir sunucu oluşturabilir. Bu seviyede, `suconnect` programının bağlanacağı sahte sunucuyu oluşturmak için mükemmel bir araçtır.|
|**`screen` / `tmux`**|**Terminal Çoklayıcılar**|Tek bir SSH oturumu içinde birden fazla sanal terminal penceresi oluşturmanıza ve bunlar arasında geçiş yapmanıza olanak tanır. İki ayrı SSH bağlantısı açmak yerine kullanılabilirler.|
|**Job Control (`&`, `bg`, `fg`)**|**Süreç Yönetimi**|Bir komutu arka planda (`&`) çalıştırmanıza, duraklatmanıza (`CTRL-Z`) ve ön plan (`fg`) ile arka plan (`bg`) arasında geçiş yapmanıza olanak tanır. Bu, tek bir terminalde hem sunucuyu hem de istemciyi yönetmek için alternatif bir yöntemdir.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

**Doğru Şifre:** `bandit20` seviyesinin şifresi `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`'dur.

##### **Aşama 1: İki Terminalin Hazırlanması**

İki ayrı terminal penceresi açıp her ikisinde de `bandit20` kullanıcısı olarak giriş yapıyoruz.

Bash

```
# Her iki terminalde de çalıştırılır
ssh bandit20@bandit.labs.overthewire.org -p 2220
# Şifre: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

##### **Aşama 2: Sunucuyu Başlatma (Terminal 1)**

Bu terminalde, 8080 portunda dinleyecek olan `netcat` sunucusunu başlatıyoruz.

Bash

```
# Terminal 1
bandit20@bandit:~$ nc -l -p 8080
```

_Bu terminal artık bir bağlantı bekleyerek "donmuş" gibi görünecektir. Bu normaldir._

##### **Aşama 3: İstemciyi Çalıştırma (Terminal 2)**

Şimdi, `suconnect` programını sunucumuza bağlanması için çalıştırıyoruz.

Bash

```
# Terminal 2
bandit20@bandit:~$ ./suconnect 8080
```

##### **Aşama 4: Etkileşim ve Şifrenin Ele Geçirilmesi**

`suconnect` bağlandığı anda, Terminal 1'deki "donma" sona erer ve imleç yanıp sönmeye başlar.

1. **Terminal 1'e geri dönüyoruz.**
2. `bandit20` şifresini yazıp Enter'a basıyoruz.
    
    Bash
    
    ```
    # Terminal 1
    bandit20@bandit:~$ nc -l -p 8080
    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO 
    ```
    
3. Hemen ardından, `suconnect` programı şifreyi doğrular ve yeni şifreyi geri gönderir. Yeni şifre Terminal 1'de belirir.
    
    Bash
    
    ```
    # Terminal 1 (Sonuç)
    bandit20@bandit:~$ nc -l -p 8080
    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
    Read: 0qXahG8Z...
    Password matches, sending next password
    EeoULMCra2q0dSkYj561DX7s1CpBuOBt
    ```
    

**Nihai Karar:** `suconnect` programını, kendi kontrolümüzdeki bir `netcat` sunucusuna bağlayarak kandırdık. Doğru şifreyi gönderdik ve karşılığında yeni şifreyi aldık.

**`bandit21` seviyesinin şifresi: `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`**

### **دليل Bandit التعليمي: المستوى 20 -> 21 (فن التحكم بالشبكات والعمليات)**

هذا المستوى عبارة عن لغز ذكي يجمع بين أساسيات الشبكات (نموذج العميل/الخادم) والتحكم في عمليات لينكس (job control). إنه يجبرنا على أن نلعب دور المستمع (الخادم) والمتصل (العميل) في نفس الوقت.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

![[Bandit 20 Challenge.png]] "يوجد برنامج `setuid` في المجلد الرئيسي يقوم بإنشاء اتصال بـ `localhost` على المنفذ الذي تحدده كوسيط. ثم يقرأ سطرًا من النص من الاتصال ويقارنه بكلمة مرور المستوى السابق (bandit20). إذا كانت كلمة المرور صحيحة، فسيقوم بإرسال كلمة مرور المستوى التالي (bandit21)."

**التحليل الذهني للهدف:**

1. **البرنامج الرئيسي:** لدينا برنامج `setuid` اسمه `suconnect`. عند فحصه باستخدام `ls -la`، نجد أن مالكه هو `bandit21` وأننا (`bandit20`) نملك صلاحية تشغيله. هذا يعني أن البرنامج سيعمل بامتيازات `bandit21`.
2. **سلوك البرنامج:**
    - يأخذ وسيطًا هو رقم المنفذ (مثال: `./suconnect 8080`).
    - **يتصل** بهذا المنفذ (أي أنه يعمل كـ **عميل/client**).
    - **يقرأ** سطرًا من النص من الاتصال (ينتظر كلمة السر الحالية).
    - يقارن النص الذي قرأه بكلمة سر `bandit20`.
    - إذا كانت صحيحة، **يرسل** كلمة سر `bandit21` عبر نفس الاتصال.
3. **التحدي الأساسي:** البرنامج هو العميل. نحن بحاجة إلى أن نكون **الخادم** الذي سيعطيه كلمة السر ويستقبل منه كلمة السر الجديدة. لا يمكننا أن نكون العميل والخادم في نفس الوقت في طرفية واحدة.
4. **التلميح:** اللعبة تقترح أدوات مثل `nc`, `screen`, `tmux`, و "Unix 'job control'"، مما يشير إلى أننا بحاجة إلى إدارة عمليات متعددة في وقت واحد.

**الخطة المبدئية (خطة المحقق):**

الطريقة الأوضح هي استخدام نافذتي طرفية منفصلتين.

1. **الطرفية 1 (دور الخادم):**
    - سجل الدخول كمستخدم `bandit20`.
    - ابدأ خادمًا يستمع على منفذ معين (مثل `8080`) باستخدام `nc` (netcat). هذا الخادم سينتظر اتصالاً.
2. **الطرفية 2 (دور العميل):**
    - سجل الدخول كمستخدم `bandit20`.
    - قم بتشغيل برنامج `suconnect` ليتصل بالمنفذ الذي يستمع عليه الخادم في الطرفية 1.
3. **التفاعل:**
    - بمجرد اتصال `suconnect`، عد إلى الطرفية 1.
    - اكتب كلمة سر `bandit20` في الطرفية 1 واضغط Enter.
    - سيتحقق `suconnect` من كلمة السر ويرسل كلمة السر الجديدة. ستظهر كلمة السر الجديدة في الطرفية 1.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|متى نفكر في استخدامها؟|
|---|---|---|
|**`nc` (netcat)**|**سكين الجيش السويسري للشبكات**|تُستخدم لقراءة وكتابة اتصالات TCP/UDP. يمكنها بسهولة إنشاء خادم باستخدام الخيار `-l` (listen). في هذا المستوى، هي الأداة المثالية لإنشاء الخادم الوهمي الذي سيتصل به برنامج `suconnect`.|
|**`screen` / `tmux`**|**مضاعفات الطرفية**|تسمح لك بإنشاء نوافذ طرفية افتراضية متعددة داخل جلسة SSH واحدة والتنقل بينها. يمكن استخدامها كبديل لفتح اتصالين SSH منفصلين.|
|**Job Control (`&`, `bg`, `fg`)**|**التحكم في المهام**|تسمح لك بتشغيل أمر في الخلفية (`&`)، إيقافه مؤقتًا (`CTRL-Z`)، والتبديل بين المقدمة (`fg`) والخلفية (`bg`). هي طريقة بديلة لإدارة كل من الخادم والعميل في طرفية واحدة.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

**كلمة السر الصحيحة:** كلمة سر المستوى `bandit20` هي `0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO`.

##### **المرحلة 1: تجهيز الطرفيتين**

نفتح نافذتي طرفية منفصلتين ونسجل الدخول في كلتيهما كمستخدم `bandit20`.

Bash

```
# يُنفذ في كلتا الطرفيتين
ssh bandit20@bandit.labs.overthewire.org -p 2220
# كلمة السر: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

##### **المرحلة 2: بدء الخادم (الطرفية 1)**

في هذه الطرفية، نبدأ خادم `netcat` الذي سيستمع على المنفذ 8080.

Bash

```
# الطرفية 1
bandit20@bandit:~$ nc -l -p 8080
```

_ستبدو هذه الطرفية الآن وكأنها "مجمدة" وهي تنتظر اتصالاً. هذا طبيعي._

##### **المرحلة 3: تشغيل العميل (الطرفية 2)**

الآن، نقوم بتشغيل برنامج `suconnect` ليتصل بخادمنا.

Bash

```
# الطرفية 2
bandit20@bandit:~$ ./suconnect 8080
```

##### **المرحلة 4: التفاعل والحصول على كلمة السر**

بمجرد اتصال `suconnect`، ستنتهي حالة "التجمد" في الطرفية 1 وسيبدأ المؤشر في الوميض.

1. **نعود إلى الطرفية 1.**
2. نكتب كلمة سر `bandit20` ونضغط Enter.
    
    Bash
    
    ```
    # الطرفية 1
    bandit20@bandit:~$ nc -l -p 8080
    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO 
    ```
    
3. فورًا، سيقوم برنامج `suconnect` بالتحقق من كلمة السر وإرسال كلمة السر الجديدة. ستظهر الكلمة الجديدة في الطرفية 1.
    
    Bash
    
    ```
    # الطرفية 1 (النتيجة النهائية)
    bandit20@bandit:~$ nc -l -p 8080
    0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
    Read: 0qXahG8Z...
    Password matches, sending next password
    EeoULMCra2q0dSkYj561DX7s1CpBuOBt
    ```
    

**الحكم النهائي:** لقد قمنا بخداع برنامج `suconnect` ليتصل بخادم `netcat` الذي نتحكم فيه. أرسلنا كلمة السر الصحيحة، وتلقينا كلمة السر الجديدة في المقابل.

**كلمة السر للمستوى `bandit21` هي: `EeoULMCra2q0dSkYj561DX7s1CpBuOBt`**