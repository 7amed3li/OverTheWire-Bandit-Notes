
---

### **Bandit Seviye 25 -> 26 (Kısıtlı Kabuk Tuzağı) Kılavuzu**

Bu seviye, sosyal mühendislik ve psikolojik saldırılar konusunda dahice bir derstir. Bize en karmaşık çözümün her zaman doğru çözüm olmadığını ve bir saldırganın karmaşıklıklara dalmadan önce her zaman en basit yolları düşünmesi gerektiğini öğretir.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "`bandit26`'ya `bandit25`'ten giriş yapmak oldukça kolay olmalı... `bandit26` kullanıcısının kabuğu `/bin/bash` değil, başka bir şey. Ne olduğunu, nasıl çalıştığını ve ondan nasıl kaçılacağını bulun."

**İlk Zihinsel Analiz (Bizi Tuzağa Düşüren):**
1.  **Deliller:** `bandit25` içinde `bandit26`'ya giriş yapmak için bir SSH anahtarımız var.
2.  **Görev:** Hedef, "kısıtlı kabuk" ve "ondan kaçma" konularına büyük ölçüde odaklanıyor.
3.  **Yanlış Sonuç:** Ana görevin interaktif bir oturuma girmek ve ardından bu kısıtlı kabuktan kaçmak için akıllıca bir yol bulmak olduğu sonucuna vardık. Bu, seviyenin bizi düşürmek için tasarladığı bir tuzaktı.

**Doğru Zihinsel Analiz (Tuzağı Fark Ettikten Sonra):**
1.  **Yeniden Değerlendirme:** "Kolay olmalı" ifadesi bir ipucudur. Kısıtlı bir kabuktan kaçmak genellikle "kolay" değildir.
2.  **SSH'in Temelleri:** SSH, sadece interaktif oturumlar açmak için değildir. Aynı zamanda uzak bir sunucuda tek bir komut çalıştırmak için de kullanılabilir.
3.  **Doğru Sonuç:** Eğer SSH'e çalıştıracağı bir komut verirsek, SSH varsayılan interaktif kabuğu (kısıtlı kabuk) **hiç çalıştırmaz**. Bunun yerine komutu çalıştırır, çıktıyı döndürür ve bağlantıyı kapatır. Bu, kısıtlı kabuğu tamamen atlamanın en "kolay" yoludur.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

| Araç | Türkçe Açıklama | Bu Seviyedeki Rolü |
| :--- | :--- | :--- |
| **`ssh`** | **Güvenli Kabuk (Secure Shell)** | **Bu seviyenin anahtarı.** Onu sadece giriş yapmak için değil, aynı zamanda uzak bir komutu doğrudan çalıştırmak için kullanacağız. `ssh user@host "command"` sözdizimi, kısıtlı kabuk tuzağını atlamamızı sağlar. |
| **`cat`** | **Dosya İçeriğini Görüntüleme** | `ssh` aracılığıyla uzaktan çalıştıracağımız komut budur. Amacımız `cat /etc/bandit_pass/bandit26` komutunu çalıştırmaktır. |
| **`vi`, `more`** | **Metin Düzenleyici ve Görüntüleyici** | Bunlar, kısıtlı kabuğun kendisinin bir parçası olan araçlardır. **Bunlar birer tuzaktır.** Seviye, zamanımızı bu araçlardan kaçmaya çalışarak harcamamızı ister, oysa asıl çözüm onları tamamen görmezden gelmektir. |

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: Delili Hazırlama (Yerel Makinede - Kali)**

Bu seviyeyi çözmek için `bandit25`'e giriş yapmamıza bile gerek yok. Tek ihtiyacımız olan, bir önceki seviyeden aldığımız SSH anahtarı.

1.  **Anahtarı Kopyala:** `bandit25`'in ev dizinindeki `bandit26.sshkey` dosyasının içeriğini yerel makinemizdeki (Kali) `bandit26.key` adlı bir dosyaya kopyaladık.
2.  **İzinleri Düzelt:** `ssh`'nin anahtarı kullanmayı reddetmemesi için dosya izinlerini kısıtladık.
    ```bash
    chmod 600 bandit26.key
    ```

