### **Bandit Seviye 16 -> 17 (Bilinmeyene Doğru Bir Soruşturma Yolculuğu)**

Bu seviye sadece bir bulmaca değil, siber güvenlik uzmanlarının karşılaştığı gerçek bir senaryonun simülasyonudur: "Samanlıkta iğne aramak". Bilinmeyen bir hedefimiz var, bilinmeyen bir yerde ve etrafı bir sürü aldatıcı hedefle çevrili. Bu rehber, tüm hataları ve başarılarıyla soruşturma yolculuğunun tamamını belgeleyecektir.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[25.png]] "Bir sonraki seviyenin kimlik bilgileri, mevcut seviyenin şifresini **31000 ila 32000 aralığındaki** bir **porta** **localhost** üzerinde göndererek elde edilebilir. Önce, bu portlardan hangisinde bir sunucunun dinlediğini bulun. Ardından, hangilerinin SSL/TLS konuştuğunu ve hangilerinin konuşmadığını bulun. Yalnızca 1 sunucu bir sonraki kimlik bilgilerini verecektir, diğerleri ise onlara ne gönderirseniz onu size geri gönderecektir."

**Hedefin Zihinsel Analizi:**

1. **Görev:** Mevcut şifreyi göndermek (`kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`).
2. **Yer:** `localhost` (aynı sunucu).
3. **Adres:** **Bilinmiyor!** 1001 porttan (31000'den 32000'e kadar) biri.
4. **Gereken Dil:** **SSL/TLS**.
5. **Tuzak:** "Aldatıcılar" var. Bazı sunucular gönderdiğinizin aynısını veya yanıltıcı yanıtlar verecek. Sadece biri "hazine".

**İlk Plan (Dedektif Planı):**

1. **Geniş Keşif (Tarama):** Açık portları belirlemek için tüm aralığı (31000-32000) taramalıyız. Bu bizim "şüpheliler" listemiz olacak.
2. **Sınıflandırma (Ayrıştırma):** SSL/TLS konuşup konuşmadığını belirlemek için her açık portu test etmeliyiz.
3. **Sorgulama (Test):** SSL/TLS konuşan her port için şifreyi göndermeli ve "hazine" mi yoksa "aldatıcı" mı olduğunu belirlemek için yanıtını analiz etmeliyiz.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

Bunlar, oyunun önerdiği araçlardır. Hepsini kullanmasak bile, her birinin ne işe yaradığını bilmek önemlidir.

|Araç|Türkçe Açıklama|Ne Zaman Kullanmayı Düşünmeliyiz?|
|---|---|---|
|**`nmap`**|**Ağ Haritalayıcı (Network Mapper)**|**Temel tarama aracı.** Dedektifin karanlığı aydınlatan "el feneri"dir. Belirli bir aralıktaki hangi portların (kapıların) açık olduğunu öğrenmek için kullanırız. Soruşturmaya onsuz başlanamaz.|
|**`openssl s_client`**|**SSL/TLS İstemcisi**|**Temel sorgulama aracı.** Bizim "yalan makinemiz"dir. Bir porta bağlanıp şifreli SSL/TLS dilini konuşup konuşmadığını doğrulamak için kullanırız. Bağlantı başarılı olursa, veri (şifre) göndermek için kullanabiliriz.|
|**`nc` (netcat)**|**Ağların İsviçre Çakısı**|Herhangi bir porta bağlanmak ve şifrelenmemiş veri göndermek için basit bir araçtır. Bu seviyede, **SSL/TLS konuşmayan** portları hızlıca test etmek ve tepkilerini görmek için kullanılabilir.|
|**`telnet`**|**Telnet**|`nc`'ye çok benzer. Metin tabanlı servislere bağlanmak için eski bir araçtır. Bu seviyede `nc` ile aynı amaçla kullanılabilir.|
|**`ss` / `netstat`**|**Soket İstatistikleri (Socket Statistics)**|**Aynı makine** üzerindeki mevcut ağ bağlantılarını görüntülemek için kullanılan komutlardır. Hangi programların hangi portlarda dinlediğini görmek için kullanışlıdır, ancak `nmap` dışarıdan aktif tarama için daha iyidir.|
|**`ncat` / `socat`**|**`netcat`'in Gelişmiş Versiyonları**|SSL bağlantıları oluşturma yeteneği de dahil olmak üzere birçok ek özelliğe sahip çok güçlü ağ araçlarıdır, ancak `openssl s_client` bu görev için daha özel ve net bir araçtır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: İlk Keşif ve Şüphelilerin Belirlenmesi**

Önce `bandit16`'ya giriş yapar ve belirtilen aralığı taramak için `nmap` kullanırız.

Bash

```
bandit16@bandit:~$ nmap -p 31000-32000 localhost
...
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown
```

