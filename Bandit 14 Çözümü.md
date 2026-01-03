### **Bandit Seviye 14 -> 15 (Ağ Servisleriyle Konuşma)**

Bu seviye, dosya manipülasyonundan çıkarak siber güvenliğin temel taşlarından biri olan **ağ servisleri ile etkileşime** odaklanıyor.

#### **Bölüm 1: Bu Seviyedeki Anahtar Kavramlar**

|Kavram / Araç|Türkçe Açıklama|Ne İşe Yarar ve Nasıl Kullanılır?|
|---|---|---|
|**Ağ Portu**|**Network Port**|Bir sunucu üzerinde çalışan farklı programları veya servisleri ayırt etmek için kullanılan sanal bir "kapı numarası"dır. (Örn: 80, 443, 30000).|
|`localhost`|**Yerel Makine**|Çalıştığınız makinenin kendisine atıfta bulunan özel bir ağ adıdır (IP adresi: 127.0.0.1).|
|`nmap`|**Network Mapper**|Bir ağdaki cihazları ve bu cihazlardaki açık portları, çalışan servisleri keşfetmek için kullanılan güçlü bir ağ tarama aracıdır. **Kullanım:** `nmap -p [port] [hedef]`|
|`nc` (netcat)|**Ağların İsviçre Çakısı**|Ağ üzerinden veri okumak ve yazmak için kullanılan son derece basit ve çok yönlü bir araçtır. Ham (raw) TCP/UDP bağlantıları kurmak için mükemmeldir. **Kullanım:** `nc [hedef] [port]`|
|`echo "veri" \| nc ...`|**Veri Gönderme Yöntemi**|`echo` komutu ile bir metin oluşturup, bu metni `|

#### **Bölüm 2: Görevi Anlamak ve Plan Yapmak**

![[23.png]] "Bir sonraki seviyenin şifresi, mevcut seviyenin şifresini **localhost** üzerindeki **30000 portuna** göndererek elde edilebilir."

**Hedefin Zihinsel Analizi:**

1. **Ne Göndereceğiz?** Mevcut seviyenin (`bandit14`) şifresini. Bu şifre, bir önceki seviyede elde ettiğimiz `wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw`'dir.
2. **Nereye Göndereceğiz?** `localhost` (yani aynı sunucu) üzerindeki `30000` numaralı porta.
3. **Görev Ne?** Bir ağ bağlantısı kurup, bu bağlantı üzerinden şifreyi göndermek ve cevabı beklemek.

**İlk Plan:**

1. `bandit14` kullanıcısı olarak giriş yap.
2. **Teşhis:** `nmap` kullanarak `localhost`'un 30000 portunun gerçekten açık olup olmadığını kontrol et.
3. **Uygulama:** `nc` (netcat) aracını kullanarak `localhost:30000`'e bağlan ve mevcut şifreyi gönder.
4. Gelen cevabı oku.

#### **Bölüm 3: Çözüm Yolculuğu (Gerçek Terminal Kaydı)**

İşte bu planın gerçek terminalde nasıl uygulandığı, yapılan küçük bir hata ve ardından gelen doğru çözüm.

**Adım 1: `bandit14` Olarak Giriş Yapma** Önceki seviyeden aldığımız şifre ile giriş yaparız.

Bash

```
┌──(root㉿kali)-[~]
└─# ssh bandit14@bandit.labs.overthewire.org -p 2220
...
bandit14@bandit.labs.overthewire.org's password: (şifre buraya yapıştırılır)
...
bandit14@bandit:~$
```

**Adım 2: Teşhis (Profesyonel Bir Dokunuş)** Körü körüne bağlanmak yerine, önce hedefin gerçekten var olup olmadığını kontrol ederiz.

Bash

```
bandit14@bandit:~$ nmap -p 30000 localhost
Starting Nmap 7.94SVN ( https://nmap.org  ) at 2025-10-29 13:22 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000074s latency).

PORT      STATE SERVICE
30000/tcp open  ndmps

Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```

**Analiz:** `nmap` raporu, 30000 portunun **`open`** (açık) olduğunu onaylıyor. Planımıza devam edebiliriz.

**Adım 3: İlk Deneme ve Hata Analizi** Burada, bir önceki seviyenin (`bandit13`) şifresinin yanlışlıkla kullanıldığı bir senaryo görüyoruz. Bu, çok yaygın bir hatadır ve nedenini anlamak önemlidir.

Bash

```
bandit14@bandit:~$ echo "wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw" | nc localhost 30000
Wrong! Please enter the correct current password.
```

**DERS:** Sunucu "Yanlış! Lütfen **mevcut doğru şifreyi** girin" diye cevap verdi. Bu, `nc` komutunun çalıştığını, bağlantının başarılı olduğunu, ancak gönderdiğimiz **verinin (şifrenin) yanlış olduğunu** gösterir. Görev, "mevcut seviyenin (`bandit14`) şifresini" istiyor, bir öncekinin değil.

**Adım 4: Doğru Veri ile Çözüm** Şimdi, `bandit14`'ün doğru şifresini (`MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`) gönderelim.

Bash

```
bandit14@bandit:~$ echo "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS" | nc localhost 30000
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

**BAŞARDIK!** Sunucu, şifrenin doğru olduğunu onayladı ve bir sonraki seviyenin (`bandit15`) şifresini bize verdi: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`.

### **دليل Bandit التعليمي: المستوى 14 -> 15 (التحدث مع خدمات الشبكة)**

هذا المستوى يخرجنا من عالم التعامل مع الملفات ليركز على أحد أساسيات الأمن السيبراني: **التفاعل مع خدمات الشبكة**.

#### **الجزء الأول: المفاهيم الأساسية في هذا المستوى**

|المفهوم / الأداة|الشرح بالعربية|ما فائدته وكيف يُستخدم؟|
|---|---|---|
|**منفذ الشبكة**|**Network Port**|هو "رقم باب" افتراضي على الخادم يُستخدم للتمييز بين البرامج أو الخدمات المختلفة التي تعمل عليه. (مثال: 80، 443، 30000).|
|`localhost`|**الجهاز المحلي**|هو اسم شبكة خاص يشير إلى الجهاز الذي تعمل عليه حاليًا (عنوان IP: 127.0.0.1).|
|`nmap`|**مخطط الشبكة**|أداة مسح شبكات قوية تُستخدم لاكتشاف الأجهزة على الشبكة، والمنافذ المفتوحة عليها، والخدمات التي تعمل على تلك المنافذ. **الاستخدام:** `nmap -p [port] [target]`|
|`nc` (netcat)|**سكين الجيش السويسري للشبكات**|أداة بسيطة ومتعددة الاستخدامات بشكل لا يصدق لقراءة وكتابة البيانات عبر الشبكة. ممتازة لإنشاء اتصالات TCP/UDP خام. **الاستخدام:** `nc [target] [port]`|
|`echo "data" \| nc ...`|**طريقة إرسال البيانات**|هي طريقة لإرسال البيانات إلى خدمة ما، عن طريق إنشاء نص باستخدام `echo`، ثم توجيه هذا النص مباشرةً كمدخل لأمر `nc` باستخدام الماسورة (`|

#### **الجزء الثاني: فهم المهمة ووضع الخطة**

 "يمكن الحصول على كلمة السر للمستوى التالي عن طريق إرسال كلمة المرور للمستوى الحالي إلى **المنفذ 30000** على **localhost**."
![[23.png]]
**التحليل الذهني للهدف:**

1. **ماذا سنرسل؟** كلمة المرور للمستوى الحالي (`bandit14`). هذه هي كلمة المرور التي حصلنا عليها من المستوى السابق: `wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw`.
2. **إلى أين سنرسل؟** إلى المنفذ رقم `30000` على `localhost` (أي نفس الخادم).
3. **ما هي المهمة؟** إنشاء اتصال شبكي، إرسال كلمة المرور عبر هذا الاتصال، وانتظار الرد.

**الخطة المبدئية:**

1. سجل الدخول كمستخدم `bandit14`.
2. **التشخيص:** استخدم `nmap` للتحقق من أن المنفذ 30000 على `localhost` مفتوح بالفعل.
3. **التنفيذ:** استخدم `nc` (netcat) للاتصال بالمنفذ 30000 وإرسال كلمة المرور الحالية.
4. اقرأ الرد القادم من الخادم.

#### **الجزء الثالث: رحلة الحل (سجل الطرفية الحقيقي)**

إليك كيف تم تطبيق هذه الخطة عمليًا، بما في ذلك الخطأ البسيط الذي تم ارتكابه والحل الصحيح الذي تبعه.

**الخطوة 1: تسجيل الدخول كمستخدم `bandit14`** ندخل باستخدام كلمة المرور التي حصلنا عليها من المستوى السابق.

Bash

```
┌──(root㉿kali)-[~]
└─# ssh bandit14@bandit.labs.overthewire.org -p 2220
...
bandit14@bandit.labs.overthewire.org's password: (نلصق كلمة المرور هنا)
...
bandit14@bandit:~$
```

**الخطوة 2: التشخيص (لمسة احترافية)** بدلاً من الاتصال بشكل أعمى، نتحقق أولاً من أن هدفنا موجود ومتاح.

Bash

```
bandit14@bandit:~$ nmap -p 30000 localhost
Starting Nmap 7.94SVN ( https://nmap.org  ) at 2025-10-29 13:22 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000074s latency).

PORT      STATE SERVICE
30000/tcp open  ndmps

Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```

**التحليل:** تقرير `nmap` يؤكد أن المنفذ 30000 **`open`** (مفتوح). يمكننا المتابعة في خطتنا.

**الخطوة 3: المحاولة الأولى وتحليل الخطأ** هنا، نرى سيناريو شائع جدًا حيث تم استخدام كلمة مرور المستوى السابق (`bandit13`) عن طريق الخطأ.

Bash

```
bandit14@bandit:~$ echo "wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw" | nc localhost 30000
Wrong! Please enter the correct current password.
```

**الدرس:** رد الخادم كان "خطأ! الرجاء إدخال **كلمة المرور الحالية الصحيحة**". هذا يوضح أن أمر `nc` يعمل، والاتصال ناجح، لكن **البيانات (كلمة المرور) التي أرسلناها خاطئة**. المهمة تطلب كلمة مرور "المستوى الحالي (`bandit14`)"، وليس المستوى السابق.

**الخطوة 4: الحل باستخدام البيانات الصحيحة** الآن، لنرسل كلمة المرور الصحيحة لـ `bandit14` (`MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS`).

Bash

```
bandit14@bandit:~$ echo "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS" | nc localhost 30000
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

**لقد نجحنا!** أكد الخادم أن كلمة المرور صحيحة وأعطانا كلمة مرور المستوى التالي (`bandit15`): `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`.

---

## 🇺🇸 English

### Objective
Submit the current password to a service listening on port 30000 to get the next password.

### Command
```bash
echo "MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS" | nc localhost 30000
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```

### Explanation
- `nc localhost 30000`: Connect to port 30000 on localhost
- `echo "..." |`: Pipe the password to the service

### Key Takeaways
- **Netcat (`nc`):** Swiss army knife for network connections
- **localhost:** Refers to the current machine (127.0.0.1)
- **Port:** Virtual door for network services