
---
---

### **Bandit Seviye 15 -> 16 (Şifreli Ağ İletişimi)**

Bu seviye, bir önceki seviyede öğrendiğimiz ağ temellerinin üzerine önemli bir katman daha ekliyor: **Şifreleme (Encryption)**. Bu, internette verilerimizin nasıl güvende tutulduğunu anlamak için temel bir adımdır.

#### **Bölüm 1: Bu Seviyedeki Anahtar Kavramlar**

| Kavram / Araç | Türkçe Açıklama | Ne İşe Yarar ve Nasıl Kullanılır? |
| :--- | :--- | :--- |
| **SSL/TLS** | **Güvenli Soket Katmanı / Taşıma Katmanı Güvenliği** | İnternet üzerinden gönderilen verileri şifreleyerek güvenli hale getiren bir protokoldür. Web sitelerindeki "https" ve kilit simgesi, bu teknolojinin kullanıldığını gösterir. |
| `openssl` | **OpenSSL Araç Seti** | Kriptografi (şifreleme bilimi) ile ilgili hemen hemen her şeyi yapabilen çok güçlü bir komut satırı aracıdır. |
| `openssl s_client` | **SSL/TLS İstemcisi** | `openssl` araç setinin bir parçasıdır ve şifreli (SSL/TLS) sunuculara bağlanmak için kullanılır. `nc`'nin şifreli versiyonu gibi düşünebilirsiniz. **Kullanım:** `openssl s_client -connect [hedef]:[port]` |
| **Self-Signed Certificate** | **Kendinden İmzalı Sertifika** | Normalde, bir web sitesinin kimliği "güvenilir bir otorite" tarafından doğrulanır. Kendinden imzalı sertifika ise, sunucunun kendi kimliğini kendisinin doğruladığı bir sertifikadır. Güvenli değildir ama test ortamlarında yaygındır. `openssl`'in verdiği "verify error:num=18" hatası bu yüzdendir ve bu seviyede **önemsizdir**. |

---

#### **Bölüm 2: Görevi Anlamak ve Plan Yapmak**

![[24.png]]
"Bir sonraki seviyenin şifresi, mevcut seviyenin şifresini **localhost** üzerindeki **30001 portuna** **SSL/TLS şifrelemesi kullanarak** göndererek elde edilebilir."

**Hedefin Zihinsel Analizi:**
1.  **Ne Göndereceğiz?** Mevcut seviyenin (`bandit15`) şifresi: `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`.
2.  **Nereye Göndereceğiz?** `localhost` (aynı sunucu) üzerindeki `30001` numaralı porta.
3.  **En Önemli Fark Ne?** Bu sefer bağlantı **şifreli** olmalı. Bu, `nc` gibi basit araçların işe yaramayacağı anlamına gelir. `openssl s_client` aracını kullanmamız gerekecek.

**Plan:**
1.  `bandit15` kullanıcısı olarak giriş yap.
2.  **Teşhis:** `nmap` ile `localhost:30001` portunun açık olduğunu doğrula.
3.  **Uygulama:** `openssl s_client` ile şifreli bir bağlantı kur.
4.  Bağlantı kurulduktan sonra, mevcut şifreyi gönder.
5.  Gelen cevabı oku.

---

#### **Bölüm 3: Çözüm Yolculuğu (Gerçek Terminal Kaydı)**

İşte bu planın gerçek terminalde nasıl uygulandığı ve her adımın ne anlama geldiği.

**Adım 1: `bandit15` Olarak Giriş Yapma**
Önceki seviyeden aldığımız şifre ile `bandit15` hesabına bağlanırız.
```bash
┌──(root㉿kali)-[~]
└─# ssh bandit15@bandit.labs.overthewire.org -p 2220
...
bandit15@bandit.labs.overthewire.org's password: (şifre buraya yapıştırılır)
...
bandit15@bandit:~$
```

**Adım 2: Teşhis (Hedefin Varlığını Doğrulama)**
Bir yere saldırmadan önce, oranın gerçekten var olup olmadığını kontrol etmek akıllıca bir iştir.
```bash
bandit15@bandit:~$ nmap -p 30001 localhost
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-29 13:29 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000082s latency).

PORT      STATE SERVICE
30001/tcp open  pago-services1

Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```
**Analiz:** `nmap`, 30001 portunun **`open`** (açık) olduğunu söylüyor. Yani planımızdaki hedefimiz geçerli.

**Adım 3: Çözümü Uygulama (Farklı Yöntemler)**

##### **Yöntem A: İnteraktif Çözüm (Adım Adım)**

Bu yöntemde, önce bağlantıyı kurar, sonra manuel olarak şifreyi gireriz. Bu, süreci anlamak için harikadır.

