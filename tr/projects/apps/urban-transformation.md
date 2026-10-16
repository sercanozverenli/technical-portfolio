---
# Kentsel Dönüşüm Asistanı (Mobil & Web Bilgilendirme ve Simülasyon Platformu)

Çevre, Şehircilik ve İklim Değişikliği Bakanlığı ile Kentsel Dönüşüm Başkanlığı’nın güncel mevzuatları ve kamuya açık resmi verileri doğrultusunda geliştirilen **Kentsel Dönüşüm Asistanı**; mülk sahipleri, kiracılar ve müteahhitlerin kentsel dönüşüm süreçleri, finansal destekler, hibe modelleri ve yasal haklar hakkında kolayca bilgi sahibi olmalarını sağlayan bağımsız ve çok platformlu (Android & Web) bir simülasyon ve rehber platformudur.

### ⚙️ Öne Çıkan Özellikler ve Modüler Mimari

Platform; karmaşık mevzuat adımlarını basitleştiren merkezi bir karşılama ekranı (Hub) ve kullanıcı odaklı bağımsız modüllerden oluşmaktadır:

* **1. Destek Programları ve Finansal Hesaplama Araçları:**
  * **Dünya Bankası Destekli Düşük Faizli Kredi Simülatörü:** 3.000.000 TL'ye kadar esnek kredi tutarı ve 180 aya (15 yıla) kadar vade seçenekleriyle; aylık taksitleri, toplam geri ödemeyi, 12 ay ödemesiz dönem avantajını ve %70 asgari hane geliri kuralını anında hesaplar.
  * **Mevzuat Uyumlu 4 Kademeli Faiz İndirimi:** Yıllık azami %1,25 indirim tavanı dahilinde; *Tek Konut Sahibi* (0,25 puan), *Düşük/Orta Gelir Grubu* (0,25 puan), *Özel İndirim Grubu (Emekli, engelli, şehit/gazi yakını)* (0,25 puan) ve *A/B Sınıfı Enerji Kimlik Belgesi* (0,25 - 0,50 puan) kriterlerini otomatik olarak simülasyona dahil eder.
  * **Yarısı Bizden Kampanya Rehberi:** 1.875.000 TL'lik toplam dönüşüm desteğini (700.000 TL hibe, 700.000 TL kredi, 100.000 TL tahliye desteği) detaylandıran; ödeme planlarını, hak ediş aşamalarını ve başvuru koşullarını sunan kapsamlı rehber modül.
  * **Kira Yardımı ve Belediye Ek Destekleri (İnteraktif Akordiyon):** 6306 sayılı kanun kapsamında maliklere 18 ay boyunca, kiracılara ise taşınma masrafları için tek seferlik (iki aylık) ödeme koşullarını, 1 yıllık e-Devlet başvuru süresini ve büyükşehir/ilçe belediyelerinin sağladığı ek lojistik ve kira katkılarını özetleyen dinamik bilgi kartı.

* **2. Talep / Teklif Koordinasyon Modülü:**
  * Mülk sahiplerinin ada/parsel ve bina dönüşüm taleplerini, müteahhit firmaların ise proje teklif kriterlerini yapılandırılmış adımlarla iletebilmelerini sağlayan tarafsız dijital koordinasyon altyapısı.

* **3. Ücretsiz Canlı Bilgilendirme Hattı:**
  * Kullanıcıların süreçler hakkında doğrudan ve hızlı bilgi alabilmesi için doğrudan derin bağlantı (Deep Link) ile entegre edilmiş WhatsApp iletişim ve bilgilendirme köprüsü.

* **4. Çoklu Platform (Cross-Platform) & Duyarlı (Responsive) Tasarım:**
  * Hem Android cihazlarda yerel (native) performans hem de masaüstü ve mobil web tarayıcılarında çalışan esnek arayüz mimarisi.
  * Ekran genişliğine göre otomatik uyum sağlayan akıllı ızgara (Responsive Grid) sistemi ve Google Search Console onaylı Web altyapısı.

* **5. %100 Yerel Veri Gizliliği ve Modern Deneyim:**
  * Kullanıcı girdileri üçüncü taraf sunucularda depolanmaz; SharedPreferences altyapısıyla tamamen cihaz üzerinde güvenle tutulur.
  * Gözü yormayan modern Slate & Emerald renk paletine sahip Aydınlık (Light) ve Karanlık (Dark) tema desteği.


### 📱 Uygulama Görselleri

![Ana Ekranı](../../../docs/assets/kentsel-donusum/telefon3.png ':size=22%')  ![Yarısı Bizden Ekranı](../../../docs/assets/kentsel-donusum/telefon4.png ':size=22%')  ![Kredi Simülatör Ekranı](../../../docs/assets/kentsel-donusum/telefon1.png ':size=22%')  ![Rehber ve Bilgilendirme Ekranı](../../../docs/assets/kentsel-donusum/telefon2.png ':size=22%')


### 🛠️ Kullanılan Teknolojiler (Tech Stack)

* **Çekirdek Çerçeve (Core Framework):** Flutter 3.x (Dart 3) — *Android (AAB/APK) & Web (PWA/SPA)*
* **Durum Yönetimi (State Management):** Provider
* **Yerel Saklama (Local Storage):** SharedPreferences *(Serverless / Client-Side Privacy)*
* **Arayüz & Tipografi:** Material Design 3, Google Fonts (Plus Jakarta Sans), Responsive Adaptive Layout (`LayoutBuilder`, `MediaQuery`)
* **Harici Entegrasyonlar:** URL Launcher (WhatsApp API, e-Devlet ve harici mevzuat yönlendirmeleri)
* **Derleme & Optimizasyon:** R8 Full Mode optimizasyonu, ProGuard Sınıf Yeniden Paketleme (`-repackageclasses ''`), Icon Tree-Shaking, Web WASM/CanvasKit uyumluluğu

*(Yapay zeka destekli otonom yazılım ajanları ile modern eşli programlama prensipleri doğrultusunda geliştirilmiştir.)*

---

### 📦 Mağaza ve Web Bağlantıları

Platformun canlı sürümlerine aşağıdaki bağlantılardan erişebilirsiniz:

* **[Google Play Store'da İnceleyin (Android)](https://play.google.com/store/apps/details?id=com.kentsel.donusum)**
* **[Web Platformunu Ziyaret Edin](https://kentseldonusumasistani.com)** *(veya barındırılan web adresi)*

---

> <small>*⚠️ **Yasal Bilgilendirme ve Sorumluluk Reddi (Legal Disclaimer)**<br> 
> Kentsel Dönüşüm Asistanı; Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, Kentsel Dönüşüm Başkanlığı veya herhangi bir resmi kamu kurumu/banka ile doğrudan bağı bulunmayan bağımsız bir bilgilendirme platformudur. Platform, 6306 sayılı Kanun ve ilgili Uygulama Yönetmeliği hükümleri doğrultusunda yalnızca kamuya açık veriler üzerinden rehberlik, genel bilgilendirme ve finansal simülasyon araçları sunar; resmi bir danışmanlık veya aracılık faaliyeti taahhüt etmez. Kesin kredi onayları, faiz indirimleri, hibe hak edişleri ve başvuru sonuçları ilgili kamu otoritelerinin ve yetkili bankaların nihai değerlendirmelerine tabidir.*</small>
