# GitHub Temelleri ve Depo Mimarisi

Bu doküman; GitHub platformunun mimari çalışma mantığını, uzak sunucu depolarının (repository) yapılandırma süreçlerini, Public ve Private depoların güvenlik/maliyet dinamiklerini, kimlik doğrulama protokollerini (HTTPS vs. SSH) ve GitHub arayüz bileşenlerini en ince ayrıntısına kadar ele almaktadır.

---

## 1. GitHub Nedir ve Neden Gereklidir?

Git, projenizin versiyon geçmişini kendi bilgisayarınızda (yerel/local) takip eden dağıtık bir versiyon kontrol sistemidir. GitHub ise bu yerel Git depolarının bulutta (remote) barındırılmasını, yedeklenmesini ve ekipler arası işbirliği (collaboration) süreçlerinin yönetilmesini sağlayan web tabanlı bir platformdur.

### Temel Sütunları:
* **Merkezi Dağıtık Yapı:** Bilgisayarınızın donanımsal olarak zarar görmesi durumunda, projenin tüm commit tarihçesi bulut üzerinde eksiksiz korunur.
* **Asenkron Çalışma:** Birden fazla geliştirici, aynı kod tabanının farklı kısımları üzerinde birbirlerinin kodunu ezmeden eşzamanlı çalışabilir.
* **Açık Kaynak Ekosistemi:** Dünya genelindeki projelere katkıda bulunma (Open Source Contribution) ve kod inceleme (Code Review) kültürünün merkezidir.

---

## 2. Görünürlük ve Erişim Mimarisi: Public vs. Private

GitHub üzerinde bir depo oluştururken seçilen görünürlük seviyesi, projenin erişim izinlerini, maliyet politikasını ve otomasyon sınırlarını doğrudan etkiler.

| Özellik / Metrik | Public (Açık) Depo | Private (Gizli) Depo |
| :--- | :--- | :--- |
| **Görünürlük** | İnternetteki herkes kodları inceleyebilir. | Sadece depo sahibi ve davet edilen ortaklar (Collaborators) görebilir. |
| **Yazma/Değişiklik İzni** | Sadece izin verilen yetkili kullanıcılar push yapabilir. | Sadece açıkça davet edilmiş yetkili kullanıcılar push yapabilir. |
| **Arama Motoru İndeksi** | Google ve GitHub Keşfet (Explore) üzerinde indekslenir. | Tamamen gizlidir, arama motorları tarayamaz. |
| **GitHub Actions Maliyeti** | Tamamen ücretsiz ve sınırsız dakikaya sahiptir. | Ücretsiz hesaplar için aylık 2000 dakika ile sınırlıdır. |
| **Güvenlik Taramaları** | Dependabot ve Gizli Bilgi (Secret) taramaları ücretsizdir. | Gelişmiş kurumsal taramalar için ücretli plan gerekir. |

### Güvenlik Uyarısı
Public depolarda kodun herkes tarafından okunabiliyor olması büyük bir güvenlik riski doğurabilir. Yanlışlıkla commit edilen `.env` dosyaları, API anahtarları (AWS, Firebase vb.) veya veritabanı şifreleri, otomatik botlar tarafından saniyeler içinde taranıp ele geçirilebilir. Bu tür dosyalar depoya gönderilmeden önce mutlak suretle `.gitignore` kapsamına alınmalıdır.

---

## 3. İlk Deponun Kurulumu ve Yapılandırma Dosyaları

Yeni bir repository oluşturulurken sistemin temiz ve standartlara uygun başlaması için üç temel dosya mimariye dahil edilir:

### A. README.md (Proje Vitrini)
Markdown formatında yazılan bu dosya, projenin ana sayfasında görüntülenir. Bir geliştiricinin veya potansiyel işverenin projeyle karşılaştığında okuduğu ilk yerdir. İyi bir README.md şunları içermelidir:
* Projenin amacı ve ne işe yaradığı.
* Bağımlılıklar (Requirements) ve kurulum adımları (Installation).
* Çalıştırma komutları (Usage).
* Katkıda bulunma yönergeleri (Contributing).

### B. .gitignore Şablonları
Proje tipine göre (Python, Node.js, Java, C++ vb.) yerel işletim sisteminin veya derleyicilerin ürettiği geçici dosyaların (`node_modules/`, `__pycache__/`, `.DS_Store`, `.env`) uzak sunucuya gönderilmesini engeller. Depo açılış ekranında ilgili dil seçilerek otomatik oluşturulması en sağlıklı yöntemdir.

### C. Lisanslama (License) Mimarisi
Public projelerde kodun telif ve kullanım haklarını yasal zemine oturtur. Lisans seçilmemiş bir projenin tüm hakları saklı kalır ve başkaları yasal olarak kodu kopyalayıp kullanamaz.
* **MIT Lisansı:** En yaygın ve esnek lisanstır. Kodun ticari dahil her amaçla değiştirilerek kullanılmasına izin verir; tek şart yazarın adının ve orijinal lisans metninin korunmasıdır.
* **GNU GPLv3:** Katı bir açık kaynak lisansıdır. Bu kod kullanılarak geliştirilen türev projelerin de açık kaynak kodlu ve GPLv3 lisanslı olmasını zorunlu kılar.
* **Apache 2.0:** MIT'ye benzer ancak ek olarak kullanıcıya patent hakları koruması sağlar ve yapılan büyük değişikliklerin belirtilmesini ister.