##### **Aşama 2: Tuzağı Atlamak ve Hedefi Vurmak**

Şimdi, yerel makinemizden (Kali), kısıtlı kabuğu tamamen atlayan tek ve kesin komutu çalıştırıyoruz.

```bash
┌──(kali㉿kali)-[~]
└─$ ssh -i bandit26.key bandit26@bandit.labs.overthewire.org -p 2220 "cat /etc/bandit_pass/bandit26"

... (Sunucu karşılama mesajı) ...

**`s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`**
```

**Komutun Detaylı Analizi:**
*   `ssh -i bandit26.key bandit26@... -p 2220`: Bu kısım, doğru anahtar, kullanıcı ve port ile sunucuya bağlanır.
*   `"cat /etc/bandit_pass/bandit26"`: Bu, sihrin gerçekleştiği yerdir. SSH'e "interaktif bir oturum başlatma, sadece bu komutu çalıştır, sonucunu bana göster ve sonra ayrıl" diyoruz.

**Nihai Karar:**
SSH, `bandit26` kullanıcısı olarak oturum açtı, ancak kısıtlı kabuğu çalıştırmak yerine, doğrudan `cat` komutunu çalıştırdı. Bu, şifre dosyasının içeriğini terminalimize yazdırdı ve ardından bağlantıyı kapattı. Kısıtlı kabuk tuzağını tamamen atladık.

**`bandit26` seviyesinin şifresi: `**`s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`**`**

---
---

### **دليل Bandit التعليمي: المستوى 25 -> 26 (فخ الصدفة المقيدة)**

هذا المستوى هو درس عبقري في الهندسة الاجتماعية والهجمات النفسية. إنه يعلمنا أن الحل الأكثر تعقيدًا ليس دائمًا هو الحل الصحيح، وأن المهاجم يجب أن يفكر دائمًا في أبسط الطرق أولاً قبل الغوص في التعقيدات.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "تسجيل الدخول إلى `bandit26` من `bandit25` يجب أن يكون سهلاً... الصدفة (shell) للمستخدم `bandit26` ليست `/bin/bash`، بل شيء آخر. اكتشف ما هي، وكيف تعمل، وكيف تهرب منها."

**التحليل الذهني الأولي (الذي أوقعنا في الفخ):**
1.  **الأدلة:** لدينا مفتاح SSH في `bandit25` للدخول إلى `bandit26`.
2.  **المهمة:** الهدف يركز بشكل كبير على "الصدفة المقيدة" و"الهروب منها".
3.  **الاستنتاج الخاطئ:** استنتجنا أن المهمة الأساسية هي الدخول في جلسة تفاعلية ثم إيجاد طريقة ذكية للهروب من هذه الصدفة المقيدة. هذا كان فخًا صممه المستوى لنقع فيه.

**التحليل الذهني الصحيح (بعد إدراك الفخ):**
1.  **إعادة التقييم:** عبارة "يجب أن يكون سهلاً" هي تلميح. الهروب من صدفة مقيدة عادة ليس "سهلاً".
2.  **أساسيات SSH:** بروتوكول SSH ليس فقط لفتح جلسات تفاعلية. يمكن استخدامه أيضًا لتنفيذ أمر واحد على خادم بعيد.
3.  **الاستنتاج الصحيح:** إذا قمنا بتمرير أمر إلى `ssh` ليقوم بتنفيذه، فإن `ssh` **لن يقوم بتشغيل الصدفة التفاعلية** الافتراضية (الصدفة المقيدة) على الإطلاق. بدلاً من ذلك، سينفذ الأمر، ويعيد الناتج، ثم يغلق الاتصال. هذه هي الطريقة "الأسهل" لتجاوز الصدفة المقيدة بالكامل.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