**Analiz:** 5 şüpheli portumuz var. Soruşturma başlıyor.

##### **Aşama 2: Şüphelilerin Tek Tek Sorgulanması**

**1. Şüpheli #1'in Sorgulanması (Port `31046`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31046
...
4087F0F7FF7F0000:error:0A0000F4:SSL routines:unexpected message...
...
```

**Karar:** **Masum**. `unexpected message` hatası, SSL/TLS konuşmadığı anlamına gelir.

**2. Şüpheli #2'nin Sorgulanması (Port `31518`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31518
...
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx  <-- (Şifreyi gönderdik)
KEYUPDATE
closed
```

**Karar:** **Aldatıcı**. SSL/TLS konuşuyor ama doğru cevap olmayan `KEYUPDATE` yanıtını veriyor.

**3. Şüpheli #3'ün Sorgulanması (Port `31691`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31691
...
4087F0F7FF7F0000:error:0A0000F4:SSL routines:unexpected message...
...
```

**Karar:** **Masum**. İlk şüpheliyle aynı sonuç, SSL/TLS konuşmuyor.

**4. Şüpheli #4'ün Sorgulanması (Port `31790`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31790
...
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx  <-- (Şifreyi gönderdik)
KEYUPDATE
Wrong! Please enter the correct current password.
closed
```

**Karar:** **Profesyonel Aldatıcı**. SSL/TLS konuşuyor ama şifrenin yanlış olduğunu söyleyen çift ve yanıltıcı bir yanıt veriyor.

**5. Şüpheli #5'in Sorgulanması (Port `31960`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31960
...
4087F0F7FF7F0000:error:0A0000F4:SSL routines:unexpected message...
...
```

**Karar:** **Masum**. SSL/TLS konuşmuyor.

##### **Aşama 3: Şüphe Anı ve Yeniden Soruşturma**

Bir çıkmaza ulaştık. Tüm şüpheliler ya masum ya da aldatıcı. Bu, ilk soruşturma yöntemimizde bir hata olduğu anlamına geliyor. **Ders:** İlk sonuçlarınıza her zaman güvenmeyin. Bazen, hedefle etkileşim kurma şekliniz, onun tepkisini değiştirir.

Ana şüphelileri (`31518` ve `31790`) otomatik bir yöntemle (`echo | openssl`) yeniden sorgulamaya karar verdik.

**`31518`'in Otomatik Yöntemle Yeniden Sorgulanması:**

Bash

```
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31518 -ign_eof
...
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

**Nihai Karar:** **Aldatıcı Olduğu Onaylandı**. Gönderdiğimiz şifrenin aynısını geri verdi. Sadece bir "yankı".

**`31790`'ın Otomatik Yöntemle Yeniden Sorgulanması (Hakikat Anı):**

Bash

```
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31790 -ign_eof
...
read R BLOCK
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
closed

```

**Karar:** **Yakalandı!** Otomatik yöntem gerçeği ortaya çıkardı. "Hazine" bu.

##### **Aşama 4: Hazinenin Kullanımı ve Son Engelle Karşılaşma**

Bir SSH özel anahtarı elde ettik. İlk plan, onu doğrudan `bandit16`'dan `bandit17@localhost`'a bağlanmak için kullanmaktı.

Bash

```
bandit16@bandit:~$ ssh -i /tmp/bandit17.key -p 2220 bandit17@localhost
...
!!! Connecting from localhost is blocked to conserve resources.
...
```

**Engel:** "`localhost`'tan bağlanmak engellendi" tuzağıyla karşılaştık. **Seviyenin Gerçek Dersi:** Bu tür anahtarlar aynı sunucunun içinden değil, harici bir makineden (kendi makineniz) kullanılmalıdır.

##### **Aşama 5: Dışarıdan Son Saldırı**

1. **`bandit16` üzerinde:** `cat /tmp/bandit17.key` kullanarak anahtarın içeriğini görüntüledik ve kopyaladık.
2. **Kali makinesinde:** Yeni bir dosya `nano bandit17.key` oluşturduk ve anahtarı içine yapıştırdık.
3. **Kali'de:** `chmod 600 bandit17.key` komutuyla dosyayı güvence altına aldık.
4. **Kali'de:** Son ve doğru bağlantıyı gerçekleştirdik.
    
    Bash
    
    ```
    ┌──(root㉿kali)-[~]
    └─# ssh -i bandit17.key -p 2220 bandit17@bandit.labs.overthewire.org
    ...
    bandit17@bandit:~$ 
    ```
    

**Sonuç:** **Hedef başarıyla ele geçirildi!**

----
---
---
### **دليل Bandit التعليمي: المستوى 16 -> 17 (رحلة التحقيق في المجهول)**

