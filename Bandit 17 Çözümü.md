### **Bandit Seviye 17 -> 18 (Karşılaştırma Sanatı)**

Bu seviye, bize dijital analiz ve programlamada temel bir dersi öğretir: iki veri kümesi arasındaki farkları nasıl bulacağımızı. Çoğu zaman, önemli bilgi verinin kendisinde değil, üzerinde meydana gelen **değişikliktedir**.

#### **Bölüm 1: Olay Mahalli Analizi (Hedefi Anlamak)**

![[26.png]] "Ev dizininde iki dosya var: `passwords.old` ve `passwords.new`. Bir sonraki seviyenin şifresi `passwords.new` dosyasındadır ve `passwords.old` ile `passwords.new` arasında değiştirilmiş olan tek satırdır."

**Hedefin Zihinsel Analizi:**

1. **Deliller:** `passwords.old` ve `passwords.new` adında iki dosyamız var.
2. **Görev:** Hedef çok açık ve nettir. Şifrenin "değiştirilmiş olan tek satır" olduğunu söylüyor.
3. **Sonuç:** Görevimiz tek bir dosyanın içinde arama yapmak değil, iki dosyayı birbiriyle **karşılaştırmak** ve aralarındaki tek farkı çıkarmaktır.

**İlk Plan (Dedektif Planı):**

1. **Giriş:** Seviye 16'dan aldığımız SSH anahtarını kullanarak `bandit17`'ye giriş yap.
2. **Keşif:** Dosyaların varlığını doğrulamak için `ls -la` komutunu kullan.
3. **Karşılaştırma:** Farklı satırı bulmak için dosya karşılaştırmada uzmanlaşmış bir araç kullan. Bu görev için mükemmel araç `diff`'tir.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

Bunlar, bu seviye için oyunun önerdiği araçlardır.

