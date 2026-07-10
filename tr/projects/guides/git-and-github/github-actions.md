# CI/CD Otomasyonu ve GitHub Actions Mimarisi

Bu doküman; yazılım geliştirme döngüsündeki test, derleme ve sunucuya dağıtım (deploy) süreçlerinin insan müdahalesi olmadan otomatikleştirilmesini sağlayan GitHub Actions mimarisini, CI/CD prensiplerini ve YAML tabanlı iş akışı (Workflow) yapılandırmasını detaylandırmaktadır.

---

## 1. CI/CD Kavramı Nedir?

GitHub Actions'ın temel amacı **CI/CD** (Sürekli Entegrasyon ve Sürekli Dağıtım) süreçlerini işletmektir.

* **Sürekli Entegrasyon (Continuous Integration - CI):** Birden fazla geliştiricinin kodları ana depoya (main branch) gönderildiğinde, bu kodların mevcut sistemi bozup bozmadığını anlamak için **otomatik testlerin** çalıştırılmasıdır.
* **Sürekli Dağıtım (Continuous Deployment - CD):** CI aşamasından (testlerden) başarıyla geçen kodların, herhangi bir manuel FTP/SSH işlemine gerek kalmadan doğrudan canlı sunucuya, bulut servisine veya paket yöneticisine (Örn: PyPI) aktarılmasıdır.

---

## 2. GitHub Actions'ın Temel Bileşenleri (Anatomi)

Sistem, hiyerarşik olarak birbirine bağlı 5 temel bileşenden oluşur:

1.  **Workflow (İş Akışı):** Bir veya daha fazla görevi (Job) barındıran, YAML formatında yazılmış en üst düzey otomatik süreçtir. Projenin `.github/workflows/` dizininde tutulur.
2.  **Event (Tetikleyici):** Bir Workflow'un ne zaman çalışacağını belirleyen olaydır (Örn: depoya push yapıldığında, PR açıldığında veya her gece saat 03:00'te).
3.  **Runner (Sunucu):** Workflow'un üzerinde çalıştığı sanal makinedir (VM). GitHub size varsayılan olarak Ubuntu, Windows ve macOS sunucularını ücretsiz sağlar.
4.  **Job (Görev):** Aynı Runner üzerinde çalışan adımlar (Steps) bütünüdür. Bir Workflow'da birden fazla Job olabilir ve bunlar varsayılan olarak birbirine paralel (aynı anda) çalışır.
5.  **Step (Adım):** Bir Job'ın içindeki tekil işlemlerdir. Bir Step, ya doğrudan bir terminal komutu çalıştırır (`run: npm install`) ya da hazır bir Action kullanır (`uses: actions/checkout@v4`).

---

## 3. İlk Workflow'u Oluşturma (YAML Mimarisi)

Veri setlerini test eden veya doğrulama (validation) katmanlarını barındıran bir Python projesi (Örn: Amplify Core veya PBGO tarzı bir mimari) için örnek bir CI (Sürekli Entegrasyon) akışı yazalım.

Bu dosya mutlaka deponun ana dizininde şu yola kaydedilmelidir: `.github/workflows/python-ci.yml`

```yaml
# 1. İş Akışının Adı (GitHub Actions sekmesinde bu isim görünür)
name: Data Validation CI

# 2. Tetikleyiciler (Hangi durumlarda çalışacak?)
on:
  push:
    branches: [ "main", "development" ]
  pull_request:
    branches: [ "main" ]

# 3. Görevler
jobs:
  test-and-validate:
    # Hangi işletim sisteminde çalışacağı
    runs-on: ubuntu-latest

    # Adımlar (Sırayla çalışır)
    steps:
      # Adım 1: GitHub deposundaki kodu Runner'ın (sanal makinenin) içine kopyala
      - name: Kodu Checkout Et
        uses: actions/checkout@v4

      # Adım 2: Sanal makineye Python'ın belirli bir sürümünü kur
      - name: Python 3.10 Kurulumu
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'

      # Adım 3: Projenin veri işleme ve test bağımlılıklarını yükle
      - name: Bağımlılıkları Yükle
        run: |
          python -m pip install --upgrade pip
          pip install pandas numpy pytest scikit-learn
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

      # Adım 4: Veri doğrulama testlerini çalıştır
      - name: Pytest ile Testleri Çalıştır
        run: pytest tests/
```

**Bu yapı ne yapar?**
Geliştirici `main` dalına kod pushladığında veya PR açtığında, GitHub bulutta boş bir Ubuntu Linux sunucusu ayağa kaldırır. İçine kodları çeker, Python kurar, Pandas/NumPy gibi kütüphaneleri yükler ve `pytest` çalıştırır. Eğer testlerden biri bile patlarsa, GitHub o PR'ın birleştirilmesine kırmızı çarpı koyarak engeller.

---

## 4. İleri Seviye Tetikleyiciler (Events)

Kod gönderimi dışında da bir akışı tetikleyebilirsiniz. En sık kullanılan diğer event'ler şunlardır:

**A. Zamanlanmış Görevler (Cron - Schedule)**
Örneğin veritabanı yedeği almak veya haftalık veri analizi raporu çıkarmak için workflow'u saat kurar gibi çalıştırabilirsiniz.
```yaml
on:
  schedule:
    # Her gece saat 00:00'da çalışır (UTC zaman dilimiyle)
    - cron: '0 0 * * *'
```

**B. Manuel Tetikleme (Workflow Dispatch)**
Bir akışı GitHub arayüzünden kendi elinizle, bir butona basarak başlatmak isterseniz kullanılır. Ek olarak arayüzden parametre (input) de isteyebilirsiniz.
```yaml
on:
  workflow_dispatch:
    inputs:
      ortam:
        description: 'Dağıtım yapılacak ortamı seçin'
        required: true
        default: 'test'
        type: choice
        options:
          - test
          - production
```

---

## 5. Çoklu Ortam Testleri (Matrix Build)

Yazdığınız kodun veya algoritmanın Python 3.8, 3.9 ve 3.10'da aynı anda çalışıp çalışmadığını veya hem Linux hem Windows'ta hatasız derlenip derlenmediğini test etmek için **Matrix** stratejisi kullanılır.

```yaml
jobs:
  multi-test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ['3.9', '3.10']
    
    steps:
      - uses: actions/checkout@v4
      - name: Python ${{ matrix.python-version }} Kur
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
```
*Bu yapı, belirttiğiniz her işletim sistemi ve her Python sürümü kombinasyonu için ayrı ayrı (toplam 4 farklı) Runner ayağa kaldırır ve testleri paralel olarak koşturur.*

---

## 6. Güvenli Veri Yönetimi: GitHub Secrets

Workflow'lar çalışırken AWS, Docker Hub, veya harici bir API servisine bağlanması gerekebilir. API anahtarlarını, şifreleri veya token'ları asla YAML dosyasının (kodun) içine açık metin olarak yazmamalısınız.

**Nasıl Yapılır?**
1. GitHub deponuzda **Settings -> Secrets and variables -> Actions** sekmesine gidin.
2. **New repository secret** butonuna tıklayın. (Örn: Name: `API_KEY`, Value: `12345-abcde`)
3. YAML dosyanızın içinde bu şifreyi şu şekilde güvenle çağırın:

```yaml
      - name: API'ye Veri Gönder
        run: python verigonder.py
        env:
          GIZLI_API_ANAHTARI: ${{ secrets.API_KEY }}
```
