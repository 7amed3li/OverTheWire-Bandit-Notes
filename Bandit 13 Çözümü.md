
---
### **Bandit Seviye 13 -> 14 (Gerçek Bir Çözüm Yolculuğu)**

Bu rehber, sadece doğru komutları göstermekle kalmayacak, aynı zamanda bir aceminin geçtiği tüm adımları, yaptığı hataları ve bu hatalardan öğrendiği dersleri adım adım anlatacaktır. Bu, teorik bir çözüm değil, **gerçek bir terminal kaydıdır**.

#### **Bölüm 1: Bu Seviyedeki Anahtar Kavramlar**

Bu seviye, parola tabanlı kimlik doğrulamasından, siber güvenlikte temel bir konsept olan **anahtar tabanlı kimlik doğrulamasına (Key-Based Authentication)** geçiş yapmamızı sağlıyor.

|Kavram / Araç|Türkçe Açıklama|Ne İşe Yarar ve Nasıl Kullanılır?|
|---|---|---|
|**SSH Anahtar Çifti**|**SSH Key Pair**|Parola yerine kullanılan iki dosyadan oluşan bir sistemdir: **Özel Anahtar (Private Key)** ve **Genel Anahtar (Public Key)**.|
|**Özel Anahtar**|**Private Key**|**Gizli kalması gereken** anahtardır. Sizin kimliğinizi kanıtlar. Bu seviyede bize verilen `sshkey.private` dosyasıdır.|
|**Genel Anahtar**|**Public Key**|Sunucuya yerleştirilen ve özel anahtarınızla eşleşen anahtardır. Kimliğinizi doğrulamak için kullanılır.|
|`ssh -i [anahtar]`|**Kimlik Dosyası Belirt**|`ssh` komutunun `-i` (identity file) seçeneği, kimlik doğrulama için parola yerine belirli bir özel anahtar dosyası kullanmasını sağlar. **Kullanım:** `ssh -i ozel_anahtarim bandit14@...`|
|`chmod 600 [anahtar]`|**İzinleri Kısıtla**|Özel anahtar dosyaları çok hassastır. `ssh` istemcisi, izinleri çok açık olan (herkesin okuyabildiği) bir anahtarı kullanmayı reddeder. `chmod 600`, dosyayı **sadece sahibinin okuyup yazabilmesini** sağlar. Bu, zorunlu bir güvenlik adımıdır.|
|`localhost`|**Yerel Makine**|Çalıştığınız makinenin kendisine atıfta bulunan özel bir ağ adıdır (IP adresi: 127.0.0.1).|

#### **Bölüm 2: Görevi Anlamak ve İlk Plan**

![[22.png]] "Bir sonraki seviyenin şifresi `/etc/bandit_pass/bandit14` dosyasında saklıdır ve **sadece bandit14 kullanıcısı tarafından okunabilir**. Bu seviye için bir sonraki şifreyi almayacaksınız, bunun yerine bir sonraki seviyeye giriş yapmak için kullanılabilecek bir **özel SSH anahtarı (private SSH key)** alacaksınız."

**Hedefin Zihinsel Analizi:**

1. **Problem:** Şifre dosyası `bandit13` olarak okunamaz. Çözüm için `bandit14` kullanıcısı olmalıyız.
2. **Araç:** Bize parola yerine bir **özel SSH anahtarı** verildi.
3. **İlk Fikir (Mantıklı Görünen Yanlış Yol):** Hedefteki `localhost` ipucundan yola çıkarak, `bandit13` sunucusu içindeyken `ssh bandit14@localhost` komutunu denemek.

#### **Bölüm 3: Çözüm Yolculuğu (Hatalar ve Öğrenilen Dersler)**

Bu, teorinin pratiğe döküldüğü ve hatalardan ders çıkardığımız yolculuktur.

##### **Adım 1: Anahtarı Bulma ve İlk Deneme (Bandit13 Sunucusunda)**

Önce `bandit13` hesabındaki anahtarı buluruz.

Bash

