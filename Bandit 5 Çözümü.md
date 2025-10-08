
---
# Bandit 5 -> 6 Çözümü

## 1. Amaç (Objective)
Çok katmanlı bir dizin yapısı içinde, aşağıdaki özelliklere sahip dosyayı bulmak:
- İnsan tarafından okunabilir (human-readable)
- Boyutu 1033 byte
- Çalıştırılamaz (not executable)

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (En Verimli - `find`):** `find` komutunu, dosya türü (`-type`), dosya boyutu (`-size`) ve dosya izinleri (`-perm` veya `-executable`) gibi testlerle kullanarak doğrudan hedef dosyayı bulmak.
*   **Yöntem B (Manuel Arama):** Her bir alt dizine `cd` ile girip, `ls -la` ile dosyaları listelemek ve özelliklerini manuel olarak kontrol etmek. Bu yöntem çok yavaş ve hataya açıktır.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `inhere` dizinine girme ve keşif
```bash
bandit5@bandit:~$ cd inhere
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere02  maybehere04  maybehere06  maybehere08  maybehere10  maybehere12  maybehere14  maybehere16  maybehere18
maybehere01  maybehere03  maybehere05  maybehere07  maybehere09  maybehere11  maybehere13  maybehere15  maybehere17  maybehere19
```

### Adım 2: `find` komutu ile hedef dosyayı bulma
```bash
bandit5@bandit:~/inhere$ find . -type f -size 1033c
./maybehere07/.file2
```
*   **Açıklama:**
    *   `find .`: Aramaya mevcut dizinden (`.`) başla.
    *   `-type f`: Sadece dosyaları (`f`ile) bul, dizinleri (`d`irectory) bulma.
    *   `-size 1033c`: Sadece boyutu tam olarak 1033 byte (`c`haracters) olan dosyaları bul.

### Adım 3: Şifreyi okuma
```bash
bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

---

## 4. Öğrenilenler (What I Learned)
- **`find` Komutunun Gücü:** `find` komutunun, dosya sisteminde karmaşık aramalar yapmak için ne kadar esnek ve güçlü olduğunu öğrendim.
- **`find` Testleri:** `-type`, `-size`, `-name`, `-perm` gibi testleri birleştirerek arama sonuçlarını hassas bir şekilde filtreleyebileceğimi anladım.
- **Birimlerin Önemi:** `find` komutunda `-size` kullanırken, `c` (bytes), `k` (kilobytes), `M` (megabytes) gibi birim belirteçlerinin ne kadar önemli olduğunu öğrendim.


