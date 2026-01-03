
---

### **Bandit Seviye 24 -> 25 (Kaba Kuvvet Sanatı ve Otomasyonun Gücü)**

Bu seviye, sızma testinin en klasik ve temel saldırı türlerinden birini öğretir: **Kaba Kuvvet Saldırısı (Brute-force Attack)**. Bize, manuel çabaların yetersiz kaldığı durumları ve bu tür problemleri çözmek için otomasyonun neden zorunlu olduğunu gösterir.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[Bandit 24 Challenge.png]] "30002 portunda dinleyen bir servis var. Bu servis, `bandit24` şifresini ve 4 haneli gizli bir PIN kodunu verirseniz size `bandit25` şifresini verecektir. PIN kodunu elde etmenin tek yolu, kaba kuvvet saldırısı olarak bilinen 10.000 kombinasyonun tamamını denemektir."

**Önemli Not:** "Her seferinde yeni bağlantı kurmanıza gerek yok."

**Hedefin Zihinsel Analizi:**

1.  **Protokol:** `localhost:30002` portunda çalışan bir servisle etkileşime geçeceğiz.
2.  **Girdi Formatı:** Servis, belirli bir formatta girdi bekliyor: `[bandit24_şifresi] [4_haneli_PIN]`. Örneğin: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 1234`.
3.  **Temel Zorluk:** PIN kodu bilinmiyor. `0000`'dan `9999`'a kadar tüm olasılıkları denemek zorundayız.
4.  **Kritik İpucu:** "Her seferinde yeni bağlantı kurmanıza gerek yok" notu, tek bir ağ bağlantısı açıp tüm 10.000 denemeyi bu bağlantı üzerinden gönderebileceğimiz anlamına gelir. Bu, saldırıyı çok daha hızlı ve verimli hale getirir.
5.  **Sonuç:** Görevimiz, 10.000 denemeyi otomatik olarak oluşturacak ve bunları tek bir bağlantı üzerinden hedefe gönderecek bir mekanizma tasarlamaktır.

#### **Bölüm 2: Çözüm Yöntemleri (Geleneksel ve Otomatik Yaklaşım)**

Bu problemi çözmek için iki temel yaklaşım düşünülebilir.

##### **Yaklaşım 1: Geleneksel Yöntem (Manuel Deneme)**

Bu, problemi bir insanın "elle" çözmeye çalışmasıdır.

**Mantık:**
Bir terminalde `netcat` ile sunucuya bağlanır ve 10.000 kombinasyonun tamamını klavyeyle tek tek yazarız.

**Adımlar:**
1.  Sunucuya bağlan: `nc localhost 30002`
2.  İlk denemeyi yaz: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0000` ve Enter'a bas.
3.  İkinci denemeyi yaz: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0001` ve Enter'a bas.
4.  ...bu işlemi 9999'a kadar tekrarla.

**Neden Bu Yöntem Uygulanamaz?**
*   **Zaman:** Her denemenin 3 saniye sürdüğünü varsayarsak, 10.000 deneme `30.000` saniye, yani **8 saatten fazla** kesintisiz çalışma gerektirir.
*   **İnsan Hatası:** Bu süreç son derece sıkıcıdır ve hataya çok açıktır. Hangi sayıda kaldığınızı unutmak veya bir rakamı yanlış yazmak çok kolaydır.

**Sonuç:** Manuel yöntem teorik olarak mümkün olsa da, pratik olarak **imkansızdır**. Bu seviye, bize otomasyonun bir lüks değil, bir **zorunluluk** olduğunu öğretmek için tasarlanmıştır.

##### **Yaklaşım 2: Otomatik Yöntem (Akıllı ve Verimli Çözüm)**

Bu, bilgisayarın gücünü kullanarak tekrarlayan işi bizim yerimize yapmasını sağlamaktır. Bu görev için en basit ve en etkili otomasyon aracı, `bash` kabuğunun kendi döngü yeteneğidir.

**Mantık:**
Bir `for` döngüsü kullanarak `0000`'dan `9999`'a kadar tüm sayıları üretiriz. Her sayı için, şifre ile birleştirip bir satır oluştururuz. Sonra tüm bu 10.000 satırı tek bir seferde `netcat` bağlantısına yönlendiririz.

**Plan:**
1.  `for` döngüsü ile sayıları üret.
2.  `echo` ile her sayıyı şifreyle birleştir.
3.  Tüm çıktıları (`|` pipe kullanarak) `nc localhost 30002` komutuna gönder.
4.  Sunucudan gelen binlerce "Wrong!" yanıtını filtrelemek ve sadece doğru yanıtı görmek için `grep -v "Wrong"` kullan.

#### **Bölüm 3: Sızma Yolculuğu (Gerçek Terminal Kaydı)**

İşte otomasyonun gücünü kullanarak saldırının nasıl gerçekleştirildiği.

**Doğru Şifre:** `bandit24` seviyesinin şifresi `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`'dir.

##### **Tek Satırlık Saldırı Kodu**

Aşağıdaki komut, tüm planı tek bir satırda uygular.

```bash
# {0000..9999} aralığındaki her 'i' sayısı için...
# "şifre [sayı]" formatında bir satır yazdır...
# ve tüm bu satırları netcat bağlantısına gönder...
# ve "Wrong" içermeyen yanıtları göster.
for i in {0000..9999}; do echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"; done | nc localhost 30002 | grep -v "Wrong"
```

##### **Sonuç**

Bu komut çalıştırıldığında, terminal bir süre meşgul kalır. Binlerce deneme gönderilir ve sunucudan gelen "Wrong!" mesajları `grep` tarafından filtrelenir. Sonunda, sadece doğru denemeye verilen yanıt ekranda belirir:

```
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

