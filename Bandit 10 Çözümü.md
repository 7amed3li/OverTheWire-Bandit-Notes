

---
### **Bandit Seviye 10 -> 11 (Uzman Yöntemi)**

#### **Bölüm 1: Olası Araç Kutunuz (Toolbox)**

Herhangi bir göreve başlamadan önce, elinizin altındaki araçları bilmek önemlidir. İşte bu seviyede veya benzer görevlerde faydalı olabilecek komutların bir özeti.

| Komut                           | Açıklama                                                                                                                   | Ne Zaman Kullanılır?                                                                                   |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span class="red">ls -la</span> | **Listele (Detaylı):** Tüm dosyaları (gizli olanlar dahil) izinleri, boyutu ve tarihi gibi ayrıntılarla listeler.          | Her zaman! Bir dizine ilk girdiğinizde çevrenizi keşfetmek için temel komuttur.                        |
| `cat`                           | **Birleştir ve Görüntüle:** Metin dosyalarının içeriğini ekranda görüntüler.                                               | Bir dosyanın metin tabanlı olup olmadığını hızlıca kontrol etmek ve içeriğini okumak için.             |
| file                            | **Dosya Türünü Belirle:** Bir dosyanın içeriğini analiz ederek türünü (metin, ikili, sıkıştırılmış vb.) tahmin eder.       | `cat` anlamsız karakterler gösterdiğinde, dosyanın gerçekte ne olduğunu anlamak için.                  |
| strings                         | **Dizeleri Çıkar:** İkili (binary) bir dosyadan okunabilir metin dizelerini (strings) ayıklar.                             | İkili bir dosyanın içinde gizlenmiş metin tabanlı ipuçları veya şifreler ararken.                      |
| <span class="blue">grep</span>  | **Desen Ara:** Aldığı metin girdisi içinde belirli bir kelimeyi veya deseni (pattern) arar ve eşleşen satırları gösterir.  | `strings` veya `cat` komutlarının çıktısı çok uzun olduğunda, ilgilendiğiniz bilgiyi filtrelemek için. |
| `sort`                          | **Sırala:** Metin satırlarını alfabetik veya sayısal olarak sıralar.                                                       | Düzensiz verileri analiz etmeyi kolaylaştırmak için.                                                   |
| `uniq`                          | **Tekrarları Kaldır:** Sıralanmış bir listedeki tekrar eden satırları kaldırır. Genellikle `sort` ile birlikte kullanılır. | Bir listede her öğenin kaç kez geçtiğini saymak (`-c`) veya sadece benzersiz öğeleri görmek için.      |
| `tr`                            | **Çevir (Translate):** Karakterleri sistematik olarak başka karakterlerle değiştirir veya siler.                           | Basit şifreleri (ROT13 gibi) çözmek veya metindeki karakterleri değiştirmek için.                      |
| <span class="red">base64</span> | **Base64 Kodla/Çöz:** Verileri Base64 formatına kodlar veya bu formattan çözer.                                            | Base64 ile kodlanmış verilerle karşılaştığınızda.                                                      |
| `gzip`, `bzip2`                 | **Sıkıştırma Araçları:** Dosyaları sıkıştırmak (`gzip dosya`) veya açmak (`gzip -d dosya.gz`) için kullanılır.             | `.gz` veya `.bz2` uzantılı sıkıştırılmış dosyalarla çalışırken.                                        |
| `tar`                           | **Arşiv Aracı:** Birden çok dosyayı tek bir `.tar` dosyasında birleştirir veya bir arşivden dosyaları çıkarır.             | `.tar` uzantılı arşiv dosyalarını yönetmek için.                                                       |
| `xxd`                           | **Hex Dökümü:** Bir dosyanın içeriğini onaltılık (hexadecimal) formatta gösterir.                                          | Dosyanın ham bayt (raw byte) seviyesindeki yapısını derinlemesine incelemek için.                      |

#### **Bölüm 2: Görevi Anlamak ve Plan Yapmak**

![[17.png]] "Bir sonraki seviyenin şifresi, `base64` ile kodlanmış veriler içeren `data.txt` dosyasında saklıdır."

**Hedefin Zihinsel Analizi:**

