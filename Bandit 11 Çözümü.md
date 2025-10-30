
---

# Bandit 11 -> 12 Çözümü

## 1. Amaç (Objective)
`data.txt` dosyasının içinde bulunan ve ROT13 şifrelemesi ile "döndürülmüş" olan metni çözerek bir sonraki seviyenin şifresini elde etmek.

---

## 2. Temel Kavramlar (Core Concepts)

###  **ROT13 Nedir? (Çocuklar İçin Anlatım)**

Merhaba arkadaşlar! Bugün alfabe ile sihirli bir oyun oynayacağız. Oyunumuzun adı **ROT13**.

#### **Adım: Alfabeyi İkiye Bölmek**

İngiliz alfabesinde 26 harf var, değil mi? Hadi bu alfabeyi tam ortadan ikiye bölelim. Tıpkı bir elmayı ikiye bölmek gibi!

- **İlk Yarı (13 Harf):** `A B C D E F G H I J K L M`
    
- **İkinci Yarı (13 Harf):** `N O P Q R S T U V W X Y Z`
    

Şimdi elimizde iki tane takım var.

#### **Adım: Sihirli Kural: "Karşı Takıma Zıpla!"**

ROT13 oyununun tek bir kuralı var: "Bir harf seçtiğinde, onun diğer takımdaki tam karşılığına zıpla."

Hadi deneyelim:

- Seçtiğimiz harf **A** olsun. **A**, ilk takımın en başında.
    
    - İkinci takımın en başındaki harf ne? **N**.
    - Demek ki **A**'nın şifresi **N**'dir.
- Seçtiğimiz harf **C** olsun. **C**, ilk takımda 3. sırada.
    
    - İkinci takımda 3. sıradaki harf ne? **P**.
    - Demek ki **C**'nin şifresi **P**'dir.

Peki tersten yaparsak ne olur?

- Seçtiğimiz harf **N** olsun. **N**, ikinci takımın en başında.
    - İlk takımın en başındaki harf ne? **A**.
    - Demek ki **N**'nin şifresi de **A**'dır.

Gördünüz mü? Sihir burada! **Şifrelemek için yaptığın hareketin aynısı, şifreyi çözmek için de işe yarıyor!**

#### **Adım: Özet**

**ROT13**, bir harfi, alfabenin diğer yarısındaki tam karşılığı ile değiştirmektir.

- `A` <-> `N`
- `B` <-> `O`
- `C` <-> `P`
- ...ve bu şekilde devam eder.

İşte bu kadar basit! Bilgisayara da tam olarak bu "karşılığını bul" görevini verdiğimizde, bizim için bütün metni anında çeviriyor.

### `tr` Komutu Nasıl Çalışır?
`tr` (translate) komutu, karakter tabanlı çeviri veya silme işlemleri yapar. Temel yapısı şöyledir:
`tr 'GİRDİ_KÜMESİ' 'ÇIKTI_KÜMESİ'`
Bu komut, `GİRDİ_KÜMESİ`'ndeki her karakteri, `ÇIKTI_KÜMESİ`'ndeki aynı pozisyondaki karakterle değiştirir.

---

## 3. Komutun Mantığını Anlamak (Understanding the Command's Logic)

ROT13 işlemini `tr` ile yapmak için doğru girdi ve çıktı kümelerini oluşturmalıyız.

1.  **Girdi Kümesi (INPUT_SET):** Çevirmek istediğimiz tüm karakterler. Bunlar, sıralı alfabenin tamamıdır.
    *   `'a-zA-Z'` (Tüm küçük ve büyük harfler)

2.  **Çıktı Kümesi (OUTPUT_SET):** Bu, Girdi Kümesi'nin ROT13 uygulanmış halidir.
    *   **Küçük Harfler İçin:**
        *   Alfabenin ilk yarısı (`a-m`), ikinci yarısı (`n-z`) olur.
        *   Alfabenin ikinci yarısı (`n-z`), ilk yarısı (`a-m`) olur.
        *   Bu yüzden çıktı kümesi: `n-z` ve ardından `a-m` gelir. -> `'n-za-m'`
    *   **Büyük Harfler İçin:**
        *   Aynı mantıkla, çıktı kümesi: `'N-ZA-M'`
    *   **Tam Çıktı Kümesi:** İkisini birleştiririz: `'n-za-mN-ZA-M'`