**Nihai Karar:** Manuel olarak çözülmesi imkansız olan bir problemi, basit bir `for` döngüsü ve birkaç temel Linux aracı kullanarak saniyeler içinde çözdük. Bu, otomasyonun ve programlama mantığının siber güvenlikteki temel önemini göstermektedir.

**`bandit25` seviyesinin şifresi: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`**

---
---

### **دليل Bandit التعليمي: المستوى 24 -> 25 (فن القوة الغاشمة وقوة الأتمتة)**

هذا المستوى يعلمنا أحد أكثر أنواع الهجمات كلاسيكية وأهمية في عالم اختبار الاختراق: **هجوم القوة الغاشمة (Brute-force Attack)**. إنه يوضح لنا الحالات التي تفشل فيها الجهود اليدوية ولماذا تعتبر الأتمتة ضرورية لحل مثل هذه المشاكل.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

![[Bandit 24 Challenge.png]] "يوجد خادم يستمع على المنفذ 30002. سيعطيك هذا الخادم كلمة سر `bandit25` إذا أعطيته كلمة سر `bandit24` ورمز PIN سري مكون من 4 أرقام. لا توجد طريقة للحصول على رمز PIN إلا بتجربة كل الـ 10000 احتمال، وهو ما يسمى بالقوة الغاشمة."

**ملاحظة هامة:** "لست بحاجة إلى إنشاء اتصالات جديدة في كل مرة."

**التحليل الذهني للهدف:**

1.  **البروتوكول:** سنتفاعل مع خادم على المنفذ `localhost:30002`.
2.  **تنسيق الإدخال:** يتوقع الخادم إدخالاً بتنسيق معين: `[password_of_bandit24] [4-digit_PIN]`. على سبيل المثال: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 1234`.
3.  **التحدي الأساسي:** رمز PIN غير معروف. يجب علينا تجربة جميع الاحتمالات من `0000` إلى `9999`.
4.  **التلميح الحاسم:** ملاحظة "لست بحاجة إلى إنشاء اتصالات جديدة في كل مرة" تعني أنه يمكننا فتح اتصال شبكة واحد وإرسال كل الـ 10,000 محاولة عبر هذا الاتصال المفتوح. هذا يجعل الهجوم أسرع وأكثر كفاءة.
5.  **الاستنتاج:** مهمتنا هي تصميم آلية تقوم بإنشاء 10,000 محاولة تلقائيًا وإرسالها إلى الهدف عبر اتصال واحد.

#### **الجزء الثاني: طرق الحل (النهج التقليدي والآلي)**

يمكن التفكير في نهجين أساسيين لحل هذه المشكلة.

##### **النهج الأول: الطريقة التقليدية (المحاولة اليدوية)**

هذا هو ما يحدث عندما يحاول الإنسان حل المشكلة "يدويًا".

**المنطق:**
نتصل بالخادم باستخدام `netcat` في طرفية، ثم نكتب كل الـ 10,000 احتمال باستخدام لوحة المفاتيح واحدًا تلو الآخر.

