# GitHub Temelleri

Bu doküman, GitHub platformunun temel kullanımını, repository oluşturmayı, temel yapılandırmaları ve GitHub arayüzünde bilinmesi gereken önemli kavramları kapsar.

> **Not:** Bu dokümanda Git komutları, Push, Pull Request, Clone, Fork ve ekip çalışma süreçleri anlatılmaz. Bunlar sonraki rehberlerde ayrıntılı olarak ele alınacaktır.

---

# Bölüm 1: GitHub Nedir?

## GitHub Nedir?

GitHub, Git tabanlı projelerin internet üzerinde saklanmasını, yönetilmesini ve ekip halinde geliştirilmesini sağlayan bulut tabanlı bir versiyon kontrol platformudur.

Git yalnızca sürüm kontrol sistemidir. GitHub ise bu Git depolarını internet üzerinde barındıran, ekip çalışmasını kolaylaştıran ve yazılım geliştirme süreçlerini yöneten bir platformdur.

GitHub sayesinde;

- Projeler internet üzerinde güvenli şekilde saklanabilir.
- Kod geçmişi korunur.
- Birden fazla geliştirici aynı proje üzerinde çalışabilir.
- Kod inceleme (Code Review) yapılabilir.
- Açık kaynak projelere katkı sağlanabilir.
- CI/CD, Issue, Wiki ve Actions gibi ek geliştirme araçları kullanılabilir.

---

# Bölüm 2: Repository (Depo) Kavramı

## Repository Nedir?

Repository (Repo), bir projenin Git tarafından takip edilen bütün dosyalarını ve geçmişini barındıran çalışma alanıdır.

Bir repository içerisinde;

- Kaynak kodlar
- Dokümantasyon
- Resimler
- Yapılandırma dosyaları
- Commit geçmişi
- Branch'ler
- Release'ler

bulunabilir.

GitHub üzerindeki her proje aslında bir Repository'dir.

---

# Bölüm 3: Yeni Repository Oluşturma

GitHub ana sayfasından sağ üst köşedeki **New Repository** seçeneği kullanılarak yeni bir depo oluşturulur.

Repository oluşturulurken aşağıdaki bilgiler belirlenir.

## Repository Name

Repository'nin benzersiz ismidir.

İyi örnekler:

```
portfolio

python-notes

amplify-core

machine-learning-roadmap
```

Kaçınılması gereken örnekler:

```
Yeni Proje

Proje1

Test

Deneme
```

Repository ismi mümkün olduğunca;

- kısa
- açıklayıcı
- İngilizce
- boşluk içermeyen

bir isim olmalıdır.

---

## Description

Repository hakkında kısa açıklamadır.

Bu alan zorunlu değildir.

Örnek:

```
Personal portfolio website

Machine Learning study notes

Data Reliability & Decision Routing System
```

---

# Bölüm 4: Public ve Private Repository

Repository oluşturulurken görünürlük belirlenir.

## Public Repository

Herkes repository'yi görebilir.

Özellikleri:

- Kaynak kodları herkes görüntüleyebilir.
- Açık kaynak projeler için uygundur.
- Portföy projelerinde tercih edilir.
- Arama sonuçlarında görünür.

Kullanım alanları:

- Açık kaynak projeler
- Eğitim projeleri
- Portföy
- Dokümantasyon

---

## Private Repository

Sadece erişim verilen kullanıcılar görebilir.

Özellikleri:

- Kodlar herkese açık değildir.
- Arama sonuçlarında görünmez.
- Şirket projelerinde kullanılır.
- Davet edilen kullanıcılar erişebilir.

Kullanım alanları:

- Ticari projeler
- Şirket yazılımları
- Müşteri projeleri
- Gizli çalışmalar

---

# Bölüm 5: Repository Oluştururken Karşılaşılan Seçenekler

Yeni repository oluştururken birkaç ek seçenek sunulur.

---

## Add a README file

Repository içerisine başlangıçta README.md oluşturur.

README;

- projenin tanıtımı
- kurulum adımları
- kullanım bilgileri
- lisans
- örnekler