Bu mantıkla, `tr` komutuna "her 'a' gördüğünde 'n' yaz, her 'n' gördüğünde 'a' yaz" demiş oluruz.

---

## 4. Tam Komut (Full Command)

```bash
bandit11@bandit:~$ cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'
The password is JQttfApK4SeyHwDlIusA1337OQ7n3r0h
```
*   **Açıklama:**
    1.  `cat data.txt`: ROT13 ile şifrelenmiş metni okur.
    2.  `|`: Çıktıyı `tr` komutuna yönlendirir.
    3.  `tr 'a-zA-Z' 'n-za-mN-ZA-M'`: Gelen metindeki her harfi, ROT13 kuralına göre karşılığı olan harfle değiştirir ve sonucu ekrana basar.

---

## 5. Öğrenilenler (What I Learned)

- **`tr` Komutunun Gücü:** `tr` komutunun, karakter tabanlı karmaşık dönüşüm ve "çeviri" işlemleri için ne kadar esnek bir araç olduğunu öğrendim.
- **Şifreleme Kavramları:** ROT13 gibi basit ikame şifrelerinin (substitution ciphers) nasıl çalıştığını anladım.
- **Mantıksal Küme Oluşturma:** Bir komutun parametrelerini (özellikle `tr` için girdi/çıktı kümeleri) oluşturmak için problemi mantıksal adımlara ayırmanın önemini kavradım.
- **Problem Çözme:** Bir problemi çözmek için sadece komutu bilmenin yetmediğini, komutun çalışma mantığını derinden anlamanın gerektiğini öğrendim.



---

# حل المستوى 11 -> 12 (بانديت)

## 1. الهدف من المستوى ده إيه؟

المطلوب مننا نفك شفرة نص موجود جوه ملف `data.txt`. النص ده متشفر بشفرة اسمها ROT13، اللي هي بتعمل "تدوير" للحروف، عشان نطلع كلمة السر.

---

## 2. مفاهيم أساسية لازم تعرفها

### **إيه هي شفرة ROT13؟ (شرح للأطفال)**

أهلاً بيكم يا شباب! النهاردة هنلعب لعبة سحرية بالحروف الأبجدية. لعبتنا اسمها **ROT13**.

#### **الخطوة الأولى: نقسم الحروف الأبجدية نصين**

الحروف الإنجليزية 26 حرف، صح؟ تخيل إننا هنقسمهم من النص بالظبط، كأننا بنقسم تفاحة نصين!

*   **النص الأول (13 حرف):** `A B C D E F G H I J K L M`
*   **النص التاني (13 حرف):** `N O P Q R S T U V W X Y Z`

دلوقتي بقى معانا فريقين.

#### **الخطوة التانية: القاعدة السحرية: "نط للفريق التاني!"**

لعبة ROT13 ليها قاعدة واحدة بس: "لما تختار حرف، نط للحرف اللي قصاده بالظبط في الفريق التاني."

يلا نجرب:

*   لو اخترنا حرف **A**. حرف **A** هو أول واحد في الفريق الأول.
    *   مين أول واحد في الفريق التاني؟ **N**.
    *   يبقى شفرة حرف **A** هي **N**.

*   لو اخترنا حرف **C**. حرف **C** هو تالت واحد في الفريق الأول.
    *   مين تالت واحد في الفريق التاني؟ **P**.
    *   يبقى شفرة حرف **C** هي **P**.

طب لو عملنا العكس؟

*   لو اخترنا حرف **N**. حرف **N** هو أول واحد في الفريق التاني.
    *   مين أول واحد في الفريق الأول؟ **A**.
    *   يبقى شفرة حرف **N** هي برضه **A**.

شفتوا السحر فين؟ **الحركة اللي بتعملها عشان تشفر، هي هي نفس الحركة اللي بتفك بيها الشفرة!**

#### **الخطوة التالتة: الخلاصة**

شفرة **ROT13** هي إنك بتبدل أي حرف بالحرف اللي قصاده في النص التاني من الحروف الأبجدية.

*   `A` <-> `N`
*   `B` <-> `O`
*   `C` <-> `P`
*   ... وهكذا.

بسيطة أهي! وإحنا بندي للكمبيوتر نفس المهمة دي "لاقي الحرف اللي قصاده"، وهو بينفذها على النص كله في ثانية.

### **أمر `tr` بيشتغل إزاي؟**