1. **Nerede?** `data.txt` dosyasında.
2. **Ne?** `base64` ile kodlanmış veri. Bu, en önemli ipucu. Verinin doğrudan okunabilir olmadığını, bir "kodlama" işleminden geçtiğini söylüyor.
3. **Görev Ne?** Kodlanmış bu veriyi orijinal haline geri getirmek, yani **"çözmek" (Decoding)**.

**İlk Plan:**

1. `data.txt` dosyasının içeriğini oku.
2. Base64 kodunu çözebilen bir araç bul. Araç kutumdan hemen aklıma `base64` komutu geliyor.
3. Bu aracı, veriyi kodlamak yerine **çözmesi** için doğru seçenekle kullan.
---

#### **2. Düşünce Süreci: Problemi Adım Adım Çözmek**
![[18.jpg]]
**Sahne:** `bandit10` seviyesine yeni girdim. Her zamanki gibi, keşif ve teşhis ile başlıyoruz.

**Adım 1: Keşif ve Teşhis (`ls` ve `cat`)** Her zamanki gibi, önce kanıtları inceleriz.

Bash
```
bandit10@bandit:~$ ls -l
-rw-r----- 1 bandit11 bandit10 69 Oct 14 09:25 data.txt

bandit10@bandit:~$ cat data.txt
VGhlIHBhc3N3b3JkIGlzIGR0UjE3M2ZaS2IwUlJzREZTR3NnMlJXbnBOVmozcVJyCg==
```

**Hızlı Analiz:**

1. **Teşhis:** `cat` komutu içeriği sorunsuz görüntüledi. Bu bir metin dosyası, ikili değil.
2. **Analiz:** Metin, anlamsız ve rastgele görünen uzun bir karakter dizisi. Bu genellikle bir tür "kodlama" (encoding) olduğuna işarettir.
3. **İpucu:** Seviye hedefi, bunun `base64` ile kodlandığını zaten açıkça belirtti. Bu, en güçlü kanıtımız.

**Sonuç:** Görevimiz, `base64` ile kodlanmış bu metni orijinal haline **çözmektir (Decoding)**.

**Adım 2: Doğru Aracı Bulmak**

Şimdi, zihinsel "araç kutumda" Base64 ile başa çıkabilecek bir komut arıyorum.

**`base64` Komutu Özet Tablosu**

| Komut / Seçenek                | Açıklama                                                                          | Örnek Kullanım                 |
| ------------------------------ | --------------------------------------------------------------------------------- | ------------------------------ |
| `base64 [dosya]`               | **Kodlama (Varsayılan):** Bir dosyanın içeriğini Base64 formatına kodlar.         | `base64 gizli.txt`             |
| `base64 -d [dosya]`            | **Kod Çözme:** Base64 ile kodlanmış bir dosyanın içeriğini orijinal haline çözer. | `base64 -d data.txt`           |
| `echo "metin" \| base64`       | **Metin Kodlama:** Doğrudan bir metin dizesini Base64'e kodlar.                   | `echo "selam" \| base64`       |
| `echo "c2VsYW0K" \| base64 -d` | **Metin Kod Çözme:** Base64 formatındaki bir metni doğrudan çözer.                | `echo "c2VsYW0K" \| base64 -d` |

**Kullanım Kılavuzu Analizi:**
*   `base64` komutu hem kodlama **hem de** kod çözme yapar.
*   Varsayılan olarak (seçeneksiz), kodlama (Encoding) yapar.
*   Kod çözmek için `-d` veya `--decode` seçeneğini kullanmalıyız.

