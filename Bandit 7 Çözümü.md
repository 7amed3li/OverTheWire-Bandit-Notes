
---
# Bandit 7 -> 8 Çözümü

## 1. Amaç (Objective)
`data.txt` adında büyük bir metin dosyasının içinde, "millionth" kelimesinin yanındaki şifreyi bulmak.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (En Verimli - `grep`):** `grep` komutunu kullanarak doğrudan "millionth" kelimesini içeren satırı aramak. Bu, en hızlı ve en temiz yöntemdir.
*   **Yöntem B (Manuel Arama):** `cat data.txt` veya `less data.txt` komutlarıyla dosyanın tamamını okumak ve istenen satırı gözle aramak. Bu yöntem çok yavaş ve verimsizdir.

---

## 3. Tam Komutlar (Full Commands)

### Komut 1 (Önerilen)
```bash
bandit7@bandit:~$ grep "millionth" data.txt
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```
*   **Açıklama:** `grep` komutu, `data.txt` dosyasını satır satır okur ve sadece "millionth" kelimesini içeren satırı ekrana basar.

### Komut 2 (Alternatif - Pipe Kullanımı)
```bash
bandit7@bandit:~$ cat data.txt | grep "millionth"
millionth       dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```
*   **Açıklama:** `cat` komutu dosyanın tüm içeriğini standart çıktıya gönderir. `|` (pipe) sembolü bu çıktıyı alır ve `grep` komutunun standart girdisi yapar. `grep` de bu girdiyi filtreler. Sonuç aynıdır, ancak bir adım fazladan içerir.

---

## 4. Öğrenilenler (What I Learned)
- **`grep` Komutunun Temel Kullanımı:** `grep`'in, büyük dosyalarda hızlı bir şekilde belirli bilgileri bulmak için ne kadar temel ve güçlü bir araç olduğunu öğrendim.
- **Pipe (`|`) vs. Argüman:** Bir komutun hem argüman olarak dosya alabildiğini (`grep "kelime" dosya.txt`) hem de pipe üzerinden veri alabildiğini (`cat dosya.txt | grep "kelime"`) anladım. Genellikle ilk yöntem daha verimlidir.
- **Problem Çözme Yaklaşımı:** Bir sorunla karşılaştığımda, mevcut araçları (verilen komut listesi) analiz edip görev için en uygun olanı seçme yeteneğimi geliştirdim.
