# Uzak Sunucudan Veri Çekme ve Senkronizasyon (Fetch vs. Pull)

Bu doküman; uzak sunucudaki (GitHub) değişikliklerin yerel depoya nasıl aktarılacağını, `git fetch` ve `git pull` komutları arasındaki mimari ve güvenlik farklarını, temiz bir commit tarihçesi oluşturmak için `--rebase` parametresinin kullanımını detaylandırmaktadır.

---

## 1. Güvenli İndirme: git fetch Mimarisi

`git fetch` komutu, uzak sunucudaki (remote) tüm yeni commit'leri, branch'leri ve tag'leri yerel bilgisayarınıza indirir ancak **aktif olarak çalıştığınız kodlara veya dosyalarınıza asla dokunmaz.** Arka planda sadece yerel Git veritabanınızdaki uzak sunucu haritasını (`origin/main`, `origin/development` vb.) günceller.

```bash
# Uzak sunucudaki (origin) tüm güncellemeleri yerel veritabanına indir
git fetch origin

# Sadece spesifik bir daldaki güncellemeleri indir
git fetch origin main
```

**Neden ve Ne Zaman Kullanılmalı?**
* Takım arkadaşlarınızın neleri değiştirdiğini, kendi kodunuzu bozma riski olmadan incelemek istediğinizde.
* İnternetsiz bir ortama geçmeden önce sunucudaki son durumu bilgisayarınıza yedeklemek için.
* (İpucu) Fetch işleminden sonra sunucu ile yerel kodunuz arasındaki farkı görmek için şu komutu kullanabilirsiniz:
  `git diff main..origin/main`

---

## 2. İndir ve Birleştir: git pull Mimarisi

`git pull`, uzak sunucudaki güncel değişiklikleri indiren ve anında aktif olarak çalıştığınız yerel kod dalına (branch) entegre eden (merge) bileşik bir komuttur.

Mimari olarak `git pull` bağımsız bir komut değildir. Arka planda şu iki komutun peş peşe çalıştırılmış halidir:
`git pull = git fetch + git merge`

```bash
# Uzak sunucudaki main dalını indir ve yereldeki aktif dala otomatik birleştir
git pull origin main
```

**Tehlikeleri Nelerdir?**
Eğer siz ve takım arkadaşınız aynı dosyanın aynı satırlarında farklı kodlar yazdıysanız, `git pull` komutu anında bir **Merge Conflict (Çakışma)** yaratır ve çalışan projenizi geçici olarak bozuk bir duruma sokar. Bu nedenle büyük projelerde körü körüne `git pull` yapmak yerine önce `fetch` yapıp durumu incelemek daha profesyoneldir.

---

## 3. Karşılaştırmalı Analiz: Fetch vs. Pull

| Özellik / Metrik | `git fetch` | `git pull` |
| :--- | :--- | :--- |
| **Çalışma Alanı (Working Dir)** | Dosyalarınızı kesinlikle değiştirmez. | Dosyalarınızı günceller/değiştirir. |
| **Kod Güvenliği** | %100 Güvenlidir. Çakışma (Conflict) yaratmaz. | Risk barındırır. Çakışma yaratabilir. |
| **Kullanım Amacı** | Bilgi almak ve durumu gözlemlemek. | Kodu doğrudan entegre etmek ve eşitlemek. |

---

## 4. İleri Seviye Senkronizasyon: git pull --rebase

Standart bir `git pull` işlemi, uzak sunucudaki kodlarla yerel kodlarınızı birleştirirken genellikle fazladan bir "Merge Commit" (Birleştirme Commiti) oluşturur. Çok geliştiricili projelerde bu durum, commit tarihçesinin bir spagettiye dönmesine neden olur.

Tarihçeyi düz (lineer) ve temiz tutmak için `--rebase` parametresi kullanılır.

```bash
# Sunucudaki kodları indir, benim commitlerimi geçici olarak kaldır, sunucu kodlarını koy, benim commitlerimi onların üstüne yeniden ekle
git pull --rebase origin main
```

**Mekanizma Nasıl Çalışır?**
1. Git, yerelde yaptığınız ama henüz sunucuya göndermediğiniz commit'leri bir kenara ayırır.
2. Sunucudaki en güncel commit'leri alır ve yerel projenize yerleştirir (Fast-forward).
3. Kenara ayırdığı sizin commit'lerinizi, sunucudan gelen en son commit'in ucuna sırayla yeniden uygular.
4. Sonuç olarak gereksiz merge commit'leri oluşmaz, tarihçe tek bir düz çizgi halinde ilerler.

---

## 5. Fast-Forward (İleri Sarma) ve Çakışma Yönetimi

`git pull` işlemi sırasında Git, iki farklı senaryo ile karşılaşır:

* **Fast-Forward Merge:** Eğer siz kodu indirdikten sonra yerelde hiçbir yeni commit yapmadıysanız, sunucudan gelen kodlar sadece mevcut kodunuzun ilerisine eklenir. Buna "İleri Sarma" denir ve sorunsuz gerçekleşir.
* **Diverged (Ayrışmış) Tarihçe:** Hem siz yerelde yeni bir şeyler yazmış (commit) hem de başka biri sunucuya yeni kod göndermişse yollar ayrışmıştır. Bu durumda standart `git pull` birleştirme commiti atar, `git pull --rebase` ise tarihi düzleştirir. Çakışma (conflict) varsa Git işlemi durdurur ve sizden dosyaları manuel olarak düzeltmenizi ister.
