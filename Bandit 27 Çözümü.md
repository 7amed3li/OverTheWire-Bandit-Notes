### **Bandit Seviye 27 -> 28 (Git Klonlama Sanatı) Kılavuzu**

Bu seviye, bizi `Git` dünyasına sokar ve bir sunucuda barındırılan özel bir depoyu (repository) nasıl klonlayacağımızı öğretir. Bu seviyedeki en önemli ders, bazı görevlerin sunucunun içinden değil, **yerel makinemizden** gerçekleştirilmesi gerektiğini anlamaktır.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "`ssh://bandit27-git@bandit.labs.overthewire.org/home/bandit27-git/repo` adresinde, 2220 portu üzerinden erişilebilen bir Git deposu bulunmaktadır. `bandit27-git` kullanıcısının şifresi, `bandit27` kullanıcısının şifresiyle aynıdır. Depoyu klonlayın ve bir sonraki seviyenin şifresini bulun."

**Zihinsel Analiz:**

1. **Görev:** Görev çok açık: Bir Git deposunu klonlamak.
2. **Konum:** Depo, `localhost` üzerinde (Bandit sunucusunun kendisi) barındırılıyor.
3. **Kimlik Doğrulama:** `bandit27-git` kullanıcısı olarak bağlanmamız gerekiyor ve şifresi, `bandit27` kullanıcısının şifresiyle (`YnQpBuifNMas1hcUFk70Zmq13378j0VO`) aynı.
4. **Kritik Nokta:** `bandit27` kullanıcısı olarak sunucuya giriş yaptığımızda, `git` komutu yüklü olmayabilir veya gerekli ortam sağlanmamış olabilir. En temiz ve doğru yöntem, bu işlemi kendi yerel makinemizden (örneğin Kali) gerçekleştirmektir. `git` komutu, `ssh` protokolünü kullanarak sunucuya bağlanabilir.

**İlk Plan (Dedektif Planı):**

1. **Hazırlık (Yerel Makinede):** Klonlanan dosyaları saklamak için geçici bir dizin oluştur. Bu, işimizi düzenli tutar.
2. **Klonlama (Yerel Makinede):** `git clone` komutunu, verilen `ssh` adresi, kullanıcı adı ve port bilgisiyle birlikte kullan.
3. **Kimlik Doğrulama:** Komut şifre sorduğunda, `bandit27`'nin şifresini gir.
4. **Keşif (Yerel Makinede):** Klonlanan `repo` dizinine gir ve şifreyi bulmak için içindeki dosyaları (muhtemelen `README`) incele.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

|Araç|Türkçe Açıklama|Bu Seviyedeki Rolü|
|---|---|---|
|**`mkdir`**|**Dizin Oluşturma**|Yerel makinemizde klonlama işlemi için temiz bir çalışma alanı oluşturmak amacıyla kullanılır.|
|**`cd`**|**Dizin Değiştirme**|Oluşturduğumuz çalışma dizinine ve daha sonra klonlanan `repo` dizinine girmek için kullanılır.|
|**`git clone`**|**Depo Klonlama**|Bu seviyenin temel komutudur. Uzak bir sunucudaki Git deposunun tam bir kopyasını yerel makinemize indirir.|
|**`cat`**|**Dosya İçeriğini Görüntüleme**|Klonlanan depo içindeki `README` dosyasının içeriğini okuyarak şifreyi ortaya çıkarmak için kullanılır.|

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye. **Tüm bu komutlar yerel makinenizde (Kali) çalıştırılmalıdır.**

##### **Aşama 1: Çalışma Alanını Hazırlama**

Önce, klonlanacak depo için geçici bir klasör oluşturuyoruz.

Bash

```
# Kali makinenizde
mkdir /tmp/bandit27
cd /tmp/bandit27
```

##### **Aşama 2: Depoyu Klonlama**

Şimdi, `git clone` komutunu kullanarak uzak depoyu çekiyoruz.

