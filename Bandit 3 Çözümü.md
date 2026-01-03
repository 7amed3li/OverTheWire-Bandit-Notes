
---

# Bandit 3 -> 4 Çözümü

## 1. Amaç (Objective)
`inhere` adında bir dizin içinde bulunan ve adı `.` ile başlayan "gizli" bir dosyadan bir sonraki seviyenin şifresini bulmak.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (Adım Adım Keşif):**
    1.  `ls -la` komutu ile mevcut dizindeki tüm dosyaları ve dizinleri listelemek.
    2.  Bulunan `inhere` dizinine `cd` komutu ile girmek.
    3.  `inhere` dizini içinde tekrar `ls -la` komutunu çalıştırarak adı `.` ile başlayan dosyayı bulmak.
    4.  `cat` komutu ile dosyanın içeriğini okumak. Dosya adı özel karakterler içeriyorsa, tırnak işaretleri (`" "`) kullanmak iyi bir pratiktir.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `inhere` dizinini bulma
Mevcut dizinde `ls -la` çalıştırıldığında, `inhere` adında bir dizin bulunur.
```bash
bandit3@bandit:~$ ls -la
total 24
drwxr-xr-x   3 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 150 root    root    4096 Aug 15 13:18 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Aug 15 13:09 .bashrc
drwxr-xr-x   2 root    root    4096 Aug 15 13:16 inhere
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile
```

### Adım 2: Dizin içine girme ve dosyayı bulma
```bash
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls -la
total 12
drwxr-xr-x 2 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 3 root    root    4096 Aug 15 13:16 ..
-rw-r----- 1 bandit4 bandit3   33 Aug 15 13:16 ...Hiding-From-You
```

### Adım 3: Şifreyi okuma
```bash
bandit3@bandit:~/inhere$ cat "...Hiding-From-You"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

---

## 4. Öğrenilenler (What I Learned)
- **Gizli Dosya Tanımı:** Linux'ta bir dosyanın "gizli" olması için adının **sadece bir nokta** (`.`) ile başlaması yeterlidir. `...` ile başlayan bir dosya teknik olarak gizli değildir, ancak `ls`'in standart çıktısında görünmeyebilir ve kafa karıştırıcı olabilir.
- **`-a` Seçeneğinin Gücü:** `ls -a` komutu, adı `.` veya `..` ile başlayan her şeyi gösterir. Bu, dizin yapısını anlamak için kritik öneme sahiptir.
- **Doğrulama ve Güven:** Her zaman kendi komutlarımın çıktısına güvenmeliyim, varsayımlara veya eski bilgilere değil. Gördüğüm şey gerçektir.
- **Tırnak İşaretleri:** Garip karakterler içeren dosya adlarıyla uğraşırken, adı tırnak işaretleri (`" "`) içine almak her zaman güvenli bir yaklaşımdır.

---

# حل المستوى 3 -> 4 (بانديت)

## 1. الهدف من المستوى ده إيه؟

المطلوب مننا نلاقي كلمة السر اللي موجودة جوه ملف "مخفي" اسمه بيبدأ بعلامة النقطة (`.`). الملف ده نفسه موجود جوه فولدر تاني اسمه `inhere`.

---

## 2. إزاي ممكن نحلها؟ (الطرق الممكنة)

*   **الطريقة الوحيدة (الاستكشاف خطوة بخطوة):**
    1.  نستخدم أمر `ls -la` عشان نعرض كل الملفات والفولدرات اللي في المكان اللي إحنا فيه، بما فيهم المخفيين.
    2.  لما نلاقي الفولدر اللي اسمه `inhere`، ندخله باستخدام أمر `cd`.
    3.  جوه فولدر `inhere`، نستخدم أمر `ls -la` تاني عشان نشوف إيه اللي جواه ونلاقي الملف المخفي.
    4.  نستخدم أمر `cat` عشان نقرا محتوى الملف ده. وكعادة كويسة، لو اسم الملف فيه حروف غريبة، الأحسن نحطه بين علامات تنصيص (`" "`).

---

## 3. الأوامر الكاملة (من الآخر)

### الخطوة الأولى: نلاقي فولدر `inhere`

لما بنشغل `ls -la` في الفولدر الرئيسي، بنلاقي فولدر اسمه `inhere`.
```bash
bandit3@bandit:~$ ls -la
total 24
drwxr-xr-x   3 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 150 root    root    4096 Aug 15 13:18 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Aug 15 13:09 .bashrc
drwxr-xr-x   2 root    root    4096 Aug 15 13:16 inhere
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile
```

### الخطوة التانية: ندخل الفولدر ونلاقي الملف

```bash
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls -la
total 12
drwxr-xr-x 2 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 3 root    root    4096 Aug 15 13:16 ..
-rw-r----- 1 bandit4 bandit3   33 Aug 15 13:16 ...Hiding-From-You
```

### الخطوة التالتة: نقرا كلمة السر

```bash
bandit3@bandit:~/inhere$ cat "...Hiding-From-You"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

---

## 4. أنا اتعلمت إيه من المستوى ده؟

*   **تعريف الملف المخفي:** في لينكس، عشان الملف يبقى "مخفي" بجد، لازم اسمه يبدأ **بنقطة واحدة بس** (`.`). الملف اللي اسمه بيبدأ بـ `...` (3 نقط) مش بيعتبر ملف مخفي بالمعنى الرسمي، لكنه ممكن ميبانش في أمر `ls` العادي ويكون شكله ملخبط.
*   **قوة اختيار `-a`:** اختيار `-a` مع أمر `ls` (زي `ls -a` أو `ls -la`) بيعرض **كل حاجة** بتبدأ بنقطة، زي `.` (الفولدر الحالي) و `..` (الفولدر اللي قبله) وأي ملفات مخفية تانية. ده اختيار مهم جدًا عشان تفهم تركيبة الفولدرات صح.
*   **الثقة في اللي بتشوفه:** لازم أثق دايمًا في ناتج الأوامر اللي أنا بكتبها بنفسي، مش على افتراضات أو معلومات قديمة. اللي أنا بشوفه بعيني على الشاشة هو ده الحقيقة.
*   **علامات التنصيص:** لما أتعامل مع أسماء ملفات فيها حروف غريبة أو مسافات، دايمًا من الأمان إني أحط الاسم بين علامات تنصيص (`" "`) عشان أتجنب أي مشاكل.

---

## 🇺🇸 English

### Objective
Find the password stored in a "hidden" file (starting with `.`) inside the `inhere` directory.

### Method
1. Use `ls -la` to list all files including hidden ones
2. Navigate to `inhere` directory with `cd`
3. Use `ls -la` again to find hidden files
4. Read the file with `cat`

### Commands
```bash
cd inhere
ls -la
cat "...Hiding-From-You"
```

### Key Takeaways
- **Hidden Files:** In Linux, a file is "hidden" if its name starts with a single dot (`.`)
- **`-a` Flag:** The `ls -a` command shows everything starting with `.`, including hidden files
- **Quotes:** Always use quotes (`" "`) for filenames with special characters