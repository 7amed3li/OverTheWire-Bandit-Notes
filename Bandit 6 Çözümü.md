
---

# Bandit 6 -> 7 Çözümü

## 1. Amaç (Objective)
Sunucunun tamamında, aşağıdaki özelliklere sahip dosyayı bularak bir sonraki seviyenin şifresini elde etmek:
- Sahibi (user): `bandit7`
- Grubu (group): `bandit6`
- Boyutu (size): 33 byte

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (En Verimli - `find`):** `find` komutunu, aramanın başlangıç noktasını kök dizin (`/`) olarak ayarlayarak ve arama kriterlerini (`-user`, `-group`, `-size`) belirterek kullanmak. Hata mesajlarını gizlemek için `2>/dev/null` kullanmak önemlidir.
*   **Yöntem B (İmkansız Manuel Arama):** Sunucudaki on binlerce dosyayı manuel olarak kontrol etmek. Bu yöntem pratik değildir ve imkansızdır.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `find` komutu ile tüm sunucuda arama yapma
```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```
*   **Açıklama:**
    *   `find /`: Aramaya kök dizinden (`/`), yani tüm dosya sisteminden başla.
    *   `-user bandit7`: Sadece sahibi `bandit7` olan dosyaları bul.
    *   `-group bandit6`: Sadece grubu `bandit6` olan dosyaları bul.
    *   `-size 33c`: Sadece boyutu tam olarak 33 byte (`c`haracters) olan dosyaları bul.
    *   `2>/dev/null`: Hata akışını (`2`, stderr) `/dev/null`'a (kara delik) yönlendirerek "Permission denied" gibi hata mesajlarını gizle.

### Adım 2: Bulunan dosyadaki şifreyi okuma
```bash
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

---

## 4. Öğrenilenler (What I Learned)
- **Kök Dizininden Arama:** `find` komutunu `/` ile başlatarak tüm dosya sisteminde arama yapabileceğimi öğrendim.
- **Metadata ile Arama:** Dosyaları sadece isme göre değil, aynı zamanda sahip, grup, boyut, izinler gibi metadata (üstveri) bilgilerine göre de arayabileceğimi anladım.
- **Hata Yönlendirme (`2>/dev/null`):** Komut çıktısını temiz tutmak ve sadece ilgili sonuçları görmek için hata mesajlarını nasıl gizleyeceğimi öğrendim. Bu, özellikle büyük aramalarda çok önemlidir.
- **Linux Dizin Yapısı:** Şifrenin `/var/lib/dpkg/info/` gibi beklenmedik bir yerde olabileceğini gördüm. Bu, Linux'un standart dizin yapısı (FHS) hakkında daha fazla bilgi edinmenin önemini gösteriyor.


---

# حل المستوى 6 -> 7 (بانديت)

## 1. الهدف من المستوى ده إيه؟

المطلوب مننا نلاقي ملف معين على **السيرفر كله**، مش بس في الفولدر اللي إحنا فيه. الملف ده ليه 3 شروط محددة:
1.  يكون المالك (user) بتاعه هو `bandit7`.
2.  يكون الجروب (group) بتاعه هو `bandit6`.
3.  يكون حجمه (size) بالظبط 33 بايت.

---

## 2. إزاي ممكن نحلها؟ (الطرق الممكنة)

*   **الطريقة الأولى (الأكثر كفاءة - باستخدام `find`):** نستخدم أمر `find` مرة تانية، بس المرة دي هنقوله يبدأ البحث من أول السيرفر خالص (من الـ root directory `/`)، وهنحدد له الشروط اللي بندور عليها (`-user`, `-group`, `-size`). وحاجة مهمة جدًا، هنستخدم `2>/dev/null` عشان نخفي رسايل الخطأ اللي هتظهر.

*   **الطريقة التانية (البحث اليدوي المستحيل):** نحاول ندور بنفسنا في عشرات الآلاف من ملفات السيرفر. طبعًا الطريقة دي مستحيلة عمليًا ومضيعة للوقت.

---

## 3. الأوامر الكاملة (من الآخر)

### الخطوة الأولى: نستخدم أمر `find` عشان ندور في السيرفر كله

```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```

*   **شرح بسيط:**
    *   `find /`: بقوله "ابدأ دور من أول السيرفر خالص (`/`)".
    *   `-user bandit7`: بقوله "هات لي بس الملفات اللي المالك بتاعها هو `bandit7`".
    *   `-group bandit6`: بقوله "وهاتها بس لو الجروب بتاعها هو `bandit6`".
    *   `-size 33c`: بقوله "وهاتها بس لو حجمها بالظبط 33 بايت (`c`haracters)".
    *   `2>/dev/null`: **(دي أهم حتة)** معناها "أي رسالة خطأ تظهر (زي `Permission denied`)، ارميها في الزبالة (`/dev/null`) ومتعرضهاش على الشاشة". ده بيخلي الناتج نضيف ومفيهوش غير النتيجة اللي إحنا عايزينها بس.

### الخطوة التانية: نقرا كلمة السر من الملف اللي لقيناه

```bash
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

---

## 4. أنا اتعلمت إيه من المستوى ده؟

*   **البحث من الـ Root:** اتعلمت إني ممكن أبدأ البحث بأمر `find` من `/` عشان أدور في كل حتة في النظام.
*   **البحث بالـ Metadata:** فهمت إني مش بس بدور على الملفات باسمها، لأ، أنا ممكن أدور عليها بخصائصها التانية زي المالك والجروب والحجم والصلاحيات (ودي بنسميها metadata أو بيانات عن البيانات).
*   **إعادة توجيه الأخطاء (`2>/dev/null`):** اتعلمت إزاي أخلي ناتج الأوامر بتاعتي نضيف، وإزاي أخفي رسايل الخطأ عشان أركز بس على النتايج المهمة. دي حركة مهمة جدًا خصوصًا في عمليات البحث الكبيرة.
*   **تركيبة الفولدرات في لينكس:** شفت إن كلمة السر ممكن تكون مستخبية في مكان غريب زي `/var/lib/dpkg/info/`. ده بيخليني أفهم أهمية إني أتعلم أكتر عن تركيبة الفولدرات القياسية في لينكس (FHS).

---

## 🇺🇸 English

### Objective
Find the password file anywhere on the server with these properties:
- Owner: `bandit7`
- Group: `bandit6`
- Size: 33 bytes

### Command
```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```

### Explanation
- `find /`: Search from root (entire filesystem)
- `-user bandit7`: Files owned by bandit7
- `-group bandit6`: Files belonging to group bandit6
- `-size 33c`: Exactly 33 bytes
- `2>/dev/null`: Suppress "Permission denied" errors

### Key Takeaways
- **System-wide Search:** Use `find /` to search the entire server
- **Metadata Search:** Find files by owner, group, size, permissions
- **Error Redirection:** `2>/dev/null` hides errors for cleaner output