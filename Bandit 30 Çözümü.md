### ** Bandit Seviye 30 -> 31 (Etiketlerdeki Sır) Kılavuzu**

Bu seviye, Git'in genellikle göz ardı edilen bir özelliğini, **etiketleri (tags)**, bir bilgi gizleme mekanizması olarak nasıl kullanılabileceğini gösterir. Etiketler normalde önemli `commit`'leri (örneğin, sürüm notları) işaretlemek için kullanılır, ancak bu seviyede, etiketin kendisi bir sırrı barındırmaktadır. Bu, bir siber güvenlik analistinin her olasılığı değerlendirmesi gerektiğini hatırlatan mükemmel bir derstir.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "`bandit30-git` kullanıcısının Git deposunu klonlayın ve bir sonraki seviyenin şifresini bulun."

**Zihinsel Analiz:**

1. **Görev:** Bir Git deposunu daha klonlamamız gerekiyor.
2. **Kritik Nokta:** Depoyu klonladıktan sonra, `README` dosyasının boş olduğunu, `git log`'un hiçbir `commit` göstermediğini ve `git branch -a`'nın ek bir dal olmadığını fark edeceğiz. Bu, deponun "boş" gibi görünmesi için kasıtlı olarak tasarlandığını gösterir.
3. **Strateji:** Geçmiş ve dallar temiz olduğuna göre, dikkatimizi Git'in başka bir meta-veri özelliğine çevirmeliyiz: **Etiketler**. Planımız, depoda herhangi bir etiket olup olmadığını kontrol etmek ve varsa içeriğini incelemektir.

**İlk Plan (Dedektif Planı):**

1. **Hazırlık ve Klonlama (Yerel Makinede):** Yerel makinemizde geçici bir dizin oluştur ve `git clone` komutuyla depoyu klonla. Kimlik doğrulaması için `bandit30`'un şifresini kullan.
2. **Yüzeyi İnceleme (Yerel Makinede):** Klonlanan `repo` dizinine gir ve `README` dosyasını oku. `git log` ve `git branch -a` komutlarını çalıştırarak deponun gerçekten "boş" göründüğünü doğrula.
3. **Gizli İşaretleri Arama (Yerel Makinede):** `git tag` komutunu kullanarak depoda tanımlanmış tüm etiketleri listele.
4. **İşaretin Sırrını Çözme (Yerel Makinede):** Bulunan etiketin neye işaret ettiğini veya ne içerdiğini görmek için `git show <etiket_adı>` komutunu kullan.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Bu Seviyedeki Rolü|
|---|---|---|
|**`git clone`**|**Depo Klonlama**|Uzak depoyu yerel makinemize indirmek için kullanılır.|
|**`git tag`**|**Etiketleri Listeleme**|Depodaki tüm etiketleri listeler. Bu, normalde görünmeyen işaretleri bulmamızı sağlar.|
|**`git show`**|**Nesne Detaylarını Gösterme**|Bir `commit`, dal veya bu durumda bir **etiketin** içeriğini göstermek için kullanılır. Bu, sırrı doğrudan ortaya çıkaran anahtar komuttur.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|`README` dosyasının boş ve aldatıcı olduğunu doğrulamak için kullanılır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye. **Tüm bu komutlar yerel makinenizde (Kali) çalıştırılmalıdır.**

##### **Aşama 1: Klonlama ve Boş Depo Teyidi**

Önce depoyu klonlayıp "boş" olduğunu teyit ediyoruz.

Bash

```
# Kali makinenizde
mkdir /tmp/bandit30 && cd /tmp/bandit30
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
# bandit30'un şifresini girin: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL

cd repo
cat README.md
# Çıktı: just an epmty file... muahaha
```

**Analiz:** `README` dosyası ve alaycı mesaj, bizi yanlış yönlendirmek için tasarlanmış bir tuzak.

##### **Aşama 2: Etiketi Keşfetme**

Şimdi, normalde bakmayacağımız bir yere, etiketlere bakıyoruz.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit30/repo]
└─$ git tag
secret
```

**Analiz:** Bingo! `secret` adında bir etiket bulduk. Bu, araştırmamız gereken tek ipucu.

##### **Aşama 3: Etiketin İçeriğini Ortaya Çıkarma**

Son olarak, `git show` komutuyla bu etiketin ne sakladığını görüyoruz.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit30/repo]
└─$ git show secret
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

**Nihai Karar:** Bu seviyede, sır bir `commit`'e veya dosyaya bağlı değildi; etiketin kendisi doğrudan şifreyi içeriyordu. `git show` komutu, etiketin işaret ettiği nesneyi (bu durumda sadece bir metin dizesi) göstererek şifreyi ortaya çıkardı. Bu, Git'in meta-verilerinin bile hassas bilgiler içerebileceğini gösteren önemli bir güvenlik dersidir.

**`bandit31` seviyesinin şifresi: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`**

### **دليل Bandit التعليمي: المستوى 30 -> 31 (السر في العلامات)**