```
bandit13@bandit:~$ ls -la
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
...
-rw-r-----   1 bandit14 bandit13 1679 Oct 14 09:26 sshkey.private
```

Anahtarımız `sshkey.private`. Şimdi ilk fikrimizi deneyelim: `localhost` üzerinden `bandit14`'e bağlanmak.

Bash

```
bandit13@bandit:~$ ssh -i sshkey.private bandit14@localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
!!! You are trying to log into this SSH server on port 22, which is not intended.
...
bandit14@localhost: Permission denied (publickey).
```

**DERS 1: HATA MESAJINI OKU!** Mesaj açık: "Port 22'yi kullanmaya çalışıyorsunuz, bu amaçlanmamıştır." OverTheWire oyunları her zaman **2220** portunu kullanır.

##### **Adım 2: Portu Düzelterek Tekrar Deneme (Bandit13 Sunucusunda)**

Şimdi doğru portu (`-p 2220`) ekleyerek tekrar deneyelim.

Bash

```
bandit13@bandit:~$ ssh -i sshkey.private -p 2220 bandit14@localhost
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
!!! Connecting from localhost is blocked to conserve resources.
!!! Please log out and log in again.
...
Received disconnect from 127.0.0.1 port 2220:2: no authentication methods enabled
```

**DERS 2: EN ÖNEMLİ HATA MESAJI!** Mesaj bu sefer daha da net: **"localhost üzerinden bağlanmak engellendi."** Bu, oyun tasarımcılarının bu yolu kasıtlı olarak kapattığı anlamına gelir. **Çözüm, sunucu içinden değil, dışından gelmelidir.**

##### **Adım 3: Anahtarı Yerel Makineye Taşıma (Strateji Değişikliği)**

Artık doğru planı biliyoruz: Anahtarı kendi makinemize (Kali) alıp oradan bağlanmalıyız.

**3.1. Anahtarın İçeriğini Kopyala (Bandit13 Sunucusunda)**

Bash

```
bandit13@bandit:~$ cat sshkey.private
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----

```

Bu adımdaki tek görevimiz, bu metnin tamamını fare ile seçip **kopyalamaktır**.

**3.2. Anahtarı Yerel Makineye Kaydet (Kali Makinesinde)** Şimdi **kendi Kali makinemizdeki** yeni bir terminalde çalışıyoruz.

**ÖNEMLİ:** `cat > dosya_adı` komutu, terminalin "girdi moduna" geçmesini sağlar. Bu moddayken, yapıştırdığınız metin doğrudan dosyaya yazılır. Kaydetmek için `Ctrl+D` kullanılır. `Ctrl+C` ise işlemi **iptal eder** ve boş bir dosya oluşturur.

Bash

```
# Kendi Kali makinemizdeyiz.
┌──(root㉿kali)-[~]
└─# cat > bandit14.key
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
... (kopyalanan anahtarın tamamı buraya yapıştırılır) ...
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
... (kopyalanan anahtarın tamamı buraya yapıştırılır) ...
-----END RSA PRIVATE KEY-----
```

_(Metni yapıştırdıktan sonra, dosyayı kaydetmek ve komut satırına geri dönmek için `Ctrl+D` tuşlarına basılır.)_

**3.3. Dosyanın Dolu Olduğunu Kontrol Et (Kali Makinesinde)** Emin olmak için dosyanın boyutunu kontrol edelim.

Bash

```
┌──(root㉿kali)-[~]
└─# ls -l bandit14.key
-rw-rw-r-- 1 root root 1679 Oct 29 09:06 bandit14.key
```

Dosya boyutu **1679 byte**, yani **0 değil**. Bu, anahtarın doğru bir şekilde kaydedildiğini gösterir.

##### **Adım 4: İzinleri Düzeltme ve Son Bağlantı (Kali Makinesinde)**

`ssh`, özel anahtar dosyalarının sadece sahibi tarafından okunabilir olmasını ister. Bu bir güvenlik standardıdır.

Bash