| الأداة | الشرح بالعربية | دورها في هذا المستوى |
| :--- | :--- | :--- |
| **`ssh`** | **الصدفة الآمنة (Secure Shell)** | **مفتاح هذا المستوى.** لن نستخدمه فقط لتسجيل الدخول، بل لتنفيذ أمر عن بعد مباشرة. الصيغة `ssh user@host "command"` تسمح لنا بتجاوز فخ الصدفة المقيدة. |
| **`cat`** | **عرض محتوى الملفات** | هذا هو الأمر الذي سنقوم بتنفيذه عن بعد عبر `ssh`. هدفنا هو تشغيل `cat /etc/bandit_pass/bandit26`. |
| **`vi`, `more`** | **محرر وعارض نصوص** | هذه هي الأدوات التي تشكل جزءًا من الصدفة المقيدة نفسها. **إنها فخ.** المستوى يريدنا أن نضيع وقتنا في محاولة الهروب من هذه الأدوات، بينما الحل الحقيقي هو تجاهلها تمامًا. |

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

##### **المرحلة 1: تحضير الدليل (على الجهاز المحلي - كالي)**

لسنا بحاجة حتى إلى تسجيل الدخول إلى `bandit25` لحل هذا المستوى. كل ما نحتاجه هو مفتاح SSH الذي حصلنا عليه من المستوى السابق.

1.  **نسخ المفتاح:** قمنا بنسخ محتوى ملف `bandit26.sshkey` من المجلد الرئيسي لـ `bandit25` إلى ملف باسم `bandit26.key` على جهازنا المحلي (كالي).
2.  **تصحيح الصلاحيات:** قمنا بتقييد صلاحيات الملف حتى لا يرفض `ssh` استخدامه.
    ```bash
    chmod 600 bandit26.key
    ```

##### **المرحلة 2: تجاوز الفخ وضرب الهدف**

الآن، من جهازنا المحلي (كالي)، نقوم بتنفيذ الأمر الواحد والحاسم الذي يتجاوز الصدفة المقيدة تمامًا.

```bash
┌──(kali㉿kali)-[~]
└─$ ssh -i bandit26.key bandit26@bandit.labs.overthewire.org -p 2220 "cat /etc/bandit_pass/bandit26"

... (رسالة ترحيب الخادم) ...

**`s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`**
```

**التحليل المفصل للأمر:**
*   `ssh -i bandit26.key bandit26@... -p 2220`: هذا الجزء يتصل بالخادم باستخدام المفتاح والمستخدم والمنفذ الصحيح.
*   `"cat /etc/bandit_pass/bandit26"`: هنا يحدث السحر. نحن نقول لـ `ssh`: "لا تبدأ جلسة تفاعلية، فقط نفذ هذا الأمر، أرني نتيجته، ثم ارحل".

**الحكم النهائي:**
قام `ssh` بتسجيل الدخول كمستخدم `bandit26`، ولكن بدلاً من تشغيل الصدفة المقيدة، قام مباشرة بتنفيذ الأمر `cat`. أدى هذا إلى طباعة محتوى ملف كلمة السر في الطرفية الخاصة بنا، ثم تم إغلاق الاتصال. لقد تجاوزنا فخ الصدفة المقيدة بالكامل.

**كلمة السر للمستوى `bandit26` هي: `**`s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ`**`**

---

## 🇺🇸 English

### Objective
Bypass a restricted shell using SSH direct command execution.

### Command
```bash
ssh -i bandit26.key bandit26@bandit.labs.overthewire.org -p 2220 "cat /etc/bandit_pass/bandit26"
```

### Explanation
- The restricted shell exits immediately on login
- By adding a command at the end of SSH, it executes directly without starting the shell
- This bypasses the restricted shell entirely

### Key Takeaways
- **SSH Command Execution:** Same technique as Level 18
- **Restricted Shell Bypass:** When shell is broken, execute commands directly
- **Direct Reading:** Read password file without needing interactive access