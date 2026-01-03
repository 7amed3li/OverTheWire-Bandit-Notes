

---
# Bandit 4 -> 5 Çözümü

## 1. Amaç (Objective)
`inhere` dizinindeki çok sayıda dosya arasından, insan tarafından okunabilir (human-readable) tek metin dosyasını bulmak ve içindeki şifreyi elde etmek.

---

## 2. Olası Yöntemler (Possible Methods)

*   **Yöntem A (En Verimli):** `file` komutunu kullanarak her dosyanın türünü belirlemek. `file` komutu, bir dosyanın "ASCII text" mi yoksa "data" (binary) mı olduğunu bize söyler.
*   **Yöntem B (Deneme Yanılma):** Bir `for` döngüsü veya `cat *` gibi bir komutla tüm dosyaların içeriğini ekrana basmak ve okunabilir metin içeren çıktıyı gözle aramak. Bu yöntem, çok sayıda dosya olduğunda verimsiz ve kafa karıştırıcıdır.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `inhere` dizinine girme
```bash
bandit4@bandit:~$ cd inhere
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
```

### Adım 2: Dosya türlerini belirlemek için `file` komutunu kullanma
`*` (wildcard) karakteri, "tüm dosyalar" anlamına gelir ve `file` komutunun tüm dosyaları tek seferde analiz etmesini sağlar.
```bash
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data 
./-file07: ASCII text
./-file08: data
./-file09: data
```
Bu çıktı, `-file06` dosyasının okunabilir metin içerdiğini açıkça gösterir.

### Adım 3: Şifreyi okuma
```bash
bandit4@bandit:~/inhere$ cat ./-file07
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```
*Not: Dosya adı `-` ile başladığı için, belirsizliği önlemek amacıyla `./` göreli yolunu kullanmak iyi bir pratiktir.*

---

## 4. Öğrenilenler (What I Learned)
- **`file` Komutu:** `file` komutunun, bir dosyanın içeriğini açmadan türünü (metin, resim, program, vb.) hızlıca belirlemek için ne kadar güçlü ve kullanışlı bir araç olduğunu öğrendim.
- **Wildcards (`*`):** `*` gibi wildcard karakterlerinin, bir komutu birden çok dosyaya aynı anda uygulamak için ne kadar zaman kazandırıcı olduğunu anladım.
- **Veri Türleri:** Dosyaların sadece metin içermediğini, aynı zamanda "data" olarak adlandırılan ve doğrudan okunması anlamsız olan ikili (binary) veriler de içerebileceğini öğrendim.

---

# حل المستوى 4 -> 5 (بانديت)

## 1. الهدف من المستوى ده إيه؟

المطلوب مننا نلاقي كلمة السر اللي موجودة في ملف نصي **وحيد** ينفع يتقري (human-readable)، وسط مجموعة كبيرة من الملفات التانية اللي موجودة جوه فولدر `inhere`.

---

## 2. إزاي ممكن نحلها؟ (الطرق الممكنة)

*   **الطريقة الأولى (الأكثر كفاءة):** نستخدم أمر `file` عشان نحدد نوع كل ملف. أمر `file` ده عامل زي الخبير اللي بيبص على الملف من غير ما يفتحه ويقولنا ده ملف نصي عادي (ASCII text) ولا ملف بيانات (data/binary) مش مفهوم.

*   **الطريقة التانية (التجربة والخطأ):** نستخدم حلقة `for` أو أمر زي `cat *` عشان نعرض محتوى كل الملفات على الشاشة، ونقعد ندور بعينينا على الكلام المفهوم. الطريقة دي متعبة جدًا وممكن تلخبط لو عدد الملفات كبير.

---

## 3. الأوامر الكاملة (من الآخر)

### الخطوة الأولى: ندخل فولدر `inhere`

```bash
bandit4@bandit:~$ cd inhere
bandit4@bandit:~/inhere$ ls
-file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
```

### الخطوة التانية: نستخدم أمر `file` عشان نحدد نوع الملفات

علامة `*` (النجمة) دي بنسميها "wildcard"، ومعناها "كل الملفات". استخدامها بيخلي أمر `file` يحلل كل الملفات اللي في الفولدر مرة واحدة.
```bash
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data 
./-file07: ASCII text
./-file08: data
./-file09: data
```
الناتج ده بيوضح لنا بصراحة إن الملف اللي اسمه `-file07` هو الوحيد اللي بيحتوي على نص ينفع يتقري.

### الخطوة التالتة: نقرا كلمة السر

```bash
bandit4@bandit:~/inhere$ cat ./-file07
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```
*ملحوظة: عشان اسم الملف بيبدأ بعلامة `-`، من الأفضل دايمًا نستخدم المسار `./` قبله عشان نمنع أي لخبطة.*

---

## 4. أنا اتعلمت إيه من المستوى ده؟

*   **أمر `file`:** اتعلمت قد إيه أمر `file` ده قوي ومفيد عشان أعرف نوع أي ملف بسرعة (سواء كان نص، صورة، برنامج، ...إلخ) من غير ما أضطر أفتحه.
*   **الـ Wildcards (زي `*`):** فهمت إن الـ wildcards زي النجمة `*` بتوفر وقت ومجهود كبير جدًا لما أكون عايز أطبق أمر معين على ملفات كتير في نفس الوقت.
*   **أنواع البيانات:** اتعلمت إن الملفات مش كلها نصوص بس، في أنواع تانية زي "البيانات" (data) أو الملفات الثنائية (binary)، ودي بتكون بيانات مش مفهومة لو حاولت تقراها مباشرة كأنها نص.

---

## 🇺🇸 English

### Objective
Find the only human-readable text file among many files in the `inhere` directory.

### Method
Use the `file` command to determine each file's type quickly without opening them.

### Commands
```bash
cd inhere
file ./*
cat ./-file07
```

### Output Analysis
```
./-file07: ASCII text   # This is the human-readable file!
./-file00: data         # Binary data, not readable
```

### Key Takeaways
- **`file` Command:** Quickly identifies file types (text, image, binary, etc.) without opening them
- **Wildcards (`*`):** Apply commands to multiple files at once
- **Data Types:** Not all files are text; some contain binary data that's meaningless when read as text
