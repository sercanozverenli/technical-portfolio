# Sürümlendirme ve Yayın Yönetimi (Tags & Releases)

Bu doküman; geliştirme döngüsünde belirli kilometre taşlarına (örneğin ilk kararlı sürüm veya büyük bir yama) ulaşıldığında kodun o anki durumunu dondurup etiketleme (Tagging) işlemlerini, Semantik Sürümlendirme (SemVer) standartlarını ve bu etiketleri GitHub üzerinde indirilebilir, resmi sürümlere (Releases) dönüştürme süreçlerini ele almaktadır.

---

## 1. Semantik Sürümlendirme (Semantic Versioning - SemVer)

Git etiketleri oluşturulurken endüstri standardı olan **SemVer (Semantik Sürümlendirme)** mantığı kullanılır. Bir sürüm numarası genellikle üç basamaktan oluşur: `v(Major).(Minor).(Patch)`

Örneğin; geliştirmekte olduğunuz PiratesButGoodOnes (PBGO) veya veri doğrulama katmanlarını içeren Amplify Core gibi mimarilerde versiyonlamayı şu mantıkla kurgulamalısınız:

* **Major (Ana Sürüm - v1.x.x):** Projede geriye dönük uyumluluğu bozan, çok büyük ve köklü mimari değişiklikler yapıldığında artırılır. (Örn: Veritabanı yapısının tamamen değişmesi).
* **Minor (Alt Sürüm - x.1.x):** Geriye dönük uyumluluğu bozmadan, projeye yeni ve büyük bir özellik (feature) eklendiğinde artırılır.
* **Patch (Yama - x.x.1):** Yeni bir özellik eklenmeyen, sadece mevcut koddaki ufak tefek hataların (bugfix) ve güvenlik açıklarının kapatıldığı durumlarda artırılır.

---

## 2. Git Tag (Etiket) Mimarisi

Bir Git etiketi (tag), belirli bir commit'e atanan ve zaman içinde asla değişmeyen (sabitlenmiş) bir işaretçidir. Branch'ler (dallar) yeni commit'ler geldikçe ileriye doğru hareket ederken, Tag'ler atandıkları commit'te sabit kalırlar.

İki tür etiketleme yöntemi vardır:

### A. Hafif Etiket (Lightweight Tag)
Sadece belirli bir commit'e isim vermek için kullanılır. Ekstra bir meta veri (kimin oluşturduğu, tarih, açıklama mesajı) barındırmaz. Sadece yerel denemeler veya geçici işaretlemeler için uygundur.

```bash
# Bulunduğunuz aktif commit'e hafif bir etiket ekler
git tag v1.0.1
```

### B. Açıklamalı Etiket (Annotated Tag - Önerilen)
Gerçek sürüm yayınları için kullanılması zorunlu olan standarttır. Kendi başına tam bir Git objesidir; etiketi kimin oluşturduğunu, oluşturulma tarihini, GPG imzasını ve bir açıklama mesajını barındırır.

```bash
# -a (annotated) ve -m (mesaj) parametreleriyle etiket oluşturma
git tag -a v1.1.0 -m "Amplify Core: Veri doğrulama katmanı entegre edildi"
```

---

## 3. Geçmiş Commit'leri Etiketleme ve Analiz

Etiketler her zaman en son commit'e (HEAD) atılmak zorunda değildir. Geçmişte atılmış ve kararlı olduğu sonradan fark edilmiş bir commit'e de etiket atanabilir.

```bash
# 1. Önce geçmiş commit'lerin hash değerlerini (Örn: 9fceb02) bulun
git log --oneline

# 2. İlgili hash değerini komutun sonuna ekleyerek geçmişi etiketleyin
git tag -a v0.9.0 9fceb02 -m "Beta sürümü aday versiyonu"
```

**Etiketleri Listeleme ve İnceleme:**
```bash
# Yereldeki tüm etiketleri listeler
git tag

# Belirli bir desene uyan etiketleri arar (Örn: Sadece v1 ile başlayanlar)
git tag -l "v1.*"

# Açıklamalı bir etiketin tüm detaylarını (kim, ne zaman, hangi mesajla) gösterir
git show v1.1.0
```

---

## 4. Etiketleri Uzak Sunucuya (GitHub) Gönderme

En çok yapılan hatalardan biri, yerelde oluşturulan etiketlerin standart bir `git push` komutu ile sunucuya gideceğini düşünmektir. **Varsayılan olarak `git push` komutu etiketleri sunucuya GÖNDERMEZ.** Etiketleri ayrıca göndermeniz gerekir.

```bash
# Senaryo 1: Sadece spesifik bir etiketi uzak sunucuya (origin) gönderme
git push origin v1.1.0

# Senaryo 2: Yerelde oluşturduğunuz TÜM etiketleri tek seferde sunucuya pushlama
git push origin --tags
```

---

## 5. Etiket Silme İşlemleri (Yerel ve Uzak)

Hatalı veya yanlış commit'e atanan bir etiketi silmek için hem yerel veritabanında hem de uzak sunucuda işlem yapmak gerekir.

```bash
# 1. Etiketi yerel bilgisayarınızdan silin (-d: delete)
git tag -d v1.1.0

# 2. Silinme işlemini uzak sunucuya da yansıtarak oradan da kaldırın
git push origin --delete v1.1.0
```

---

## 6. GitHub Releases (Sürüm Yayınlama)

Git etiketleri (`v1.0.0`) kod bazında bir işaretçidir. **GitHub Releases** ise bu etiketleri alıp son kullanıcıların (veya diğer geliştiricilerin) okuyabileceği, indirebileceği bir "Ürün Sayfasına" dönüştüren GitHub'a özel bir arayüz özelliğidir.

Bir GitHub Release sayfası şunları içerir:
* **Release Notes (Sürüm Notları):** O sürümde nelerin değiştiğinin, hangi hataların düzeltildiğinin insan dilinde yazılmış bir özeti (Changelog).
* **Derlenmiş Dosyalar (Assets):** Eğer projeniz derlenen bir yazılımsa (Örn: `.exe`, `.dmg`, `.apk`), kodların derlenmiş son hali buraya yüklenir.
* **Kaynak Kodu (Source Code):** O anki versiyonun kodlarının bulunduğu otomatik oluşturulmuş `.zip` ve `.tar.gz` arşivi.

### GitHub Arayüzünden Release Nasıl Oluşturulur?
1.  GitHub deponuzun ana sayfasında sağ taraftaki **Releases** bölümüne tıklayın.
2.  **"Draft a new release"** (Yeni bir sürüm tasarla) butonuna basın.
3.  **"Choose a tag"** kısmından daha önce pushladığınız bir etiketi seçin (veya arayüzden yeni bir etiket yazın, GitHub sizin yerinize o etiketi oluşturur).
4.  Otomatik oluşturulan PR geçmişini sürüm notlarına eklemek için **"Generate release notes"** butonuna tıklayın.
5.  Derlenmiş binary dosyalarınız varsa (Asset) yükleyin ve **"Publish release"** diyerek canlıya alın.
