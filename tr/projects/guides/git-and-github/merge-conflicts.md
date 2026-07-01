# Gelişmiş Merge Stratejileri ve Çakışma (Conflict) Yönetimi

Bu doküman; Git versiyon kontrol sisteminin arka plandaki birleştirme algoritmalarını, commit ağaçlarının mimari davranışlarını ve aynı dosya üzerinde yapılan çakışan değişikliklerin manuel/kod düzeyinde nasıl çözüleceğini uçtan uca kapsar.

---

## Bölüm 1: Birleşme Teorileri ve Arka Plan Mekanizması

Git, iki farklı dalı birleştirirken (`git merge`) hedef ve kaynak dalların commit geçmiş grafiklerini (commit graph) analiz eder. Bu analiz sonucunda geçmişin yapısına göre iki ana stratejiden birini otomatik olarak uygular.

### 1. Fast-Forward Merge (Hızlı İleri Sarım)

#### Mimari Şartlar
Kaynak dal (örn: `feature`) ana daldan (`main`) ayrıldıktan sonra, `main` dalı üzerinde hiçbir yeni commit (`isolated commit`) atılmamışsa bu strateji tetiklenir.

#### Mekanizma ve Grafik Yapısı
Git geçmişi çatallanmamış, doğrusal bir çizgi olarak algılar. Yeni bir birleşme commit'i üretilmesine gerek yoktur. Git sadece `main` dalının işaretçisini (pointer) ve `HEAD` kavramını, `feature` dalının en son commit'inin üzerine taşır.

```text
[Önce]
A --- B (main)
       \
        C --- D (feature)

[Sonra - Fast-Forward]
A --- B --- C --- D (main, feature, HEAD)
```

#### Terminal Uygulama Senaryosu
```bash
# Hedef dala geçiş
git switch main

# Birleştirme işlemi
git merge feature

# Terminal Çıktısı:
# Updating b3a2f1c..d4e5f6a
# Fast-forward
#  index.html | 2 ++
#  1 file changed, 2 insertions(+)
```

### 2. Three-Way Merge (Üç Yönlü Birleştirme)

#### Mimari Şartlar
Kaynak dal açıldıktan sonra, hem kaynak dalda hem de hedef dalda (`main`) eşzamanlı ve bağımsız commit'ler atılarak geçmiş iki farklı yöne doğru çatallanmışsa bu strateji tetiklenir.

#### Mekanizma ve Grafik Yapısı
Git geçmişi düz bir çizgi halinde ileri saramaz. Bu nedenle ortak geçmişi bulmak adına 3 farklı commit noktasını referans alır:
1.  **Ortak Ata (Common Ancestor / Base)**: İki dalın geçmişte ayrıldığı ilk ortak commit noktası.
2.  **Hedef Dal Commit'i (Mine / Ours)**: `main` dalının güncel uç commit'i.
3.  **Kaynak Dal Commit'i (Theirs)**: Birleştirilmek istenen dalın güncel uç commit'i.

Git bu üç noktayı algoritmik olarak birleştirir ve geçmiş ağacını doğrusal hale getirmek için **"Merge Commit"** adında özel, iki ebeveyni (parent) olan yeni bir kayıt üretir.

```text
[Önce]
A --- B --- C (main)
       \
        D --- E (feature)

[Sonra - Three-Way Merge]
A --- B --- C --------- F (main, HEAD)
       \               /
        D --- E ------- (feature)
```

#### Terminal Uygulama Senaryosu
```bash
git switch main
git merge feature

# Git, otomatik olarak varsayılan metin editörünü açarak commit mesajı ister.
# Varsayılan Mesaj: "Merge branch 'feature' into main"
# Editör kaydedilip kapatıldığında terminal çıktısı:
```
```text
Merge made by the 'ort-ort' strategy.
 app.js | 4 ++++
 1 file changed, 4 insertions(+)
```

---

## Bölüm 2: Merge Conflict (Birleşme Çakışması) Anatomisi

