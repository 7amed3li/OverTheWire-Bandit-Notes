
---

# Bandit 6 -> 7 Çözümü

## 1. Amaç (Objective)
Sunucunun tamamında, aşağıdaki özelliklere sahip dosyayı bularak bir sonraki seviyenin şifresini elde etmek:
- Sahibi (user): `bandit7`
- Grubu (group): `bandit6`
- Boyutu (size): 33 byte

---

## 2. Olası Yöntemler (Possible Methods)
*   **Yöntem A (En Verimli - `find`):** `find` komutunu, aramanın başlangıç noktasını kök dizin (`/`) olarak ayarlayarak ve arama kriterlerini (`-user`, `-group`, `-size`) belirterek kullanmak. Hata mesajlarını gizlemek için `2>/dev/null` kullanmak önemlidir.
*   **Yöntem B (İmkansız Manuel Arama):** Sunucudaki on binlerce dosyayı manuel olarak kontrol etmek. Bu yöntem pratik değildir ve imkansızdır.

---

## 3. Tam Komutlar (Full Commands)

### Adım 1: `find` komutu ile tüm sunucuda arama yapma
```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```
*   **Açıklama:**
    *   `find /`: Aramaya kök dizinden (`/`), yani tüm dosya sisteminden başla.
    *   `-user bandit7`: Sadece sahibi `bandit7` olan dosyaları bul.
    *   `-group bandit6`: Sadece grubu `bandit6` olan dosyaları bul.
    *   `-size 33c`: Sadece boyutu tam olarak 33 byte (`c`haracters) olan dosyaları bul.
    *   `2>/dev/null`: Hata akışını (`2`, stderr) `/dev/null`'a (kara delik) yönlendirerek "Permission denied" gibi hata mesajlarını gizle.

### Adım 2: Bulunan dosyadaki şifreyi okuma
```bash
bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```

---

## 4. Öğrenilenler (What I Learned)
- **Kök Dizininden Arama:** `find` komutunu `/` ile başlatarak tüm dosya sisteminde arama yapabileceğimi öğrendim.
- **Metadata ile Arama:** Dosyaları sadece isme göre değil, aynı zamanda sahip, grup, boyut, izinler gibi metadata (üstveri) bilgilerine göre de arayabileceğimi anladım.
- **Hata Yönlendirme (`2>/dev/null`):** Komut çıktısını temiz tutmak ve sadece ilgili sonuçları görmek için hata mesajlarını nasıl gizleyeceğimi öğrendim. Bu, özellikle büyük aramalarda çok önemlidir.
- **Linux Dizin Yapısı:** Şifrenin `/var/lib/dpkg/info/` gibi beklenmedik bir yerde olabileceğini gördüm. Bu, Linux'un standart dizin yapısı (FHS) hakkında daha fazla bilgi edinmenin önemini gösteriyor.
