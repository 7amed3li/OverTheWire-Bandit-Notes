
### **Bandit Seviye 29 -> 30 (Diğer Daldaki Sır) Kılavuzu**

Bu seviye, Git'in en güçlü özelliklerinden biri olan **dallanma (branching)** üzerine odaklanır. Geliştiriciler, ana kod tabanını (genellikle `master` veya `main` dalı) etkilemeden yeni özellikler denemek veya hataları düzeltmek için dallar oluşturur. Ancak bu dallar, bazen "üretim ortamına hazır olmayan" hassas bilgileri içerebilir. Görevimiz, bu unutulmuş dallardan birini bulup içindeki sırrı ortaya çıkarmaktır.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "`bandit29-git` kullanıcısının Git deposunu klonlayın ve bir sonraki seviyenin şifresini bulun."

**Zihinsel Analiz:**

1. **Görev:** Yine bir Git deposunu klonlamamız gerekiyor.
2. **Kritik Nokta:** Önceki seviyelerden farklı olarak, bu sefer sır ne mevcut dosyada ne de geçmiş `commit`'lerde. İpucu, şifrenin farklı bir **dalda (branch)** olduğu yönünde.
3. **Strateji:** Ana daldaki dosyaları incelemek yeterli olmayacak. Depodaki tüm dalları listelemeli, şüpheli görünen bir dala geçiş yapmalı ve dosyaları orada yeniden incelemeliyiz.

**İlk Plan (Dedektif Planı):**

1. **Hazırlık ve Klonlama (Yerel Makinede):** Yerel makinemizde geçici bir dizin oluştur ve `git clone` komutuyla depoyu klonla. Kimlik doğrulaması için `bandit29`'un şifresini kullan.
2. **Mevcut Dalı İnceleme (Yerel Makinede):** Klonlanan `repo` dizinine gir ve `README` dosyasını oku. Şifrenin olmadığını teyit et.
3. **Dalları Keşfetme (Yerel Makinede):** `git branch -a` komutunu kullanarak hem yerel hem de uzak (remote) tüm dalları listele. `dev` gibi geliştirme odaklı isimlere sahip dalları ara.
4. **Dal Değiştirme ve Kanıtı Bulma (Yerel Makinede):** `git checkout <dal_adı>` komutuyla şüpheli dala geçiş yap. Ardından `README` dosyasını tekrar oku ve şifreyi bul.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Bu Seviyedeki Rolü|
|---|---|---|
|**`git clone`**|**Depo Klonlama**|Uzak depoyu yerel makinemize indirmek için kullanılır.|
|**`git branch -a`**|**Tüm Dalları Listeleme**|Depodaki tüm yerel ve uzak dalları gösterir. Bu, gizli odaları bulmamızı sağlayan haritamızdır. `-a` bayrağı "all" (tümü) anlamına gelir.|
|**`git checkout`**|**Dal Değiştirme**|Çalışma alanımızı belirtilen dalın içeriğiyle günceller. Bu, bir daldan diğerine "atlamamızı" sağlar.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Farklı dallardaki `README` dosyasının içeriğini karşılaştırmak için kullanılır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye. **Tüm bu komutlar yerel makinenizde (Kali) çalıştırılmalıdır.**

##### **Aşama 1: Klonlama ve `master` Dalını İnceleme**

Önce depoyu klonlayıp varsayılan `master` dalındaki `README` dosyasına bakıyoruz.

Bash

```
# Kali makinenizde
mkdir /tmp/bandit29 && cd /tmp/bandit29
git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
# bandit29'un şifresini girin: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7

cd repo
cat README.md
# Çıktı:
# ...
# - password: <no passwords in production!>
```

**Analiz:** Beklendiği gibi, `master` dalı "temiz". Şifre burada değil.

##### **Aşama 2: Gizli Dalları Keşfetme**

`git branch -a` komutuyla tüm dalları listeliyoruz.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit29/repo]
└─$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

**Analiz:** `remotes/origin/dev` dalı hemen dikkat çekiyor. "dev" (development/geliştirme), genellikle test ve deneme kodlarının bulunduğu yerdir.

##### **Aşama 3: `dev` Dalına Geçiş ve Sırrı Ortaya Çıkarma**

Şimdi `git checkout` komutuyla `dev` dalına geçip `README` dosyasını tekrar okuyoruz.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit29/repo]
└─$ git checkout dev
branch 'dev' set up to track 'origin/dev'.
Switched to a new branch 'dev'

┌──(kali㉿kali)-[/tmp/bandit29/repo]
└─$ cat README.md
# Bandit Notes
...
## credentials
- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

**Nihai Karar:** `git checkout` komutuyla `dev` dalına geçtiğimizde, aynı `README.md` dosyasının farklı bir versiyonuyla karşılaştık. Bu versiyon, üretim dalından gizlenmiş olan gerçek şifreyi içeriyordu. Bu, geliştirme süreçlerinde dalların nasıl farklı bilgiler barındırabileceğinin ve bunun bir güvenlik açığına dönüşebileceğinin harika bir örneğidir.

