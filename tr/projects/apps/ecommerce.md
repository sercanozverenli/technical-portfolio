# Mantı Mood - Butik Ev Mantısı & Gurme E-Ticaret Platformu (Web & Mobil)

Geleneksel lezzetleri modern dijital ticaret deneyimiyle buluşturmak amacıyla geliştirilen **Mantı Mood**, butik el açması mantı ve gurme ürünlerin sipariş yönetimini, dinamik ürün/fiyat kataloglamasını, WhatsApp ve e-posta tabanlı çok kanallı sipariş bildirimlerini tek bir çatı altında toplayan, hem Web hem de Mobil (Android) platformlarda çalışan kapsamlı bir e-ticaret ekosistemidir.

### ⚙️ Sistem Mekaniği ve Öne Çıkan Özellikler

Platform, yüksek performanslı hibrit mimari, bulut tabanlı gerçek zamanlı veritabanı (realtime) ve işletme operasyonlarını kolaylaştıran interaktif yönetim paneli üzerine kurulmuştur:

* **Gerçek Zamanlı Ürün Kataloğu ve Dinamik Sepet Yönetimi:** Kullanıcılar gramaj, porsiyon ve sos varyasyonlarına göre ürünleri özelleştirebilir; dinamik fiyatlandırma, teslimat ücreti hesaplama ve minimum sipariş tutarı filtreleri ile anlık sepet güncellemesi deneyimler. Sayfa yenilemeye gerek kalmadan arka plan veritabanı ile çift yönlü senkronizasyon sağlanır.

* **Çok Kanallı Sipariş ve Otomatik Fatura İletimi:** Verilen siparişler, müşteri konumu ve sipariş dökümüyle birlikte anında işletmenin WhatsApp sipariş hattına yapılandırılmış mesaj olarak iletilir. Eş zamanlı olarak Supabase Edge Functions altyapısı üzerinden tetiklenen SMTP servisi, müşteriye ve işletmeye şık tasarımlı HTML sipariş faturası/onay e-postası ulaştırır.

* **Rol Tabanlı Güvenli Yönetici Paneli (Admin Dashboard):** Yalnızca yetkili (whitelist) e-posta adreslerinin erişebildiği gizli yönetim paneli; ürün ekleme/düzenleme/silme, anlık fiyat güncelleme, kampanya banner'larını dinamik olarak yönetme (resim kırpma ve ölçekleme kontrolleri) ve gelen siparişlerin durumunu takip etme olanağı tanır.

* **Kesintisiz Çoklu Platform (Web & Mobil) Entegrasyonu:** Tek bir kod tabanından (Flutter) derlenen sistem; web tarayıcılarında PWA/SPA olarak yüksek hızda çalışırken, Android ekosisteminde donanımsal optimizasyon, akıllı görsel önbellekleme (CachedNetworkImage), çek-yenile (pull-to-refresh) ve yerel ağ erişim izinleriyle donatılmış bir mobil uygulama performansı sunar.

* **Sunucusuz ve Güvenli Bulut Altyapısı (Serverless Backend):** Müşteri ve sipariş verileri, Supabase PostgreSQL altyapısında Row Level Security (RLS) politikaları ve şifreli bağlantılar ile korunur. Fatura gönderimi gibi hassas arka plan operasyonları, istemciye gizli anahtarları açık etmeden Deno/TypeScript tabanlı mikro servislerle yürütülür.


### 📱 Uygulama Görselleri

![Ana Ekran ve Menü](<../../../docs/assets/mantimood/tel1.png ':size=20%'>)  ![Ürün Detayı ve Sos Seçimi](<../../../docs/assets/mantimood/tel2.png ':size=20%'>)  ![Sepet ve Hızlı Sipariş](<../../../docs/assets/mantimood/tel3.png ':size=20%'>)  ![Yönetici Paneli](<../../../docs/assets/mantimood/tel4.png ':size=20%'>) 


### 🛠️ Kullanılan Teknolojiler (Tech Stack)

* **Çoklu Platform Çerçevesi:** Flutter (Dart) — *Web & Android Native Tek Kod Tabanı*
* **Durum Yönetimi (State Management):** Provider
* **Backend & Veritabanı:** Supabase (PostgreSQL, Realtime Data Sync, Row Level Security - RLS)
* **Sunucusuz Fonksiyonlar (Serverless):** Supabase Edge Functions (Deno / TypeScript)
* **İletişim & Bildirim Servisleri:** Gmail SMTP Entegrasyonu, Özel HTML E-Posta Şablon Motoru, WhatsApp Business API Deep-Linking
* **Web Dağıtımı & Barındırma:** Cloudflare Pages (Global CDN & Edge Dağıtımı)
* **Mobil Altyapı & Güvenlik:** Android Keystore (V1/V2 İmzalı AAB & APK), ProGuard R8 Optimizasyonu, Google Sign-In (OAuth 2.0)
* **Görsel & Arayüz:** Material Design 3, Google Fonts, Shimmer Yükleme Efektleri, CachedNetworkImage Önbellek Mimarisi

*(Yapay zeka destekli otonom yazılım ajanları ile iş birliği içinde üretilmiştir.)*

---

### 📦 Canlı Bağlantılar ve Kaynak Kodu

Projenin canlı web sürümüne ve kaynak kod deposuna aşağıdaki bağlantılardan ulaşabilirsiniz:

* **[Canlı Web Sürümünü İnceleyin](https://mantimood.pages.dev)** *(veya kendi özel alan adınız)*
* **[GitHub Kaynak Kodu](https://github.com/FabMorph/manti-mood)**

---

> <small>*⚠️ **Sipariş & Hizmet Yasal Bilgilendirmesi**<br> Mantı Mood platformu üzerinden verilen siparişler, taze ve hijyenik gıda standartlarına uygun olarak el yapımı hazırlanmaktadır. Teslimat saatleri, teslimat bölgesine ve sipariş yoğunluğuna bağlı olarak değişiklik gösterebilir. Alerjen bilgileri ve özel beslenme talepleri için lütfen sipariş öncesinde WhatsApp destek hattı üzerinden iletişime geçiniz.*</small>
