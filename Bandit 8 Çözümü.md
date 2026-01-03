
# Bandit 8 -> 9 Çözümü

## 1. Amaç (Objective)
`data.txt` dosyasının içinde, yalnızca bir kez geçen (tekrarlanmayan) tek satırı bularak bir sonraki seviyenin şifresini elde etmek.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (En Verimli - `sort` + `uniq`):** Dosyayı önce `sort` komutu ile sıralayarak tüm aynı satırları bir araya getirmek, ardından `uniq -u` komutunu kullanarak sadece benzersiz (unique) olan satırı yazdırmak.
*   **Yöntem B (Alternatif - `sort` + `uniq` + `grep`):** `sort data.txt | uniq -c` komutunu kullanarak her satırın kaç kez tekrarlandığını saymak, ardından çıktıyı `grep " 1 "` komutu ile filtreleyerek sadece bir kez tekrarlandığı belirtilen satırı bulmak. Bu yöntem daha uzundur ancak `-c` seçeneğinin gücünü gösterir.

---

## 3. Tam Komutlar (Full Commands)

### Komut 1 (En Etkili Yöntem)
```bash
bandit8@bandit:~$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```
*   **Açıklama:**
    *   `sort data.txt`: `data.txt` dosyasının içeriğini satır satır okur ve alfabetik olarak sıralar. Bu, tüm tekrar eden satırların art arda gelmesini sağlar.
    *   `|`: Pipe (boru) operatörü, `sort` komutunun çıktısını `uniq` komutunun girdisi olarak yönlendirir.
    *   `uniq -u`: `uniq` komutuna, sadece tekrarlanmayan (unique) satırları yazdırmasını söyler. `uniq` komutunun doğru çalışabilmesi için girdinin sıralı olması şarttır.

### Komut 2 (Alternatif Yöntem)
```bash
bandit8@bandit:~$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
bandit8@bandit:~$ sort data.txt | uniq -c | grep " 1 "
      1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

```
*   **Açıklama:**
    *   `uniq -c`: Her satırın başında kaç kez tekrarlandığını gösterir.
    *   `grep " 1 "`: Çıktıyı filtreleyerek sadece başında " 1 " (bir boşluk, rakam 1, bir boşluk) olan satırları gösterir.

---

## 4. Öğrenilenler (What I Learned)
- **Komutları Zincirleme (Piping):** `|` (pipe) operatörünün, basit komutları bir araya getirerek karmaşık veri işleme görevlerini yerine getiren güçlü "boru hatları" (pipelines) oluşturmak için nasıl kullanıldığını öğrendim.
- **`sort` ve `uniq` Ortaklığı:** `uniq` komutunun gücünün, ancak veriler önce `sort` ile sıralandığında ortaya çıktığını anladım. Bu ikili, veri temizleme ve analizinde çok yaygın olarak kullanılır.
- **`uniq` Seçenekleri:** `uniq` komutunun `-u` (unique) ve `-c` (count) gibi farklı seçeneklerinin, farklı senaryolarda nasıl faydalı olabileceğini öğrendim.


---

# حل المستوى 8 -> 9 (بانديت)

## 1. الهدف من المستوى ده إيه؟

المطلوب مننا نلاقي السطر **الوحيد** اللي اتكتب مرة واحدة بس (مش متكرر) جوه ملف `data.txt`.

---

## 2. إزاي ممكن نحلها؟ (الطرق الممكنة)

*   **الطريقة الأولى (الأكثر كفاءة - باستخدام `sort` + `uniq`):** الأول، نرتب الملف باستخدام أمر `sort` عشان نخلي كل السطور المتشابهة تحت بعض. بعد كده، نستخدم أمر `uniq -u` عشان نقوله يعرض لنا بس السطر الفريد اللي مش متكرر.

*   **الطريقة التانية (طريقة بديلة):** نستخدم `sort data.txt | uniq -c` عشان نعد كل سطر متكرر كام مرة. بعد كده، نستخدم `grep " 1 "` عشان نفلتر النتيجة ونخليه يعرض بس السطر اللي مكتوب جنبه إنه اتكرر مرة واحدة بس. الطريقة دي أطول شوية بس بتورينا قوة اختيار `-c` في أمر `uniq`.

---

## 3. الأوامر الكاملة (من الآخر)

### الأمر الأول (الطريقة الأكثر فعالية)

```bash
bandit8@bandit:~$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

*   **شرح بسيط:**
    *   `sort data.txt`: بيقرا محتوى ملف `data.txt` ويرتبه أبجديًا. الخطوة دي مهمة جدًا عشان تخلي كل السطور المتكررة ورا بعض بالظبط.
    *   `|`: علامة الـ pipe (الماسورة) بتاخد ناتج أمر `sort` المترتب، وتمرره لأمر `uniq`.
    *   `uniq -u`: أمر `uniq` بيستقبل البيانات المترتبة، واختيار `-u` بيخليه يعرض بس السطور اللي مش متكررة (unique). **ملحوظة مهمة:** أمر `uniq` مش بيشتغل صح غير لو البيانات اللي داخلة له مترتبة الأول.

### الأمر الثاني (طريقة بديلة)

```bash
bandit8@bandit:~$ sort data.txt | uniq -c | grep " 1 "
      1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

*   **شرح بسيط:**
    *   `uniq -c`: هنا، `uniq` بيعد كل سطر اتكرر كام مرة ويكتب الرقم ده في أول السطر.
    *   `grep " 1 "`: بعد كده، `grep` بيفلتر النتيجة دي وبيعرض بس السطور اللي بيبدأ فيها بـ " 1 " (مسافة، رقم 1، مسافة).

---

## 4. أنا اتعلمت إيه من المستوى ده؟

*   **سلسلة الأوامر (Piping):** اتعلمت إزاي أستخدم علامة `|` (الـ pipe) عشان أربط أوامر بسيطة ببعض وأخليهم يشتغلوا مع بعض كـ "خط أنابيب" قوي يقدر يعمل عمليات معالجة بيانات معقدة.
*   **الشراكة بين `sort` و `uniq`:** فهمت إن القوة الحقيقية لأمر `uniq` بتظهر بس لما البيانات تكون مترتبة الأول باستخدام `sort`. الثنائي ده بيستخدم كتير جدًا في تنضيف وتحليل البيانات.
*   **اختيارات `uniq`:** اتعلمت إن أمر `uniq` عنده اختيارات مفيدة زي `-u` (عشان تجيب السطور الفريدة) و `-c` (عشان تعد التكرارات)، وكل واحد فيهم مفيد في سيناريو مختلف.

---

## 🇺🇸 English

### Objective
Find the only non-repeating line in `data.txt`.

### Command
```bash
sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

### Alternative (with count)
```bash
sort data.txt | uniq -c | grep " 1 "
```

### Explanation
- `sort`: Sorts lines alphabetically (required for `uniq`)
- `uniq -u`: Shows only unique (non-repeating) lines
- `uniq -c`: Shows count of occurrences for each line

### Key Takeaways
- **Piping Commands:** Use `|` to chain simple commands into powerful pipelines
- **sort + uniq Partnership:** `uniq` only works correctly on sorted data
- **uniq Options:** `-u` for unique lines, `-c` for counting occurrences