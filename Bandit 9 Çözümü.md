
---

# Bandit 9 -> 10 Çözümü

## 1. Amaç (Objective)
`data.txt` adında bir ikili (binary) dosyanın içinde, birkaç `=` karakterinden sonra gelen ve okunabilir olan şifreyi bulmak.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (Filtreleme - `strings` + `grep`):** `strings` komutu ile dosyadaki tüm okunabilir metinleri ayıklamak, ardından bu çıktıyı `grep` komutuna yönlendirerek sadece `====` içeren satırları filtrelemek. Bu, en sistematik yöntemdir.
*   **Yöntem B (Görsel İnceleme - `strings`):** `strings` komutunun çıktısını doğrudan incelemek ve şifreyi gözle bulmak. Bu seviyede, okunabilir metin sayısı az olduğu için bu yöntem de oldukça etkiliydi.

---

## 3. Tam Komutlar (Full Commands)

### Komut 1 (Filtreleme Yöntemi)
```bash
bandit9@bandit:~$ strings data.txt | grep "===="
========== the
========== password
Q========== is%
>u`9J========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```
*   **Açıklama:**
    *   `strings data.txt`: `data.txt` dosyasındaki anlamsız ikili verileri atar ve sadece okunabilir metin dizelerini çıkarır.
    *   `| grep "===="`: `strings` komutunun temiz çıktısını alır ve sadece içinde `====` geçen satırları gösterir. Şifre, bu satırlardan birinde açıkça görülür.

### Komut 2 (Görsel İnceleme Yöntemi)
```bash
bandit9@bandit:~$ strings data.txt
# (Çıktının bir kısmı)
...
g`UZ
========== password
mgaw
...
>u`9J========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
...
```
*   **Açıklama:** Bu yöntemde, `strings` komutunun tüm çıktısı ekrana basılır ve kullanıcı, deseni (`====` ve ardından gelen şifre) gözleriyle arar.

---

## 4. Öğrenilenler (What I Learned)
- **`strings` Komutunun Önemi:** İkili (binary) dosyalarla çalışırken, `strings` komutunun, içlerindeki okunabilir metinleri ayıklamak için ne kadar kritik bir araç olduğunu öğrendim.
- **İkili Dosya Analizi:** Bir dosyanın içeriği anlamsız göründüğünde, `cat` yerine `file` veya `strings` gibi araçları kullanmam gerektiğini anladım.
- **Duruma Göre Strateji Seçimi:** Bazen en karmaşık komut zincirinin gerekli olmadığını, basit bir görsel incelemenin daha hızlı olabileceğini öğrendim. Önemli olan, duruma en uygun çözümü seçmektir.