```
# 1. İzinleri kısıtlayalım.
┌──(root㉿kali)-[~]
└─# chmod 600 bandit14.key

# 2. Şimdi anahtarı kullanarak doğrudan bandit14'e bağlanalım.
┌──(root㉿kali)-[~]
└─# ssh -i bandit14.key -p 2220 bandit14@bandit.labs.overthewire.org
```

Bu komut, `ssh`'e "kimlik doğrulaması için `bandit14.key` dosyasını kullan ve `bandit.labs.overthewire.org` sunucusunun 2220 portundaki `bandit14` kullanıcısına bağlan" der.

Plain Text

```
... (Hoşgeldin mesajı) ...
Enjoy your stay!

bandit14@bandit:~$
```

**BAŞARDIK!** Artık `bandit14` kullanıcısıyız.

##### **Adım 5: Şifreyi Okuma (Bandit14 Sunucusunda)**

Son adım, hedefimiz olan şifre dosyasını okumaktır.

Bash

```
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```

#### **Nihai Sonuç (Öğrenilen Ders)**

Bu uzun ve hatalarla dolu yolculuk, bize paha biçilmez dersler öğretti:

1. **Hata Mesajları Dostunuzdur:** `Permission denied`, `port 22`, `localhost is blocked` gibi mesajlar, size neyin yanlış gittiğini ve stratejinizi nasıl değiştirmeniz gerektiğini söyleyen yol işaretleridir.
2. **Doğru Ortamda Çalış:** `bandit13` sunucusu, anahtarı bulma yeridir. `Kali` makinesi ise bu anahtarı kullanma yeridir. Ortamları karıştırmak, `command not found` gibi hatalara yol açar.
3. **Güvenlik Standartları Önemlidir:** `chmod 600` sadece bir komut değil, güvenli bir sistemin temel gereksinimidir.
4. **`Ctrl+C` vs. `Ctrl+D`:** `Ctrl+C` bir işlemi **öldürür**. `Ctrl+D` ise bir girdi akışını **sonlandırır** (bu durumda dosyayı kaydeder). Bu küçük fark, bu seviyedeki başarının anahtarıydı.
---
----
---
### **  
دليل Bandit: المستوى 13 -> 14 (رحلة حل حقيقية)**

هذا الدليل لا يكتفي بعرض الأوامر الصحيحة فقط، بل يروي القصة الكاملة لرحلة الحل، بما في ذلك كل الخطوات التي يمر بها المبتدئ، والأخطاء التي يقع فيها، والدروس التي يتعلمها من هذه الأخطاء. هذا ليس مجرد حل نظري، بل هو **توثيق لسجل طرفية حقيقي**.

#### **الجزء الأول: المفاهيم الأساسية في هذا المستوى**

ينقلنا هذا المستوى من المصادقة المستندة إلى كلمة المرور إلى مفهوم أساسي في الأمن السيبراني: **المصادقة المستندة إلى المفاتيح (Key-Based Authentication)**.

|المفهوم / الأداة|الشرح بالعربية|ما فائدته وكيف يُستخدم؟|
|---|---|---|
|**زوج مفاتيح SSH**|**SSH Key Pair**|نظام يتكون من ملفين يُستخدمان بدلاً من كلمة المرور: **مفتاح خاص (Private Key)** و **مفتاح عام (Public Key)**.|
|**المفتاح الخاص**|**Private Key**|هو المفتاح الذي **يجب أن يظل سريًا**. يثبت هويتك. في هذا المستوى، هو ملف `sshkey.private` الذي أُعطي لنا.|
|**المفتاح العام**|**Public Key**|هو المفتاح الذي يوضع على الخادم ويتوافق مع مفتاحك الخاص. يُستخدم للتحقق من هويتك.|
|`ssh -i [key]`|**تحديد ملف الهوية**|الخيار `-i` (identity file) في أمر `ssh` يجعله يستخدم ملف مفتاح خاص معين للمصادقة بدلاً من كلمة المرور. **الاستخدام:** `ssh -i my_private_key bandit14@...`|
|`chmod 600 [key]`|**تقييد الأذونات**|ملفات المفاتيح الخاصة حساسة للغاية. يرفض عميل `ssh` استخدام مفتاح إذا كانت أذوناته مفتوحة جدًا (يمكن للجميع قراءتها). `chmod 600` يجعل الملف **قابلًا للقراءة والكتابة من قبل المالك فقط**. هذه خطوة أمان إلزامية.|
|`localhost`|**الجهاز المحلي**|هو اسم شبكة خاص يشير إلى الجهاز الذي تعمل عليه حاليًا (عنوان IP: 127.0.0.1).|

