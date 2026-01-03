#### **Bölüm 1: Bu Seviyedeki Anahtar Kavramlar**

Bu seviye, farklı dosya türlerini ve sıkıştırma formatlarını tanıma ve bunlarla başa çıkma becerimizi test eder. İşte karşılaşacağımız temel kavramlar ve araçlar:

|Kavram / Araç|Türkçe Açıklama|Ne İşe Yarar ve Nasıl Kullanılır?|
|---|---|---|
|**Hexdump**|**Onaltılık Döküm**|Bir dosyanın içeriğini, insanlar tarafından okunması zor olan ikili (binary) format yerine, onaltılık (hexadecimal) karakterlerle gösteren bir metin temsilidir.|
|`xxd -r`|**Hexdump'ı Geri Çevir**|`xxd` komutunun `-r` (reverse) seçeneği, bir hexdump metnini alıp orijinal ikili dosyasına geri dönüştürür. **Kullanım:** `xxd -r hexdump.txt > orijinal_dosya`|
|**Sıkıştırma**|**Compression**|Dosya boyutunu küçültmek için verileri özel bir algoritma kullanarak yeniden düzenleme işlemidir. Farklı algoritmalar farklı formatlar oluşturur.|
|`file`|**Dosya Türü Teşhisi**|Bir dosyanın içeriğini analiz ederek türünü (örneğin, metin, resim, gzip, bzip2, tar) belirleyen **en önemli teşhis aracımızdır**. **Kullanım:** `file dosya_adı`|
|**`gzip`**|**GNU Zip**|Yaygın bir sıkıştırma formatıdır. `.gz` uzantılı dosyalar bu formatı kullanır.|
|`gzip -d`|**Gzip'i Aç**|`.gz` uzantılı bir dosyayı açar (decompress). **Kullanım:** `gzip -d dosya.gz`|
|**`bzip2`**|**Bzip2**|Genellikle `gzip`'ten daha iyi sıkıştırma sağlayan başka bir popüler sıkıştırma formatıdır. `.bz2` uzantılı dosyalar bu formatı kullanır.|
|`bzip2 -d`|**Bzip2'yi Aç**|`.bz2` uzantılı bir dosyayı açar. **Kullanım:** `bzip2 -d dosya.bz2`|
|**`tar`**|**Tape Archive**|Kendi başına bir sıkıştırma aracı değildir. Asıl görevi, birden çok dosya ve dizini tek bir **arşiv dosyasında (`.tar`)** birleştirmektir. Genellikle bu `.tar` dosyası daha sonra `gzip` veya `bzip2` ile sıkıştırılır (örn. `dosya.tar.gz`).|
|`tar -xf`|**Tar Arşivini Çıkar**|Bir `.tar` arşivinin içindeki dosyaları ve dizinleri dışarı çıkarır (extract). **Kullanım:** `tar -xf arsiv.tar`|
#### **Bölüm 2: Görevi Anlamak ve Plan Yapmak**

![[21.png]] "Bir sonraki seviyenin şifresi, tekrar tekrar sıkıştırılmış bir dosyanın **hexdump**'ı olan `data.txt` dosyasında saklıdır. Bu seviye için **/tmp** altında çalışabileceğiniz bir dizin oluşturmanız faydalı olabilir..."

**Hedefin Zihinsel Analizi:**

1. **Nerede?** `data.txt` dosyasında.
2. **Ne?** Bu dosya bir **hexdump**. İlk görevimiz `xxd -r` komutu ile bu işlemi tersine çevirmektir.
3. **Asıl Problem Ne?** Orijinal dosya **tekrar tekrar sıkıştırılmış**. Bu, tek bir komutun yetmeyeceği ve bir döngüye girmemiz gerektiği anlamına gelir.
4. **Görev Ne?** Bu sıkıştırma katmanlarını, bir soğanı soyar gibi, teker teker açmak. Her katmanı açtıktan sonra, bir sonraki katmanın ne olduğunu `file` komutu ile teşhis etmeli ve doğru aracı kullanmalıyız.
5. **İpucu:** Hedef, `/tmp` altında çalışmamızı öneriyor. `mktemp -d` bunun için mükemmel bir araçtır.
**İlk Plan:**

1. `mktemp -d` ile güvenli bir çalışma alanı oluştur.
2. `data.txt` dosyasını bu alana kopyala.
3. `xxd -r` ile hexdump'ı ikili bir dosyaya geri çevir.
4. Bir döngü başlat: a. `file` ile mevcut dosyanın türünü **kontrol et**. b. Tespite göre doğru aracı (`gzip`, `bzip2`, `tar` vb.) kullanarak dosyayı **aç**. c. "ASCII text" türünde bir dosya elde edene kadar bu döngüyü **tekrarla**.
5. `cat` ile son dosyayı oku.

