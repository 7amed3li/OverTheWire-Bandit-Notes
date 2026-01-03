
---
#### **Bölüm 1: Olası Araç Kutunuz (Toolbox)**

Herhangi bir göreve başlamadan önce, elinizin altındaki araçları bilmek önemlidir. İşte bu seviyede veya benzer görevlerde faydalı olabilecek komutların bir özeti.

| Komut                                | Açıklama                                                                                                                   | Ne Zaman Kullanılır?                                                                                   |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span class="orange">ls -la</span>   | **Listele (Detaylı):** Tüm dosyaları (gizli olanlar dahil) izinleri, boyutu ve tarihi gibi ayrıntılarla listeler.          | Her zaman! Bir dizine ilk girdiğinizde çevrenizi keşfetmek için temel komuttur.                        |
| `cat`                                | **Birleştir ve Görüntüle:** Metin dosyalarının içeriğini ekranda görüntüler.                                               | Bir dosyanın metin tabanlı olup olmadığını hızlıca kontrol etmek ve içeriğini okumak için.             |
| <span class="red">file</span><br>    | **Dosya Türünü Belirle:** Bir dosyanın içeriğini analiz ederek türünü (metin, ikili, sıkıştırılmış vb.) tahmin eder.       | `cat` anlamsız karakterler gösterdiğinde, dosyanın gerçekte ne olduğunu anlamak için.                  |
| <span class="red">strings</span><br> | **Dizeleri Çıkar:** İkili (binary) bir dosyadan okunabilir metin dizelerini (strings) ayıklar.                             | İkili bir dosyanın içinde gizlenmiş metin tabanlı ipuçları veya şifreler ararken.                      |
| <span class="blue">grep</span><br>   | **Desen Ara:** Aldığı metin girdisi içinde belirli bir kelimeyi veya deseni (pattern) arar ve eşleşen satırları gösterir.  | `strings` veya `cat` komutlarının çıktısı çok uzun olduğunda, ilgilendiğiniz bilgiyi filtrelemek için. |
| `sort`                               | **Sırala:** Metin satırlarını alfabetik veya sayısal olarak sıralar.                                                       | Düzensiz verileri analiz etmeyi kolaylaştırmak için.                                                   |
| `uniq`                               | **Tekrarları Kaldır:** Sıralanmış bir listedeki tekrar eden satırları kaldırır. Genellikle `sort` ile birlikte kullanılır. | Bir listede her öğenin kaç kez geçtiğini saymak (`-c`) veya sadece benzersiz öğeleri görmek için.      |
| `tr`                                 | **Çevir (Translate):** Karakterleri sistematik olarak başka karakterlerle değiştirir veya siler.                           | Basit şifreleri (ROT13 gibi) çözmek veya metindeki karakterleri değiştirmek için.                      |
| `base64`                             | **Base64 Kodla/Çöz:** Verileri Base64 formatına kodlar veya bu formattan çözer.                                            | Base64 ile kodlanmış verilerle karşılaştığınızda.                                                      |
| `gzip`, `bzip2`                      | **Sıkıştırma Araçları:** Dosyaları sıkıştırmak (`gzip dosya`) veya açmak (`gzip -d dosya.gz`) için kullanılır.             | `.gz` veya `.bz2` uzantılı sıkıştırılmış dosyalarla çalışırken.                                        |
| `tar`                                | **Arşiv Aracı:** Birden çok dosyayı tek bir `.tar` dosyasında birleştirir veya bir arşivden dosyaları çıkarır.             | `.tar` uzantılı arşiv dosyalarını yönetmek için.                                                       |
| `xxd`                                | **Hex Dökümü:** Bir dosyanın içeriğini onaltılık (hexadecimal) formatta gösterir.                                          | Dosyanın ham bayt (raw byte) seviyesindeki yapısını derinlemesine incelemek için.                      |
|                                      |                                                                                                                            |                                                                                                        |