**الخطوات:**
1.  اتصل بالخادم: `nc localhost 30002`
2.  اكتب المحاولة الأولى: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0000` واضغط Enter.
3.  اكتب المحاولة الثانية: `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 0001` واضغط Enter.
4.  ... كرر هذه العملية حتى تصل إلى 9999.

**لماذا هذه الطريقة غير قابلة للتطبيق؟**
*   **الوقت:** بافتراض أن كل محاولة تستغرق 3 ثوانٍ، فإن 10,000 محاولة تتطلب `30,000` ثانية، أي **أكثر من 8 ساعات** من العمل المتواصل.
*   **الخطأ البشري:** هذه العملية مملة للغاية وعرضة للأخطاء. من السهل جدًا أن تنسى أين وصلت أو أن تخطئ في كتابة رقم.

**الخلاصة:** الطريقة اليدوية، على الرغم من أنها ممكنة نظريًا، إلا أنها **مستحيلة** عمليًا. تم تصميم هذا المستوى ليعلمنا أن الأتمتة ليست رفاهية، بل هي **ضرورة حتمية**.

##### **النهج الثاني: الطريقة الآلية (الحل الذكي والفعال)**

هذا هو تسخير قوة الكمبيوتر للقيام بالعمل المتكرر نيابة عنا. أبسط وأقوى أداة أتمتة لهذه المهمة هي قدرة `bash` نفسها على إنشاء الحلقات التكرارية.

**المنطق:**
نستخدم حلقة `for` لتوليد جميع الأرقام من `0000` إلى `9999`. لكل رقم، نقوم بدمجه مع كلمة السر لإنشاء سطر. ثم نوجه كل هذه الـ 10,000 سطر دفعة واحدة إلى اتصال `netcat`.

**الخطة:**
1.  أنشئ الأرقام باستخدام حلقة `for`.
2.  استخدم `echo` لدمج كل رقم مع كلمة السر.
3.  وجه كل المخرجات (باستخدام `|` pipe) إلى الأمر `nc localhost 30002`.
4.  استخدم `grep -v "Wrong"` لتصفية آلاف الردود الخاطئة من الخادم وإظهار الرد الصحيح فقط.

#### **الجزء الثالث: رحلة الاختراق (سجل الطرفية الحقيقي)**

إليك كيف تم تنفيذ الهجوم باستخدام قوة الأتمتة.

**كلمة السر الصحيحة:** كلمة سر المستوى `bandit24` هي `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`.

##### **كود الهجوم في سطر واحد**

الأمر التالي ينفذ الخطة بأكملها في سطر واحد.

```bash
# لكل رقم 'i' في النطاق {0000..9999}...
# اطبع سطرًا بالتنسيق "password [number]"...
# وأرسل كل هذه الأسطر إلى اتصال netcat...
# واعرض فقط الردود التي لا تحتوي على كلمة "Wrong".
for i in {0000..9999}; do echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"; done | nc localhost 30002 | grep -v "Wrong"
```

##### **النتيجة**

عند تشغيل هذا الأمر، تظل الطرفية مشغولة لبعض الوقت. يتم إرسال آلاف المحاولات، ويتم تصفية رسائل "Wrong!" الواردة من الخادم بواسطة `grep`. في النهاية، يظهر الرد على المحاولة الصحيحة فقط على الشاشة:

```
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```

**الحكم النهائي:** لقد قمنا بحل مشكلة كان من المستحil حلها يدويًا في غضون ثوانٍ باستخدام حلقة `for` بسيطة وبعض أدوات لينكس الأساسية. هذا يوضح الأهمية الجوهرية للأتمتة والمنطق البرمجي في مجال الأمن السيبراني.

**كلمة السر للمستوى `bandit25` هي: `iCi86ttT4KSNe1armKiwbQNmB3YJP3q4`**

---

## 🇺🇸 English

### Objective
Brute-force a 4-digit PIN to get the next password.

### Command
```bash
for i in {0000..9999}; do echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"; done | nc localhost 30002 | grep -v "Wrong"
```

### Explanation
- `for i in {0000..9999}`: Loop through all 10000 PINs
- `echo "password $i"`: Generate each attempt
- `nc localhost 30002`: Send to service
- `grep -v "Wrong"`: Show only correct response

### Key Takeaways
- **Brute Force:** Automate repetitive attempts with loops
- **Bash Scripting:** Essential for penetration testing
- **Filtering:** Use `grep -v` to exclude unwanted output