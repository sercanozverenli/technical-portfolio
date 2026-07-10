# Büyük Projelerin Yapısal Organizasyonu: Git LFS ve Submodules

Bu doküman; standart Git mimarisinin sınırlarını zorlayan yüksek boyutlu dosyaların (büyük veri setleri, makine öğrenmesi modelleri vb.) repoyu şişirmeden nasıl yönetileceğini sağlayan **Git LFS** teknolojisini ve mikroservis/modüler mimarilerde bir Git deposunun içine başka bir Git deposunu entegre etmeyi sağlayan **Git Submodules** yapısını ele almaktadır.

---

## Bölüm 1: Git LFS (Large File Storage) Mimarisi

Git, metin tabanlı (text-based) kaynak kodların satır satır değişimini (diff) takip etmek üzere tasarlanmıştır. Ancak projeye yüksek boyutlu ikili (binary) dosyalar (örneğin `.csv`, `.parquet`, `.pkl` model ağırlıkları, yüksek çözünürlüklü grafikler veya `.mp4` videolar) eklendiğinde sistem tıkanır.

**Sorun Nedir?**
100 MB'lık bir veri setinde ufak bir değişiklik yapıp commit attığınızda, Git eski dosyayı silmez, 100 MB'lık yeni bir kopya daha oluşturur. Birkaç commit sonra deponuzun boyutu GB'lara ulaşır, `git clone` veya `git push` işlemleri saatler sürer ve GitHub'ın 100 MB tekil dosya sınırına takılıp hata alırsınız.

**LFS Nasıl Çözer?**
Git LFS, büyük dosyaları deponuzun ana Git geçmişinde tutmak yerine harici bir LFS sunucusunda depolar. Sizin yerel Git deponuzda ise o dosyanın nerede olduğuna dair sadece 1-2 KB'lık küçük bir **işaretçi (pointer)** metin dosyası kalır. Siz kodu çektiğinizde, LFS araya girip işaretçiyi okur ve gerçek büyük dosyayı indirir.

---

## Bölüm 2: Git LFS Kurulumu ve Kullanımı

### A. LFS'i Aktifleştirme
Sisteminize Git LFS aracını kurduktan sonra (işletim sistemine göre indirilir), kullanıcı hesabınız için LFS'i global olarak bir kez aktifleştirmeniz gerekir:

```bash
git lfs install
# Çıktı: Git LFS initialized.
```

### B. Dosyaları Takibe Alma (.gitattributes)
LFS'in hangi dosyaları yöneteceğini belirtmek için `track` komutu kullanılır. Veri analizi veya modelleme süreçlerinde sıklıkla kullanılan büyük dosya formatlarını LFS'e emanet edebilirsiniz.

```bash
# Tüm .csv uzantılı büyük veri setlerini LFS ile takip et
git lfs track "*.csv"

# Eğitilmiş makine öğrenmesi modellerini (.pkl) LFS ile takip et
git lfs track "*.pkl"
```

Bu işlemi yaptığınızda projenizin kök dizininde otomatik olarak bir `.gitattributes` dosyası oluşur (veya güncellenir). Bu dosya, tıpkı `.gitignore` gibi Git tarafından okunur ve LFS kurallarını barındırır.

### C. Commit ve Push İşlemi
`track` işleminden sonra, eklenen büyük dosyalar ve `.gitattributes` dosyası standart Git komutlarıyla commitlenir.

```bash
git add .gitattributes
git add veriseti.csv
git commit -m "feat: 500MB'lık müşteri veri seti LFS ile projeye dahil edildi"
git push origin main
```
*Push işlemi sırasında terminalde LFS'in devreye girip dosyayı parçalar halinde uzak sunucuya yüklediğini (uploading LFS objects) görebilirsiniz.*

---

## Bölüm 3: Git Submodules (Alt Modüller) Mimarisi

Bir projenin içinde, tamamen bağımsız olarak geliştirilen başka bir projeyi kullanmanız gereken durumlar olabilir. 

Örneğin; tüketim verilerini analiz eden "PBGO" (PiratesButGoodOnes) adında bir ana projeniz olduğunu varsayalım. Bu projenin veri doğrulama işlemleri için "Amplify Core" adındaki başka bir deponuzu kullanmak istiyorsunuz. İki deponun kodlarını birbirine karıştırmak yerine, **Submodule** mimarisiyle Amplify Core'u PBGO'nun içine gömebilirsiniz.

**Avantajı:** İki depo birbirine bağlanır ancak kendi bağımsız commit tarihçelerini, branch'lerini ve etiketlerini korumaya devam eder.

---

## Bölüm 4: Submodule Ekleme ve Güncelleme

### A. Yeni Bir Submodule Ekleme
Ana projenizin içindeyken, dışarıdan bir Git deposunu alt klasör olarak eklemek için şu komut kullanılır:

```bash
# Dışarıdaki projeyi "libs/veri-dogrulama" klasörüne submodule olarak ekle
git submodule add git@github.com:kullanici-adi/amplify-core.git libs/veri-dogrulama
```

Bu işlem kök dizinde bir `.gitmodules` dosyası oluşturur. Bu dosya, alt deponun URL'sini ve yerel klasör yolunu kaydeder. Ana projeyi pushladığınızda, GitHub arayüzünde alt deponun klasörü tıklanabilir bir köprü (link) olarak görünür; tıklandığında doğrudan o deponun sayfasına gider.

### B. Submodule Barındıran Projeyi Klonlama
Bir iş arkadaşınız içinde submodule bulunan ana projenizi standart `git clone` ile indirirse, submodule klasörleri (örneğin `libs/veri-dogrulama`) **boş** gelir. 

Tek seferde tüm sistemi indirmek için `--recurse-submodules` parametresi şarttır:

```bash
git clone --recurse-submodules git@github.com:kullanici-adi/pbgo-main.git
```

*(Eğer klonlama yaparken parametreyi unuttuysanız, alt depoları sonradan çekmek için: `git submodule update --init --recursive`)*

### C. Submodule'u En Güncel Sürüme Çekme
Alt deponun orijinal sahibi kendi projesini güncellediğinde, bu değişiklikler sizin ana projenize otomatik olarak yansımaz. Güncellemeyi manuel olarak almanız gerekir:

```bash
# Tüm alt modülleri kendi sunucularındaki en güncel koda göre yenile
git submodule update --remote --merge

# Güncelleme sonrası ana projede bir commit atarak yeni versiyonu sabitleyin
git add .
git commit -m "chore: alt modül güncellemeleri entegre edildi"
```

---

## Bölüm 5: Submodule Silme (Kritik İşlem)

Git mimarisinde bir alt modülü silmek, sadece klasörü `Delete` tuşuyla silmekten çok daha karmaşıktır. Sistemin dört farklı yerinden deponun izlerini temizlemeniz gerekir.

```bash
# 1. Submodule'u önbellekten çıkarın ve Git sisteminden ayırın (De-init)
git submodule deinit -f -- libs/veri-dogrulama

# 2. Submodule'un .git dizinindeki arka plan klasörünü tamamen yok edin
rm -rf .git/modules/libs/veri-dogrulama

# 3. Submodule'u Git indeksinden silin (.gitmodules dosyasından da silinecektir)
git rm -f libs/veri-dogrulama

# 4. Temizliği kaydedip uzak sunucuya gönderin
git commit -m "refactor: veri doğrulama alt modülü projeden tamamen kaldırıldı"
git push origin main
```