#### **Bölüm 1 : Sadece komutları değil, düşünme şeklini de öğrenelim.**
Bu bölümde amacımız sadece bir komutu öğrenmek değil. Asıl amacımız, bir problemi çözmek için nasıl bir **yol izleyeceğimizi** anlamak. Çünkü asıl mesele komutları bilmek değil, onları doğru bir **yöntemle** ne zaman ve nasıl kullanacağını bilmektir. Şimdi bu yöntemi iş başında görelim.

---
#### **Bölüm 2: Görevi Anlamak ve Plan Yapmak**

![[9.png]]
"Bir sonraki seviyenin şifresi, `data.txt` dosyasının içinde, öncesinde birkaç tane ‘=’ karakteri bulunan, okunabilir birkaç metin dizisinden birinde saklıdır."

**Hedefin Zihinsel Analizi:**

1. **Nerede?** `data.txt` dosyasında.
2. **Ne?** "Okunabilir birkaç metin dizisinden biri" (`human-readable strings`). Bu, dosyanın büyük olasılıkla **ikili (binary)** olduğunu ve içinde metin parçaları sakladığını ima ediyor. Araç kutumdan hemen aklıma `strings` komutu geliyor.

3. **Nasıl?** "Öncesinde birkaç tane `=` karakteri bulunan". Bu, bize aramamamız gereken **deseni (Pattern)** veriyor. Bu da aklıma hemen `grep` komutunu getiriyor.

**İlk Plan:**

1. `strings` kullanarak `data.txt` dosyasından tüm okunabilir metinleri çıkar.
2. `grep` kullanarak bu metinler içinde `====` desenini içeren satırları filtrele.

#### **Bölüm 3: Çözüm Yolculuğu (Düşünce Süreci)**

**Sahne:** `bandit9` seviyesine yeni girdim.
![[10.jpg]]

**Adım 1: Keşif (`ls -la`)** Her zaman olduğu gibi, önce etrafa bakarım.
![[11 1.jpg]]
Bash

```
bandit9@bandit:~$ ls -la
-rw-r-----   1 bandit10 bandit9 19382 Oct 14 09:25 data.txt
```
Bu satır, `data.txt` adında, sahibi `bandit10` ve grubu `bandit9` olan, 19382 byte boyutunda bir dosya olduğunu ve sahibinin okuma/yazma, grubun ise sadece okuma iznine sahip olduğunu belirtir.
[[Linux Dosya İzinleri Açıklaması (-rwxrwxrwx)]]
Hedefimiz `data.txt` burada.

**Adım 2: Teşhis (`cat` ve `file`)** Planım dosyanın ikili olduğunu varsayıyor, ama emin olmalıyım.

Bash

```
bandit9@bandit:~$ cat data.txt
(Ekran anlamsız sembollerle dolar)
```
![[12.jpg]]
`cat`'in çıktısı anlamsız. Bu, ikili dosya teorimi güçlendiriyor. `file` ile teyit edelim.

![[13 1.jpg]]
"data" çıktısı, bunun belirli bir türe sahip olmayan ham bir veri dosyası olduğunu, yani ikili olduğunu onaylar.


strings data.txt  
![[14.jpg]]

![[15.jpg]]

![[16.jpg]]
**`strings data.txt`** komutunu kullandığımızda şifreyi bulabiliyoruz, ancak bu sadece dosya boyutu küçük olduğu için (örneğin 7 KB) mümkün oldu. Eğer dosya 20 MB gibi çok daha büyük olsaydı, binlerce satır arasında şifreyi gözle bulmak imkansız hale gelirdi. İşte bu yüzden, bu büyük veri yığını içinden sadece aradığımız desene uyan satırları filtrelemek için `grep` komutunu kullanmak çok daha verimli ve profesyonel bir yöntemdir.

---

**Adım 3: Planı Uygulamak** 
Artık planımı uygulamaya hazırım. Amatörlerin yaptığı gibi önce `strings` çalıştırıp sonra gözle aramak yerine, iki adımı tek bir komutta birleştirmek için **Boru Hattı (`|`)** kullanacağım.

**Sihirli Zinciri Oluşturma:**

