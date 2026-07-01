Zaman Tüneli, Branching (Dallanma) Mimarisi ve Temel Merge 

Bu doküman, projenizin geçmişini incelemeyi, ana gövdeden bağımsız güvenli çalışma alanları (dallar) açmayı, modern navigasyon araçlarını kullanmayı ve sorunsuz çakışmasız bir birleştirme sürecini eksiksiz kapsar.

---

## Bölüm 1: Proje Zaman Tüneli (git log)

Projede geçmişten bugüne kadar atılan tüm commit'ler kronolojik olarak Git tarihçesinde saklanır. `git log` komutu, projenin zaman tünelini okumanızı sağlar.

### git log
*   **Açıklama**: Projedeki tüm commit geçmişini yazar, benzersiz commit ID'si (SHA-1), tarih ve yazar bilgileriyle birlikte detaylı olarak listeler.
```bash
# 1. Senaryo: Tüm commit geçmişini detaylı listeleme
git log

# 2. Senaryo: Her commit'i tek bir satıra sığdırarak sıkışık ve hızlı okunabilir listeleme
git log --oneline

# 3. Senaryo: Son yapılan işlem sayısını kısıtlayarak listeleme (Örn: son 3 commit)
git log -n 3

# 4. Senaryo: Commit geçmişini dallanma haritasıyla birlikte görsel bir grafik olarak görme
git log --oneline --graph --all
```

---

## Bölüm 2: Git Mekaniğinin Kalbi: HEAD Kavramı

### 1. HEAD Nedir?
Git altyapısında HEAD, yerel deponuzda **o an üzerinde çalıştığınız aktif commit'i veya aktif dalı (branch) gösteren bir işaretçidir (pointer)**. Bilgisayarınızın ekranında açık olan güncel kod versiyonunun neresi olduğunu Git bu işaretçi sayesinde bilir.

### 2. HEAD'in Mimari Yapısı ve Yer Değiştirmesi
Normal şartlarda HEAD, aktif olan dalın (örneğin `main`) en son commit'ini işaret eder. Siz yeni bir commit attıkça, dalınız ileri gider, HEAD de otomatik olarak o dal ile birlikte en son commit'e taşınır.

*   **Detached HEAD (Kopuk HEAD) Durumu**: Eğer `git checkout <eski-commit-id>` komutuyla geçmişteki spesifik bir commit anına dönerseniz, HEAD bir dalı işaret etmeyi bırakır ve doğrudan o eski commit'e tutunur. Bu duruma "Detached HEAD" denir. Bu durumdayken yapılan değişiklikler herhangi bir dala bağlı olmadığı için kalıcı olmaz.

---

## Bölüm 3: Modern Dal Yönetimi (git branch & git switch)

Dallar (Branches), ana projenizin çalışan kodunu bozmadan yeni özellikler geliştirebileceğiniz, hataları çözebileceğiniz bağımsız paralel evrenlerdir.

### 1. Kritik Fark: Neden git checkout yerine git switch?
Eski Git versiyonlarında `git checkout` komutu iki çok farklı işi tek başına yapıyordu: Hem dallar arasında geçiş sağlıyor hem de silinen/değişen dosyaları geri getiriyordu. Bu durum kafa karışıklığı yarattığı için modern Git mimarisinde bu görevler ayrılmıştır:
*   `git switch`: Sadece **dallar arası geçiş ve dal yönetimi** için tasarlanmış modern komuttur.
*   `git restore`: Dosyaları eski haline getirmek veya geri yüklemek için ayrılmıştır.
Mülakatlarda ve modern projelerde dal değiştirme işlemleri için her zaman **git switch** kullanılması önerilir.

### 2. Komutlar ve Varyasyonlar

#### git branch
*   **Açıklama**: Projedeki mevcut dalları listeler veya sıfırdan yeni bir yan dal oluşturur.
```bash
# 1. Senaryo: Mevcut tüm yerel dalları listeleme (Aktif dalın başında * işareti bulunur)
git branch

# 2. Senaryo: Projede "giriş-ekrani" adında yeni bir yan dal oluşturma (Henüz o dala geçiş yapmaz)
git branch giriş-ekrani

# 3. Senaryo: İşlevi biten bir yan dalı güvenli bir şekilde silme (Birleştirilmemiş kod varsa uyarır)
git branch -d giriş-ekrani

# 4. Senaryo: Bir dalı, içinde birleşmemiş kodlar olsa dahi zorla tamamen silme
git branch -D silinecek-dal
```

#### git switch
*   **Açıklama**: HEAD işaretçisini hareket ettirerek mevcut dallar arasında geçiş yapmanızı veya tek komutla yeni dal açıp oraya bağlanmanızı sağlar.
```bash
# 1. Senaryo: Mevcut olan başka bir dala geçiş yapma
git switch giriş-ekrani

# 2. Senaryo: Tek komutla hem yeni bir dal oluşturma hem de o dala direkt geçiş yapma (-c / create)
git switch -c ödeme-sistemi

# 3. Senaryo: Bir önceki üzerinde çalıştığınız dala hızlıca geri dönme
git switch -
```

---

## Bölüm 4: Kod Karşılaştırma (git diff)

`git diff` komutu, projenizdeki iki farklı aşama veya iki farklı dal arasındaki kod satırı değişikliklerini (eklenenleri yeşil, silinenleri kırmızı olarak) detaylıca görmenizi sağlar.

### git diff
*   **Açıklama**: Çalışma alanınız (Working Directory), hazırlık alanınız (Staging Area) veya mevcut dallarınız arasındaki farkları satır satır listeler.
```bash
# 1. Senaryo: Çalışma alanında yaptığınız ama henüz "git add" ile sahneye almadığınız farkları görme
git diff

# 2. Senaryo: Sahneye aldığınız (Staging Area) kodlar ile son yapılan commit arasındaki farkları görme
git diff --staged

# 3. Senaryo: İki farklı dal arasındaki tüm kod farklarını karşılaştırma
git diff main..ödeme-sistemi

# 4. Senaryo: Sadece belirli bir dosyanın iki dal arasındaki farkını görme
git diff main..ödeme-sistemi index.html
```

---

## Bölüm 5: En Temel Birleştirme (git merge)

Yan dalda yazdığınız kodlar tamamlandığında ve test edildiğinde, bu kodları ana projeye (`main` veya `master` dalına) katmanız gerekir. Bu işleme birleştirme (merge) denir.

### git merge
*   **Açıklama**: Belirtilen daldaki commit geçmişini ve kod değişikliklerini, o an üzerinde bulunduğunuz aktif dalın içerisine çeker.
```bash
# Sorunsuz (Çakışmasız) Temel Birleştirme Senaryosu:

# Adım 1: Kodların birleştirileceği hedef ana dala geçiş yapılır
git switch main

# Adım 2: Yan daldaki güncel kodlar main dalının içine çağrılır
git merge ödeme-sistemi
```

### Mekanizma Nasıl İşler?
Bu temel senaryoda, eğer `ödeme-sistemi` dalı açıldıktan sonra `main` dalı üzerinde başka hiçbir yeni commit atılmadıysa, Git birleştirmeyi doğrusal olarak yapar. Bu sorunsuz süreçte dosyalar çakışmadan otomatik olarak birleşir ve terminal sizden bir işlem beklemeden süreci tamamlar.