#### **Bölüm 3: Çözüm Yolculuğu (İki Yöntem)**

Bu seviyeyi çözmek için iki yaklaşım vardır: sıkıcı ama öğretici olan manuel yöntem ve hızlı ve verimli olan otomatik (script) yöntem.

##### **Yöntem A: Manuel Çözüm (Sıkıcı Ama Öğretici Döngü)**

Bu yöntem, "Kontrol Et -> Uygula -> Tekrarla" döngüsünü tam olarak anlamanızı sağlar. İşte gerçek terminal çıktılarınızla adım adım çözüm:

**Adım 0: Çalışma Alanını Hazırlama**

Bash

```
bandit12@bandit:~$ WORKDIR=$(mktemp -d)
bandit12@bandit:~$ cd $WORKDIR
bandit12@bandit:~$ cp /home/bandit12/data.txt .
```

**Adım 1: Hexdump'ı Geri Çevirme**

Bash

```
bandit12@bandit:~$ xxd -r data.txt > file.bin
```

**Adım 2: Sıkıştırma Döngüsü** Şimdi, her adımda aynı iki komutu tekrar edeceğiz: `file` ve doğru açma aracı.

Bash

```
# ----- Döngü 1 (gzip) -----
bandit12@bandit:~$ file file.bin
file.bin: gzip compressed data...
bandit12@bandit:~$ mv file.bin file.gz && gzip -d file.gz

# ----- Döngü 2 (bzip2) -----
bandit12@bandit:~$ file file
file: bzip2 compressed data...
bandit12@bandit:~$ mv file file.bz2 && bzip2 -d file.bz2

# ----- Döngü 3 (gzip) -----
bandit12@bandit:~$ file file
file: gzip compressed data...
bandit12@bandit:~$ mv file file.gz && gzip -d file.gz

# ----- Döngü 4 (tar) -----
bandit12@bandit:~$ file file
file: POSIX tar archive (GNU)
bandit12@bandit:~$ mv file file.tar && tar -xf file.tar
# 'ls' komutu 'data5.bin' adında yeni bir dosya gösterir

# ----- Döngü 5 (tar) -----
bandit12@bandit:~$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:~$ mv data5.bin file.tar && tar -xf file.tar
# 'ls' komutu 'data6.bin' adında yeni bir dosya gösterir

# ----- Döngü 6 (bzip2) -----
bandit12@bandit:~$ file data6.bin
data6.bin: bzip2 compressed data...
bandit12@bandit:~$ mv data6.bin file.bz2 && bzip2 -d file.bz2

# ----- Döngü 7 (tar) -----
bandit12@bandit:~$ file file
file: POSIX tar archive (GNU)
bandit12@bandit:~$ mv file file.tar && tar -xf file.tar
# 'ls' komutu 'data8.bin' adında yeni bir dosya gösterir

# ----- Döngü 8 (gzip) -----
bandit12@bandit:~$ file data8.bin
data8.bin: gzip compressed data...
bandit12@bandit:~$ mv data8.bin file.gz && gzip -d file.gz
```

**Adım 3: Sonucu Okuma** Bu sıkıcı döngüden sonra, son bir kontrol yapalım:

Bash

```
bandit12@bandit:~$ file file
file: ASCII text
```

"ASCII text"! Başardık. Şimdi şifreyi okuyabiliriz:

Bash

```
bandit12@bandit:~$ cat file
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

##### **Yöntem B: Otomatik Çözüm (Uzman Script'i)**

Manuel yöntemde yaptığımız tekrarlayan işi fark ettikten sonra, bir uzman bu süreci otomatikleştirmek için küçük bir script yazar. Bu hem daha hızlıdır hem de hata yapma olasılığını ortadan kaldırır.

Aşağıdaki script'i kopyalayıp terminale yapıştırmanız yeterlidir.

**Otomasyon Script'i:**

Bash

```
#!/bin/bash
WORKDIR=$(mktemp -d) && cd $WORKDIR && cp /home/bandit12/data.txt . && xxd -r data.txt > file
while [[ $(file -b file) != "ASCII text" ]]; do
    TYPE=$(file -b file | cut -d' ' -f1)
    case $TYPE in
        gzip) mv file temp.gz; gzip -d temp.gz; OLDFILE="temp";;
        bzip2) mv file temp.bz2; bzip2 -d temp.bz2; OLDFILE="temp";;
        POSIX) mv file temp.tar; tar -xf temp.tar; OLDFILE=$(ls | grep -v temp.tar);;
    esac
    mv $OLDFILE file
