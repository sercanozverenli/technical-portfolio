# Uzak Sunucu Bağlantıları ve İndirme Stratejileri

Bu doküman; yerel Git projelerini buluttaki uzak sunuculara (GitHub, GitLab, Bitbucket vb.) bağlama mekanizmalarını, çoklu uzak depo yönetimini ve büyük ölçekli kurumsal projeleri optimize ederek yerel ortama indirme (cloning) stratejilerini ileri seviye parametreleriyle birlikte ele almaktadır.

---

## 1. Git Remote (Uzak Depo) Kavramı ve Yönetimi

Yerel bilgisayarınızda `git init` ile başlattığınız bir projenin buluttaki bir depo ile konuşabilmesi için bir tünel/adres tanımına ihtiyacı vardır. Git mimarisinde bu adreslere **Remote (Uzak Depo)** adı verilir.

Bir yerel depo, aynı anda birden fazla uzak depo adresine sahip olabilir. Bu, özellikle açık kaynak projelerde veya hem şirket içi (GitLab) hem de genel (GitHub) sunuculara aynı anda kod gönderilen senaryolarda kritik önem taşır.

### A. Uzak Depo Ekleme (git remote add)
Yerel depoya harici bir sunucu adresi bağlamak için kullanılır. Git standartlarında ilk ve ana uzak depoya geleneksel olarak `origin` ismi verilir.

```bash
# Yerel depoya "origin" takma adıyla bir SSH adresi bağlama
git remote add origin git@github.com:kullanici-adi/proje-adi.git

# Açık kaynak bir projede orijinal depoyu "upstream" adıyla bağlama
git remote add upstream git@github.com:orijinal-geliştirici/proje-adi.git
```

### B. Bağlantıları Listeleme ve Doğrulama (git remote -v)
Depoya bağlı olan tüm uzak sunucu adreslerini, hangi adrese veri gönderileceğini (push) ve hangi adresten veri çekileceğini (fetch) görmek için kullanılır.

```bash
git remote -v
```
**Örnek Çıktı:**
```text
origin  git@github.com:kullanici-adi/proje-adi.git (fetch)
origin  git@github.com:kullanici-adi/proje-adi.git (push)
upstream  git@github.com:orijinal-geliştirici/proje-adi.git (fetch)
upstream  git@github.com:orijinal-geliştirici/proje-adi.git (push)
```

### C. Uzak Depo Adresini Değiştirme (git remote set-url)
Projenin taşınması, isim değiştirmesi veya HTTPS protokolünden SSH protokolüne geçiş yapılması durumunda mevcut remote adresini güncellemek için kullanılır.

```bash
# "origin" takma adına ait adresi yeni bir SSH adresiyle güncelleme
git remote set-url origin git@github.com:yeni-kullanici/yeni-proje-adi.git
```

### D. Uzak Depo Bağlantısını Silme (git remote rm)
Yerel projenin artık belirli bir uzak sunucuyla senkronize olmasını istemiyorsanız, o remote tanımını yerelden kaldırır. Buluttaki projeyi silmez, sadece bağı koparır.

```bash
# "upstream" bağlantısını yerel depodan kaldırma
git remote rm upstream
```

---

## 2. Git Clone (Proje Klonlama) Mekanizması

`git clone`, uzak sunucuda halihazırda var olan bir depoyu, tüm commit geçmişi, branch haritası ve tag (etiket) verileriyle birlikte yerel bilgisayarınıza tek bir komutla indirme işlemidir. 

`git clone` komutu arka planda şu üç işlemi otomatik olarak gerçekleştirir:
1. Hedef klasörü oluşturur ve içinde `git init` çalıştırır.
2. Belirtilen URL'i `origin` adıyla uzak depo olarak ekler (`git remote add origin`).
3. Sunucudaki tüm veriyi çeker (`git fetch`) ve en güncel kodu ekrana açar (`git checkout`).

### Temel Kullanım Varyasyonları:

```bash
# Projeyi, uzak sunucudaki klasör adıyla bulunduğunuz dizine klonlama
git clone git@github.com:kullanici-adi/proje-adi.git

# Projeyi, yerelde sizin belirleyeceğiniz özel bir klasör ismiyle klonlama
git clone git@github.com:kullanici-adi/proje-adi.git ozel-klasor-adi
```

---

## 3. İleri Seviye ve Optimize Edilmiş Klonlama Stratejileri

Yıllarca geliştirilmiş, içinde on binlerce commit, büyük dosyalar veya yüzlerce branch barındıran devasa kurumsal projeleri standart `git clone` ile indirmek saatler sürebilir ve ağ hatasıyla yarıda kalabilir. Bu durumlar için özel indirme stratejileri mevcuttur.

### A. Sığ Klonlama (Shallow Clone: --depth)
Projenin geçmişteki binlerce commit geçmişiyle ilgilenmiyorsanız, sadece projenin en güncel durumunu indirmek için kullanılır. Belirtilen sayı kadar son commit geçmişini getirir.

```bash
# Sadece son 1 commit'i ve güncel dosyaları indirerek devasa repoyu saniyeler içinde klonlama
git clone --depth 1 git@github.com:kullanici-adi/büyük-proje.git
```
* **Avantajı:** Ağ trafiğini ve disk kullanımını muazzam derecede azaltır. CI/CD hatlarında (otomatik test sunucularında) standart olarak bu yöntem kullanılır.
* **Not:** Daha sonra geçmiş commitlere ihtiyaç duyulursa `git fetch --unshallow` komutuyla tarihçenin tamamı geriye dönük olarak indirilebilir.

### B. Spesifik Branch Klonlama (--branch ve --single-branch)
Varsayılan olarak `git clone` sunucudaki tüm branch'lerin meta verilerini indirir. Eğer sadece tek bir geliştirme dalı üzerinde çalışacaksanız, diğer dalları yok sayarak klonlama yapabilirsiniz.

```bash
# Sadece "development" branch'ini ve tarihçesini indirir, diğer branchleri yerelde oluşturmaz
git clone -b development --single-branch git@github.com:kullanici-adi/proje-adi.git
```

### C. Alt Modüllerle Birlikte Klonlama (--recurse-submodules)
Bazı projeler, kendi içlerinde başka bağımsız Git depolarını bağımlılık (submodule) olarak barındırır. Standart klonlama yapıldığında bu alt modüllerin klasörleri boş gelir. Projenin eksiksiz çalışabilmesi için alt depoların da eşzamanlı indirilmesi gerekir.

```bash
# Ana projeyi ve içindeki tüm gömülü alt Git depolarını tek seferde indirir
git clone --recurse-submodules git@github.com:kullanici-adi/ana-proje.git
```
*Eğer proje daha önce standart şekilde klonlandıysa ve alt modüller boş kaldıysa, sonradan şu komutla doldurulabilir:*
```bash
git submodule update --init --recursive
```

---

## 4. Klonlama Sonrası Yerel Durum Analizi

Bir projeyi başarılı bir şekilde klonladıktan sonra, projenin durumunu analiz etmek için şu adımlar izlenir:

```bash
# 1. Klonlanan proje klasörünün içine girin
cd proje-adi

# 2. Hangi branch'te olduğunuzu ve yerel temizlik durumunu kontrol edin
git status

# 3. Projenin uzak sunucu bağlantı adreslerini doğrulayın
git remote -v

# 4. Yakın zamanda atılmış olan son commit geçmişini listeleyin
git log --oneline -n 5
```
