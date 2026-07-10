# Açık Kaynak Mimarisi: Fork ve Pull Request (PR)

Bu doküman; doğrudan yazma/değiştirme yetkiniz olmayan başkalarına ait projelere (Açık Kaynak veya farklı takım depolarına) nasıl güvenli bir şekilde katkıda bulunabileceğinizi, Fork mekanizmasını, kod birleştirme talebi olan Pull Request (PR) süreçlerini ve Code Review (Kod İnceleme) dinamiklerini kapsamlı bir şekilde ele almaktadır.

---

## 1. Fork Mimarisi Nedir?

GitHub üzerinde bir projeye kod göndermek (push) istiyorsanız, o deponun sahibi tarafından size "Collaborator" (Ortak) yetkisi verilmiş olması gerekir. Ancak açık kaynak dünyasında binlerce geliştiriciye yetki vermek imkansızdır. Bu sorunu çözen mekanizma **Fork (Çatallama)** sistemidir.

**Fork**, başkasına ait bir GitHub deposunun tam bir kopyasını (commit geçmişiyle birlikte) alıp, kendi GitHub hesabınızın altına yerleştirme işlemidir. Artık bu kopya tamamen size aittir ve üzerinde dilediğiniz gibi değişiklik yapıp pushlayabilirsiniz.

### A. Fork Edilmiş Projeyi Yerel Bilgisayara İndirme
Önce GitHub arayüzünden ilgili projenin sağ üst köşesindeki "Fork" butonuna basarak projeyi kendi hesabınıza kopyalayın. Ardından kendi hesabınızdaki bu kopyayı bilgisayarınıza indirin:

```bash
# Orijinal projeyi DEĞİL, kendi hesabınızdaki fork kopyasını klonlayın
git clone git@github.com:kendi-kullanici-adiniz/proje-adi.git

cd proje-adi
```

### B. Upstream (Orijinal Kaynak) Bağlantısını Kurma
Siz kendi fork'unuz üzerinde çalışırken, orijinal proje başkalarının katkılarıyla güncellenmeye devam edecektir. Kendi deponuzun geride kalmasını önlemek için yerel bilgisayarınızdaki Git'e orijinal deponun adresini (upstream) öğretmeniz gerekir.

```bash
# Orijinal deponun adresini "upstream" adıyla uzak depo (remote) olarak ekleyin
git remote add upstream git@github.com:orijinal-sahip/proje-adi.git

# Bağlantıları kontrol ederek origin (sizin deponuz) ve upstream'in (orijinal depo) varlığını doğrulayın
git remote -v
```

---

## 2. Fork'u Orijinal Proje ile Senkronize Tutmak

Kendi fork'unuzu, orijinal projede yapılan son değişikliklerle eşitlemek (güncellemek) için şu profesyonel adımlar izlenir:

```bash
# 1. Orijinal depodaki (upstream) yeni güncellemeleri yerel veritabanınıza indirin (kodunuzu ezmez)
git fetch upstream

# 2. Yerelde ana dalınızda (genellikle main veya master) olduğunuza emin olun
git checkout main

# 3. Orijinal depodan indirdiğiniz güncel kodları kendi ana dalınızla birleştirin
git merge upstream/main

# 4. Kendi ana dalınızı güncelledikten sonra, bu yeni durumu kendi GitHub deponuza (origin) yansıtın
git push origin main
```
*Bu işlemi düzenli yapmak, ileride açacağınız PR'larda çakışma (conflict) çıkma ihtimalini sıfıra yaklaştırır.*

---

## 3. Pull Request (PR) Nedir ve Nasıl Açılır?

Kendi fork'unuzda veya şirketin projesindeki bir yan dalda (branch) geliştirmeyi tamamladınız. Bu kodların ana projeye (main branch) dahil edilmesi için başlattığınız resmi onay ve inceleme sürecine **Pull Request (Birleştirme Talebi)** denir. Yani depo sahibine *"Benim kodlarımı kendi kodlarının içine çeker (pull) misin?"* diye talepte bulunursunuz.

### PR Öncesi Geliştirme İş Akışı:
Ana dal (main) üzerinde doğrudan çalışmak yerine, her yeni özellik veya hata çözümü için izole bir dal açmak endüstri standardıdır.

```bash
# 1. Yeni bir özellik için main dalından ayrılarak yeni bir dal oluşturun ve oraya geçin
git checkout -b feature/login-sayfasi

# 2. Kodlarınızı yazın ve değişiklikleri kaydedin
git add .
git commit -m "feat: login sayfası UI tasarımı eklendi"

# 3. Bu yeni dalı kendi GitHub deponuza (origin) gönderin
git push -u origin feature/login-sayfasi
```

### GitHub Arayüzünden PR Açma:
1. Kodları pushladıktan sonra kendi GitHub deponuza girdiğinizde üstte sarı/yeşil bir bar ile **"Compare & pull request"** butonu belirir. Bu butona tıklayın.
2. Hedef (Base) olarak orijinal projenin `main` dalını, kaynak (Compare) olarak kendi deponuzun `feature/login-sayfasi` dalını seçin.
3. PR açıklamasına; bu kodun neyi çözdüğünü, hangi issue'yu kapattığını (Örn: `Closes #42`) detaylıca yazıp **"Create pull request"** butonuna basın.

---

## 4. Code Review (Kod İnceleme) ve Değişiklik Talepleri

PR açıldıktan sonra takım liderleri veya proje sahipleri (Maintainers) yazdığınız kodu inceler. Bu aşamada GitHub arayüzünden satır satır yorum yapabilir veya değişiklik talep edebilirler (Request Changes).

**PR'ı Güncellemek:**
Açık olan bir PR'ı güncellemek için yeni bir PR açmanıza **gerek yoktur**. PR'ı bağladığınız yerel dalda (`feature/login-sayfasi`) istenen düzeltmeleri yapıp aynı dala yeniden push atarsanız, GitHub bu yeni commit'leri otomatik olarak mevcut PR'ın içine ekler.

```bash
# İstenen düzeltmeleri yapın
git add .
git commit -m "fix: code review sonrası buton renkleri güncellendi"
git push origin feature/login-sayfasi
```

---

## 5. Branch Protection (Dal Koruması) ve PR Zorunluluğu

Kurumsal projelerde `main` veya `production` gibi kritik dallara doğrudan kod pushlamak (`git push origin main`) genellikle **yasaktır**. Depo yöneticileri GitHub Settings üzerinden bu dallara **Branch Protection Rules** ekler.

**Sık Kullanılan Koruma Kuralları:**
* **Require a pull request before merging:** Kodu doğrudan pushlayamazsınız, mutlaka bir yan dal açıp PR üzerinden gelmeniz zorunludur.
* **Require approvals:** PR'ın birleştirilebilmesi (merge) için en az X sayıda yetkili kişinin kodu inceleyip "Approve" (Onayla) vermesi gerekir.
* **Require status checks to pass:** PR açıldığında GitHub Actions üzerinden otomatik testler çalışır. Bu testler başarıyla geçmeden (yeşil tik almadan) kod ana branch'e birleştirilemez.
