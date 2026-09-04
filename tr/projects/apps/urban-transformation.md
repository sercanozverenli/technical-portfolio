# Kentsel Dönüşüm Asistanı (Kredi Hesaplama & Simülasyon Aracı)

Çevre, Şehircilik ve İklim Değişikliği Bakanlığı ile Kentsel Dönüşüm Başkanlığı güncel mevzuat verileri doğrultusunda geliştirilen **Kentsel Dönüşüm Asistanı**, evini kentsel dönüşüm kapsamında yenilemek isteyen hak sahiplerinin devlet destekli kredi tutarlarını, faiz indirim oranlarını, aylık taksitlerini ve asgari hane geliri kriterlerini anında hesaplamalarını sağlayan bağımsız bir mobil simülasyon ve rehber uygulamasıdır.

### ⚙️ Öne Çıkan Özellikler ve Simülasyon Mekaniği

Uygulama, mevzuat karmaşıklığını ortadan kaldıran dinamik hesaplama algoritmaları ve rehber içeriklerden oluşmaktadır:

* **Dinamik Kredi Hesaplama Simülasyonu:** Kullanıcılar 3.000.000 TL'ye kadar esnek kaydırıcı (slider) ile kredi tutarını ve 180 aya (15 yıla) kadar vade seçeneklerini belirler. Sistem; aylık taksit tutarını, toplam geri ödemeyi ve faiz yükünü anında ekrana yansıtır.

* **Mevzuat Uyumlu Otomatik Faiz İndirimi Kategorileri:** Yıllık azami %1,25 faiz indirimi sınırı dahilinde mevzuatta tanımlı tüm kategorileri otomatik hesaplar:
  1. *Kategori 1 (Tek Konut Sahibi):* Yıllık 0,25 puan indirim.
  2. *Kategori 2 (Düşük/Orta Gelir Haneleri):* 81 il için belirlenen aylık azami hane gelir sınırlarına göre yıllık 0,25 puan indirim.
  3. *Kategori 3 (Özel İndirim Grubu):* Emekliler, 65 yaş üstü, şehit/gazi yakınları, engelliler ve kapıcılar için yıllık 0,25 puan indirim.
  4. *Kategori 4 (Enerji Kimlik Belgesi):* Yeni binanın Enerji Sınıfına göre A Sınıfı için 0,50 puan, B Sınıfı için 0,25 puan ek indirim.

* **Asgari Hane Geliri Hesaplayıcı (%70 Kuralı):** Kredi çekebilmek için bankalarca aranan aylık taksit tutarının toplam hane gelirinin maksimum %70'ini geçmemesi kuralına dayalı asgari gelir eşiğini otomatik hesaplar.

* **5 Adımda Başvuru Rehberi ve Pilot İller:** Dünya Bankası destekli *İklim ve Afetlere Dayanıklı Şehirler Projesi* kapsama alanındaki pilot iller (İstanbul, İzmir, Manisa, Kahramanmaraş, Tekirdağ) ve başvuru süreçlerine dair detaylı rehber sunar.

* **%100 Yerel Veri Saklama ve Kullanıcı Dostu Tasarım:** Girdiler hiçbir bulut sunucusuna aktarılmadan cihazda (SharedPreferences) tutulur. Göz yormayan Karanlık (Dark) ve Aydınlık (Light) tema seçeneği sunar.


### 📱 Uygulama Görselleri

![Kredi Simülatör Ekranı](../../../docs/assets/kentsel-donusum/telefon1.png ':size=22%')  ![Rehber ve Bilgilendirme Ekranı](../../../docs/assets/kentsel-donusum/telefon2.png ':size=22%')


### 🛠️ Kullanılan Teknolojiler (Tech Stack)

* **Mobil Uygulama Çerçevesi:** Flutter (Dart)
* **Durum Yönetimi (State Management):** Provider
* **Yerel Saklama ve Mimari:** SharedPreferences *(Serverless / Local Data Storage)*
* **Derleme ve Performans Optimizasyonu:** R8 Full Mode optimizasyonu, ProGuard Sınıf Yeniden Paketleme (`-repackageclasses ''`), AndroidX Room & WorkManager kalkanları
* **Görsel & Arayüz:** Google Fonts, Material Design 3 (Dark/Light Tema), Google Mobile Ads (AdMob)

*(Arayüz tasarımı, hesaplama algoritmaları, R8/ProGuard optimizasyonları ve Play Store yayınlama süreçleri **Google Antigravity / Gemini AI** yapay zeka asistanı pair-programming desteğiyle geliştirilmiştir.)*

---

### 📦 Mağaza Bağlantıları

Uygulamanın derlenmiş canlı sürümüne aşağıdaki bağlantıdan ulaşabilirsiniz:

**[Google Play Store'da İnceleyin](https://play.google.com/store/apps/details?id=com.kentsel.donusum)**

---

> <small>*⚠️ **Yasal Bilgilendirme ve Sorumluluk Reddi (Legal Disclaimer)**<br> Bu uygulama Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, Kentsel Dönüşüm Başkanlığı, herhangi bir resmi kurum veya bankaya bağlı değildir. Uygulama içerisinde sunulan hesaplamalar, faiz indirimi simülasyonları ve rehber içerikleri yalnızca genel bilgilendirme amaçlıdır. Kesin kredi tutarları, faiz indirim oranları, tahsis şartları ve asgari gelir kriterleri ilgili resmi kurumların ve anlaşmalı bankaların nihai onay süreçlerine tabidir.*</small>