يوضح هذا المستوى كيف يمكن استخدام ميزة غالبًا ما يتم تجاهلها في Git، وهي **العلامات (Tags)**، كآلية لإخفاء المعلومات. تُستخدم العلامات عادةً لوضع إشارة على `commits` مهمة (مثل إصدارات البرامج)، ولكن في هذا المستوى، العلامة نفسها هي التي تحمل السر. هذا درس ممتاز يذكر محلل الأمن السيبراني بضرورة تقييم كل الاحتمالات.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "قم باستنساخ مستودع Git الخاص بالمستخدم `bandit30-git` وابحث عن كلمة السر للمستوى التالي."

**التحليل الذهني للهدف:**

1. **المهمة:** نحتاج إلى استنساخ مستودع Git آخر.
2. **النقطة الحاسمة:** بعد استنساخ المستودع، سنلاحظ أن ملف `README` فارغ، وأن `git log` لا يعرض أي `commits`، وأن `git branch -a` لا يظهر أي فروع إضافية. هذا يشير إلى أن المستودع مصمم عمدًا ليبدو "فارغًا".
3. **الاستراتيجية:** بما أن التاريخ والفروع نظيفة، يجب أن نوجه انتباهنا إلى ميزة أخرى من بيانات Git الوصفية: **العلامات (Tags)**. خطتنا هي التحقق مما إذا كانت هناك أي علامات في المستودع، وإذا وجدت، فحص محتواها.

**الخطة المبدئية (خطة المحقق):**

1. **التحضير والاستنساخ (على الجهاز المحلي):** قم بإنشاء مجلد مؤقت على جهازك المحلي واستنسخ المستودع باستخدام `git clone`. استخدم كلمة مرور `bandit30` للمصادقة.
2. **فحص السطح (على الجهاز المحلي):** ادخل إلى مجلد `repo` المستنسخ واقرأ ملف `README`. قم بتشغيل `git log` و `git branch -a` لتأكيد أن المستودع يبدو "فارغًا" بالفعل.
3. **البحث عن العلامات المخفية (على الجهاز المحلي):** استخدم الأمر `git tag` لسرد جميع العلامات المعرفة في المستودع.
4. **فك لغز العلامة (على الجهاز المحلي):** استخدم الأمر `git show <tag_name>` لمعرفة ما تشير إليه العلامة التي تم العثور عليها أو ما تحتويه.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|دورها في هذا المستوى|
|---|---|---|
|**`git clone`**|**استنساخ المستودع**|يستخدم لتنزيل المستودع البعيد إلى جهازنا المحلي.|
|**`git tag`**|**سرد العلامات**|يسرد جميع العلامات في المستودع. هذا يسمح لنا بالعثور على العلامات التي لا تكون مرئية عادة.|
|**`git show`**|**عرض تفاصيل الكائن**|يستخدم لعرض محتوى `commit` أو فرع أو في هذه الحالة **علامة**. هذا هو الأمر الرئيسي الذي يكشف السر مباشرة.|
|**`cat`**|**عرض محتوى الملفات**|يستخدم لتأكيد أن ملف `README` فارغ ومخادع.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط. **يجب تنفيذ كل هذه الأوامر على جهازك المحلي (كالي).**

##### **المرحلة 1: الاستنساخ وتأكيد المستودع الفارغ**

أولاً، نستنسخ المستودع ونتأكد من أنه "فارغ".

Bash

```
# على جهاز كالي
mkdir /tmp/bandit30 && cd /tmp/bandit30
git clone ssh://bandit30-git@bandit.labs.overthewire.org:2220/home/bandit30-git/repo
# أدخل كلمة مرور bandit30: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL

cd repo
cat README.md
# الناتج: just an epmty file... muahaha
```

**التحليل:** ملف `README` والرسالة الساخرة هما فخ مصمم لتضليلنا.

##### **المرحلة 2: اكتشاف العلامة**

الآن، ننظر إلى مكان لا ننظر إليه عادةً، العلامات.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit30/repo]
└─$ git tag
secret
```

**التحليل:** bingo! لقد وجدنا علامة باسم `secret`. هذا هو الدليل الوحيد الذي يجب أن نحقق فيه.

##### **المرحلة 3: كشف محتوى العلامة**

أخيرًا، نرى ما تخفيه هذه العلامة باستخدام الأمر `git show`.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit30/repo]
└─$ git show secret
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

**الحكم النهائي:** في هذا المستوى، لم يكن السر مرتبطًا بـ `commit` أو ملف؛ بل كانت العلامة نفسها تحتوي مباشرة على كلمة السر. كشف الأمر `git show` عن الكائن الذي تشير إليه العلامة (في هذه الحالة، مجرد سلسلة نصية)، مما كشف عن كلمة السر. هذا درس أمني مهم يوضح أنه حتى البيانات الوصفية لـ Git يمكن أن تحتوي على معلومات حساسة.

**كلمة السر للمستوى `bandit31` هي: `fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy`**

---

## 🇺🇸 English

### Objective
Find password hidden in a Git tag.

### Commands
```bash
git tag                    # List all tags
git show secret            # Reveal tag contents
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```

### Key Insight
No useful commits or branches - the secret was stored in a tag named `secret`.

### Key Takeaways
- **Git Tags:** Used for version marking but can store arbitrary data
- **`git tag`:** List all tags in repository
- **`git show <tag>`:** Reveal what the tag points to