```bash
bandit15@bandit:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)
... (Burada bir sürü sertifika bilgisi ve teknik detaylar görünür) ...
Verification error: self-signed certificate
... (Çıktının sonuna doğru geliriz) ...
---
read R BLOCK
```
**Açıklama:** Terminal şu anda `read R BLOCK` satırında duruyor ve bizden bir girdi bekliyor. Bu, şifreli tünelin kurulduğu ve sunucunun "Evet, seni dinliyorum, göndereceğin veriyi bekliyorum" dediği anlamına gelir.

Şimdi tek yapmamız gereken şifreyi yazıp `Enter`'a basmak.
```
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

closed
```
**BAŞARDIK!** Şifreyi gönderdik, sunucu "Correct!" (Doğru!) dedi ve bize bir sonraki seviyenin (`bandit16`) şifresini verdi: `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`.

##### **Yöntem B: Otomatik Çözüm (`echo` ve `|` ile)**

Bu yöntem, bir önceki seviyede öğrendiğimiz `echo` ve `|` (pipe) tekniğini kullanır. Tek fark, `nc` yerine `openssl s_client` kullanmamızdır. Bu, tek satırda çözüme ulaşmanın en hızlı yoludur.

```bash
bandit15@bandit:~$ echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | openssl s_client -connect localhost:30001 -quiet
```
**Bu Komutun Analizi:**
*   `echo "..."`: Şifreyi oluşturur.
*   `|`: Bu şifreyi alır ve bir sonraki komuta "girdi" olarak verir.
*   `openssl s_client -connect localhost:30001`: Şifreli bağlantıyı kurar.
*   **`-quiet`**: Bu yeni ve çok kullanışlı bir seçenek. `openssl`'in normalde gösterdiği uzun sertifika bilgilerini gizler ve sadece sunucudan gelen asıl cevabı gösterir. Bu, çıktıyı çok daha temiz hale getirir.

**Bu komutun çıktısı çok daha temiz olacaktır:**
```bash
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

---
---

### **دليل Bandit التعليمي: المستوى 15 -> 16 (الاتصالات الشبكية المشفرة)**

هذا المستوى يبني على ما تعلمناه في المستوى السابق، ويضيف طبقة حيوية جديدة فوق أساسيات الشبكات: **التشفير (Encryption)**. هذه خطوة أساسية لفهم كيفية الحفاظ على أمان بياناتنا على الإنترنت.

#### **الجزء الأول: المفاهيم الأساسية في هذا المستوى**

| المفهوم / الأداة | الشرح بالعربية | ما فائدته وكيف يُستخدم؟ |
| :--- | :--- | :--- |
| **SSL/TLS** | **طبقة المقابس الآمنة / أمان طبقة النقل** | هو بروتوكول يقوم بتشفير البيانات المرسلة عبر الإنترنت لجعلها آمنة. علامة القفل و "https" في متصفح الويب تشير إلى استخدام هذه التقنية. |
| `openssl` | **مجموعة أدوات OpenSSL** | هي أداة سطر أوامر قوية للغاية يمكنها فعل أي شيء تقريبًا يتعلق بعلم التشفير. |
| `openssl s_client` | **عميل SSL/TLS** | هو جزء من مجموعة أدوات `openssl` ويُستخدم للاتصال بالخوادم المشفرة (SSL/TLS). يمكنك اعتباره النسخة المشفرة من أمر `nc`. **الاستخدام:** `openssl s_client -connect [target]:[port]` |
| **Self-Signed Certificate** | **شهادة ذاتية التوقيع** | في العادة، يتم التحقق من هوية موقع الويب من قبل "سلطة موثوقة". أما الشهادة ذاتية التوقيع، فهي شهادة يقوم فيها الخادم بالتحقق من هويته بنفسه. هي ليست آمنة ولكنها شائعة في بيئات الاختبار. خطأ `verify error:num=18` الذي يعطيه `openssl` ناتج عن هذا وهو **غير مهم** في هذا المستوى. |

---

#### **الجزء الثاني: فهم المهمة ووضع الخطة**
![[24.png]]

"يمكن الحصول على كلمة السر للمستوى التالي عن طريق إرسال كلمة المرور للمستوى الحالي إلى **المنفذ 30001** على **localhost** **باستخدام تشفير SSL/TLS**."

**التحليل الذهني للهدف:**
1.  **ماذا سنرسل؟** كلمة المرور للمستوى الحالي (`bandit15`): `8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo`.
2.  **إلى أين سنرسل؟** إلى المنفذ رقم `30001` على `localhost` (نفس الخادم).
3.  **ما هو الاختلاف الأهم؟** هذه المرة، يجب أن يكون الاتصال **مشفرًا**. هذا يعني أن الأدوات البسيطة مثل `nc` لن تعمل. سنحتاج إلى استخدام `openssl s_client`.

**الخطة:**
1.  سجل الدخول كمستخدم `bandit15`.
2.  **التشخيص:** تحقق من أن المنفذ `localhost:30001` مفتوح باستخدام `nmap`.
3.  **التنفيذ:** أنشئ اتصالاً مشفرًا باستخدام `openssl s_client`.
4.  بعد إنشاء الاتصال، أرسل كلمة المرور الحالية.
5.  اقرأ الرد القادم.

---

#### **الجزء الثالث: رحلة الحل (سجل الطرفية الحقيقي)**

إليك كيف تم تطبيق هذه الخطة عمليًا، وماذا يعني كل جزء من الناتج.

**الخطوة 1: تسجيل الدخول كمستخدم `bandit15`**
نتصل بحساب `bandit15` باستخدام كلمة المرور التي حصلنا عليها.
```bash
┌──(root㉿kali)-[~]
└─# ssh bandit15@bandit.labs.overthewire.org -p 2220
...
bandit15@bandit.labs.overthewire.org's password: (نلصق كلمة المرور هنا)
...
bandit15@bandit:~$
```

**الخطوة 2: التشخيص (التأكد من وجود الهدف)**
من الحكمة دائمًا التحقق من أن هدفك موجود قبل محاولة الاتصال به.
```bash
bandit15@bandit:~$ nmap -p 30001 localhost
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-10-29 13:29 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000082s latency).