**`bandit30` seviyesinin şifresi: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`**

### **دليل Bandit التعليمي: المستوى 29 -> 30 (السر في الفرع الآخر)**

يركز هذا المستوى على واحدة من أقوى ميزات Git: **التفريع (Branching)**. يقوم المطورون بإنشاء فروع لتجربة ميزات جديدة أو إصلاح الأخطاء دون التأثير على قاعدة الكود الرئيسية (عادةً فرع `master` أو `main`). ومع ذلك، يمكن أن تحتوي هذه الفروع أحيانًا على معلومات حساسة "غير جاهزة للإنتاج". مهمتنا هي العثور على أحد هذه الفروع المنسية وكشف السر بداخله.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "قم باستنساخ مستودع Git الخاص بالمستخدم `bandit29-git` وابحث عن كلمة السر للمستوى التالي."

**التحليل الذهني للهدف:**

1. **المهمة:** مرة أخرى، نحتاج إلى استنساخ مستودع Git.
2. **النقطة الحاسمة:** على عكس المستويات السابقة، هذه المرة السر ليس في الملف الحالي ولا في الـ `commits` السابقة. التلميح هو أن كلمة السر موجودة في **فرع (branch)** مختلف.
3. **الاستراتيجية:** لن يكون فحص الملفات في الفرع الرئيسي كافيًا. يجب علينا سرد جميع الفروع في المستودع، والتبديل إلى فرع يبدو مشبوهًا، ثم فحص الملفات هناك مرة أخرى.

**الخطة المبدئية (خطة المحقق):**

1. **التحضير والاستنساخ (على الجهاز المحلي):** قم بإنشاء مجلد مؤقت على جهازك المحلي واستنسخ المستودع باستخدام `git clone`. استخدم كلمة مرور `bandit29` للمصادقة.
2. **فحص الفرع الحالي (على الجهاز المحلي):** ادخل إلى مجلد `repo` المستنسخ واقرأ ملف `README`. تأكد من أن كلمة السر غير موجودة.
3. **استكشاف الفروع (على الجهاز المحلي):** استخدم الأمر `git branch -a` لسرد جميع الفروع المحلية والبعيدة. ابحث عن الفروع ذات الأسماء الموجهة للتطوير مثل `dev`.
4. **تبديل الفرع والعثور على الدليل (على الجهاز المحلي):** استخدم الأمر `git checkout <branch_name>` للتبديل إلى الفرع المشبوه. بعد ذلك، اقرأ ملف `README` مرة أخرى وابحث عن كلمة السر.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|دورها في هذا المستوى|
|---|---|---|
|**`git clone`**|**استنساخ المستودع**|يستخدم لتنزيل المستودع البعيد إلى جهازنا المحلي.|
|**`git branch -a`**|**سرد جميع الفروع**|يعرض جميع الفروع المحلية والبعيدة في المستودع. هذه هي خريطتنا للعثور على الغرف المخفية. علامة `-a` تعني "all" (الكل).|
|**`git checkout`**|**تبديل الفرع**|يقوم بتحديث مساحة العمل الخاصة بنا بمحتويات الفرع المحدد. هذا يسمح لنا "بالقفز" من فرع إلى آخر.|
|**`cat`**|**عرض محتوى الملفات**|يستخدم لمقارنة محتوى ملف `README` في الفروع المختلفة.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط. **يجب تنفيذ كل هذه الأوامر على جهازك المحلي (كالي).**

##### **المرحلة 1: الاستنساخ وفحص فرع `master`**

أولاً، نستنسخ المستودع ونلقي نظرة على ملف `README` في فرع `master` الافتراضي.

Bash

```
# على جهاز كالي
mkdir /tmp/bandit29 && cd /tmp/bandit29
git clone ssh://bandit29-git@bandit.labs.overthewire.org:2220/home/bandit29-git/repo
# أدخل كلمة مرور bandit29: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7

cd repo
cat README.md
# الناتج:
# ...
# - password: <no passwords in production!>
```

**التحليل:** كما هو متوقع، فرع `master` "نظيف". كلمة السر ليست هنا.

##### **المرحلة 2: استكشاف الفروع المخفية**

نسرد جميع الفروع باستخدام الأمر `git branch -a`.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit29/repo]
└─$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```

**التحليل:** يلفت فرع `remotes/origin/dev` الانتباه على الفور. "dev" (development/تطوير) هو المكان الذي يوجد فيه عادةً كود الاختبار والتجربة.

##### **المرحلة 3: التبديل إلى فرع `dev` وكشف السر**

الآن نستخدم الأمر `git checkout` للتبديل إلى فرع `dev` ونقرأ ملف `README` مرة أخرى.

Bash

```
┌──(kali㉿kali)-[/tmp/bandit29/repo]
└─$ git checkout dev
branch 'dev' set up to track 'origin/dev'.
Switched to a new branch 'dev'

┌──(kali㉿kali)-[/tmp/bandit29/repo]
└─$ cat README.md
# Bandit Notes
...
## credentials
- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

**الحكم النهائي:** عندما انتقلنا إلى فرع `dev` باستخدام `git checkout`، واجهنا نسخة مختلفة من نفس الملف `README.md`. احتوت هذه النسخة على كلمة السر الحقيقية التي تم إخفاؤها من فرع الإنتاج. هذا مثال رائع على كيف يمكن للفروع أن تحتوي على معلومات مختلفة أثناء عمليات التطوير، وكيف يمكن أن يتحول ذلك إلى ثغرة أمنية.

**كلمة السر للمستوى `bandit30` هي: `qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL`**

---

## 🇺🇸 English

### Objective
Find password hidden in a development branch.

### Commands
```bash
git branch -a              # List all branches
git checkout dev           # Switch to dev branch
cat README.md              # Password is here!
```

### Key Insight
Production branch had `<no passwords in production!>` but dev branch contained real password.

### Key Takeaways
- **Git Branches:** `git branch -a` shows all branches including remote
- **Development vs Production:** Dev branches may contain sensitive test data
- **`git checkout`:** Switch between branches to explore