1. `strings data.txt`: İkili dosyadan okunabilir metinleri çıkar **(Kaynak)**.
2. `|`: Çıkan tüm metni bir sonraki komuta yönlendir **(Boru Hattı)**.
3. `grep "===="`: Gelen metin içinde `====` desenini ara ve sadece eşleşenleri göster **(Filtre)**.

**Nihai Komutun Uygulanması:**

![[17.jpg]]
Sonuç mükemmel. Gürültülü veriler içinde aradığımız şifreyi bir saniyeden kısa sürede bulduk.

#### **Nihai Sonuç**

Bu seviyede asıl ders, komutları ezberlemek değil, **bir metodolojiye sahip olmaktır**:

1. **Keşfet:** `ls` ile çevreni tanı.
2. **Teşhis Et:** `file` ve `cat` ile problemin doğasını anla (metin mi, ikili mi?).
3. **Planla:** Hedefe ve teşhise göre zihnindeki araç kutusundan doğru araçları seç (`strings`, `grep`).
4. **Uygula:** Araçları boru hatları (`|`) ile birleştirerek verimli ve güçlü bir çözüm oluştur.
---

#### **Bölüm 2: Problemi Büyütmek (İmkansız Senaryo)**

Ne demek istediğimi göstermek için size özel bir senaryo hazırladım. Adı `big_confusing_file.dat`, boyutu 20 MB ve şifre aynı şekilde içinde saklı. Eğer aynı "gözle arama" yöntemini burada deneseydik...

```bash
strings big_confusing_file.dat
(Ekranın binlerce bitmeyen satırla dolduğunu hayal edin)
```
Gördünüz mü? **İmkansız**. İşte burada, eski yöntemimiz tamamen başarısız oldu. Daha akıllı bir yola ihtiyacımız var.

---

#### **Bölüm 3: Profesyonel Çözüm (Boru Hatları ve Filtrelerin Gücü)**

**Profesyonel Düşünce (Hacker):**
İyi bir hacker ayrıntılarda boğulmaz. Probleme yukarıdan bakar ve sorar: "Aradığım **desen (Pattern)** nedir?"
Desen, `====` karakterleridir.

Öyleyse, her şeyi okumama gerek yok. Tüm bu gürültüyü filtreleyecek ve bana sadece desenimi içeren satırları gösterecek bir araca ihtiyacım var. Bu araç `grep`'tir.

**Sihirli Zinciri Oluşturma (The Pipeline):**
1.  `strings big_confusing_file.dat`: Tüm okunabilir metinleri çıkar **(Kaynak)**.
2.  `|`: Tüm bu çıktıyı al ve bir sonraki komuta ver **(Boru Hattı)**.
3.  `grep "===="`: Sana gelen her şeyi filtrele ve sadece `====` içeren satırları göster **(Filtre)**.

**İmkansız Senaryoya Uygulama:**
```bash
strings big_confusing_file.dat | grep "===="
========== THIS_IS_THE_HIDDEN_PASSWORD_YOU_ARE_LOOKING_FOR
```
Bir saniyeden kısa sürede, kaosu net bir cevaba dönüştürdük.

**Gerçek Göreve Geri Dönüş (Nihai Uygulama):**
Şimdi, Bandit'in orijinal dosyasına geri dönelim ve aynı profesyonel çözümü uygulayalım.

```bash
bandit9@bandit:~$ strings data.txt | grep "===="
========== the
========== password
Q========== is%
>u`9J========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

---

#### **Nihai Sonuç**

**Buradaki asıl ders şifre değil, Yöntemdir**:
1.  Çevrenizi **keşfedin**.
2.  Problemin türünü **teşhis edin** (metin dosyası mı, ikili dosya mı?).
3.  Doğru aracı kullanın (`cat` metin için, `strings` ikili için).
4.  Çıktıda bir **desen arayın**.
5.  Araçları birleştirerek (`|` ve `grep`) arama sürecini **otomatikleştirin**.

Bu yöntem sadece bu seviyeyi çözmekle kalmaz, boyutu veya karmaşıklığı ne olursa olsun karşılaşacağınız benzer sorunları da çözer.

---

