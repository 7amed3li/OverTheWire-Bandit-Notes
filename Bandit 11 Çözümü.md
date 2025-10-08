
---

# Bandit 11 -> 12 Çözümü

## 1. Amaç (Objective)
`data.txt` dosyasının içinde bulunan ve ROT13 şifrelemesi ile "döndürülmüş" olan metni çözerek bir sonraki seviyenin şifresini elde etmek.

---

## 2. Temel Kavramlar (Core Concepts)

###  **ROT13 Nedir? (Çocuklar İçin Anlatım)**

Merhaba arkadaşlar! Bugün alfabe ile sihirli bir oyun oynayacağız. Oyunumuzun adı **ROT13**.

#### **Adım: Alfabeyi İkiye Bölmek**

İngiliz alfabesinde 26 harf var, değil mi? Hadi bu alfabeyi tam ortadan ikiye bölelim. Tıpkı bir elmayı ikiye bölmek gibi!

- **İlk Yarı (13 Harf):** `A B C D E F G H I J K L M`
    
- **İkinci Yarı (13 Harf):** `N O P Q R S T U V W X Y Z`
    

Şimdi elimizde iki tane takım var.

#### **Adım: Sihirli Kural: "Karşı Takıma Zıpla!"**

ROT13 oyununun tek bir kuralı var: "Bir harf seçtiğinde, onun diğer takımdaki tam karşılığına zıpla."

Hadi deneyelim:

- Seçtiğimiz harf **A** olsun. **A**, ilk takımın en başında.
    
    - İkinci takımın en başındaki harf ne? **N**.
    - Demek ki **A**'nın şifresi **N**'dir.
- Seçtiğimiz harf **C** olsun. **C**, ilk takımda 3. sırada.
    
    - İkinci takımda 3. sıradaki harf ne? **P**.
    - Demek ki **C**'nin şifresi **P**'dir.

Peki tersten yaparsak ne olur?

- Seçtiğimiz harf **N** olsun. **N**, ikinci takımın en başında.
    - İlk takımın en başındaki harf ne? **A**.
    - Demek ki **N**'nin şifresi de **A**'dır.

Gördünüz mü? Sihir burada! **Şifrelemek için yaptığın hareketin aynısı, şifreyi çözmek için de işe yarıyor!**

#### **Adım: Özet**

**ROT13**, bir harfi, alfabenin diğer yarısındaki tam karşılığı ile değiştirmektir.

- `A` <-> `N`
- `B` <-> `O`
- `C` <-> `P`
- ...ve bu şekilde devam eder.

İşte bu kadar basit! Bilgisayara da tam olarak bu "karşılığını bul" görevini verdiğimizde, bizim için bütün metni anında çeviriyor.

### `tr` Komutu Nasıl Çalışır?
`tr` (translate) komutu, karakter tabanlı çeviri veya silme işlemleri yapar. Temel yapısı şöyledir:
`tr 'GİRDİ_KÜMESİ' 'ÇIKTI_KÜMESİ'`
Bu komut, `GİRDİ_KÜMESİ`'ndeki her karakteri, `ÇIKTI_KÜMESİ`'ndeki aynı pozisyondaki karakterle değiştirir.

---

## 3. Komutun Mantığını Anlamak (Understanding the Command's Logic)

ROT13 işlemini `tr` ile yapmak için doğru girdi ve çıktı kümelerini oluşturmalıyız.

1.  **Girdi Kümesi (INPUT_SET):** Çevirmek istediğimiz tüm karakterler. Bunlar, sıralı alfabenin tamamıdır.
    *   `'a-zA-Z'` (Tüm küçük ve büyük harfler)

2.  **Çıktı Kümesi (OUTPUT_SET):** Bu, Girdi Kümesi'nin ROT13 uygulanmış halidir.
    *   **Küçük Harfler İçin:**
        *   Alfabenin ilk yarısı (`a-m`), ikinci yarısı (`n-z`) olur.
        *   Alfabenin ikinci yarısı (`n-z`), ilk yarısı (`a-m`) olur.
        *   Bu yüzden çıktı kümesi: `n-z` ve ardından `a-m` gelir. -> `'n-za-m'`
    *   **Büyük Harfler İçin:**
        *   Aynı mantıkla, çıktı kümesi: `'N-ZA-M'`
    *   **Tam Çıktı Kümesi:** İkisini birleştiririz: `'n-za-mN-ZA-M'`

Bu mantıkla, `tr` komutuna "her 'a' gördüğünde 'n' yaz, her 'n' gördüğünde 'a' yaz" demiş oluruz.

---

## 4. Tam Komut (Full Command)

```bash
bandit11@bandit:~$ cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'
The password is JQttfApK4SeyHwDlIusA1337OQ7n3r0h
```
*   **Açıklama:**
    1.  `cat data.txt`: ROT13 ile şifrelenmiş metni okur.
    2.  `|`: Çıktıyı `tr` komutuna yönlendirir.
    3.  `tr 'a-zA-Z' 'n-za-mN-ZA-M'`: Gelen metindeki her harfi, ROT13 kuralına göre karşılığı olan harfle değiştirir ve sonucu ekrana basar.

---

## 5. Öğrenilenler (What I Learned)

- **`tr` Komutunun Gücü:** `tr` komutunun, karakter tabanlı karmaşık dönüşüm ve "çeviri" işlemleri için ne kadar esnek bir araç olduğunu öğrendim.
- **Şifreleme Kavramları:** ROT13 gibi basit ikame şifrelerinin (substitution ciphers) nasıl çalıştığını anladım.
- **Mantıksal Küme Oluşturma:** Bir komutun parametrelerini (özellikle `tr` için girdi/çıktı kümeleri) oluşturmak için problemi mantıksal adımlara ayırmanın önemini kavradım.
- **Problem Çözme:** Bir problemi çözmek için sadece komutu bilmenin yetmediğini, komutun çalışma mantığını derinden anlamanın gerektiğini öğrendim.