أمر `tr` (اختصار translate) بيعمل عمليات ترجمة أو تبديل للحروف. طريقته بسيطة:
`tr 'مجموعة_الحروف_الأصلية' 'مجموعة_الحروف_الجديدة'`
الأمر ده بيبدل كل حرف في المجموعة الأصلية بالحرف اللي في نفس مكانه في المجموعة الجديدة.

---

## 3. فهم منطق الأمر (من جوه)

عشان نعمل ROT13 بأمر `tr`، لازم نجهز له المجموعتين صح.

1.  **مجموعة الإدخال (INPUT_SET):** دي كل الحروف اللي عايزين نترجمها، اللي هي الحروف الأبجدية كلها بالترتيب.
    *   `'a-zA-Z'` (كل الحروف الصغيرة والكبيرة).

2.  **مجموعة الإخراج (OUTPUT_SET):** دي لازم تكون هي ناتج تطبيق ROT13 على الحروف الأبجدية.
    *   **للحروف الصغيرة:**
        *   النص الأول من الحروف (`a-m`) بيتحول للنص التاني (`n-z`).
        *   والنص التاني (`n-z`) بيتحول للنص الأول (`a-m`).
        *   يبقى مجموعة الإخراج هي: `n-z` وبعدها `a-m` -> `'n-za-m'`.
    *   **للحروف الكبيرة:** بنفس المنطق، مجموعة الإخراج هي: `'N-ZA-M'`.
    *   **مجموعة الإخراج الكاملة:** نلزقهم في بعض: `'n-za-mN-ZA-M'`.

بالطريقة دي، إحنا بنقول لأمر `tr`: "كل ما تشوف 'a' اكتب 'n'، وكل ما تشوف 'n' اكتب 'a'"، وهكذا.

---

## 4. الأمر الكامل (من الآخر)

```bash
bandit11@bandit:~$ cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'
The password is JQttfApK4SeyHwDlIusA1337OQ7n3r0h
```

*   **شرح بسيط:**
    1.  `cat data.txt`: بيقرا النص المتشفر بـ ROT13.
    2.  `|`: بيمرر النص ده لأمر `tr`.
    3.  `tr 'a-zA-Z' 'n-za-mN-ZA-M'`: بيستقبل النص، ويبدل كل حرف بالحرف اللي قصاده حسب قاعدة ROT13، ويعرض لنا الناتج اللي هو كلمة السر.

---

## 5. أنا اتعلمت إيه من المستوى ده؟

*   **قوة أمر `tr`:** اتعلمت إن أمر `tr` ده أداة مرنة جدًا ومفيدة في عمليات تبديل وترجمة الحروف المعقدة.
*   **مفاهيم التشفير:** فهمت إزاي شفرات التبديل البسيطة (substitution ciphers) زي ROT13 بتشتغل.
*   **بناء المجموعات المنطقية:** استوعبت أهمية إني أقسم المشكلة لخطوات منطقية عشان أقدر أبني الباراميترز بتاعة الأمر صح (خصوصًا مجموعات الإدخال والإخراج في `tr`).
*   **حل المشاكل:** فهمت إن عشان أحل مشكلة، مش كفاية إني أعرف اسم الأمر، لازم أكون فاهم بعمق هو بيشتغل إزاي من جوه.



---


#### **المفهوم الأساسي: الشفرة كقاموس**

تخيل أن أمر `tr` يبني "قاموس تبديل" من مجموعتين من الحروف.
*   `tr 'مجموعة_الأصل' 'مجموعة_النتيجة'`

**1. للتشفير (بناء قاموس التشفير):**
الهدف: نحرك كل حرف 3 خطوات للأمام (A -> D).

*   **مجموعة الأصل:** هي الحروف العادية بالترتيب.
    `a b c d e f g h i j k l m n o p q r s t u v w x y z`

*   **مجموعة النتيجة (المشفرة):** هي الحروف بعد إزاحتها 3 خطوات.
    *   `a` يتحرك 3 خطوات ليصبح `d`.
    *   `b` يتحرك 3 خطوات ليصبح `e`.
    *   ... وهكذا حتى نصل للحروف الأخيرة التي "تلف" حول الأبجدية:
    *   `x` يتحرك 3 خطوات (`y, z, a`) ليصبح `a`.
    *   `y` يتحرك 3 خطوات (`z, a, b`) ليصبح `b`.
    *   `z` يتحرك 3 خطوات (`a, b, c`) ليصبح `c`.

    إذًا، مجموعة النتيجة هي:
    `d e f g h i j k l m n o p q r s t u v w x y z a b c`

