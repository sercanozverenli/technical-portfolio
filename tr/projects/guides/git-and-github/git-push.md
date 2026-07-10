# Yerel Değişiklikleri Sunucuya Gönderme ve Uzak Tarihçe Yönetimi

Bu doküman; yerel Git ortamında kaydedilen commit'lerin uzak sunucuya (GitHub) güvenli bir şekilde aktarılmasını, upstream bağının kurulmasını, etiket ve dalların toplu yönetimini, veri silme işlemlerini ve ekip çalışmasında yıkıcı sonuçlar doğurabilecek tarihçe ezme (`--force` ve `--force-with-lease`) parametrelerinin mimari işleyişini ele almaktadır.

---

## 1. Temel Git Push Mekanizması ve Upstream Kavramı

`git push` komutu, yerel deponuzda bulunan ve henüz uzak sunucuya gönderilmemiş olan commit geçmişini (tarihçeyi) buluttaki deponuza aktarır. Bu işlem sadece kod dosyalarını değil, o commit'lere ait tüm meta verileri ve hash değerlerini de sunucuya kopyalar.

### Upstream (-u / --set-upstream) Nedir?
Yerel bir daldan (branch) uzak sunucuya ilk kez push işlemi yapılırken, yerel dal ile uzak sunucudaki dal arasında kalıcı bir takip köprüsü kurulmalıdır. Bu köprüye **Upstream (Üst Kaynak)** adı verilir.

```bash
# Yerel "main" dalını uzak sunucudaki (origin) "main" dalına bağlayarak ilk push'u yapma
git push -u origin main
```

**Neden Kritik?**
* `-u` parametresini bir kez kullandıktan sonra, Git hangi yerel dalın hangi uzak dalla eşleştiğini hafızasına alır.
* Sonraki geliştirme süreçlerinde aynı dalda sadece `git push` veya `git pull` yazmanız yeterli olur; sunucu ve dal adını tekrar belirtmenize gerek kalmaz.

---

## 2. İleri Seviye Push Parametreleri ve Toplu İşlemler

Büyük projelerde veya sürüm yönetim süreçlerinde, dalları veya sürüm etiketlerini tek tek pushlamak yerine toplu transfer komutları tercih edilir.

### A. Tüm Dalları Aynı Anda Gönderme (--all)
Yerel bilgisayarınızda açtığınız ve uzak sunucuda henüz var olmayan tüm geliştirme dallarını tek bir komutla GitHub'a göndermek için kullanılır.

```bash
# Yereldeki tüm branch yapısını uzak sunucuya toplu olarak pushlar
git push origin --all
```

### B. Sürüm Etiketlerini Gönderme (--tags)
Yerelde `git tag` komutuyla oluşturulan sürüm/yayın etiketleri (Örn: v1.0.0, v2.1.3), standart bir `git push` komutu çalıştırıldığında güvenlik gerekçesiyle sunucuya **gönderilmez**. Etiketleri GitHub'daki "Releases" sayfasına yansıtmak için özel parametre kullanılmalıdır.

```bash
# Yerelde oluşturulmuş tüm versiyon etiketlerini sunucuya toplu aktarır
git push origin --tags
```

---

## 3. Tehlikeli Bölge: Tarihçeyi Ezmek (--force vs. --force-with-lease)

Yerel depoda `git reset` veya `git rebase` gibi komutlarla commit geçmişini değiştirdikten sonra standart bir `git push` yapmaya çalışırsanız, GitHub sunucusu yerel tarihçeniz ile uzak sunucu tarihçesinin uyuşmadığını görerek işlemi **reddedir (Rejected)**. Bu engeli aşmak için tarihçeyi zorla ezmek gerekir.

### A. Yıkıcı Kuvvet: git push --force (-f)
Sunucudaki mevcut tüm commit geçmişini tamamen siler ve yerel bilgisayarınızda bulunan tarihçeyi onun üzerine yazar.

```bash
# Sunucu tarihçesini körü körüne yerel tarihçe ile değiştirir (TEHLİKELİ)
git push origin main --force
```

* **Mimarideki Tehlikesi:** Siz push yapmadan 5 dakika önce bir takım arkadaşınız sunucuya yeni commit'ler göndermiş olabilir. `--force` kullandığınızda, arkadaşınızın yazdığı ve sizin bilgisayarınızda henüz var olmayan o güncel kodlar sunucudan **kalıcı olarak silinir**. Takım çalışmalarında asla kullanılmamalıdır.

### B. Güvenli Alternatif: git push --force-with-lease
`--force` komutunun körü körüne yıkıcı etkisini engellemek için geliştirilmiş kurumsal bir koruma mekanizmasıdır. 

```bash
# Sadece sunucu durumundan haberdarsanız tarihçeyi ezer (GÜVENLİ)
git push origin main --force-with-lease
```

**Mekanizma Nasıl Çalışır?**
1. Git, sizin yereldeki uzak sunucu haritanızı (`origin/main` ref değerini) uzak sunucunun gerçek durumuyla karşılaştırır.
2. Eğer siz en son `git fetch` yaptıktan sonra sunucuya başka biri kod pushlamışsa, sunucudaki durum değiştiği için Git işlemi **engeller**.
3. Eğer sizden sonra kimse kod göndermediyse, işlem güvenli bir şekilde tamamlanır ve tarihçe ezilir. Böylece takım arkadaşlarınızın kodunu yanlışlıkla silmemiş olursunuz.

---

## 4. Uzak Sunucudan Dal (Branch) Silme

Geliştirmesi biten ve ana koda (main) başarıyla birleştirilen yan dalların (feature branches) GitHub üzerinde kalabalık yapmaması için temizlenmesi gerekir. Bu işlem yereldeki dalı silmez, sadece uzak sunucudaki kopyasını yok eder.

```bash
# Uzak sunucudaki (origin) "payment-fix" isimli dalı tamamen siler
git push origin --delete payment-fix
```

---

## 5. Doğrulama ve Güvenli Push İş Akışı

Uzak sunucuya veri gönderirken beklenmedik çakışmaları (conflict) ve veri kayıplarını önlemek için şu profesyonel iş akışı sırasıyla uygulanmalıdır:

```bash
# Adım 1: Push yapmadan önce sunucudaki güncel durumu yerel haritaya indirin
git fetch origin

# Adım 2: Yerel dalınız ile sunucu dalı arasında bir fark veya çakışma var mı kontrol edin
git status

# Adım 3: Eğer sunucu sizden ilerideyse, değişiklikleri çakışmasız şekilde yerelinize entegre edin
git pull --rebase origin main

# Adım 4: Her şey temiz ve çakışmasız ise commit'lerinizi güvenle uzak sunucuya gönderin
git push origin main
```
