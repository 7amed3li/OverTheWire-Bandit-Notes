
---
# Bandit 1 -> 2 Çözümü

## 1. Amaç (Objective)
Mevcut dizinde adı `-` (tire) olan bir dosyayı okumak. Bu dosya adı, komut satırında özel bir anlama sahip olduğu için doğrudan `cat -` komutuyla okunamaz.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A:** Dosyanın mevcut dizinde olduğunu belirten göreli bir yol (`./`) kullanmak. Bu, kabuğa (shell) `-` karakterini bir dosya adı olarak yorumlaması için sinyal verir.
*   **Yöntem B:** Kabuğun girdi yönlendirme (`<`) özelliğini kullanarak dosyanın içeriğini `cat` komutuna "beslemek".

---

## 3. Tam Komutlar (Full Commands)

### Komut 1 (Yöntem A'ya göre)
```bash
cat ./-
```
*   **Açıklama:**
    *   `cat`: Dosyayı okumak için kullanılan komut.
    *   `./`: Kabuğa, mevcut dizindeki bir dosyaya başvurduğumuzu açıkça belirtir. Bu, `-` karakterinin özel anlamını (standart girdi) geçersiz kılar ve onu bir dosya adı olarak ele alır.

### Komut 2 (Yöntem B'ye göre)
```bash
cat < -
```
*   **Açıklama:**
    *   `<`: Girdi yönlendirme operatörü. Kabuğa, adı `-` olan dosyanın içeriğini alıp `cat` komutunun standart girdisine yönlendirmesini söyler. `cat` komutu da bu girdiyi alıp standart çıktıya (ekrana) basar.

---

## 4. Öğrenilenler (What I Learned)
- **Özel Karakterler (Special Characters):** Komut satırındaki bazı karakterlerin (`-`, `<`, `>`) özel anlamları olduğunu ve bu anlamları geçersiz kılmak veya kullanmak için farklı teknikler olduğunu öğrendim.
- **Yolun Önemi (The Importance of Paths):** Bir dosya yolunu (`./`) açıkça belirtmenin, komutların argümanları nasıl yorumladığını değiştirebileceğini anladım. Bu, belirsizliği ortadan kaldırmak için güçlü bir tekniktir.
```

---