*   **الأمر في `tr`:** نختصر المجموعتين الطويلتين.
    *   الأصل: `'a-zA-Z'`
    *   النتيجة: `'d-za-cD-ZA-C'`
    *   **الأمر الكامل:** `tr 'a-zA-Z' 'd-za-cD-ZA-C'`

**2. لفك التشفير (بناء القاموس العكسي):**
الهدف: نعكس العملية. نحرك كل حرف مشفر 3 خطوات للخلف (D -> A). ببساطة، **نبدل أماكن المجموعتين**.

*   **مجموعة الأصل (التي سنترجمها):** هي الحروف المشفرة.
    `d e f g h i j k l m n o p q r s t u v w x y z a b c`

*   **مجموعة النتيجة (التي نريد الوصول إليها):** هي الحروف العادية بالترتيب.
    `a b c d e f g h i j k l m n o p q r s t u v w x y z`

*   **الأمر في `tr`:** نختصر المجموعتين ونبدل أماكنهما.
    *   الأصل: `'d-za-cD-ZA-C'`
    *   النتيجة: `'a-zA-Z'`
    *   **الأمر الكامل:** `tr 'd-za-cD-ZA-C' 'a-zA-Z'`

**الخلاصة:** فك الشفرة هو مجرد عكس عملية التشفير عن طريق تبديل مجموعة الإدخال ومجموعة الإخراج في أمر `tr`.

---



#### **Temel Kavram: Şifreleme Bir Sözlüktür**

`tr` komutunun iki harf kümesinden bir "değiştirme sözlüğü" oluşturduğunu hayal edin.
*   `tr 'ORİJİNAL_KÜME' 'SONUÇ_KÜMESİ'`

**1. Şifreleme İçin (Şifreleme Sözlüğünü Oluşturma):**
Amaç: Her harfi 3 pozisyon ileri kaydırmak (A -> D).

*   **Orijinal Küme:** Normal sıralı alfabe.
    `a b c d e f g h i j k l m n o p q r s t u v w x y z`

*   **Sonuç Kümesi (Şifreli):** Harflerin 3 pozisyon kaydırılmış hali.
    *   `a`, 3 pozisyon kayarak `d` olur.
    *   `b`, 3 pozisyon kayarak `e` olur.
    *   ... ve alfabenin sonuna gelindiğinde "başa döner":
    *   `x`, 3 pozisyon kayarak (`y, z, a`) `a` olur.
    *   `y`, 3 pozisyon kayarak (`z, a, b`) `b` olur.
    *   `z`, 3 pozisyon kayarak (`a, b, c`) `c` olur.

    Yani, sonuç kümesi şudur:
    `d e f g h i j k l m n o p q r s t u v w x y z a b c`

*   **`tr` Komutu:** Bu uzun kümeleri kısaltırız.
    *   Orijinal: `'a-zA-Z'`
    *   Sonuç: `'d-za-cD-ZA-C'`
    *   **Tam Komut:** `tr 'a-zA-Z' 'd-za-cD-ZA-C'`

**2. Şifre Çözme İçin (Ters Sözlüğü Oluşturma):**
Amaç: İşlemi tersine çevirmek. Her şifreli harfi 3 pozisyon geri kaydırmak (D -> A). Basitçe, **iki kümenin yerini değiştiririz**.

*   **Orijinal Küme (Çevireceğimiz Küme):** Şifreli harfler.
    `d e f g h i j k l m n o p q r s t u v w x y z a b c`

*   **Sonuç Kümesi (Ulaşmak İstediğimiz Küme):** Normal sıralı alfabe.
    `a b c d e f g h i j k l m n o p q r s t u v w x y z`

*   **`tr` Komutu:** Kısaltılmış kümelerin yerini değiştiririz.
    *   Orijinal: `'d-za-cD-ZA-C'`
    *   Sonuç: `'a-zA-Z'`
    *   **Tam Komut:** `tr 'd-za-cD-ZA-C' 'a-zA-Z'`

**Sonuç:** Şifre çözme, `tr` komutundaki girdi ve çıktı kümelerinin yerini değiştirerek şifreleme işlemini tersine çevirmekten ibarettir.