هذا المستوى ليس مجرد لغز، بل هو محاكاة حقيقية لسيناريو يواجهه خبراء الأمن السيبراني: البحث عن "إبرة في كومة قش". لدينا هدف غير معروف، في مكان غير معروف، وسط الكثير من الأهداف الخادعة. هذا الدليل سيوثق رحلة التحقيق كاملة، بكل أخطائها ونجاحاتها.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**
![[25.png]]
 "يمكن الحصول على بيانات اعتماد المستوى التالي عن طريق إرسال كلمة المرور للمستوى الحالي إلى **منفذ** على **localhost** في النطاق **31000 إلى 32000**. أولاً، اكتشف أي من هذه المنافذ لديه خادم يستمع إليه. ثم اكتشف أي منها يتحدث بلغة SSL/TLS وأيها لا. يوجد خادم واحد فقط سيعطي بيانات الاعتماد التالية، والآخرون سيعيدون إليك ببساطة كل ما ترسله إليهم."

**التحليل الذهني للهدف:**

1. **المهمة:** إرسال كلمة المرور الحالية (`kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`).
2. **المكان:** `localhost` (نفس السيرفر).
3. **العنوان:** **مجهول!** هو واحد من 1001 منفذ (من 31000 إلى 32000).
4. **اللغة المطلوبة:** **SSL/TLS**.
5. **الفخ:** هناك "مخادعون". بعض الخوادم سترد عليك بنفس ما أرسلته، أو بردود مضللة. واحد فقط هو "الكنز".

**الخطة المبدئية (خطة المحقق):**

1. **الاستطلاع الواسع (المسح):** يجب أن نمسح النطاق الكامل (31000-32000) لتحديد المنافذ المفتوحة. هذه هي قائمة "المشتبه فيهم".
2. **التصنيف (الفرز):** يجب أن نختبر كل منفذ مفتوح لتحديد ما إذا كان يتحدث بلغة SSL/TLS أم لا.
3. **الاستجواب (الاختبار):** لكل منفذ يتحدث SSL/TLS، يجب أن نرسل كلمة المرور ونحلل رده لتحديد ما إذا كان هو "الكنز" أم "مخادع".

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

هذه هي الأدوات التي اقترحتها اللعبة. حتى لو لم نستخدمها كلها، من المهم أن نعرف وظيفة كل منها.

|الأداة|الشرح بالعربية|متى نفكر في استخدامها؟|
|---|---|---|
|**`nmap`**|**مخطط الشبكة (Network Mapper)**|**الأداة الأساسية للمسح.** هي "كشاف" المحقق الذي ينير الظلام. نستخدمها لمعرفة أي المنافذ (الأبواب) مفتوحة في نطاق معين. لا يمكن بدء التحقيق بدونها.|
|**`openssl s_client`**|**عميل SSL/TLS**|**الأداة الأساسية للاستجواب.** هي "جهاز كشف الكذب" الخاص بنا. نستخدمها للاتصال بمنفذ والتأكد من أنه يتحدث بلغة SSL/TLS المشفرة. إذا نجح الاتصال، يمكننا استخدامها لإرسال البيانات (كلمة السر).|
|**`nc` (netcat)**|**سكين الجيش السويسري للشبكات**|أداة بسيطة للاتصال بأي منفذ وإرسال بيانات غير مشفرة. في هذا المستوى، يمكن استخدامها لاختبار المنافذ التي **لا تتحدث SSL/TLS** بسرعة ورؤية رد فعلها.|
|**`telnet`**|**Telnet**|يشبه `nc` إلى حد كبير. أداة قديمة للاتصال بالخدمات النصية. يمكن استخدامها لنفس غرض `nc` في هذا المستوى.|
|**`ss` / `netstat`**|**إحصائيات المقابس (Socket Statistics)**|أوامر تُستخدم لعرض الاتصالات الشبكية الحالية على الجهاز **نفسه**. هي مفيدة لمعرفة البرامج التي تستمع على منافذ معينة، لكن `nmap` أفضل للمسح النشط من الخارج.|
|**`ncat` / `socat`**|**إصدارات مطورة من `netcat`**|أدوات شبكات قوية جدًا مع ميزات إضافية كثيرة، بما في ذلك القدرة على إنشاء اتصالات SSL، لكن `openssl s_client` هو الأداة المتخصصة والأوضح لهذه المهمة.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

##### **المرحلة 1: الاستطلاع الأولي وتحديد المشتبه فيهم**

أولاً، نسجل الدخول إلى `bandit16` ونستخدم `nmap` لمسح النطاق المحدد.

Bash

```
bandit16@bandit:~$ nmap -p 31000-32000 localhost
Starting Nmap 7.94SVN ( https://nmap.org  ) at 2025-10-30 07:48 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00012s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown
```

**التحليل:** لدينا 5 مشتبه فيهم. التحقيق يبدأ الآن.