done
echo "--- ÇÖZÜM BULUNDU! ---" && cat file
```

**Script Açıklaması:**

- `while [[ $(file -b file) != "ASCII text" ]]`: Bu bir döngü oluşturur. "Dosya türü 'ASCII text' olmadığı sürece bu döngüye devam et" anlamına gelir.
- `TYPE=$(... | cut -d' ' -f1)`: `file` komutunun çıktısından sadece ilk kelimeyi (`gzip`, `bzip2`, `POSIX`) alır.
- `case $TYPE in ... esac`: `TYPE` değişkeninin değerine göre doğru komutu çalıştırır (`gzip -d`, `bzip2 -d`, veya `tar -xf`).
- Döngü, dosya türü "ASCII text" olduğunda otomatik olarak durur ve son olarak `cat file` komutuyla şifreyi ekrana basar.

#### **Nihai Sonuç (Öğrenilen Ders)**

Bu seviyenin asıl dersi **otomasyonun gücüdür**.

1. **Önce Manuel Yap:** Problemi anlamak için süreci bir veya iki kez manuel olarak yapın.
2. **Deseni Fark Et:** Tekrarlayan bir desen (`file` -> `decompress`) fark ettiğinizde, bu otomasyon için bir işarettir.
3. **Otomatikleştir:** Tekrarlayan görevi bir script'e dönüştürerek zamandan kazanın ve insan hatasını ortadan kaldırın. Bu, basit bir problem çözücü ile uzman bir sistem yöneticisi arasındaki farktır.
---
---
---
#### **الجزء الأول: المفاهيم الأساسية في هذا المستوى**

يختبر هذا المستوى قدرتنا على التعرف على أنواع الملفات وتنسيقات الضغط المختلفة والتعامل معها. إليك المفاهيم والأدوات الأساسية التي سنواجهها:

|المفهوم / الأداة|الشرح بالعربية|ما فائدته وكيف يُستخدم؟|
|---|---|---|
|**Hexdump**|**تفريغ سداسي عشري**|هو تمثيل نصي لمحتوى ملف ما باستخدام الأحرف السداسية عشرية (hexadecimal)، بدلاً من الصيغة الثنائية (binary) التي يصعب على البشر قراءتها.|
|`xxd -r`|**عكس التفريغ السداسي عشري**|الخيار `-r` (reverse) في أمر `xxd` يأخذ نص التفريغ السداسي عشري ويعيد تحويله إلى ملفه الثنائي الأصلي. **الاستخدام:** `xxd -r hexdump.txt > original_file`|
|**الضغط**|**Compression**|عملية إعادة ترتيب البيانات باستخدام خوارزمية معينة لتقليل حجم الملف. الخوارزميات المختلفة تُنشئ تنسيقات مختلفة.|
|`file`|**تشخيص نوع الملف**|هي **أهم أداة تشخيص لدينا**. تقوم بتحليل محتوى الملف لتحديد نوعه (على سبيل المثال: نص، صورة، gzip، bzip2، tar). **الاستخدام:** `file file_name`|
|**`gzip`**|**GNU Zip**|تنسيق ضغط شائع جدًا. الملفات ذات الامتداد `.gz` تستخدم هذا التنسيق.|
|`gzip -d`|**فك ضغط Gzip**|يقوم بفك ضغط (decompress) ملف بامتداد `.gz`. **الاستخدام:** `gzip -d file.gz`|
|**`bzip2`**|**Bzip2**|تنسيق ضغط شائع آخر، وغالبًا ما يوفر ضغطًا أفضل من `gzip`. الملفات ذات الامتداد `.bz2` تستخدم هذا التنسيق.|
|`bzip2 -d`|**فك ضغط Bzip2**|يقوم بفك ضغط ملف بامتداد `.bz2`. **الاستخدام:** `bzip2 -d file.bz2`|
|**`tar`**|**Tape Archive**|ليست أداة ضغط بحد ذاتها. مهمتها الأساسية هي دمج عدة ملفات ومجلدات في **ملف أرشيف واحد (`.tar`)**. عادةً ما يتم ضغط ملف `.tar` هذا لاحقًا باستخدام `gzip` أو `bzip2` (مثال: `file.tar.gz`).|
|`tar -xf`|**استخراج أرشيف Tar**|يقوم باستخراج (extract) الملفات والمجلدات الموجودة داخل أرشيف `.tar`. **الاستخدام:** `tar -xf archive.tar`|

#### **الجزء الثاني: فهم المهمة ووضع الخطة**

![[21.png]] "كلمة السر للمستوى التالي مخزنة في ملف `data.txt`، وهو عبارة عن **hexdump** لملف تم **ضغطه بشكل متكرر**. قد يكون من المفيد لهذا المستوى إنشاء مجلد تحت **/tmp** يمكنك العمل فيه..."

**التحليل الذهني للهدف:**

1. **أين؟** في ملف `data.txt`.
2. **ماذا؟** هذا الملف هو **hexdump**. مهمتنا الأولى هي عكس هذه العملية باستخدام `xxd -r`.
3. **المشكلة الحقيقية؟** الملف الأصلي تم **ضغطه بشكل متكرر**. هذا يعني أن أمرًا واحدًا لن يكون كافيًا، وأننا بحاجة إلى الدخول في حلقة (loop).
4. **المهمة؟** تقشير طبقات الضغط هذه واحدة تلو الأخرى، مثل تقشير بصلة. بعد كل عملية فك ضغط، يجب أن نشخص نوع الطبقة التالية باستخدام `file` ونستخدم الأداة الصحيحة.
5. **تلميح:** الهدف ينصحنا بالعمل في `/tmp`. `mktemp -d` هو الأداة المثالية لذلك.

**الخطة المبدئية:**

1. أنشئ مساحة عمل آمنة باستخدام `mktemp -d`.
2. انسخ ملف `data.txt` إلى هذه المساحة.
3. حوّل الـ hexdump إلى ملف ثنائي باستخدام `xxd -r`.
4. ابدأ حلقة: أ. **تحقق** من نوع الملف الحالي باستخدام `file`. ب. **نفّذ** عملية فك الضغط باستخدام الأداة الصحيحة (`gzip`, `bzip2`, `tar`... إلخ). ج. **كرر** هذه الحلقة حتى تحصل على ملف من نوع "ASCII text".
5. اقرأ الملف النهائي باستخدام `cat`.

#### **الجزء الثالث: رحلة الحل (طريقتان)**

لحل هذا المستوى، هناك نهجان: الطريقة اليدوية المملة ولكنها تعليمية، والطريقة التلقائية (السكريبت) السريعة والفعالة.

##### **الطريقة (أ): الحل اليدوي (الحلقة المملة والتعليمية)**

تجعلك هذه الطريقة تفهم حلقة "تحقق -> نفذ -> كرر" تمامًا. إليك الحل خطوة بخطوة بناءً على مخرجاتك الحقيقية من الطرفية:

**الخطوة 0: تجهيز مساحة العمل**

Bash

```
bandit12@bandit:~$ WORKDIR=$(mktemp -d)
bandit12@bandit:~$ cd $WORKDIR
bandit12@bandit:~$ cp /home/bandit12/data.txt .
```

**الخطوة 1: عكس التفريغ السداسي عشري**

Bash

```
bandit12@bandit:~$ xxd -r data.txt > file.bin
```

**الخطوة 2: حلقة فك الضغط** الآن، سنكرر نفس الأمرين في كل خطوة: `file` والأداة المناسبة لفك الضغط.

Bash

```
# ----- الحلقة 1 (gzip) -----
bandit12@bandit:~$ file file.bin
file.bin: gzip compressed data...
bandit12@bandit:~$ mv file.bin file.gz && gzip -d file.gz

