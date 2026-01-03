
### **Bandit Seviye 31 -> 32 (Sunucuyu Tetiklemek) Kılavuzu**

Bu seviye, Git'i pasif bir bilgi deposu olarak kullanmaktan aktif bir etkileşim aracına dönüştürüyor. Görevimiz, sadece bilgi aramak değil, aynı zamanda uzak sunucudaki otomatik bir süreci (bir Git Hook) tetiklemek için belirli bir eylem gerçekleştirmektir. Bu seviye, `.gitignore` dosyasını atlatma, Git kimliğini ayarlama ve sunucu tarafı otomasyonu gibi ileri düzey ve pratik Git kavramlarını öğretir.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "`README.md` dosyasında belirtilen talimatları izleyerek uzak depoya bir dosya itin (`push`) ve bir sonraki seviyenin şifresini alın."

**Zihinsel Analiz:**

1. **Görev:** Bu sefer bir sır bulmayacağız, bir "anahtar" göndereceğiz. `README.md` dosyası bize tam olarak ne yapmamız gerektiğini söylüyor: `key.txt` adında, içinde `May I come in?` yazan bir dosya oluşturup `master` dalına `push` etmeliyiz.
2. **Olası Engeller (Tuzaklar):**
    - **`.gitignore` Tuzağı:** Geliştiriciler, `.txt` veya `key.txt` gibi dosyaların yanlışlıkla depoya eklenmesini önlemek için `.gitignore` kullanır. Bu, `git add` komutumuzun başarısız olabileceği ve dosyayı zorla eklememiz (`-f` bayrağı) gerekebileceği anlamına gelir.
    - **Kimlik Tuzağı:** `git commit` komutu bir yazar adı ve e-posta gerektirir. Eğer bu bilgiler yerel makinede ayarlanmamışsa, `commit` işlemi başarısız olur.
    - **Sunucu Tuzağı:** Sunucu, dosyayı aldıktan sonra ne yapacak? Muhtemelen bir "pre-receive hook" (ön alım kancası) betiği çalıştıracak, dosyayı doğrulayacak, şifreyi yazdıracak ve ardından `push` işlemini reddederek depoyu temiz tutacaktır.

**İlk Plan (Dedektif Planı):**

1. **Hazırlık ve Klonlama (Yerel Makinede):** Depoyu yerel makineye klonla.
2. **Anahtarı Oluşturma (Yerel Makinede):** `README.md`'deki talimatlara göre `key.txt` dosyasını oluştur.
3. **Engelleri Aşma (Yerel Makinede):**
    - `cat .gitignore` ile dosyayı kontrol et.
    - `git add -f key.txt` ile dosyayı zorla ekle.
    - Gerekirse `git config` ile kullanıcı adı ve e-postanı ayarla.
    - `git commit` ile değişikliği kaydet.
4. **Tetiği Çekme (Yerel Makinede):** `git push origin master` komutuyla dosyayı sunucuya gönder ve çıktıyı dikkatlice oku.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Bu Seviyedeki Rolü|
|---|---|---|
|**`git clone`**|**Depo Klonlama**|Uzak depoyu yerel makinemize indirmek için kullanılır.|
|**`echo >`**|**Dosya Oluşturma**|Belirtilen içerikle hızlıca bir metin dosyası oluşturur.|
|**`cat .gitignore`**|**Yoksayma Kurallarını Görüntüleme**|Hangi dosyaların Git tarafından kasıtlı olarak yoksayıldığını gösterir. Tuzağı ortaya çıkarır.|
|**`git add -f`**|**Zorla Ekleme**|`.gitignore` tarafından yoksayılan bir dosyayı "staging area"ya zorla ekler. `-f` "force" (zorla) anlamına gelir.|
|**`git config`**|**Git'i Yapılandırma**|`commit` yapabilmek için gerekli olan kullanıcı adı ve e-posta gibi ayarları tanımlar.|
|**`git commit`**|**Değişikliği Kaydetme**|Dosyadaki değişikliği yerel depo geçmişine kaydeder.|
|**`git push`**|**Uzak Depoya İtme**|Yerel `commit`'leri uzak sunucuya gönderir. Bu, sunucudaki betiği tetikleyen eylemdir.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye. **Tüm bu komutlar yerel makinenizde (Kali) çalıştırılmalıdır.**

##### **Aşama 1: Hazırlık ve Tuzakları Keşfetme**

Bash