gibi bilgilerin bulunduğu ana dokümandır.

İlk repository oluşturulurken işaretlenmesi önerilir.

---

## Add .gitignore

GitHub, kullanılan teknolojiye göre hazır `.gitignore` şablonları sunar.

Örneğin;

- Python
- Node.js
- Java
- Visual Studio
- Unity

gibi birçok hazır şablon bulunmaktadır.

Bu seçenek sayesinde gereksiz dosyalar başlangıçtan itibaren takip edilmez.

> `.gitignore` dosyasının çalışma mantığı Git rehberinde ayrıntılı olarak açıklanmıştır.

---

## Choose a License

Açık kaynak projelerde kullanılacak lisans belirlenebilir.

En yaygın lisanslar:

| Lisans | Kullanım |
|----------|----------|
| MIT | En yaygın ve serbest kullanım |
| Apache 2.0 | Patent koruması içerir |
| GPL | Türetilen projelerin de açık kaynak olmasını zorunlu kılar |

Kişisel portföy projelerinde en sık kullanılan lisans **MIT License**'dır.

---

# Bölüm 6: Repository Ana Sayfası

Repository oluşturulduktan sonra GitHub ana ekranında birçok bölüm bulunur.

En sık kullanılanlar:

- Code
- Issues
- Pull Requests
- Actions
- Projects
- Wiki
- Security
- Insights
- Settings

Bu rehberde yalnızca temel kullanım açısından önemli olan bölümler anlatılacaktır.

---

## Code

Repository'nin ana sayfasıdır.

Burada;

- dosyalar
- klasörler
- branch seçimi
- commit geçmişi

görüntülenebilir.

---

## Releases

Projenin yayınlanan sürümlerini barındırır.

Örneğin;

```
v1.0

v1.1

v2.0
```

şeklinde sürüm etiketleri oluşturulabilir.

Release'ler özellikle son kullanıcıya dağıtılan projelerde kullanılır.

---

## Tags

Tag, belirli bir commit'e verilen isimdir.

En yaygın kullanım:

```
v1.0

v2.0

v3.1
```

Release'ler çoğunlukla Tag'ler üzerine oluşturulur.

---

# Bölüm 7: Repository Ayarları

Repository oluşturulduktan sonra **Settings** bölümünden birçok ayar değiştirilebilir.

---

## Repository Adını Değiştirme

Settings → General bölümünden repository ismi değiştirilebilir.

GitHub eski bağlantıları büyük ölçüde yönlendirse de, projeyi kullanan kişilerin yeni adresi kullanması önerilir.

---

## Repository Açıklamasını Güncelleme

Repository Description daha sonra istenildiği zaman değiştirilebilir.

---

## Repository Görünürlüğünü Değiştirme

Repository;

- Public
- Private

arasında daha sonra dönüştürülebilir.

Bu işlem Settings → General bölümünden yapılır.

---

## Repository Silme

Artık kullanılmayacak repository tamamen silinebilir.

Silme işlemi geri alınamaz.

GitHub güvenlik amacıyla repository isminin tekrar yazılmasını ister.

---

# Bölüm 8: Default Branch

Repository oluşturulduğunda varsayılan olarak bir ana branch belirlenir.

Günümüzde bu branch çoğunlukla:

```
main
```

olarak oluşturulur.

Default Branch;

- repository açıldığında ilk görülen branch'tir,
- yeni Pull Request'lerin varsayılan hedefidir,
- birçok otomasyon tarafından ana geliştirme dalı olarak kabul edilir.

Gerekirse Settings → Branches bölümünden değiştirilebilir.

---

# Bölüm 9: Repository Arşivleme (Archive)

Artık geliştirilmeyecek ancak silinmesi de istenmeyen projeler arşivlenebilir.

Archive edilen repository;

- salt okunur hale gelir,
- yeni commit kabul etmez,
- Pull Request açılamaz,
- Issue oluşturulamaz.

Ancak tüm geçmiş korunmaya devam eder.

Bu yöntem eski projeleri saklamak için silmeye göre daha güvenli bir çözümdür.