|Araç|Türkçe Açıklama|Ne Zaman Kullanmayı Düşünmeliyiz?|
|---|---|---|
|**`diff`**|**Dosya Karşılaştırıcı (Difference)**|**Bu seviye için mükemmel ve temel araç.** Tek işlevi, iki dosyayı satır satır karşılaştırmak ve aralarındaki farkları göstermektir. Hedef bizden "değiştirilmiş tek satırı" bulmamızı istediği için, `diff` ilk ve en doğal seçenektir. **Kullanım:** `diff dosya1 dosya2`|
|**`grep`**|**Desen Arama Aracı**|`grep`, bu bulmacayı çözmek için karmaşık bir şekilde kullanılabilir (örneğin, `passwords.new`'deki her satırı `passwords.old` içinde aramak ve eşleşmesi olmayan satırı göstermek), ancak bu `diff`'e kıyasla uzun ve dolaylı bir yoldur.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Dosyaları gözlerimizle karşılaştırmak istersek içeriklerini ekranda görüntülemek için kullanırız, ancak dosyalar binlerce benzer satır içerdiği için bu pratik olarak imkansızdır.|
|**`ls`**|**Dizin İçeriğini Listeleme**|Başlangıçta `passwords.old` ve `passwords.new` dosyalarının varlığından emin olmak için bir keşif adımı olarak kullanırız.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: Olay Mahaline Giriş**

Öncelikle, bir önceki seviyeden aldığımız SSH anahtarını kullanarak yerel makinemizden (Kali) `bandit17`'ye giriş yaparız.

Bash

```
┌──(root㉿kali)-[~]
└─# ssh -i bandit17.key -p 2220 bandit17@bandit.labs.overthewire.org
...
bandit17@bandit:~$ 
```

**Analiz:** Giriş başarılı. Artık `bandit17` hesabının içindeyiz.

##### **Aşama 2: Delillerin Keşfi**

Bizi neyin beklediğini görmek için `ls -la` kullanırız.

Bash

```
bandit17@bandit:~$ ls -la
...
-rw-r-----   1 bandit18 bandit17 3300 Oct 14 09:26 passwords.new
-rw-r-----   1 bandit18 bandit17 3300 Oct 14 09:26 passwords.old
...
```

**Analiz:** Dosyaların varlığı doğrulandı. Boyutları aynı, bu da değişikliğin çok küçük olduğu anlamına gelir (neredeyse aynı sayıda karaktere sahip bir satır değiştirilmiş).

##### **Aşama 3: Belirleyici Aracın Kullanımı ve Gerçeğin Ortaya Çıkarılması**

Şimdi, bu görev için özel olarak tasarlanmış aracı, `diff`'i, iki dosyayı karşılaştırmak için kullanıyoruz.

Bash

```
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< pGozC8kOHLkBMOaL0ICPvLV1IjQ5F1VA
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

**Çıktının Detaylı Analizi:**

- `42c42`: Bu, `diff`'ten gelen teknik bir koddur ve "ilk dosyadaki **42.** satır, ikinci dosyadaki **42.** satıra **değiştirildi (changed)**" anlamına gelir.
- `< pGozC8kOHLkBMOaL0ICPvLV1IjQ5F1VA`: `<` işareti, **birinci dosyadan** (`passwords.old`) bir satırı belirtir.
- `---`: Bu, silinen ve eklenen arasındaki ayırıcı çizgidir.
- `> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`: `>` işareti, **ikinci dosyadan** (`passwords.new`) bir satırı belirtir.

**Nihai Karar:** `diff` komutu bize açıkça iki dosya arasındaki tek farkın 42. satırda olduğunu söylüyor. `passwords.new` dosyasındaki yeni satır, aradığımız şifredir.

**`bandit18` seviyesinin şifresi: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`**

---

---

### **دليل Bandit التعليمي: المستوى 17 -> 18 (فن المقارنة)**

هذا المستوى يعلمنا درسًا أساسيًا في التحليل الرقمي والبرمجة: كيفية العثور على الفروقات بين مجموعتين من البيانات. في كثير من الأحيان، تكون المعلومة الهامة ليست في البيانات نفسها، بل في **التغيير** الذي طرأ عليها.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**
![[25.png]]
 "يوجد ملفان في المجلد الرئيسي: `passwords.old` و `passwords.new`. كلمة السر للمستوى التالي موجودة في `passwords.new` وهي السطر الوحيد الذي تم تغييره بين `passwords.old` و `passwords.new`."

**التحليل الذهني للهدف:**

1. **الأدلة:** لدينا ملفان، `passwords.old` و `passwords.new`.
2. **المهمة:** الهدف واضح ومباشر جدًا. يخبرنا أن كلمة السر هي "السطر الوحيد الذي تم تغييره".
3. **الاستنتاج:** مهمتنا ليست البحث داخل ملف واحد، بل **مقارنة** الملفين ببعضهما البعض واستخراج الاختلاف الوحيد بينهما.

**الخطة المبدئية (خطة المحقق):**

1. **تسجيل الدخول:** استخدم مفتاح SSH الذي حصلنا عليه من المستوى 16 للدخول إلى `bandit17`.
2. **الاستكشاف:** استخدم الأمر `ls -la` لتأكيد وجود الملفين.
3. **المقارنة:** استخدم أداة متخصصة في مقارنة الملفات للعثور على السطر المختلف. الأداة المثالية لهذه المهمة هي `diff`.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

هذه هي الأدوات التي اقترحتها اللعبة لهذا المستوى.

|الأداة|الشرح بالعربية|متى نفكر في استخدامها؟|
|---|---|---|
|**`diff`**|**مقارن الملفات (Difference)**|**الأداة المثالية والأساسية لهذا المستوى.** وظيفتها الوحيدة هي مقارنة ملفين سطراً بسطر وإظهار الاختلافات بينهما. بما أن الهدف يطلب منا العثور على "السطر الوحيد الذي تم تغييره"، فإن `diff` هو الخيار الأول والطبيعي. **الاستخدام:** `diff file1 file2`|
|**`grep`**|**أداة البحث عن الأنماط**|يمكن استخدام `grep` بطريقة معقدة لحل هذا اللغز (على سبيل المثال، البحث عن كل سطر من `passwords.new` داخل `passwords.old` وعرض السطر الذي لا يوجد له مثيل)، لكنها طريقة طويلة وغير مباشرة مقارنة بـ `diff`.|
|**`cat`**|**عرض محتوى الملفات**|نستخدمه لعرض محتوى الملفين على الشاشة إذا أردنا مقارنتهما بأعيننا، ولكن هذا مستحil عمليًا لأن الملفات تحتوي على آلاف السطور المتشابهة.|
|**`ls`**|**عرض محتويات المجلد**|نستخدمه في البداية كخطوة استكشافية للتأكد من وجود الملفين `passwords.old` و `passwords.new`.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط.

##### **المرحلة 1: الدخول إلى مسرح الجريمة**

أولاً، نستخدم مفتاح SSH الذي حصلنا عليه من المستوى السابق للدخول إلى `bandit17` من جهازنا المحلي (كالي).

Bash

```
┌──(root㉿kali)-[~]
└─# ssh -i bandit17.key -p 2220 bandit17@bandit.labs.overthewire.org
...
Welcome to OverTheWire!
...
bandit17@bandit:~$ 
```

**التحليل:** نجحنا في الدخول. نحن الآن داخل حساب `bandit17`.

##### **المرحلة 2: استكشاف الأدلة**

نستخدم `ls -la` لنرى ما ينتظرنا.

Bash

```
bandit17@bandit:~$ ls -la
total 36
drwxr-xr-x   3 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 150 root     root     4096 Oct 14 09:29 ..
...
-rw-r-----   1 bandit18 bandit17 3300 Oct 14 09:26 passwords.new
-rw-r-----   1 bandit18 bandit17 3300 Oct 14 09:26 passwords.old
...
```

**التحليل:** تم تأكيد وجود الملفين. حجمهما متطابق، مما يعني أن التغيير طفيف جدًا (سطر واحد تم تعديله بنفس عدد الأحرف تقريبًا).

##### **المرحلة 3: استخدام الأداة الحاسمة وكشف الحقيقة**

الآن نستخدم الأداة المصممة خصيصًا لهذه المهمة، `diff`، لمقارنة الملفين.

Bash

```
bandit17@bandit:~$ diff passwords.old passwords.new
42c42
< pGozC8kOHLkBMOaL0ICPvLV1IjQ5F1VA
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

**التحليل المفصل للناتج:**

- `42c42`: هذا رمز من `diff` يعني "السطر رقم **42** في الملف الأول تم **تغييره (changed)** إلى السطر رقم **42** في الملف الثاني".
- `< pGozC8kOHLkBMOaL0ICPvLV1IjQ5F1VA`: علامة `<` تشير إلى سطر من **الملف الأول** (`passwords.old`).
- `---`: هذا مجرد خط فاصل بين ما تم حذفه وما تم إضافته.
- `> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`: علامة `>` تشير إلى سطر من **الملف الثاني** (`passwords.new`).

**الحكم النهائي:** الأمر `diff` يخبرنا بوضوح أن الاختلاف الوحيد بين الملفين هو في السطر 42. السطر الجديد في ملف `passwords.new` هو كلمة السر التي نبحث عنها.

**كلمة السر للمستوى `bandit18` هي: `x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO`**

---

## 🇺🇸 English

### Objective
Find the only line that differs between `passwords.old` and `passwords.new`.

### Command
```bash
diff passwords.old passwords.new
42c42
< pGozC8kOHLkBMOaL0ICPvLV1IjQ5F1VA
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

### Explanation
- `diff`: Compares two files line by line
- `42c42`: Line 42 was changed
- `<`: Content from first file (old)
- `>`: Content from second file (new) - **This is the password**

### Key Takeaways
- **`diff` Command:** Essential for comparing files and finding changes