PORT      STATE SERVICE
30001/tcp open  pago-services1

Nmap done: 1 IP address (1 host up) scanned in 0.03 seconds
```
**التحليل:** يخبرنا `nmap` أن المنفذ 30001 **`open`** (مفتوح). إذن، هدفنا موجود وصالح.

**الخطوة 3: تنفيذ الحل (طرق مختلفة)**

##### **الطريقة (أ): الحل التفاعلي (خطوة بخطوة)**

في هذه الطريقة، ننشئ الاتصال أولاً، ثم ندخل كلمة المرور يدويًا. هذه الطريقة ممتازة لفهم العملية.

```bash
bandit15@bandit:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)
... (تظهر هنا الكثير من معلومات الشهادة والتفاصيل التقنية) ...
Verification error: self-signed certificate
... (نصل إلى نهاية الناتج) ...
---
read R BLOCK
```
**الشرح:** توقفت الطرفية الآن عند `read R BLOCK` وهي تنتظر إدخالاً منك. هذا يعني أن النفق المشفر قد تم إنشاؤه بنجاح وأن الخادم يقول لك: "أنا أستمع، في انتظار البيانات التي سترسلها".

الآن كل ما عليك فعله هو كتابة كلمة المرور والضغط على `Enter`.
```
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx

closed
```
**لقد نجحنا!** أرسلنا كلمة المرور، ورد الخادم بـ "Correct!" (صحيح!) وأعطانا كلمة مرور المستوى التالي (`bandit16`): `kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx`.

##### **الطريقة (ب): الحل التلقائي (باستخدام `echo` و `|`)**

هذه الطريقة تستخدم تقنية `echo` و `|` (الماسورة) التي تعلمناها، ولكن مع استبدال `nc` بـ `openssl s_client`. هذه هي أسرع طريقة للوصول إلى الحل في سطر واحد.

```bash
bandit15@bandit:~$ echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | openssl s_client -connect localhost:30001 -quiet
```
**تحليل هذا الأمر:**
*   `echo "..."`: ينشئ النص (كلمة المرور).
*   `|`: يأخذ هذا النص ويمرره كـ "مدخل" للأمر التالي.
*   `openssl s_client -connect localhost:30001`: ينشئ الاتصال المشفر.
*   **`-quiet`**: هذا خيار جديد ومفيد جدًا. يقوم بإخفاء معلومات الشهادة الطويلة التي يعرضها `openssl` عادةً، ويظهر فقط الرد الفعلي من الخادم، مما يجعل الناتج أكثر نظافة.

**سيكون ناتج هذا الأمر أكثر نظافة:**
```bash
depth=0 CN = SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN = SnakeOil
verify return:1
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```

---

## 🇺🇸 English

### Objective
Submit the password to an SSL/TLS encrypted service on port 30001.

### Command
```bash
echo "8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo" | openssl s_client -connect localhost:30001 -quiet
```

### Explanation
- `openssl s_client`: Creates SSL/TLS connection (like HTTPS)
- `-connect localhost:30001`: Target host and port
- `-quiet`: Suppresses certificate details for cleaner output

### Key Takeaways
- **SSL/TLS:** Encrypted communication layer; `nc` won't work for this
- **openssl s_client:** Tool for connecting to SSL-enabled services
- **Self-signed Certificates:** May show warnings but still work