# ----- الحلقة 2 (bzip2) -----
bandit12@bandit:~$ file file
file: bzip2 compressed data...
bandit12@bandit:~$ mv file file.bz2 && bzip2 -d file.bz2

# ----- الحلقة 3 (gzip) -----
bandit12@bandit:~$ file file
file: gzip compressed data...
bandit12@bandit:~$ mv file file.gz && gzip -d file.gz

# ----- الحلقة 4 (tar) -----
bandit12@bandit:~$ file file
file: POSIX tar archive (GNU)
bandit12@bandit:~$ mv file file.tar && tar -xf file.tar
# أمر 'ls' سيظهر ملفًا جديدًا باسم 'data5.bin'

# ----- الحلقة 5 (tar) -----
bandit12@bandit:~$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:~$ mv data5.bin file.tar && tar -xf file.tar
# أمر 'ls' سيظهر ملفًا جديدًا باسم 'data6.bin'

# ----- الحلقة 6 (bzip2) -----
bandit12@bandit:~$ file data6.bin
data6.bin: bzip2 compressed data...
bandit12@bandit:~$ mv data6.bin file.bz2 && bzip2 -d file.bz2

# ----- الحلقة 7 (tar) -----
bandit12@bandit:~$ file file
file: POSIX tar archive (GNU)
bandit12@bandit:~$ mv file file.tar && tar -xf file.tar
# أمر 'ls' سيظهر ملفًا جديدًا باسم 'data8.bin'