---

## 4. Kimlik Doğrulama Protokolleri: HTTPS vs. SSH

Yerel bilgisayardaki Git terminalinin, GitHub bulut sunucularına veri aktarabilmesi (push/pull) için güvenli bir tünel kurulmalıdır. GitHub, düz metin şifrelerle girişi tamamen yasaklamıştır.

### 1. HTTPS ve Personal Access Token (PAT)
* **Protokol Adresi:** `https://github.com/kullanici-adi/repo-adi.git`
* **Çalışma Mantığı:** GitHub hesabınızın şifresi yerine, arayüzden üretilen süreli ve özel yetkilere sahip bir şifre dizesi (Token) kullanılır.
* **Kullanım Senaryosu:** Hızlı kurulum gerektiren durumlar ve SSH portlarının (Port 22) kurumsal ağlar tarafından engellendiği senaryolar için uygundur.

### 2. SSH (Secure Shell) - Kriptografik Doğrulama
* **Protokol Adresi:** `git@github.com:kullanici-adi/repo-adi.git`
* **Çalışma Mantığı:** Bilgisayarınızda asimetrik şifreleme ile biri gizli (Private Key), diğeri açık (Public Key) iki anahtar üretilir. Public anahtar GitHub'a tanımlanır. Yerel bilgisayar her işlemde şifresiz bir şekilde arka planda doğrulanır.

#### Adım Adım SSH Anahtarı Oluşturma ve Yapılandırma Komutları:

**Adım 1:** Güncel ve en güvenli algoritma olan Ed25519 kullanarak yeni bir SSH anahtar çifti üretin:
```bash
ssh-keygen -t ed25519 -C "kullanici_email@example.com"
```
*(Komut sonrası sistem size dosya konumu ve ek şifre [passphrase] soracaktır. Doğrudan Enter tuşuna basarak varsayılan ayarlarla geçebilirsiniz.)*

**Adım 2:** Arka planda çalışan SSH kimlik denetim ajanını (ssh-agent) aktif edin:
```bash
eval "$(ssh-agent -s)"
```

**Adım 3:** Üretilen gizli anahtarı bu ajana tanıtın:
```bash
ssh-add ~/.ssh/id_ed25519
```

**Adım 4:** Üretilen açık (Public) anahtarı kopyalayın ve GitHub profilinizdeki `Settings -> SSH and GPG keys -> New SSH Key` alanına yapıştırın:
```bash
cat ~/.ssh/id_ed25519.pub
```

**Adım 5:** Bağlantının sorunsuz çalıştığını terminal üzerinden test edin:
```bash
ssh -T git@github.com
```
*Başarılı ise terminalde şu çıktı alınmalıdır:*
`Hi kullanici-adi! You've successfully authenticated, but GitHub does not provide shell access.`

---

## 5. İlk Senkronizasyon Öncesi Global Kimlik Ayarları

Uzak sunucuya ilk kez commit ve push yapmadan önce, Git tarihçesinde adınızın ve e-posta adresinizin doğru görünmesi için terminalden global ayarları yapılandırmalısınız:

```bash
# GitHub hesabınızda görünen adınızı tanımlayın
git config --global user.name "Sercan Özverenli"

# GitHub hesabınıza bağlı e-posta adresinizi tanımlayın
git config --global user.email "kullanici_email@example.com"

# Yapılandırılan ayarları listeyerek doğrulayın
git config --list
```

---

## 6. GitHub Arayüz Anatomisi ve Sekmelerin İşlevleri

Bir GitHub deposunun üst menüsünde yer alan sekmeler projenin yönetim panelleridir:

1.  **Code:** Projenin aktif dosya ağacını, branch listesini, commit geçmişini ve release sürümlerini barındıran ana ekrandır.
2.  **Issues:** Projedeki hataların (bugs), eklenecek yeni özelliklerin (features) veya yapılacak işlerin listelendiği, etiketlendiği ve görevlendirildiği bilet (ticket) sistemidir.
3.  **Pull Requests (PR):** Farklı bir daldan (branch) veya çatallanmış bir depodan (fork) ana kod tabanına yapılan değişikliklerin birleştirilme talebidir. Kod incelemeleri (Code Review) tamamen burada döner.
4.  **Actions:** Projenin CI/CD (Sürekli Entegrasyon / Sürekli Dağıtım) süreçlerini yöneten otomasyon merkezidir. Kod pushlandığında testlerin otomatik çalışması burada kurgulanır.
5.  **Projects:** Issues ve Pull Requests kartlarını çevik (Agile/Scrum) metodolojilere uygun olarak yönetmeyi sağlayan, entegre Trello benzeri Kanban panolarıdır.
6.  **Wiki:** Projeye dair kapsamlı kullanım dokümantasyonlarının, kılavuzların ve mimari detayların yazıldığı ansiklopedik alt sayfadır.
7.  **Security:** Bağımlılıklardaki güvenlik açıklarını tarayan (Dependabot), koda sızmış gizli anahtarları yakalayan ve güvenlik politikalarını yöneten kısımdır.
8.  **Settings:** Deponun adını değiştirme, görünürlüğünü (Public/Private) dönüştürme, takımlara erişim yetkisi verme, dal koruma kuralları (Branch Protection) tanımlama veya depoyu tamamen silme gibi idari işlemlerin yapıldığı yönetim merkezidir.
