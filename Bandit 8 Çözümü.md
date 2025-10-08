
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
