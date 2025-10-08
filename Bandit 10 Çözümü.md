
---
# Bandit 10 -> 11 Çözümü

## 1. Amaç (Objective)
`data.txt` dosyasının içinde bulunan ve Base64 ile kodlanmış (encoded) olan veriyi çözerek (decode) bir sonraki seviyenin şifresini elde etmek.

---

## 2. Temel Kavramlar (Core Concepts)

### Base64 Nedir?
Base64, ikili (binary) verileri (resimler, programlar, vb.) metin tabanlı formatlara dönüştürmek için kullanılan bir kodlama şemasıdır. Amacı veriyi gizlemek (şifrelemek) değil, verinin metin tabanlı sistemlerde (e-posta, HTML, vb.) sorunsuz bir şekilde taşınmasını sağlamaktır. Base64, veriyi sadece 64 karakterlik güvenli bir alfabe (A-Z, a-z, 0-9, +, /) kullanarak temsil eder.

### Kodlama (Encoding) vs. Çözme (Decoding)
*   **Kodlama (Encoding):** Herhangi bir veriyi Base64 formatına dönüştürme işlemidir. `base64` komutu, varsayılan olarak bu işlemi yapar.
    *   `base64 data.txt` -> `data.txt` dosyasını Base64 olarak kodlar.
*   **Çözme (Decoding):** Base64 formatındaki bir metni orijinal verisine geri dönüştürme işlemidir. `base64` komutuna `-d` seçeneği eklenerek bu işlem yapılır.
    *   `base64 -d data.txt` -> `data.txt` dosyasını Base64'ten çözer.

---

## 3. Tam Komutlar (Full Commands)

### Yöntem 1 (Pipe | Kullanımı)
```bash
bandit10@bandit:~$ cat data.txt | base64 -d
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```
*   **Açıklama:**
    1.  `cat data.txt`: `data.txt` dosyasının Base64 kodlu içeriğini okur ve standart çıktıya gönderir.
    2.  `|`: Pipe operatörü, bu çıktıyı `base64` komutunun standart girdisi olarak yönlendirir.
    3.  `base64 -d`: Girdiyi alır ve `-d` (decode) seçeneği sayesinde Base64'ten orijinal metne çözer.

### Yöntem 2 (Input Redirection < Kullanımı)
```bash
bandit10@bandit:~$ base64 -d < data.txt
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```
*   **Açıklama:** Bu daha kısa ve verimli bir yöntemdir.
    1.  `base64 -d`: Komutu çözme modunda başlatır.
    2.  `< data.txt`: Komutun standart girdisini klavye yerine doğrudan `data.txt` dosyasından almasını sağlar.

---

## 4. Öğrenilenler (What I Learned)
- **Base64 Tanıma:** Uzun, anlamsız görünen ve genellikle `=` ile biten metinlerin Base64 olabileceğini öğrendim.
- **Komutun Varsayılan Davranışı:** Bir komutun (örneğin `base64`) varsayılan olarak kodlama yaptığını ve davranışını değiştirmek için `-d` gibi seçeneklerin (options/flags) kullanılması gerektiğini anladım.
- **Veri Yönlendirme Yöntemleri:** Bir komuta veri beslemek için hem pipe (`|`) hem de input redirection (`<`) kullanabileceğimi ve her birinin ne zaman daha uygun olduğunu öğrendim.
- **Kodlama vs. Şifreleme:** Kodlamanın (Encoding) amacının veri formatını değiştirmek olduğunu, şifrelemenin (Encryption) ise veriyi yetkisiz erişime karşı korumak olduğunu anladım. Base64 bir kodlama yöntemidir, güvenlik yöntemi değildir.