Bash

```
# Kali makinenizde
┌──(kali㉿kali)-[/tmp/bandit27]
└─$ git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
Cloning into 'repo'...
The authenticity of host '[bandit.labs.overthewire.org]:2220 ([176.9.9.179]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[bandit.labs.overthewire.org]:2220' (ED25519) to the list of known hosts.
bandit27-git@bandit.labs.overthewire.org's password: [BURAYA ŞİFREYİ GİRİN]
...
Receiving objects: 100% (3/3), done.
```

**Analiz:** Komut, `bandit27-git` kullanıcısının şifresini sordu. `YnQpBuifNMas1hcUFk70Zmq13378j0VO` şifresini girdikten sonra, depo başarıyla klonlandı.

##### **Aşama 3: Şifreyi Ortaya Çıkarma**

Artık `repo` adında yerel bir dizinimiz var. İçine girip `README` dosyasını okuyoruz.

Bash

```
# Kali makinenizde
┌──(kali㉿kali)-[/tmp/bandit27]
└─$ ls
repo

┌──(kali㉿kali)-[/tmp/bandit27]
└─$ cd repo

┌──(kali㉿kali)-[/tmp/bandit27/repo]
└─$ cat README
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

**Nihai Karar:** Şifre, beklendiği gibi, klonlanan deponun içindeki `README` dosyasındaydı. Bu seviye, sunucuya doğrudan giriş yapmanın her zaman çözüm olmadığını, bazen istemci araçlarını (client tools) uzaktan kullanmanın gerekli olduğunu öğretmiştir.

**`bandit28` seviyesinin şifresi: `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`**

### **دليل Bandit التعليمي: المستوى 27 -> 28 (فن استنساخ Git)**

هذا المستوى ينقلنا إلى عالم `Git` ويعلمنا كيفية استنساخ مستودع (repository) خاص مستضاف على خادم. الدرس الأهم في هذا المستوى هو فهم أن بعض المهام يجب أن تتم من **جهازنا المحلي**، وليس من داخل الخادم نفسه.

#### **الجزء الأول: تحليل مسرح الجريمة (فهم الهدف)**

**الهدف المعلن:** "يوجد مستودع Git على العنوان `ssh://bandit27-git@bandit.labs.overthewire.org/home/bandit27-git/repo` عبر المنفذ 2220. كلمة المرور للمستخدم `bandit27-git` هي نفسها كلمة المرور للمستخدم `bandit27`. قم باستنساخ المستودع وابحث عن كلمة السر للمستوى التالي."

**التحليل الذهني للهدف:**

1. **المهمة:** المهمة واضحة جدًا: استنساخ مستودع Git.
2. **الموقع:** المستودع مستضاف على `localhost` (خادم Bandit نفسه).
3. **المصادقة:** نحتاج إلى الاتصال كمستخدم `bandit27-git`، وكلمة المرور الخاصة به هي نفسها كلمة مرور `bandit27` (`YnQpBuifNMas1hcUFk70Zmq13378j0VO`).
4. **النقطة الحاسمة:** عند تسجيل الدخول إلى `bandit27`، قد لا يكون أمر `git` مثبتًا أو قد لا تكون البيئة مهيأة. الطريقة الأنظف والأصح هي تنفيذ هذه العملية من جهازنا المحلي (مثل كالي). أمر `git` يمكنه الاتصال بالخادم باستخدام بروتوكول `ssh`.

**الخطة المبدئية (خطة المحقق):**

1. **التحضير (على الجهاز المحلي):** إنشاء مجلد مؤقت لتخزين الملفات المستنسخة. هذا يحافظ على تنظيم عملنا.
2. **الاستنساخ (على الجهاز المحلي):** استخدام الأمر `git clone` مع عنوان `ssh` واسم المستخدم والمنفذ المعطى.
3. **المصادقة:** عندما يطلب الأمر كلمة المرور، أدخل كلمة مرور `bandit27`.
4. **الاستكشاف (على الجهاز المحلي):** ادخل إلى مجلد `repo` المستنسخ وافحص الملفات بداخله (على الأرجح `README`) للعثور على كلمة السر.