### **دليل Bandit التعليمي: المستوى 9 -> 10 (التفكير كالمحترفين)**

يهدف هذا الدليل إلى تعليمك منهجية الخبير في الأمن السيبراني عند التعامل مع مشكلة: كيف يحللها، وكيف يختار أدواته، وكيف يصل إلى الحل، وليس فقط تزويدك بالحل النهائي.

#### **الجزء الأول: صندوق أدواتك المحتمل (Toolbox)**

قبل البدء في أي مهمة، من المهم أن تعرف الأدوات المتاحة لك. إليك قائمة بالأوامر التي قد تكون مفيدة في هذا المستوى أو في مهام مشابهة.

|الأمر|الشرح|متى يُستخدم؟|
|---|---|---|
|<span class="orange">ls -la</span>|**عرض القائمة (مفصل):** يعرض كل الملفات (بما في ذلك المخفية) مع تفاصيلها مثل الأذونات، الحجم، والتاريخ.|دائمًا! هو الأمر الأساسي لاستكشاف بيئتك عند دخول أي مجلد جديد.|
|`cat`|**عرض ودمج:** يعرض محتويات الملفات النصية على الشاشة.|للتحقق بسرعة مما إذا كان الملف نصيًا ولقراءة محتواه.|
|<span class="red">file</span>|**تحديد نوع الملف:** يحلل محتوى الملف لتخمين نوعه (نصي، ثنائي، مضغوط، إلخ).|عندما يعرض `cat` رموزًا غريبة، نستخدمه لفهم طبيعة الملف الحقيقية.|
|<span class="red">strings</span>|**استخراج النصوص:** يستخلص السلاسل النصية القابلة للقراءة من ملف ثنائي (binary).|عند البحث عن تلميحات نصية أو كلمات سر مخبأة داخل ملف ثنائي.|
|<span class="blue">grep</span>|**البحث عن نمط:** يبحث عن كلمة أو نمط (pattern) معين داخل أي نص يتلقاه، ويعرض فقط الأسطر التي تطابق البحث.|عندما يكون ناتج `cat` أو `strings` طويلاً جدًا، نستخدمه لفلترة المعلومات التي تهمنا فقط.|
|`sort`|**فرز:** يرتب أسطر النص أبجديًا أو رقميًا.|لتسهيل تحليل البيانات غير المرتبة.|
|`uniq`|**إزالة التكرار:** يزيل الأسطر المكررة من قائمة **مرتبة**. غالبًا ما يُستخدم مع `sort`.|لعد مرات تكرار كل عنصر (`-c`) أو لرؤية العناصر الفريدة فقط.|
|`tr`|**ترجمة (Translate):** يستبدل أو يحذف أحرفًا بشكل منهجي.|لفك تشفيرات بسيطة (مثل ROT13) أو لتعديل الأحرف في نص.|
|`base64`|**تكويد/فك تكويد Base64:** يقوم بتكويد البيانات إلى صيغة Base64 أو فك تكويدها.|عند التعامل مع بيانات مُكوَّدة بـ Base64.|
|`gzip`, `bzip2`|**أدوات ضغط:** لضغط الملفات (`gzip file`) أو فك ضغطها (`gzip -d file.gz`).|عند التعامل مع ملفات مضغوطة بامتداد `.gz` أو `.bz2`.|
|`tar`|**أداة أرشفة:** تدمج عدة ملفات في ملف أرشيف واحد (`.tar`) أو تستخرج الملفات من الأرشيف.|لإدارة ملفات الأرشيف بامتداد `.tar`.|
|`xxd`|**عرض سداسي عشري:** يعرض محتوى الملف بصيغة ستة عشرية (hexadecimal).|لفحص بنية الملف على مستوى البايتات (raw bytes) بشكل دقيق.|

#### **الجزء الثاني: فهم المهمة ووضع الخطة**

**Level Goal (الهدف من المستوى):**

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.

**التحليل الذهني للهدف:**