##### **المرحلة 2: استجواب المشتبه فيهم واحداً تلو الآخر**

**1. استجواب المشتبه به #1 (بورت `31046`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31046
CONNECTED(00000003)
4087F0F7FF7F0000:error:0A0000F4:SSL routines:unexpected message...
---
no peer certificate available
---
```

**الحكم:** **بريء**. رسالة `unexpected message` تعني أنه لا يتحدث SSL/TLS.

**2. استجواب المشتبه به #2 (بورت `31518`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31518
...
-----BEGIN CERTIFICATE-----
...
---
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx  <-- (أرسلنا كلمة السر)
KEYUPDATE
closed
```

**الحكم:** **مخادع**. يتحدث SSL/TLS ولكنه يرد بـ `KEYUPDATE`، وهي ليست الإجابة الصحيحة.

**3. استجواب المشتبه به #3 (بورت `31691`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31691
...
4087F0F7FF7F0000:error:0A0000F4:SSL routines:unexpected message...
...
```

**الحكم:** **بريء**. نفس نتيجة المشتبه به الأول، لا يتحدث SSL/TLS.

**4. استجواب المشتبه به #4 (بورت `31790`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31790
...
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx  <-- (أرسلنا كلمة السر)
KEYUPDATE
Wrong! Please enter the correct current password.
closed
```

**الحكم:** **مخادع محترف**. يتحدث SSL/TLS ولكنه يعطي ردًا مزدوجًا ومضللاً ليخبرنا أن كلمة السر خاطئة.

**5. استجواب المشتبه به #5 (بورت `31960`):**

Bash

```
bandit16@bandit:~$ openssl s_client -connect localhost:31960
...
4087F0F7FF7F0000:error:0A0000F4:SSL routines:unexpected message...
...
```

**الحكم:** **بريء**. لا يتحدث SSL/TLS.

##### **المرحلة 3: لحظة الشك وإعادة التحقيق**

وصلنا إلى طريق مسدود. كل المشتبه فيهم إما أبرياء أو مخادعون. هذا يعني أن هناك خطأ ما في طريقة تحقيقنا الأولية. **الدرس:** لا تثق دائمًا في نتائجك الأولى. أحيانًا، الطريقة التي تتفاعل بها مع الهدف تغير من رده.

قررنا إعادة استجواب المتهمين الرئيسيين (`31518` و `31790`) ولكن باستخدام طريقة آلية (`echo | openssl`) لضمان إرسال البيانات بشكل نظيف.

**إعادة استجواب `31518` بالطريقة الآلية:**

Bash

```
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31518 -ign_eof
...
read R BLOCK
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

**الحكم النهائي:** **مخادع مؤكد**. أعاد لنا نفس كلمة السر التي أرسلناها. إنه مجرد "صدى صوت".

**إعادة استجواب `31790` بالطريقة الآلية (لحظة الحقيقة):**

Bash

```
bandit16@bandit:~$ echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -connect localhost:31790 -ign_eof
...
read R BLOCK
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
closed
```

**الحكم:** **تم القبض عليه!** الطريقة الآلية كشفت الحقيقة. هذا هو "الكنز".

##### **المرحلة 4: استخدام الكنز ومواجهة العقبة الأخيرة**

حصلنا على مفتاح SSH خاص. الخطة الأولية كانت استخدامه مباشرة من `bandit16` للاتصال بـ `bandit17@localhost`.

Bash

```
bandit16@bandit:~$ ssh -i /tmp/bandit17.key -p 2220 bandit17@localhost
...
!!! Connecting from localhost is blocked to conserve resources.
...
Disconnected from 127.0.0.1 port 2220
```

**العقبة:** واجهنا فخ "الاتصال من `localhost` ممنوع". **الدرس الحقيقي للمستوى:** هذه الأنواع من المفاتيح يجب استخدامها من جهاز خارجي (جهازك أنت)، وليس من داخل نفس السيرفر.

##### **المرحلة 5: الهجوم النهائي من الخارج**

1. **على `bandit16`:** عرضنا محتوى المفتاح باستخدام `cat /tmp/bandit17.key` وقمنا بنسخه.
2. **على جهاز كالي (Kali):** أنشأنا ملفًا جديدًا `nano bandit17.key` وقمنا بلصق المفتاح فيه.
3. **على كالي:** قمنا بتأمين الملف بالأمر `chmod 600 bandit17.key`.
4. **على كالي:** قمنا بالاتصال النهائي الصحيح.
    
    Bash
    
    ```
    ┌──(root㉿kali)-[~]
    └─# ssh -i bandit17.key -p 2220 bandit17@bandit.labs.overthewire.org
    ...
    Welcome to OverTheWire!
    ...
    bandit17@bandit:~$ 
    ```
    

**النتيجة:** **تم اختراق الهدف بنجاح!**