```
# Kali makinenizde
mkdir /tmp/bandit31 && cd /tmp/bandit31
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
# bandit31'in şifresini girin: fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
cd repo
echo "May I come in?" > key.txt

# .gitignore tuzağını kontrol et
cat .gitignore
# Çıktı: *.txt

# Dosyayı eklemeye çalış (ve başarısız ol)
git add key.txt
# Git, dosyanın yoksayıldığına dair bir uyarı verir.
```

**Analiz:** Tahmin ettiğimiz gibi, `*.txt` kuralı nedeniyle `key.txt` dosyası yoksayılıyor.

##### **Aşama 2: Engelleri Aşma ve Commit Etme**

Bash

```
# Kimlik tuzağını önceden çöz
git config --global user.email "hacker@example.com"
git config --global user.name "Emar"

# Dosyayı zorla ekle
git add -f key.txt

# Değişikliği commit et
git commit -m "Add key file"
# Çıktı: [master d141820] Add key file...
```

**Analiz:** Kimliği ayarlayarak ve `-f` bayrağını kullanarak her iki tuzağı da aştık. Değişiklik artık yerel depomuzda kayıtlı.

##### **Aşama 3: Sunucuyu Tetikleme ve Şifreyi Alma**

Bash

```
# Değişikliği sunucuya it
git push origin master
# bandit31'in şifresini tekrar girin

# Sunucudan gelen çıktı:
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
To ssh://bandit.labs.overthewire.org:2220/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to '...'
```

**Nihai Karar:** Sunucu, `push` işlemimizi bir "pre-receive hook" ile yakaladı. Bu betik, gönderdiğimiz dosyayı doğruladı, doğru olduğu için şifreyi ekrana yazdırdı ve ardından işlemi reddederek depoyu temiz bıraktı. Bu, siber güvenlikte "etkileşimli" bir hedefle nasıl başa çıkılacağının mükemmel bir örneğidir.

**`bandit32` seviyesinin şifresi: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`**

### **دليل Bandit التعليمي: المستوى 31 -> 32 (تفعيل الخادم)**

هذا المستوى ينقلنا من استخدام Git كأداة سلبية للبحث عن المعلومات إلى أداة تفاعلية نشطة. مهمتنا ليست فقط البحث عن سر، بل تنفيذ إجراء معين لتفعيل عملية آلية (Git Hook) على الخادم البعيد. يعلمنا هذا المستوى مفاهيم Git متقدمة وعملية مثل تجاوز `.gitignore`، وإعداد هوية Git، والأهم من ذلك، أتمتة جانب الخادم.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "اتبع التعليمات الموجودة في ملف `README.md` لدفع (`push`) ملف إلى المستودع البعيد والحصول على كلمة السر للمستوى التالي."

**التحليل الذهني للهدف:**

1. **المهمة:** هذه المرة لن نجد سرًا، بل سنرسل "مفتاحًا". يخبرنا ملف `README.md` بالضبط بما يجب فعله: إنشاء ملف باسم `key.txt` يحتوي على `May I come in?` ودفعه إلى فرع `master`.
2. **العقبات المحتملة (الأفخاخ):**
    - **فخ `.gitignore`:** يستخدم المطورون `.gitignore` لمنع إضافة ملفات مثل `.txt` أو `key.txt` عن طريق الخطأ إلى المستودع. هذا يعني أن أمر `git add` قد يفشل، وسنحتاج إلى إجبار الملف على الإضافة (باستخدام علامة `-f`).
    - **فخ الهوية:** يتطلب أمر `git commit` اسم وبريدًا إلكترونيًا للمؤلف. إذا لم يتم تعيين هذه المعلومات على الجهاز المحلي، فسيفشل الـ `commit`.
    - **فخ الخادم:** ماذا سيفعل الخادم بعد استلام الملف؟ من المحتمل أنه سيقوم بتشغيل سكربت "pre-receive hook"، والتحقق من الملف، وطباعة كلمة المرور، ثم رفض عملية الدفع للحفاظ على نظافة المستودع.

**الخطة المبدئية (خطة المحقق):**

1. **التحضير والاستنساخ (على الجهاز المحلي):** استنسخ المستودع إلى جهازك المحلي.
2. **إنشاء المفتاح (على الجهاز المحلي):** قم بإنشاء ملف `key.txt` وفقًا للتعليمات في `README.md`.
3. **تجاوز العقبات (على الجهاز المحلي):**
    - تحقق من ملف `.gitignore` باستخدام `cat`.
    - أضف الملف بالقوة باستخدام `git add -f key.txt`.
    - إذا لزم الأمر، قم بتعيين اسم المستخدم والبريد الإلكتروني باستخدام `git config`.
    - سجل التغيير باستخدام `git commit`.
4. **الضغط على الزناد (على الجهاز المحلي):** أرسل الملف إلى الخادم باستخدام `git push origin master` واقرأ الناتج بعناية.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|دورها في هذا المستوى|
|---|---|---|
|**`git clone`**|**استنساخ المستودع**|يستخدم لتنزيل المستودع البعيد إلى جهازنا المحلي.|
|**`echo >`**|**إنشاء ملف**|ينشئ بسرعة ملفًا نصيًا بالمحتوى المحدد.|
|**`cat .gitignore`**|**عرض قواعد التجاهل**|يوضح الملفات التي يتجاهلها Git عمدًا. يكشف عن الفخ.|
|**`git add -f`**|**الإضافة بالقوة**|يجبر إضافة ملف تم تجاهله بواسطة `.gitignore` إلى "منطقة التجهيز". `-f` تعني "force" (قوة).|
|**`git config`**|**تكوين Git**|يحدد الإعدادات مثل اسم المستخدم والبريد الإلكتروني، وهي ضرورية لعمل `commit`.|
|**`git commit`**|**تسجيل التغيير**|يسجل التغيير في الملف في تاريخ المستودع المحلي.|
|**`git push`**|**الدفع إلى المستودع البعيد**|يرسل الـ `commits` المحلية إلى الخادم البعيد. هذا هو الإجراء الذي يفعل السكربت على الخادم.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط. **يجب تنفيذ كل هذه الأوامر على جهازك المحلي (كالي).**

##### **المرحلة 1: التحضير واكتشاف الأفخاخ**

Bash

```
# على جهاز كالي
mkdir /tmp/bandit31 && cd /tmp/bandit31
git clone ssh://bandit31-git@bandit.labs.overthewire.org:2220/home/bandit31-git/repo
# أدخل كلمة مرور bandit31: fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
cd repo
echo "May I come in?" > key.txt

