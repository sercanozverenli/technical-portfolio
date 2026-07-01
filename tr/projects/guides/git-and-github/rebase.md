# Git Rebase Rehberi

Bu dokümantasyon, Git versiyon kontrol sistemindeki en güçlü ve en çok tartışılan komutlardan biri olan `git rebase` mekanizmasını, mimarisini, en iyi kullanım pratiklerini (Best Practices) ve interaktif rebase süreçlerini derinlemesine ele almaktadır.

---

## 1. Temel Komut Yapısı ve Çalıştırılması

Bir `feature` (özellik) branch'inde çalışırken, `main` branch'indeki güncel değişiklikleri kendi branch'inizin arkasına almak için temel rebase komut dizisi şu şekildedir:

```bash
# 1. Ana branch'teki en güncel değişiklikleri yerel bilgisayarınıza çekin
git checkout main
git pull origin main

# 2. Üzerinde çalıştığınız özellik branch'ine geçiş yapın
git checkout feature-branch

# 3. Rebase işlemini başlatın
git rebase main
```

**Alternatif (Tek Satırda):**
```bash
git rebase main feature-branch
```
*Bu komut, Git'e doğrudan `feature-branch` üzerinde `main` branch'ini temel alarak rebase yapmasını söyler.*

---

## 2. Git Rebase Nedir? Mimari Analiz

`git rebase`, üzerinde çalışılan bir branch'in başlangıç noktasını (base) başka bir commit veya branch'in en güncel ucuna **yeniden konumlandırma** işlemidir. 

### Çalışma Mekanizması:
1. Git, hedef branch (`main`) ile mevcut branch'inizin (`feature`) ortak olduğu son ortak commit'i (ata commit) bulur.
2. Mevcut branch'inizde o ortak commit'ten sonra attığınız tüm commit'leri geçici bir alana (`.git/rebase-merge/`) kaydeder.
3. Mevcut branch'inizi sıfırlayarak hedef branch'in en güncel commit'ine getirir.
4. Geçici alana kaydedilen commit'leri, yeni başlangıç noktasının üzerine sırasıyla **tek tek yeniden uygular (apply)**.

> **Kritik Bilgi:** Rebase işlemi mevcut commit'leri fiziksel olarak taşımaz. Onların içeriğini kopyalayarak **yeni hash değerlerine (SHA-1) sahip yepyeni commit'ler** oluşturur. Eski commit'ler ise Git'in çöp toplama (Garbage Collection) mekanizması tarafından silinene kadar arka planda kalır.

---

## 3. Git Rebase vs. Git Merge

| Özellik | Git Merge | Git Rebase |
| :--- | :--- | :--- |
| **Geçmiş Yapısı** | Dallı budaklı, gerçek zamanlı kronolojik tarihçe. | Lineer (düz bir çizgi), temiz ve sıralı tarihçe. |
| **Commit Yapısı** | Yeni bir "Merge Commit" oluşturur. | Mevcut commit'leri yeniden yazar, yeni commit oluşturmaz. |
| **İzlenebilirlik** | Branch'in nereden ayrılıp nereye birleştiği net görünür. | Branch'ler sanki hiç ayrılmamış, hep ana kolda yazılmış gibi görünür. |
| **Çakışma Çözümü** | Tüm çakışmalar (conflict) tek seferde çözülür. | Çakışmalar commit commit çözülür (interaktif süreç). |

---

## 4. Kullanılması Zorunlu mudur?

**Hayır, zorunlu değildir.** Bir projenin tarihçesinin nasıl yönetileceği tamamen takımın veya projenin mimari standartlarına bağlıdır. 

* **Rebase Akışını Seçenler:** Proje geçmişinde binlerce anlamsız *"Merge branch 'main' into..."* commit'i görmek istemeyen, temiz ve geriye dönük incelenebilir bir lineer tarihçe hedefleyen ekipler tercih eder.
* **Merge Akışını Seçenler:** Proje geçmişinin manipüle edilmesini istemeyen, her şeyin tam olarak hangi gün ve saatte birleştiğini orijinal hash değerleriyle korumak isteyen ekipler tercih eder.

---

## 5. En İyi Kullanım Senaryoları (Do's)

### A. Yerel (Local) Branch Güncelleme
Uzak sunucuya (remote) henüz push'lamadığınız, sadece kendi bilgisayarınızda geliştirdiğiniz bir özelliği, projenin ana kolundaki değişikliklerle güncellerken rebase kullanmak en güvenli yaklaşımdır.

### B. Pull Request (PR) Öncesi Geçmişi Temizleme
Bir özelliği tamamlayıp ana projeye dahil edilmesini talep etmeden önce, yerel commit geçmişinizi düzenlemek için kullanılır. Proje yöneticisine tertemiz bir commit dizisi sunmanızı sağlar.

---

## 6. Kesinlikle Kullanılmaması Gereken Senaryolar (Don'ts)

### 🚨 Rebase Altın Kuralı (The Golden Rule of Rebasing)
> **"Uzak sunucuya (remote) gönderilmiş ve başkaları tarafından kullanılan bir public branch üzerinde ASLA rebase yapmayın."**