#### **الجزء الثاني: فهم المهمة والخطة الأولية**

![[22.png]] "كلمة السر للمستوى التالي مخزنة في ملف `/etc/bandit_pass/bandit14` و**يمكن قراءتها فقط من قبل المستخدم bandit14**. لهذا المستوى، لن تحصل على كلمة السر التالية، ولكن بدلاً من ذلك ستحصل على **مفتاح SSH خاص (private SSH key)** يمكن استخدامه لتسجيل الدخول إلى المستوى التالي."

**التحليل الذهني للهدف:**

1. **المشكلة:** ملف كلمة السر لا يمكن قراءته كمستخدم `bandit13`. يجب أن نصبح المستخدم `bandit14` للوصول إليه.
2. **الأداة:** أُعطي لنا **مفتاح SSH خاص** بدلاً من كلمة مرور.
3. **الفكرة الأولى (الطريق الخاطئ الذي يبدو منطقيًا):** انطلاقًا من تلميح `localhost` في الهدف، فإن أول ما يتبادر إلى الذهن هو محاولة الاتصال بـ `bandit14` من داخل خادم `bandit13` باستخدام الأمر `ssh bandit14@localhost`.

#### **الجزء الثالث: رحلة الحل (الأخطاء والدروس المستفادة)**

هذه هي الرحلة التي نترجم فيها النظرية إلى واقع ونتعلم من أخطائنا.

##### **الخطوة 1: العثور على المفتاح والمحاولة الأولى (على خادم Bandit13)**

أولاً، نجد المفتاح في حساب `bandit13`.

Bash

```
bandit13@bandit:~$ ls -la
total 24
drwxr-xr-x   2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
...
-rw-r-----   1 bandit14 bandit13 1679 Oct 14 09:26 sshkey.private
```

مفتاحنا هو `sshkey.private`. الآن لنجرب فكرتنا الأولى: الاتصال بـ `bandit14` عبر `localhost`.

Bash

```
bandit13@bandit:~$ ssh -i sshkey.private bandit14@localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
!!! You are trying to log into this SSH server on port 22, which is not intended.
...
bandit14@localhost: Permission denied (publickey).
```

**الدرس الأول: اقرأ رسالة الخطأ!** الرسالة واضحة: "أنت تحاول استخدام المنفذ 22، وهذا غير مقصود." ألعاب OverTheWire تستخدم دائمًا المنفذ **2220**.

##### **الخطوة 2: المحاولة مرة أخرى مع تصحيح المنفذ (على خادم Bandit13)**

الآن لنجرب مرة أخرى مع إضافة المنفذ الصحيح (`-p 2220`).

Bash

```
bandit13@bandit:~$ ssh -i sshkey.private -p 2220 bandit14@localhost
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
!!! Connecting from localhost is blocked to conserve resources.
!!! Please log out and log in again.
...
Received disconnect from 127.0.0.1 port 2220:2: no authentication methods enabled
```

**الدرس الثاني: أهم رسالة خطأ!** الرسالة هذه المرة أكثر وضوحًا: **"الاتصال من `localhost` محظور."** هذا يعني أن مصممي اللعبة أغلقوا هذا الطريق عمدًا. **الحل يجب أن يأتي من خارج الخادم، وليس من داخله.**

##### **الخطوة 3: نقل المفتاح إلى الجهاز المحلي (تغيير الاستراتيجية)**

الآن نحن نعرف الخطة الصحيحة: يجب أن نأخذ المفتاح إلى جهازنا (كالي) ونتصل من هناك.

**3.1. نسخ محتوى المفتاح (على خادم Bandit13)**

Bash

