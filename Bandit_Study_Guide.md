# 🎓 Bandit Wargame Study Guide | Çalışma Rehberi

هذا الملف يحتوي على ملخص لكل مجموعة مستويات باللغتين العربية والتركية، بالإضافة إلى أسئلة تدريبية باللغة التركية.
Bu dosya, her seviye grubu için Arapça ve Türkçe özetler ile Türkçe alıştırma soruları içermektedir.

---

## 📦 Group 1: Basics (Levels 0-5) | Temeller (Seviye 0-5)

### 📋 الملخص (Özet)

#### **Bandit 0: SSH Connection | SSH Bağlantısı**
- **AR:** الاتصال بسيرفر بعيد باستخدام SSH وتحديد المنفذ (Port) عبر `-p`.
- **TR:** Uzak bir sunucuya SSH kullanarak bağlanma ve `-p` parametresi ile port belirleme.
- **Command:** `ssh bandit0@bandit.labs.overthewire.org -p 2220`

#### **Bandit 1: Special Filename (-) | Özel Dosya Adı (-)**
- **AR:** قراءة ملف اسمه `-`. نستخدم `./-` لتجنب اعتبار الشرطة معامل للأمر.
- **TR:** Adı `-` olan bir dosyayı okuma. Tirenin komut parametresi sanılmaması için `./-` kullanılır.
- **Command:** `cat ./-`

#### **Bandit 2: Spaces & Dashes | Boşluklar ve Tireler**
- **AR:** قراءة ملف به مسافات باستخدام علامات التنصيص `" "`.
- **TR:** Adında boşluk olan dosyaları tırnak işareti (`" "`) kullanarak okuma.
- **Command:** `cat "spaces in this filename"`

#### **Bandit 3: Hidden Files | Gizli Dosyalar**
- **AR:** عرض الملفات المخفية (التي تبدأ بنقطة `.`) باستخدام `ls -la`.
- **TR:** Nokta (`.`) ile başlayan gizli dosyaları `ls -la` komutuyla görüntüleme.
- **Command:** `ls -la`

#### **Bandit 4: File Type | Dosya Türü**
- **AR:** استخدام أمر `file` لمعرفة نوع الملف (ASCII text أم data).
- **TR:** Bir dosyanın türünü (metin mi yoksa binary veri mi) anlamak için `file` komutunu kullanma.
- **Command:** `file ./*`

#### **Bandit 5: Advanced Find | Gelişmiş Arama**
- **AR:** البحث عن ملف بمواصفات محددة (نوع، حجم، صلاحيات) باستخدام `find`.
- **TR:** Belirli özelliklere (tür, boyut, izinler) sahip bir dosyayı `find` komutuyla arama.
- **Command:** `find . -type f -size 1033c`

---

### 📝 Test Soruları (MCQs)

1. **Bandit sunucusuna SSH ile bağlanırken varsayılan port dışında bir port belirtmek için hangi parametre kullanılır?**
   - A) `-s`
   - B) `-p`
   - C) `-v`
   - D) `-x`
   *(Cevap: B)*

2. **Bir dosya adında boşluklar varsa (örneğin `dosya adi.txt`), bu dosyayı okumak için en doğru `cat` kullanımı hangisidir?**
   - A) `cat dosya adi.txt`
   - B) `cat "dosya adi.txt"`
   - C) `cat 'dosya adi.txt'`
   - D) B ve C seçeneklerinin her ikisi de doğrudur.
   *(Cevap: D)*

3. **İsmi nokta (`.`) ile başlayan gizli dosyaları listelemek için `ls` komutuna hangi parametre eklenmelidir?**
   - A) `-a`
   - B) `-h`
   - C) `-l`
   - D) `-S`
   *(Cevap: A)*

4. **Bir dosyanın içeriğini açmadan türünü (örneğin metin mi yoksa data mı olduğunu) kontrol eden komut hangisidir?**
   - A) `check`
   - B) `type`
   - C) `file`
   - D) `show`
   *(Cevap: C)*

5. **`find . -type f -size 1033c` komutundaki `c` harfi ne anlama gelir?**
   - A) Kilobyte
   - B) Megabyte
   - C) Byte (Karakter)
   - D) Cluster
   *(Cevap: C)*

---