# تحقق من فخ .gitignore
cat .gitignore
# الناتج: *.txt

# حاول إضافة الملف (وستفشل)
git add key.txt
# Git يعطي تحذيرًا بأن الملف تم تجاهله.
```

**التحليل:** كما توقعنا، يتم تجاهل ملف `key.txt` بسبب قاعدة `*.txt`.

##### **المرحلة 2: تجاوز العقبات وعمل Commit**

Bash

```
# حل فخ الهوية مسبقًا
git config --global user.email "hacker@example.com"
git config --global user.name "Emar"

# أضف الملف بالقوة
git add -f key.txt

# قم بعمل commit للتغيير
git commit -m "Add key file"
# الناتج: [master d141820] Add key file...
```

**التحليل:** لقد تجاوزنا كلا الفخين عن طريق إعداد الهوية واستخدام علامة `-f`. التغيير مسجل الآن في مستودعنا المحلي.

##### **المرحلة 3: تفعيل الخادم والحصول على كلمة السر**

Bash

```
# ادفع التغيير إلى الخادم
git push origin master
# أدخل كلمة مرور bandit31 مرة أخرى

# الناتج من الخادم:
remote: ### Attempting to validate files... ####
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
To ssh://bandit.labs.overthewire.org:2220/home/bandit31-git/repo
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to '...'
```

**الحكم النهائي:** التقط الخادم عملية الدفع الخاصة بنا باستخدام "pre-receive hook". قام هذا السكربت بالتحقق من الملف الذي أرسلناه، وطبع كلمة المرور على الشاشة لأنه كان صحيحًا، ثم رفض العملية للحفاظ على نظافة المستودع. هذا مثال ممتاز على كيفية التعامل مع هدف "تفاعلي" في مجال الأمن السيبراني.

**كلمة السر للمستوى `bandit32` هي: `3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K`**

---

## 🇺🇸 English

### Objective
Push a specific file to the remote repository to trigger a server-side hook.

### Process
1. Clone repo and create `key.txt` with content "May I come in?"
2. Check `.gitignore` (blocks `*.txt`)
3. Force add: `git add -f key.txt`
4. Set identity: `git config --global user.email "email" && git config --global user.name "name"`
5. Commit: `git commit -m "Add key"`
6. Push: `git push origin master`
7. Server validates file and returns password

### Key Takeaways
- **`.gitignore` Bypass:** Use `git add -f` to force-add ignored files
- **Git Hooks:** Servers can run scripts on push (pre-receive hooks)
- **Identity Required:** Git needs user.email and user.name for commits