### Çakışma Koşulları
Three-Way Merge algoritması çalışırken, aynı dosyanın aynı satırı (veya satır aralığı) her iki dalda da değiştirilmişse, Git hangi satırın üzerine yazılması gerektiğine karar veremez. Otomatik birleştirme süreci durdurulur, indeks kilitlenir ve süreç askıya alınır.

### Somut Çakışma Senaryosu ve Dosya Değişim Süreci

**Ortak Başlangıç Noktası (Commit B)**: `config.json` dosyası içeriği her iki daldada başlangıçta şu şekildedir:
```json
{
  "theme": "default",
  "port": 3000
}
```

*   **`main` Dalındaki Değişiklik (Commit C)**: Port numarası `5000` yapıldı.
*   **`feature` Dalındaki Değişiklik (Commit E)**: Aynı satırdaki port numarası `8080` yapıldı.

`main` dalındayken `git merge feature` komutu çalıştırıldığında terminal şu hatayı üretir:
```text
Auto-merging config.json
CONFLICT (content): Merge conflict in config.json
Automatic merge failed; fix conflicts and then commit the result.
```

---

## Bölüm 3: Dosya Düzeyinde Çakışma Çözümü

Çakışma gerçekleştikten sonra Git, `config.json` dosyasının içeriğini manuel müdahaleye uygun hale getirmek için özel işaretçiler (`conflict markers`) yerleştirerek şu şekilde yeniden yazar:

```json
{
  "theme": "default",
<<<<<<< HEAD
  "port": 5000
=======
  "port": 8080
>>>>>>> feature
}
```

### İşaretçilerin Yapısal Analizi
*   **`<<<<<<< HEAD`**: Bu satır ile `=======` arasındaki blok, birleştirmeyi başlattığınız aktif dalda (`main`) bulunan mevcut kod satırıdır.
*   **`=======`**: Çakışan iki farklı kod varyasyonunun sınır çizgisidir.
*   **`>>>>>>> feature`**: `=======` ile bu satır arasındaki blok, içeri çekilmeye çalışılan daldan gelen kod satırıdır.

### Manuel Çözüm ve İş Akışı Sıralaması

#### Adım 1: Askıdaki Durumu İnceleme
```bash
git status
```
```text
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   config.json
```

#### Adım 2: Kodu Temizleme ve Birleştirme
Yazılımcı, projenin mimari ihtiyacına göre iki kod bloğundan birini seçer veya ikisini harmanlar. `<<<<<<< HEAD`, `=======` ve `>>>>>>> feature` işaretçi satırları dosyadan tamamen temizlenir.

*Nihai Karar (Örnek: Portun 8080 olması gerektiği kararlaştırıldı)*. Dosya şu şekilde düzenlenir ve kaydedilir:
```json
{
  "theme": "default",
  "port": 8080
}
```

#### Adım 3: Çözümü Git İndeksine Bildirme
Temizlenen dosya staging area'ya alınarak Git'e çakışmanın çözüldüğü bilgisi verilir.
```bash
git add config.json
```

#### Adım 4: Birleşme Commit'ini Kapatma
Yarım kalan Three-Way Merge süreci yeni bir commit atılarak kalıcı olarak tamamlanır.
```bash
git commit -m "Fix: config.json dosyasındaki port çakışması 8080 olarak çözüldü"
```

---

## Bölüm 4: Acil Durum Yönetimi ve Süreç İptali

### git merge --abort

#### Açıklama
Çakışma karmaşıklığı anlaşılamadığında, çakışan satır sayısı projenin bütünlüğünü riske atacak kadar fazla olduğunda veya yanlış dallar birleştirilmeye çalışıldığında süreci tamamen durdurmak için kullanılır.

#### Mekanizma
Çalışma alanındaki (Working Directory) ve hazırlık alanındaki tüm yarıda kalmış birleşme verilerini temizler. Proje durumunu, `git merge` komutu girilmeden önceki tam sürüme (son kararlı commit noktasına) kusursuzca geri döndürür.

```bash
git merge --abort
```