#### **الجزء الثاني: أدوات التحقيق (شرح الأوامر)**

|الأداة|الشرح بالعربية|دورها في هذا المستوى|
|---|---|---|
|**`mkdir`**|**إنشاء مجلد**|يستخدم لإنشاء مساحة عمل نظيفة على جهازنا المحلي لعملية الاستنساخ.|
|**`cd`**|**تغيير المجلد**|يستخدم للدخول إلى مجلد العمل الذي أنشأناه ثم إلى مجلد `repo` المستنسخ.|
|**`git clone`**|**استنساخ المستودع**|الأمر الأساسي لهذا المستوى. يقوم بتنزيل نسخة كاملة من مستودع Git على خادم بعيد إلى جهازنا المحلي.|
|**`cat`**|**عرض محتوى الملفات**|يستخدم لقراءة محتوى ملف `README` داخل المستودع المستنسخ للكشف عن كلمة السر.|

#### **الجزء الثالث: رحلة التحقيق (سجل الطرفية الحقيقي)**

هذه هي القصة الكاملة، خطوة بخطوة، كما حدثت بالضبط. **يجب تنفيذ كل هذه الأوامر على جهازك المحلي (كالي).**

##### **المرحلة 1: تحضير مساحة العمل**

أولاً، نقوم بإنشاء مجلد مؤقت للمستودع الذي سنقوم باستنساخه.

Bash

```
# على جهاز كالي
mkdir /tmp/bandit27
cd /tmp/bandit27
```

##### **المرحلة 2: استنساخ المستودع**

الآن، نستخدم الأمر `git clone` لسحب المستودع البعيد.

Bash

```
# على جهاز كالي
┌──(kali㉿kali)-[/tmp/bandit27]
└─$ git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo
Cloning into 'repo'...
The authenticity of host '[bandit.labs.overthewire.org]:2220 ([176.9.9.179]:2220)' can't be established.
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
bandit27-git@bandit.labs.overthewire.org's password: [أدخل كلمة المرور هنا]
...
Receiving objects: 100% (3/3), done.
```

**التحليل:** طلب الأمر كلمة مرور المستخدم `bandit27-git`. بعد إدخال كلمة المرور `YnQpBuifNMas1hcUFk70Zmq13378j0VO`، تم استنساخ المستودع بنجاح.

##### **المرحلة 3: الكشف عن كلمة السر**

لدينا الآن مجلد محلي باسم `repo`. ندخل إليه ونقرأ ملف `README`.

Bash

```
# على جهاز كالي
┌──(kali㉿kali)-[/tmp/bandit27]
└─$ ls
repo

┌──(kali㉿kali)-[/tmp/bandit27]
└─$ cd repo

┌──(kali㉿kali)-[/tmp/bandit27/repo]
└─$ cat README
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

**الحكم النهائي:** كانت كلمة السر، كما هو متوقع، داخل ملف `README` في المستودع المستنسخ. هذا المستوى علمنا أن تسجيل الدخول المباشر إلى الخادم ليس هو الحل دائمًا، وأن استخدام أدوات العميل (client tools) عن بعد ضروري في بعض الأحيان.

**كلمة السر للمستوى `bandit28` هي: `Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN`**

---

## 🇺🇸 English

### Objective
Clone a git repository and read the README file.

### Commands
```bash
git clone ssh://bandit27-git@bandit.labs.overthewire.org:2220/home/bandit27-git/repo /tmp/repo27
cd /tmp/repo27
cat README
```

### Key Takeaways
- **Git Clone:** Download repository with `git clone URL`
- **SSH Git:** `ssh://user@host:port/path`
- **Remote Tools:** Sometimes you must work from external machine