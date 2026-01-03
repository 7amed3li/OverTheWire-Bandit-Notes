

---

### **Bandit Seviye 26 -> 27 (Setuid'nin Gücü) Kılavuzu**

Bu seviye, `setuid` izinlerinin temel konseptine geri dönüyor ve bize yüksek yetkilere sahip bir programın, basit olsa bile, bir sonraki seviyeye geçmek için nasıl bir kapı olabileceğini hatırlatıyor.

#### **Bölüm 1: Olay Yeri Analizi (Hedefi Anlamak)**

**Belirtilen Hedef:** "Harika bir kabuk elde ettin! Şimdi acele et ve `bandit27`'nin şifresini al!"

**Zihinsel Analiz:**
1.  **Bağlam:** Bir önceki seviyeden kaçtıktan sonra, `bandit26` kullanıcısının normal bir `bash` kabuğundayız.
2.  **Görev:** Hedef basit ve doğrudan: `bandit27`'nin şifresini bulmak.
3.  **Sonuç:** Önceki birçok seviyede olduğu gibi, en olası senaryo, ev dizininde özel izinlere sahip, özellikle de `setuid` biti ayarlanmış bir programın bulunmasıdır. Bu program, `bandit27` kullanıcısının yetkileriyle çalışacak ve bizim için şifre dosyasını okumamızı sağlayacaktır.

**İlk Plan (Dedektif Planı):**
1.  **Giriş:** `bandit26` kullanıcısı olarak giriş yap.
2.  **Keşif:** Ev dizinindeki dosyaları ve izinlerini listelemek için `ls -la` komutunu kullan. Özellikle `rws` iznine sahip bir dosya ara.
3.  **Araştırma:** Bulunan `setuid` programını çalıştırarak ne işe yaradığını anlamaya çalış.
4.  **İstismar:** Programın işlevselliğini kullanarak `/etc/bandit_pass/bandit27` dosyasını okumak için bir yol bul.

#### **Bölüm 2: Soruşturma Araçları (Komutların Açıklaması)**

| Araç | Türkçe Açıklama | Bu Seviyedeki Rolü |
| :--- | :--- | :--- |
| **`ls -la`** | **Detaylı Listeleme** | Olay yerini incelemek ve özel izinlere sahip (`-rwsr-x---` gibi) dosyaları tespit etmek için temel aracımız. |
| **`bandit27-do`** | **Setuid Programı** | Bu, seviyenin anahtarıdır. `bandit27` kullanıcısının yetkileriyle komutları çalıştıran bir "geçit" programıdır. |
| **`cat`** | **Dosya İçeriğini Görüntüleme** | `bandit27-do` programı aracılığıyla şifre dosyasının içeriğini okumak için kullanacağımız son komut. |

#### **Bölüm 3: Soruşturma Yolculuğu (Gerçek Terminal Kaydı)**

İşte tam olarak yaşandığı gibi, adım adım tüm hikaye.

##### **Aşama 1: Giriş ve Keşif**

Öncelikle, doğru şifreyi kullanarak `bandit26` olarak giriş yaparız ve hemen `ls -la` ile etrafı kolaçan ederiz.

```bash
bandit26@bandit:~$ ls -la
total 24
drwxr-xr-x  2 root     root     4096 Oct 14 09:26 .
drwxr-xr-x 41 root     root     4096 Oct 20 16:35 ..
-rwsr-x---  1 bandit27 bandit26 7496 Oct 14 09:26 bandit27-do
-rw-r--r--  1 root     root      220 Mar 31  2024 .bash_logout
-rw-r--r--  1 root     root     3851 Oct 14 09:19 .bashrc
-rw-r--r--  1 root     root      807 Mar 31  2024 .profile
```

**Analiz:** Gözümüz hemen `-rwsr-x---` izinlerine sahip `bandit27-do` dosyasına takılıyor. `s` harfi, bu programın çalıştırıldığında sahibinin (`bandit27`) yetkileriyle çalışacağını gösterir. Bu bizim altın biletimiz.

##### **Aşama 2: Programı Araştırma**

Programın nasıl kullanılacağını anlamak için onu argümansız çalıştırırız.

```bash
bandit26@bandit:~$ ./bandit27-do
Run a command as another user.
  Example: ./bandit27-do id
```

**Analiz:** Program, Seviye 19'daki programa çok benziyor. Kendisine verilen herhangi bir komutu, `bandit27` kullanıcısı olarak çalıştırıyor.

##### **Aşama 3: İstismar ve Şifreyi Ele Geçirme**

Artık her şey açık. Programı, `bandit27`'nin şifre dosyasını okumak için kullanacağız.

```bash
bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
YnQpBuifNMas1hcUFk70Zmq13378j0VO
```

**Nihai Karar:**
`bandit27-do` programı, `cat /etc/bandit_pass/bandit27` komutunu aldı ve `bandit27` kullanıcısının yetkileriyle çalıştırdı. `bandit26` kullanıcısı normalde bu dosyayı okuma iznine sahip olmasa da, `setuid` programı sayesinde bu kısıtlamayı aştık ve şifreyi başarıyla elde ettik.

**`bandit27` seviyesinin şifresi: `upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB`**

---

## 🇺🇸 English

### Objective
Use another SUID binary to read the next password.

### Command
```bash
./bandit27-do cat /etc/bandit_pass/bandit27
YnQpBuifNMas1hcUFk70Zmq13378j0VO
```

### Explanation
- Same concept as Level 19
- `bandit27-do` is SUID-enabled, runs as `bandit27`
- Execute `cat` to read the protected password file

### Key Takeaways
- **SUID Exploitation:** Common privilege escalation technique
- **Run commands as another user:** The core purpose of SUID helpers