## 🔍 Group 2: Search & Filter (Levels 6-11) | Arama ve Filtreleme (Seviye 6-11)

### 📋 الملخص (Özet)

#### **Bandit 6: System-wide Search | Sistem Genelinde Arama**
- **AR:** البحث في النظام بالكامل باستخدام `find /` مع تحديد المالك والمجموعة والحجم، وإخفاء الأخطاء باستخدام `2>/dev/null`.
- **TR:** Tüm dosya sisteminde (`/`) kullanıcı, grup ve boyut kriterlerine göre arama yapma. Hataları gizlemek için `2>/dev/null` kullanılır.
- **Command:** `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null`

#### **Bandit 7: Grep Specific Word | Belirli Kelimeyi Arama**
- **AR:** البحث عن كلمة محددة داخل ملف كبير باستخدام `grep`.
- **TR:** Büyük bir dosya içerisinde `grep` komutu ile belirli bir kelimeyi (pattern) bulma.
- **Command:** `grep "millionth" data.txt`

#### **Bandit 8: Unique Lines | Benzersiz Satırlar**
- **AR:** العثور على السطر الوحيد غير المكرر. يجب استخدام `sort` أولاً ثم `uniq -u`.
- **TR:** Sadece bir kez geçen benzersiz satırı bulma. `uniq` komutunun çalışması için verinin önce `sort` ile sıralanması gerekir.
- **Command:** `sort data.txt | uniq -u`

#### **Bandit 9: Strings in Binary | İkili Dosyadan Metin Ayıklama**
- **AR:** استخراج النصوص المقروءة من ملف ثنائي (Binary) باستخدام `strings`.
- **TR:** `strings` komutu ile ikili (binary) bir dosya içindeki okunabilir metin parçalarını bulma.
- **Command:** `strings data.txt | grep "===="`

#### **Bandit 10: Base64 Decode | Base64 Kod Çözme**
- **AR:** فك تشفير بيانات مكتوبة بصيغة Base64.
- **TR:** Base64 formatında kodlanmış veriyi `base64 -d` komutuyla orijinal haline döndürme.
- **Command:** `base64 -d data.txt`

#### **Bandit 11: ROT13 (Translate) | Karakter Çevirme**
- **AR:** فك تشفير نص (إزاحة الحروف 13 خطوة) باستخدام أمر `tr`.
- **TR:** Harflerin alfabede 13 pozisyon kaydırıldığı ROT13 şifresini `tr` komutuyla çözme.
- **Command:** `cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'`

---

### 📝 Test Soruları (MCQs)

1. **`find` komutuyla arama yaparken "Permission denied" gibi hata mesajlarını ekranda görmemek (gizlemek) için komutun sonuna ne eklenir?**
   - A) `1>/dev/null`
   - B) `2>/dev/null`
   - C) `-hide-errors`
   - D) `&>null`
   *(Cevap: B)*

2. **`uniq` komutunun bir dosyadaki tekrar eden satırları doğru şekilde tespit edebilmesi için öncelikle hangi komutun kullanılması şarttır?**
   - A) `grep`
   - B) `cat`
   - C) `sort`
   - D) `strings`
   *(Cevap: C)*

3. **İkili (binary) bir dosya içindeki insan tarafından okunabilir (human-readable) metin dizilerini ayıklamak için hangi komut kullanılır?**
   - A) `text`
   - B) `read`
   - C) `file`
   - D) `strings`
   *(Cevap: D)*

4. **Base64 ile kodlanmış bir dosyanın içeriğini çözmek (decode) için doğru komut hangisidir?**
   - A) `base64 -e data.txt`
   - B) `base64 -d data.txt`
   - C) `decode base64 data.txt`
   - D) `cat data.txt | b64`
   *(Cevap: B)*

5. **`tr 'A-Z' 'N-ZA-M'` komut zinciri genellikle hangi şifreleme türünü çözmek için kullanılır?**
   - A) Base64
   - B) MD5
   - C) ROT13
   - D) Hexadecimal
   *(Cevap: C)*

---

## 🔐 Group 3: Network & Compression (Levels 12-16) | Ağ ve Sıkıştırma (Seviye 12-16)

### 📋 الملخص (Özet)

