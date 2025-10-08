
---

# Bandit 2 -> 3 Çözümü

## 1. Amaç (Objective)
Adında hem boşluklar (spaces) hem de seçenek belirten çift tire (`--`) bulunan bir dosyayı okumak. Bu, iki sorunu aynı anda çözmeyi gerektirir.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A:** Boşlukları korumak için tırnak işaretleri (`" "`) kullanmak VE seçenek yorumlamasını durdurmak için çift tire (`--`) kullanmak.
*   **Yöntem B:** Dosya yolunu belirtmek için göreli yol (`./`) kullanmak VE her boşluktan kaçmak için ters eğik çizgi (`\`) kullanmak.

---

## 3. Tam Komutlar (Full Commands)

### Komut 1 (Yöntem A'ya göre)
```bash
cat -- "--spaces in this filename--"
```
*   **Açıklama:**
    *   `cat`: Dosyayı okumak için kullanılan komut.
    *   `--`: `cat`'e bundan sonra gelenlerin seçenek olmadığını, dosya adı olduğunu söyler.
    *   `"..."`: İçindeki metni tek bir argüman olarak kabul etmesini sağlar, böylece boşluklar korunur.

### Komut 2 (Yöntem B'ye göre)

```bash
cat ./--spaces\ in\ this\ filename--
```
*   **Açıklama:**
    *   `./`: Dosyanın mevcut dizinde olduğunu belirtir. Bu aynı zamanda `--` ile başlayan ismin seçenek olarak algılanmasını da engeller.
    *   `\`: Her boşluktan önce gelerek, boşluğun özel anlamını kaldırır ve onu dosya adının bir parçası yapar.

---

## 4. Öğrenilenler (What I Learned)
- **Tab Tamamlama (Tab Completion):** Bu gibi karmaşık durumlarda, `cat --s` yazıp `Tab` tuşuna basmak, kabuğun (shell) doğru komutu otomatik olarak oluşturmasını sağlar ve en iyi pratiktir.
- **Sorunları Ayrıştırma:** Bir sorunun birden fazla katmanı olabileceğini (boşluklar + tireler) ve her katmanı ayrı ayrı ele almam gerektiğini öğrendim.
