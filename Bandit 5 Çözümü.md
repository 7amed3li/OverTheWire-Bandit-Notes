
---
# Bandit 5 -> 6 Çözümü

## 1. Amaç (Objective)
Çok katmanlı bir dizin yapısı içinde, aşağıdaki özelliklere sahip dosyayı bulmak:
- İnsan tarafından okunabilir (human-readable)
- Boyutu 1033 byte
- Çalıştırılamaz (not executable)

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (En Verimli - `find`):** `find` komutunu, dosya türü (`-type`), dosya boyutu (`-size`) ve dosya izinleri (`-perm` veya `-executable`) gibi testlerle kullanarak doğrudan hedef dosyayı bulmak.
*   **Yöntem B (Manuel Arama):** Her bir alt dizine `cd` ile girip, `ls -la` ile dosyaları listelemek ve özelliklerini manuel olarak kontrol etmek. Bu yöntem çok yavaş ve hataya açıktır.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `inhere` dizinine girme ve keşif
```bash
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere02  maybehere04  maybehere06  maybehere08  maybehere10  maybehere12  maybehere14  maybehere16  maybehere18
maybehere01  maybehere03  maybehere05  maybehere07  maybehere09  maybehere11  maybehere13  maybehere15  maybehere17  maybehere19
```

### Adım 2: `find` komutu ile hedef dosyayı bulma
```bash
bandit5@bandit:~/inhere$ find . -type f -size 1033c
./maybehere07/.file2
```
*   **Açıklama:**
    *   `find .`: Aramaya mevcut dizinden (`.`) başla.
    *   `-type f`: Sadece dosyaları (`f`ile) bul, dizinleri (`d`irectory) bulma.
    *   `-size 1033c`: Sadece boyutu tam olarak 1033 byte (`c`haracters) olan dosyaları bul.

### Adım 3: Şifreyi okuma
```bash
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

---

## 4. Öğrenilenler (What I Learned)
- **`find` Komutunun Gücü:** `find` komutunun, dosya sisteminde karmaşık aramalar yapmak için ne kadar esnek ve güçlü olduğunu öğrendim.
- **`find` Testleri:** `-type`, `-size`, `-name`, `-perm` gibi testleri birleştirerek arama sonuçlarını hassas bir şekilde filtreleyebileceğimi anladım.
- **Birimlerin Önemi:** `find` komutunda `-size` kullanırken, `c` (bytes), `k` (kilobytes), `M` (megabytes) gibi birim belirteçlerinin ne kadar önemli olduğunu öğrendim.



---

# حل المستوى 5 -> 6 (بانديت)

## 1. الهدف من المستوى ده إيه؟

المطلوب مننا نلاقي ملف معين مستخبي جوه فولدرات كتير متداخلة. الملف ده ليه **3 شروط** لازم تتحقق فيه:
1.  يكون ملف نصي ينفع يتقري (human-readable).
2.  يكون حجمه بالظبط 1033 بايت (1033 bytes).
3.  يكون ملف **غير قابل للتشغيل** (not executable).

---

## 2. إزاي ممكن نحلها؟ (الطرق الممكنة)

*   **الطريقة الأولى (الأكثر كفاءة - باستخدام `find`):** نستخدم أمر `find` العبقري، ونحدد له الشروط اللي إحنا بندور عليها، زي نوع الملف (`-type`)، وحجمه (`-size`)، وصلاحياته (`-perm` أو `-executable`). الأمر ده هيدور في كل الفولدرات ويجيب لنا الملف اللي بينطبق عليه الشروط دي على طول.

*   **الطريقة التانية (البحث اليدوي):** ندخل كل فولدر فرعي بأمر `cd`، ونعرض الملفات اللي جواه بأمر `ls -la`، ونقعد نراجع خصائص كل ملف بعينينا. طبعًا الطريقة دي بطيئة جدًا وسهل نغلط فيها.

---

## 3. الأوامر الكاملة (من الآخر)

### الخطوة الأولى: ندخل فولدر `inhere` ونستكشف

```bash
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere02  maybehere04  maybehere06  maybehere08  maybehere10  maybehere12  maybehere14  maybehere16  maybehere18
maybehere01  maybehere03  maybehere05  maybehere07  maybehere09  maybehere11  maybehere13  maybehere15  maybehere17  maybehere19
```

### الخطوة التانية: نستخدم أمر `find` عشان نلاقي الملف المطلوب

```bash
bandit5@bandit:~/inhere$ find . -type f -size 1033c
./maybehere07/.file2
```

*   **شرح بسيط:**
    *   `find .`: بقوله "ابدأ دور من الفولدر اللي أنا واقف فيه دلوقتي (`.`)".
    *   `-type f`: بقوله "أنا بدور على **ملفات** بس (`f`ile)، مش عايزك تجيب لي فولدرات (`d`irectory)".
    *   `-size 1033c`: بقوله "هات لي بس الملفات اللي حجمها **بالضبط** 1033 بايت (`c`haracters/bytes)".

### الخطوة التالتة: نقرا كلمة السر

```bash
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

---

## 4. أنا اتعلمت إيه من المستوى ده؟

*   **قوة أمر `find`:** اتعلمت قد إيه أمر `find` ده جبار ومرن، وإنه بيقدر يعمل عمليات بحث معقدة جدًا في كل ملفات النظام.
*   **شروط البحث في `find`:** فهمت إني ممكن أدمج شروط كتير مع بعض زي `-type` و `-size` و `-name` و `-perm` عشان أفلتر نتايج البحث وأوصل للي أنا عايزه بالضبط.
*   **أهمية الوحدات:** اتعلمت إن لما بستخدم اختيار زي `-size` مع أمر `find`، مهم جدًا أحدد الوحدة اللي بقيس بيها، زي `c` (بايت)، `k` (كيلوبايت)، `M` (ميجابايت).

---

## 🇺🇸 English

### Objective
Find a file in nested directories with these properties:
- Human-readable
- Size: 1033 bytes
- Not executable

### Command
```bash
find . -type f -size 1033c
./maybehere07/.file2
```

### Explanation
- `find .`: Start searching from current directory
- `-type f`: Only files (not directories)
- `-size 1033c`: Exactly 1033 bytes (`c` = characters/bytes)

### Key Takeaways
- **`find` Power:** Extremely flexible for complex file system searches
- **Combining Tests:** Chain multiple conditions (`-type`, `-size`, `-name`, `-perm`)
- **Size Units:** `c` (bytes), `k` (kilobytes), `M` (megabytes)