#### **Bandit 12: Multi-Compression | Çoklu Sıkıştırma**
- **AR:** فك ضغط ملف مكرر الضغط عدة مرات باستخدام `gzip`, `bzip2`, `tar`. نستخدم `xxd -r` لعكس الـ hexdump.
- **TR:** `gzip`, `bzip2`, `tar` gibi araçlarla iç içe sıkıştırılmış dosyaları açma. `xxd -r` ile hexdump'ı orijinal dosyaya çevirme.
- **Commands:** `xxd -r`, `gzip -d`, `bzip2 -d`, `tar -xf`

#### **Bandit 13: SSH Keys | SSH Anahtarları**
- **AR:** استخدام مفتاح خاص (Private Key) للاتصال عبر SSH باستخدام المعامل `-i`.
- **TR:** SSH bağlantısı için şifre yerine özel anahtar (private key) kullanma. `-i` parametresi ile anahtar dosyası belirtilir.
- **Command:** `ssh -i sshkey.private bandit14@localhost -p 2220`

#### **Bandit 14: Netcat (nc) | Port Etkileşimi**
- **AR:** إرسال بيانات إلى منفذ (Port) محدد على الجهاز المحلي باستخدام `nc localhost 30000`.
- **TR:** `nc` (netcat) komutu ile belirli bir porta veri gönderme veya portu dinleme.
- **Command:** `echo "password" | nc localhost 30000`

#### **Bandit 15: OpenSSL | Güvenli Bağlantı (SSL)**
- **AR:** الاتصال بمنفذ مشفر باستخدام SSL/TLS عبر أمر `openssl s_client -connect`.
- **TR:** SSL/TLS ile korunan güvenli bir porta `openssl s_client` komutu ile bağlanma.
- **Command:** `openssl s_client -connect localhost:30001`

#### **Bandit 16: Port Scanning | Port Tarama**
- **AR:** فحص المنافذ المفتوحة ومعرفة أيها يستخدم تشفير SSL باستخدام `nmap`.
- **TR:** `nmap` aracı ile açık portları tarama ve SSL servislerini tespit etme.
- **Command:** `nmap -sV -p 31000-32000 localhost`

---

## 🛡️ Group 4: Permissions & Tools (Levels 17-20) | İzinler ve Araçlar (Seviye 17-20)

### 📋 الملخص (Özet)

#### **Bandit 17: Diff Files | Dosya Karşılaştırma**
- **AR:** مقارنة ملفين والعثور على السطر المختلف باستخدام `diff`.
- **TR:** İki dosya arasındaki farkları `diff` komutuyla bulma.
- **Command:** `diff passwords.old passwords.new`

#### **Bandit 18: Restricted Shell | Kısıtlı Kabuk**
- **AR:** الدخول عبر SSH مع تنفيذ أمر مباشر لتجاوز القيود عند تسجيل الدخول: `ssh ... "cat readme"`.
- **TR:** Giriş yapıldığında çıkış yapan kısıtlı kabukları aşmak için SSH ile doğrudan komut çalıştırma.
- **Command:** `ssh bandit18@... "cat readme"`

#### **Bandit 19: SetUID Bit | SetUID Yetkisi**
- **AR:** تنفيذ ملف بصلاحيات مالكه (bandit20) بدلاً من صلاحياتك الحالية باستخدام ثغرة SetUID.
- **TR:** Bir dosyayı sahibinin yetkileriyle çalıştırmayı sağlayan SetUID biti ile yetki yükseltme.
- **Command:** `./bandit20-do cat /etc/bandit_pass/bandit20`

#### **Bandit 20: NC & Setid | Netcat Bağlantısı**
- **AR:** ربط اتصالين ببعضهما لنقل كلمة السر عبر الشبكة المحلية.
- **TR:** İki farklı terminal arasında birini dinleyici (listener) diğerini gönderici yaparak veri aktarma.

---

## ⚙️ Group 5: Automation & Scripts (Levels 21-25) | Otomasyon ve Scriptler (Seviye 21-25)

### 📋 الملخص (Özet)

#### **Bandit 21-23: Cron Jobs | Zamanlanmış Görevler**
- **AR:** تحليل المهام المجدولة في `/etc/cron.d/` ومعرفة السكربتات التي تعمل تلقائياً لاستغلالها.
- **TR:** `/etc/cron.d/` dizinindeki zamanlanmış görevleri analiz etme ve otomatik çalışan scriptleri inceleme.

