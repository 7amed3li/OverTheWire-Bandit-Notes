  
Bu 10 karakterlik dize, bir dosya veya dizinle kimin ne yapabileceğini tanımlar. 4 ana bölüme ayrılır:

`[Tip] [Sahip] [Grup] [Diğerleri]`

**Örnek:** `drwxr-x--x`

#### **1. Dosya Tipi (İlk Karakter)**

İlk karakter dosyanın türünü belirtir.

| Karakter | Türkçe Anlamı     | İngilizce Anlamı |
| -------- | ----------------- | ---------------- |
| **`-`**  | **Dosya**         | Regular File     |
| **`d`**  | **Dizin**         | Directory        |
| **`l`**  | **Sembolik Link** | Symbolic Link    |

#### **2. İzin Kategorileri (Kullanıcı Grupları)**

Kalan 9 karakter 3'lü gruplar halinde okunur:

- **1. Üçlü (`rwx`):** **Sahip (Owner)** - Dosyayı oluşturan kullanıcının izinleri.
- **2. Üçlü (`r-x`):** **Grup (Group)** - Dosyanın ait olduğu gruptaki kullanıcıların izinleri.
- **3. Üçlü (`--x`):** **Diğerleri (Others)** - Sahip veya grup dışındaki tüm diğer kullanıcıların izinleri.

#### **3. İzin Türleri (r, w, x)**

Her üçlü grup içindeki harflerin anlamı:

| Harf    | Türkçe Anlamı  | İngilizce Anlamı | Açıklama                                                         |
| ------- | -------------- | ---------------- | ---------------------------------------------------------------- |
| **`r`** | **Okuma**      | Read             | Dosyayı okuyabilir, dizini listeleyebilirsin.                    |
| **`w`** | **Yazma**      | Write            | Dosyayı değiştirebilir, dizine dosya ekleyip silebilirsin.       |
| **`x`** | **Çalıştırma** | Execute          | Dosyayı program gibi çalıştırabilir, dizine girebilirsin (`cd`). |
| **`-`** | **İzin Yok**   | No Permission    | Belirtilen iznin olmadığını gösterir.                            |


#### **Örnek Analizi: `rw-r-----`**

- **`-`**: Bu bir **dosya**.
- **`rw-`**: **Sahibi**, dosyayı okuyabilir (`r`) ve yazabilir (`w`), ama çalıştıramaz (`-`).
- **`r--`**: **Grubu**, dosyayı sadece okuyabilir (`r`).
- **`---`**: **Diğerleri**, dosya üzerinde hiçbir izne sahip değil.