# ----- الحلقة 8 (gzip) -----
bandit12@bandit:~$ file data8.bin
data8.bin: gzip compressed data...
bandit12@bandit:~$ mv data8.bin file.gz && gzip -d file.gz
```

**الخطوة 3: قراءة النتيجة** بعد هذه الحلقة المملة، لنجرِ فحصًا أخيرًا:

Bash

```
bandit12@bandit:~$ file file
file: ASCII text
```

"ASCII text"! لقد نجحنا. الآن يمكننا قراءة كلمة السر:

Bash

```
bandit12@bandit:~$ cat file
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```

##### **الطريقة (ب): الحل التلقائي (سكريبت الخبراء)**

بعد ملاحظة العمل المتكرر في الطريقة اليدوية، يقوم الخبير بكتابة سكريبت صغير لأتمتة هذه العملية. هذا أسرع ويزيل احتمالية الخطأ البشري.

يكفي نسخ ولصق السكريبت التالي في الطرفية.

**سكريبت الأتمتة:**

Bash

```
#!/bin/bash
WORKDIR=$(mktemp -d) && cd $WORKDIR && cp /home/bandit12/data.txt . && xxd -r data.txt > file
while [[ $(file -b file) != "ASCII text" ]]; do
    TYPE=$(file -b file | cut -d' ' -f1)
    case $TYPE in
        gzip) mv file temp.gz; gzip -d temp.gz; OLDFILE="temp";;
        bzip2) mv file temp.bz2; bzip2 -d temp.bz2; OLDFILE="temp";;
        POSIX) mv file temp.tar; tar -xf temp.tar; OLDFILE=$(ls | grep -v temp.tar);;
    esac
    mv $OLDFILE file
done
echo "--- تم العثور على الحل! ---" && cat file
```

**شرح السكريبت:**

- `while [[ $(file -b file) != "ASCII text" ]]`: يُنشئ حلقة تستمر طالما أن نوع الملف ليس "ASCII text".
- `TYPE=$(... | cut -d' ' -f1)`: يأخذ ناتج أمر `file` ويقتطع منه الكلمة الأولى فقط (`gzip`, `bzip2`, `POSIX`).
- `case $TYPE in ... esac`: تعمل مثل جملة `if/else`. بناءً على قيمة المتغير `TYPE`، تقوم بتشغيل الأمر الصحيح (`gzip -d`, `bzip2 -d`, أو `tar -xf`).
- تتوقف الحلقة تلقائيًا عندما يصبح نوع الملف "ASCII text"، وأخيرًا يقوم الأمر `cat file` بطباعة كلمة السر.

#### **الخلاصة النهائية (الدرس المستفاد)**

الدرس الحقيقي في هذا المستوى هو **قوة الأتمتة (Automation)**.

1. **نفذ يدويًا أولاً:** قم بالعملية يدويًا مرة أو مرتين لفهم المشكلة.
2. **لاحظ النمط:** عندما تلاحظ نمطًا متكررًا (`file` -> `decompress`)، فهذه إشارة للأتمتة.
3. **أتمِت العملية:** حوّل المهمة المتكررة إلى سكريبت لتوفير الوقت والقضاء على الأخطاء البشرية. هذا هو الفارق بين مجرد حل المشاكل وبين إدارة الأنظمة كخبير.

---

## 🇺🇸 English

### Objective
Decompress a repeatedly compressed hexdump file to find the password.

### Process
1. Convert hexdump to binary: `xxd -r data.txt > file`
2. Check file type: `file file`
3. Decompress based on type:
   - gzip: `mv file temp.gz && gzip -d temp.gz`
   - bzip2: `mv file temp.bz2 && bzip2 -d temp.bz2`
   - tar: `tar -xf file`
4. Repeat steps 2-3 until you get ASCII text

### Key Takeaways
- **Hexdump:** Binary represented as hex; use `xxd -r` to reverse
- **Compression Tools:** `gzip`, `bzip2`, `tar` for different formats
- **Automation:** Repetitive tasks can be scripted for efficiency
```