**Adım 3: Komutu Oluşturmak (Prompt)**
Şimdi tüm parçalara sahibiz. Onları nasıl bir araya getireceğiz?
1.  **Ana Motor:** `base64 -d`. 
2.  **Veri Kaynağı:** `data.txt` dosyasının içeriğini `base64 -d` komutuna nasıl "besleyeceğiz"? İki temel yöntemimiz var:

    *   **Yöntem A: Boru Hattı `|` (Pipe) Kullanımı**
        *   Fikir: Dosyayı okumak  için bir komut (`cat`) kullanırız, ardından çıktısını `base64 -d` komutuna yönlendiririz.
        *   Yapı: `cat data.txt | base64 -d`
        *   ****Anlamı**** `data.txt`'nin içeriğini oku (`cat`), sonra çıkan sonucu `base64 -d` komutuna girdi olarak ver.
     ![[19.jpg]]
     
     * **Yöntem B: Girdi Yönlendirme `<` (Input Redirection) Kullanımı**
        * Bu yöntemde, komuta girdisini klavye yerine doğrudan bir dosyadan almasını söyleriz. Bu, genellikle daha verimli kabul edilir çünkü tek bir işlem (`base64`) çalıştırır.

        * **Yapı:** `base64 -d < data.txt`
        * **Anlamı:** "`base64 -d` komutunu çalıştır ve girdisinin doğrudan `data.txt` dosyasından gelmesini sağla."
        * ![[20.jpg]]
Her iki yöntem de doğrudur ve aynı sonucu verir. İkinci yöntem, iki işlem (`cat` ve `base64`) yerine tek bir işlem (`base64`) çalıştırdığı için daha profesyonel ve verimli kabul edilir.

---

#### **3. Nihai Sonuç (Öğrenilen Ders)**

Buradaki ders sadece `base64 -d` değildir. Ders, **metodolojidir**:
1.  **Kanıtları İncele:** Sorun hakkında ilk fikri edinmek için `cat` veya `file` kullan.
2.  **Çıktıyı Analiz Et:** Desenleri ara (rastgele metin, belirli karakterler, hedefteki ipuçları).
3.  **Aracı Bul:** Teşhis ettiğin sorun için doğru aracı bulmak üzere `man` veya `apropos` kullan.
4.  **Komutu Oluştur:** Sorunu çözmek için aracı doğru seçenekler (`-d`) ve veri besleme yöntemi (`|` veya `<`) ile birleştir.

---
---

### **دليل Bandit التعليمي: المستوى 10 -> 11 (منهجية التفكير)**

#### **1. الهدف (Objective)**

كلمة السر للمستوى القادم موجودة في ملف `data.txt`، والذي يحتوي على بيانات مُكوَّدة بنظام `base64`.

---

#### **2. رحلة التفكير: كيف نبني الحل خطوة بخطوة؟**

**المشهد:** لقد دخلت للتو إلى `bandit10`. كالعادة، نبدأ بالاستكشاف والتشخيص.

**الخطوة 1: فحص الأدلة (الملف)**
لنلقِ نظرة أولى على الملف لنفهم طبيعته.

```bash
bandit10@bandit:~$ ls -l
-rw-r----- 1 bandit11 bandit10 69 Mar 21 18:27 data.txt

bandit10@bandit:~$ cat data.txt
ZGF0YS50eHQgY29udGFpbnMgYmFzZTY0IGVuY29kZWQgZGF0YQpUaGUgcGFzc3dvcmQgaXMgZHRSMTczZlpLYjBSUnNERlNHc2cyUlducE5WajNxUnIK
```

**التحليل الذهني الفوري:**
1.  الملف نصي، وليس ثنائيًا. `cat` عرض محتوياته بدون مشاكل.
2.  النص عبارة عن سلسلة طويلة من الحروف والأرقام تبدو عشوائية. لا توجد كلمات إنجليزية واضحة.
3.  النص لا يحتوي على مسافات أو علامات ترقيم تقليدية.
4.  **ملاحظة مهمة:** النص ينتهي بحرف `K` وليس `=`. في كثير من الأحيان، ينتهي Base64 بعلامة `=` أو `==` كـ "حشو" (padding)، لكن ليس دائمًا. غيابها لا ينفي كونه Base64.
5.  الهدف من المستوى ذكر صراحةً كلمة `base64 encoded`. هذا هو الدليل الأقوى. بدون هذا الدليل، كنت سأخمن أنه نوع من التكويد أو التشفير.

**الخلاصة:** لقد شخصنا المشكلة. لدينا بيانات مُكوَّدة بـ Base64، ومهمتنا هي **فك تكويدها (Decoding)**.

