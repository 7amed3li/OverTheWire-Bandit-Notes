
---

# Bandit 3 -> 4 Çözümü

## 1. Amaç (Objective)
`inhere` adında bir dizin içinde bulunan ve adı `.` ile başlayan "gizli" bir dosyadan bir sonraki seviyenin şifresini bulmak.

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (Adım Adım Keşif):**
    1.  `ls -la` komutu ile mevcut dizindeki tüm dosyaları ve dizinleri listelemek.
    2.  Bulunan `inhere` dizinine `cd` komutu ile girmek.
    3.  `inhere` dizini içinde tekrar `ls -la` komutunu çalıştırarak adı `.` ile başlayan dosyayı bulmak.
    4.  `cat` komutu ile dosyanın içeriğini okumak. Dosya adı özel karakterler içeriyorsa, tırnak işaretleri (`" "`) kullanmak iyi bir pratiktir.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `inhere` dizinini bulma
Mevcut dizinde `ls -la` çalıştırıldığında, `inhere` adında bir dizin bulunur.
```bash
bandit3@bandit:~$ ls -la
total 24
drwxr-xr-x   3 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 150 root    root    4096 Aug 15 13:18 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Aug 15 13:09 .bashrc
drwxr-xr-x   2 root    root    4096 Aug 15 13:16 inhere
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile
```

### Adım 2: Dizin içine girme ve dosyayı bulma
```bash
bandit3@bandit:~$ cd inhere
bandit3@bandit:~/inhere$ ls -la
total 12
drwxr-xr-x 2 root    root    4096 Aug 15 13:16 .
drwxr-xr-x 3 root    root    4096 Aug 15 13:16 ..
-rw-r----- 1 bandit4 bandit3   33 Aug 15 13:16 ...Hiding-From-You
```

### Adım 3: Şifreyi okuma
```bash
bandit3@bandit:~/inhere$ cat "...Hiding-From-You"
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```

---

## 4. Öğrenilenler (What I Learned)
- **Gizli Dosya Tanımı:** Linux'ta bir dosyanın "gizli" olması için adının **sadece bir nokta** (`.`) ile başlaması yeterlidir. `...` ile başlayan bir dosya teknik olarak gizli değildir, ancak `ls`'in standart çıktısında görünmeyebilir ve kafa karıştırıcı olabilir.
- **`-a` Seçeneğinin Gücü:** `ls -a` komutu, adı `.` veya `..` ile başlayan her şeyi gösterir. Bu, dizin yapısını anlamak için kritik öneme sahiptir.
- **Doğrulama ve Güven:** Her zaman kendi komutlarımın çıktısına güvenmeliyim, varsayımlara veya eski bilgilere değil. Gördüğüm şey gerçektir.
- **Tırnak İşaretleri:** Garip karakterler içeren dosya adlarıyla uğraşırken, adı tırnak işaretleri (`" "`) içine almak her zaman güvenli bir yaklaşımdır.