1. **أين؟** في ملف `data.txt`.
2. **ماذا؟** "واحدة من السلاسل النصية القليلة القابلة للقراءة" (`human-readable strings`). هذا يلمح بقوة إلى أن الملف **ثنائي (binary)** ويخفي نصوصًا بداخله. من صندوق أدواتي، يقفز أمر `strings` إلى ذهني فورًا.
3. **كيف؟** "مسبوقة بعدة علامات `=`". هذا يعطينا **النمط (Pattern)** الذي نبحث عنه. وهذا يجعل أمر `grep` يقفز إلى ذهني.

**الخطة المبدئية:**

1. استخدم `strings` لاستخراج كل النصوص القابلة للقراءة من `data.txt`.
2. استخدم `grep` لفلترة هذه النصوص وإظهار الأسطر التي تحتوي على النمط `====` فقط.

#### **الجزء الثالث: رحلة الحل (مسار التفكير)**

**المشهد:** لقد دخلت للتو إلى `bandit9`.

**الخطوة 1: الاستكشاف (`ls -la`)** كالعادة، أول شيء هو إلقاء نظرة حولنا.

Bash

```
bandit9@bandit:~$ ls -la
-rw-r-----   1 bandit10 bandit9   7001 Aug 15 13:15 data.txt
```

هدفنا `data.txt` موجود.

**الخطوة 2: التشخيص (`cat` و `file`)** خطتي تفترض أن الملف ثنائي، لكن يجب أن أتأكد.

Bash

```
bandit9@bandit:~$ cat data.txt
(تظهر شاشة مليئة بالرموز الغريبة)
```

ناتج `cat` غير مفهوم. هذا يعزز نظريتي. لنتأكد باستخدام `file`.

Bash

```
bandit9@bandit:~$ file data.txt
data.txt: data
```

الناتج "data" يؤكد أنه ملف بيانات خام ليس له نوع محدد، أي أنه ثنائي.

**الخطوة 3: تطبيق الخطة (الطريقة الاحترافية)** أنا الآن جاهز لتنفيذ خطتي. بدلاً من تشغيل `strings` ثم البحث بالعين (طريقة الهواة)، سأستخدم **الماسورة (`|`)** لدمج الخطوتين في أمر واحد قوي.

**بناء السلسلة السحرية:**

1. `strings data.txt`: استخرج النصوص القابلة للقراءة من الملف الثنائي **(المصدر)**.
2. `|`: وجّه كل النصوص الناتجة إلى الأمر التالي **(الماسورة)**.
3. `grep "===="`: ابحث في النص القادم عن النمط `====` وأظهر فقط ما يطابقه **(الفلتر)**.

**تنفيذ الأمر النهائي:**

Bash

```
bandit9@bandit:~$ strings data.txt | grep "===="
========== the
========== password
Q========== is%
>u`9J========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

النتيجة مثالية. في أقل من ثانية، وجدنا الإبرة في كومة القش.

#### **الخلاصة النهائية**

الدرس الحقيقي في هذا المستوى ليس حفظ الأوامر، بل امتلاك **منهجية**:

1. **استكشف:** تعرف على بيئتك باستخدام `ls`.
2. **شخّص:** افهم طبيعة المشكلة (نصي أم ثنائي؟) باستخدام `file` و `cat`.
3. **خطط:** بناءً على الهدف والتشخيص، اختر الأدوات المناسبة من صندوق أدواتك الذهني (`strings`, `grep`).
4. **نفّذ:** ادمج الأدوات باستخدام الماسورة (`|`) لإنشاء حل فعال وقوي.

---

## 🇺🇸 English

### Objective
Find human-readable strings in a binary data file, looking for lines preceded by several `=` characters.

### Command
```bash
strings data.txt | grep "===="
========== the
========== password
Q========== is%
>u`9J========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```

### Explanation
- `strings`: Extracts printable character sequences from binary files
- `grep "===="`: Filters for lines containing the pattern

### Key Takeaways
- **Binary vs Text:** Binary files contain non-printable data; `strings` extracts the readable parts
- **Methodology:** Explore → Diagnose → Plan → Execute
- **Tool Chaining:** Combine `strings` and `grep` with pipe for effective filtering