```
bandit13@bandit:~$ cat sshkey.private
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
... (بقية المفتاح) ...
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
... (بقية المفتاح) ...
-----END RSA PRIVATE KEY-----
```

مهمتنا الوحيدة في هذه الخطوة هي تحديد كل هذا النص بالماوس و**نسخه (Copy)**.

**3.2. حفظ المفتاح على الجهاز المحلي (على جهاز كالي)** الآن، نعمل في **نافذة طرفية جديدة على جهاز كالي الخاص بنا**.

**ملاحظة هامة:** الأمر `cat > file_name` يجعل الطرفية تدخل في "وضع الإدخال". في هذا الوضع، يتم كتابة النص الذي تلصقه مباشرة في الملف. للحفظ، نستخدم `Ctrl+D`. أما `Ctrl+C`، فيقوم **بإلغاء** العملية وإنشاء ملف فارغ.

Bash

```
# نحن الآن على جهاز كالي.
┌──(root㉿kali)-[~]
└─# cat > bandit14.key

# (نقوم بلصق (Paste) نص المفتاح المنسوخ هنا)
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAxkkOE83W2cOT7IWhFc9aPaaQmQDdgzuXCv+ppZHa++buSkN+
gg0tcr7Fw8NLGa5+Uzec2rEg0WmeevB13AIoYp0MZyETq46t+jk9puNwZwIt9XgB
ZufGtZEwWbFWw/vVLNwOXBe4UWStGRWzgPpEeSv5Tb1VjLZIBdGphTIK22Amz6Zb
ThMsiMnyJafEwJ/T8PQO3myS91vUHEuoOMAzoUID4kN0MEZ3+XahyK0HJVq68KsV
ObefXG1vvA3GAJ29kxJaqvRfgYnqZryWN7w3CHjNU4c/2Jkp+n8L0SnxaNA+WYA7
jiPyTF0is8uzMlYQ4l1Lzh/8/MpvhCQF8r22dwIDAQABAoIBAQC6dWBjhyEOzjeA
J3j/RWmap9M5zfJ/wb2bfidNpwbB8rsJ4sZIDZQ7XuIh4LfygoAQSS+bBw3RXvzE
pvJt3SmU8hIDuLsCjL1VnBY5pY7Bju8g8aR/3FyjyNAqx/TLfzlLYfOu7i9Jet67
xAh0tONG/u8FB5I3LAI2Vp6OviwvdWeC4nOxCthldpuPKNLA8rmMMVRTKQ+7T2VS
nXmwYckKUcUgzoVSpiNZaS0zUDypdpy2+tRH3MQa5kqN1YKjvF8RC47woOYCktsD
o3FFpGNFec9Taa3Msy+DfQQhHKZFKIL3bJDONtmrVvtYK40/yeU4aZ/HA2DQzwhe
ol1AfiEhAoGBAOnVjosBkm7sblK+n4IEwPxs8sOmhPnTDUy5WGrpSCrXOmsVIBUf
laL3ZGLx3xCIwtCnEucB9DvN2HZkupc/h6hTKUYLqXuyLD8njTrbRhLgbC9QrKrS
M1F2fSTxVqPtZDlDMwjNR04xHA/fKh8bXXyTMqOHNJTHHNhbh3McdURjAoGBANkU
1hqfnw7+aXncJ9bjysr1ZWbqOE5Nd8AFgfwaKuGTTVX2NsUQnCMWdOp+wFak40JH
PKWkJNdBG+ex0H9JNQsTK3X5PBMAS8AfX0GrKeuwKWA6erytVTqjOfLYcdp5+z9s
8DtVCxDuVsM+i4X8UqIGOlvGbtKEVokHPFXP1q/dAoGAcHg5YX7WEehCgCYTzpO+
xysX8ScM2qS6xuZ3MqUWAxUWkh7NGZvhe0sGy9iOdANzwKw7mUUFViaCMR/t54W1
GC83sOs3D7n5Mj8x3NdO8xFit7dT9a245TvaoYQ7KgmqpSg/ScKCw4c3eiLava+J
3btnJeSIU+8ZXq9XjPRpKwUCgYA7z6LiOQKxNeXH3qHXcnHok855maUj5fJNpPbY
iDkyZ8ySF8GlcFsky8Yw6fWCqfG3zDrohJ5l9JmEsBh7SadkwsZhvecQcS9t4vby
9/8X4jS0P8ibfcKS4nBP+dT81kkkg5Z5MohXBORA7VWx+ACohcDEkprsQ+w32xeD
qT1EvQKBgQDKm8ws2ByvSUVs9GjTilCajFqLJ0eVYzRPaY6f++Gv/UVfAPV4c+S0
kAWpXbv5tbkkzbS0eaLPTKgLzavXtQoTtKwrjpolHKIHUz6Wu+n4abfAIRFubOdN
/+aLoRQ0yBDRbdXMsZN/jvY44eM+xRLdRVyMmdPtP8belRi2E2aEzA==
-----END RSA PRIVATE KEY-----

```