#### **Bandit 24: Brute Force | Kaba Kuvvet Saldırısı**
- **AR:** تجربة كل الاحتمالات (0000-9999) باستخدام حلقة `for` في Bash وإرسالها لـ `nc`.
- **TR:** Bash'te `for` döngüsü kurarak tüm PIN kombinasyonlarını deneme ve `nc` üzerinden porta gönderme.
- **Command:** `for i in {0000..9999}; do echo "pass $i"; done | nc localhost 30002`

#### **Bandit 25: Restricted Shell Escape | Kısıtlı Kabuktan Kaçış**
- **AR:** استغلال أمر `more` عند تصغير نافذة Terminal للدخول إلى المحرر `v` (Vim) ثم تنفيذ الأوامر.
- **TR:** Terminal boyutunu küçülterek `more` komutunu tetikleme ve ardından Vim (`v`) üzerinden kabuk komutları çalıştırma.

---

## 📂 Group 6: Advanced Shells & Git (Levels 26-30) | Gelişmiş Kabuklar ve Git (Seviye 26-30)

### 📋 الملخص (Özet)

#### **Bandit 26: Shell Escape | Kabuk Değiştirme**
- **AR:** تغيير الـ Login Shell الافتراضي للوصول إلى Bash عبر استغلال `vi` أو أوامر مشابهة داخل القشرة المقيدة.
- **TR:** Varsayılan giriş kabuğunu (`/usr/bin/showtext`) aşarak standart bir Bash oturumu elde etme.

#### **Bandit 27-30: Git Repository | Git Deposu**
- **AR:** التعامل مع مستودعات Git، استرجاع الكلمات من الـ `log` أو الـ `branches` أو الـ `tags`.
- **TR:** Git depolarında çalışma; geçmiş kayıtları (`log`), farklı dalları (`branches`) veya etiketleri (`tags`) inceleme.

---

## 🏁 Group 7: Final Levels (Levels 31-32) | Final Seviyeleri (Seviye 31-32)

### 📋 الملخص (Özet)

#### **Bandit 31: Git Push | Git'e Gönderme**
- **AR:** رفع ملف جديد إلى مستودع Git بعد إنشاء ملف `.gitignore` أو تعديله.
- **TR:** Git deposuna yeni bir dosya ekleme ve sunucuya gönderme (`push`).

#### **Bandit 32: Posix Escape | Kabuk Değişkenleri**
- **AR:** تجاوز قشرة تحول كل الأوامر إلى "UPPERCASE" باستخدام المتغيرات مثل `$0`.
- **TR:** Tüm komutları büyük harfe çeviren kısıtlı bir kabuğu, `$0` kabuk değişkenini kullanarak aşma.

---

### 📝 Test Soruları (MCQs - Genel Tekrar)

1. **SSH ile bağlanırken parola yerine özel anahtar dosyası kullanmak için hangi parametre girilmelidir?**
   - A) `-k`
   - B) `-i`
   - C) `-p`
   - D) `-key`
   *(Cevap: B)*

2. **Bir servisin SSL/TLS sertifikasını kontrol etmek veya güvenli bir porta bağlanmak için hangi araç kullanılır?**
   - A) `nc`
   - B) `telnet`
   - C) `openssl s_client`
   - D) `ssh -L`
   *(Cevap: C)*

3. **Linux'ta iki dosya arasındaki satır farklarını gösteren komut hangisidir?**
   - A) `compare`
   - B) `diff`
   - C) `sdiff`
   - D) `match`
   *(Cevap: B)*

4. **Kaba kuvvet (Brute Force) saldırısını otomatize etmek için Bash'te en yaygın kullanılan döngü yapısı hangisidir?**
   - A) `while`
   - B) `until`
   - C) `for`
   - D) `if-else`
   *(Cevap: C)*

5. **Açık portları taramak ve bu portlarda hangi servislerin çalıştığını anlamak için kullanılan en popüler araç hangisidir?**
   - A) `tcpdump`
   - B) `wireshark`
   - C) `nmap`
   - D) `netstat`
   *(Cevap: C)*

---