* **Senaryo:** `main`, `develop` veya ekiple ortak çalıştığınız bir `feature-xyz` branch'ini uzak sunucuya push'ladınız. Ardından yerelde bu branch üzerinde rebase yapıp geçmişi değiştirdiniz ve `git push --force` ile sunucuya bastınız.
* **Sonuç:** Takım arkadaşlarınız kendi yerel bilgisayarlarında `git pull` yaptıklarında Git, sunucudaki geçmiş ile yereldeki geçmişin tamamen koptuğunu görecektir. Aynı commit'lerin farklı hash'lerle mükerrer oluşmasına, devasa çakışmalara (conflict) ve veri kayıplarına yol açarsınız.

---

## 7. İnteraktif Rebase (`git rebase -i`) ile Tarihçe Manipülasyonu

İnteraktif rebase, uzak sunucuya gönderilmemiş yerel commit geçmişinizi yayınlamadan önce bir editör eşliğinde baştan aşağı düzenlemenizi sağlayan güçlü bir yönetim aracıdır.

### Komutun Tetiklenmesi:
Son 3 commit üzerinde düzenleme yapmak için:
```bash
git rebase -i HEAD~3
```

Bu komut çalıştırıldığında Git, varsayılan metin editörünüzde (örn. Vim, VS Code) en eskiden en yeniye doğru ilgili commit'leri listeleyen bir dosya açar:

```text
pick a1b2c3d İlk taslak oluşturuldu
pick e5f6g7h Yazım hataları düzeltildi
pick j9k0l1m Test senaryoları eklendi

# Komutlar:
# p, pick <commit> = commit'i olduğu gibi kullan
# r, reword <commit> = commit'i kullan ama commit mesajını değiştir
# e, edit <commit> = commit'i kullan ama içerik değiştirmek için durakla
# s, squash <commit> = commit'i kullan, bir önceki commit ile birleştir
# f, fixup <commit> = squash gibi, ama bu commit'in mesajını sil
# d, drop <commit> = commit'i tamamen geçmişten sil
```

### Uygulamalı İnteraktif Rebase Senaryoları:

#### 1. Commit Mesajını Düzeltmek (`reword`)
Eğer bir commit mesajında yazım hatası yaptıysanız veya daha açıklayıcı yazmak istiyorsanız:
* Editörde ilgili commit'in başındaki `pick` kelimesini `reword` (veya sadece `r`) olarak değiştirin.
* Dosyayı kaydedip kapatın.
* Git, size sadece o commit'in mesajını yeniden yazabileceğiniz yeni bir editör penceresi açacaktır. Mesajı düzeltip kaydedin.

#### 2. Birden Fazla Commit'i Tek Bir Commit Haline Getirmek (`squash` / `fixup`)
Geliştirme esnasında *"ufak fix"*, *"test"* gibi çok sayıda küçük ve anlamsız commit attıysanız ve bunları tek bir anlamlı commit altında toplamak istiyorsanız:
```text
pick a1b2c3d Ana özellik geliştirildi
squash e5f6g7h Ufak hata düzeltmesi yapıldı
fixup j9k0l1m Kod stili düzenlendi
```
* **`squash`:** `e5f6g7h` commit'inin değişikliklerini üstteki `a1b2c3d` ile birleştirir ve her iki commit mesajını da birleştirip yeni bir mesaj yazmanıza izin verir.
* **`fixup`:** `j9k0l1m` commit'inin değişikliklerini de birleştirir ancak bu commit'in mesajını çöpe atar, ana başlığı kirletmez.

#### 3. Commit Sıralamasını Değiştirmek
Eğer commit'lerin mantıksal sırasını değiştirmek istiyorsanız:
* Metin editöründeki satırların yerini değiştirmeniz (kes-yapıştır yapmanız) yeterlidir.
* Git, commit'leri yukarıdan aşağıya doğru yeni dizilime göre sırayla yeniden uygulayacaktır.

#### 4. Bir Commit'i Tamamen Silmek (`drop`)
* İlgili satırın başındaki `pick` ibaresini `drop` (veya `d`) yapın ya da o satırı dosyadan tamamen silin. Dosyayı kaydettiğinizde o commit geçmişten tamamen temizlenir.

---

## 8. Rebase Esnasında Çakışma (Conflict) Yönetimi

Rebase işlemi commit'leri tek tek uyguladığı için, çakışmalar da commit adımında karşınıza çıkar. Bir çakışma yaşandığında rebase süreci durur.

1. `git status` komutu ile çakışan dosyaları tespit edin.
2. Kod editörünüzde çakışmaları (Conflict) manuel olarak çözün.
3. Çözülen dosyaları sahneye ekleyin:
   ```bash
   git add <dosya-adi>
   ```
4. **DİKKAT:** Rebase esnasında asla yeni bir commit atılmaz (`git commit` yapılmaz). İşleme devam etmek için:
   ```bash
   git rebase --continue
   ```
5. Eğer işler çok karmaşık bir hal alırsa ve rebase işlemini tamamen iptal edip hiçbir şey olmamış gibi eski haline dönmek isterseniz:
   ```bash
   git rebase --abort
   ```