_(بعد لصق النص، نضغط على `Ctrl+D` لحفظ الملف والعودة إلى سطر الأوامر.)_

**3.3. التحقق من أن الملف ليس فارغًا (على جهاز كالي)** للتأكد، دعنا نتحقق من حجم الملف.

Bash

```
┌──(root㉿kali)-[~]
└─# ls -l bandit14.key
-rw-rw-r-- 1 root root 1679 Oct 29 09:06 bandit14.key
```

حجم الملف **1679 بايت**، وليس **0**. هذا يؤكد أن المفتاح تم حفظه بشكل صحيح.

##### **الخطوة 4: إصلاح الأذونات والاتصال النهائي (على جهاز كالي)**

يتطلب `ssh` أن تكون ملفات المفاتيح الخاصة قابلة للقراءة من قبل المالك فقط. هذا معيار أمان.

Bash

```
# 1. لنقيد الأذونات.
┌──(root㉿kali)-[~]
└─# chmod 600 bandit14.key

# 2. الآن لنتصل مباشرة بـ bandit14 باستخدام المفتاح.
┌──(root㉿kali)-[~]
└─# ssh -i bandit14.key -p 2220 bandit14@bandit.labs.overthewire.org
```

هذا الأمر يخبر `ssh`: "استخدم ملف `bandit14.key` للمصادقة، واتصل بالمستخدم `bandit14` على خادم `bandit.labs.overthewire.org` عبر المنفذ 2220".

Plain Text

```
... (رسالة الترحيب) ...
Enjoy your stay!

bandit14@bandit:~$
```

**لقد نجحنا!** نحن الآن المستخدم `bandit14`.

##### **الخطوة 5: قراءة كلمة السر (على خادم Bandit14)**

الخطوة الأخيرة هي قراءة ملف كلمة السر الذي أصبح لدينا الآن صلاحية الوصول إليه.

Bash

```
bandit14@bandit:~$ cat /etc/bandit_pass/bandit14
wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
```

#### **الخلاصة النهائية (الدرس المستفاد)**

هذه الرحلة الطويلة والمليئة بالأخطاء علمتنا دروسًا لا تقدر بثمن:

1. **رسائل الخطأ هي صديقك:** رسائل مثل `Permission denied`، `port 22`، و `localhost is blocked` هي علامات إرشادية تخبرك بما هو الخطأ وكيف يجب أن تغير استراتيجيتك.
2. **اعمل في البيئة الصحيحة:** خادم `bandit13` هو مكان العثور على الأداة. جهاز `Kali` هو مكان استخدام هذه الأداة. الخلط بين البيئات يؤدي إلى أخطاء مثل `command not found`.
3. **معايير الأمان مهمة:** `chmod 600` ليس مجرد أمر، بل هو متطلب أساسي في الأنظمة الآمنة.
4. **`Ctrl+C` مقابل `Ctrl+D`:** `Ctrl+C` **يقتل** العملية. `Ctrl+D` **ينهي** تدفق الإدخال (في حالتنا، يحفظ الملف). هذا الفارق البسيط كان مفتاح النجاح في هذا المستوى.