**الخطوة 2: البحث عن الأداة المناسبة**
الآن، أبحث في "صندوق أدواتي" الذهني عن أمر يمكنه التعامل مع Base64.
*   اسم المشكلة هو "base64". في عالم لينكس، غالبًا ما يكون اسم الأداة هو نفس اسم المشكلة.
*   لنجرب كتابة `base64` في الطرفية ونضغط `Tab` مرتين. نعم، الأمر موجود.
*   كيف يعمل هذا الأمر؟ لنقرأ دليل الاستخدام: `man base64`.

```bash
man base64
...
NAME
       base64 - base64 encode/decode data and print to standard output

SYNOPSIS
       base64 [OPTION]... [FILE]

DESCRIPTION
       -d, --decode
              decode data
...
```

**تحليل دليل الاستخدام:**
*   الأمر `base64` يقوم بالتكويد **وفك التكويد**.
*   بشكل افتراضي (بدون خيارات)، يقوم بالتكويد (Encoding).
*   لفك التكويد، يجب أن نستخدم الخيار `-d` أو `--decode`.

**الخطوة 3: بناء الأمر (البرومبت)**
لدينا الآن كل القطع. كيف نركبها معًا؟
1.  **المحرك الرئيسي:** `base64 -d`. هذا هو قلب عمليتنا.
2.  **مصدر البيانات:** كيف سنقوم "بتغذية" أمر `base64 -d` بمحتويات ملف `data.txt`؟ لدينا طريقتان أساسيتان:

    *   **الطريقة (أ): استخدام الماسورة `|` (Pipe)**
        *   الفكرة: نستخدم أمرًا لقراءة الملف (`cat`)، ثم نوجه ناتجه إلى أمر `base64 -d`.
        *   التركيب: `cat data.txt | base64 -d`
        *   **الترجمة الحرفية:** "اعرض محتوى `data.txt`، ثم خذ هذا المحتوى ومرره كمدخل لأمر `base64` الذي يعمل في وضع فك التكويد".

    *   **الطريقة (ب): استخدام إعادة توجيه المدخلات `<`**
        *   الفكرة: نخبر أمر `base64 -d` مباشرةً أن يأخذ مدخلاته من ملف بدلاً من لوحة المفاتيح.
        *   التركيب: `base64 -d < data.txt`
        *   **الترجمة الحرفية:** "شغّل أمر `base64` في وضع فك التكويد، واجعل مدخلاته تأتي من ملف `data.txt`".

كلا الطريقتين صحيحتان وتؤديان نفس النتيجة. الطريقة الثانية تعتبر أكثر احترافية وكفاءة لأنها تشغل عملية واحدة (`base64`) بدلاً من عمليتين (`cat` و `base64`).

---

#### **3. الحل النهائي (التنفيذ)**

الآن بعد أن بنينا الأمر بناءً على تحليل وتفكير، لننفذه:

```bash
bandit10@bandit:~$ base64 -d < data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```
وها هي كلمة السر أمامنا.

---

#### **4. الخلاصة النهائية (الدرس المستفاد)**

الدرس هنا ليس مجرد `base64 -d`. الدرس هو **المنهجية**:
1.  **افحص الأدلة:** استخدم `cat` أو `file` لتكوين فكرة أولية عن المشكلة.
2.  **حلل المخرجات:** ابحث عن أنماط (نص عشوائي، أحرف محددة، تلميحات في الهدف).
3.  **ابحث عن الأداة:** استخدم `man` أو `apropos` للعثور على الأداة المناسبة للمشكلة التي شخصتها.
4.  **ابنِ الأمر:** اجمع الأداة مع الخيارات الصحيحة (`-d`) وطريقة تمرير البيانات (`|` أو `<`) لحل المشكلة.

هذه هي الطريقة التي تحول بها المعلومات إلى حلول.

---

## 🇺🇸 English

### Objective
Decode Base64 encoded data in `data.txt`.

### Command
```bash
base64 -d < data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

### Explanation
- `base64 -d`: Decode Base64 encoded data
- `< data.txt`: Input redirection from file

### Key Takeaways
- **Base64:** Common encoding (not encryption!) for ASCII representation of binary data
- **Decoding:** Use `base64 -d` to decode
- **Methodology:** Examine → Analyze patterns → Find tool → Execute