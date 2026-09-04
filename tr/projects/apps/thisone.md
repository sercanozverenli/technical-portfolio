# ThisOne - Sigara & Alışkanlık Takibi

Kişisel gelişim, alışkanlık takibi ve motivasyon odaklı olarak geliştirilen **ThisOne**, kullanıcıların sigara, alkol veya bırakmak istedikleri olumsuz alışkanlıkları gün gün takip etmelerini sağlayan, finansal/zamansal tasarruf oranlarını hesaplayan ve ana ekran widget'ları ile sürekli motivasyon sunan kapsamlı bir mobil uygulamadır.

### ⚙️ Sistem Mekaniği ve Öne Çıkan Özellikler

Uygulama, kullanıcı verilerinin %100 gizliliğini koruyan sunucusuz (serverless) bir mimari ve motivasyonu artıran dinamik arayüz öğeleri üzerine kurulmuştur:

* **Canlı Alışkanlık Takibi ve Tasarruf Hesaplama:** Kullanıcılar bırakmak istedikleri alışkanlığın başlangıç tarihini, günlük harcama tutarını ve tüketim miktarını belirler. Sistem; geçen süreyi (Gün, Saat, Dakika, Saniye), kırılmayan zincir gün sayısını ve sağlanan maddi/zamansal tasarrufu anlık olarak hesaplar.

* **Yıllık Aktivite Isı Haritası (Heatmap) ve İstatistikler:** Kullanıcının alışkanlıklarına sadakatini görselleştiren GitHub katkı grafiği tarzında yıllık bir Isı Haritası (Heatmap) sunulur. Başarılı günler yeşil tonlarıyla ödüllendirilir. Kırılan zincir durumunda kullanıcı motivasyonel bildirimlerle yeniden başlama imkanı bulur.

* **Yerel Ana Ekran Widget Entegrasyonu (Sade & İnteraktif):** Uygulamayı açmaya gerek kalmadan takibi kolaylaştırmak amacıyla Android platformuna özel iki farklı Widget geliştirilmiştir (Sade ve İnteraktif Widget seçenekleri).

* **%100 Yerel Veri Gizliliği (Sunucusuz Mimari):** Girilen tüm alışkanlık verileri, tasarruf tutarları ve tarihler harici bir bulut sunucusuna aktarılmaz; yalnızca kullanıcının cihazında güvenle (SharedPreferences) saklanır.

* **Esnek Monetizasyon Altyapısı:** İsteğe bağlı Google Mobile Ads (AdMob) reklamları ve tek seferlik ödeme ile reklamları tamamen kaldıran Google Play Billing (IAP) Pro sürüm entegrasyonu mevcuttur.


### 📱 Uygulama Görselleri

![Ana Ekran ve Sayaç](../../../docs/assets/thisone/mobile1_tr.png ':size=20%')  ![Alışkanlık Listesi](../../../docs/assets/thisone/mobile2_tr.png ':size=20%')  ![İstatistikler ve Isı Haritası](../../../docs/assets/thisone/mobile3_tr.png ':size=20%')  ![Ayarlar ve Hatırlatıcılar](../../../docs/assets/thisone/mobile4_tr.png ':size=20%')  ![Ana Ekran Widget](../../../docs/assets/thisone/widget_tr.png ':size=25%')


### 🛠️ Kullanılan Teknolojiler (Tech Stack)

* **Mobil Uygulama Çerçevesi:** Flutter (Dart)
* **Durum Yönetimi (State Management):** Provider
* **Yerel Veri Saklama ve Mimari:** SharedPreferences *(Serverless / Local Data Architecture)*
* **Native Android Entegrasyonu:** Kotlin, `home_widget` SDK, WorkManager & Room Database ProGuard kalkanları *(FlutterFragmentActivity ile Android native kanal iletişimi)*
* **Görsel & Arayüz:** Google Fonts, Material Design 3 (Dark/Light Tema), Flutter Local Notifications
* **Monetizasyon & Servisler:** Google Mobile Ads (AdMob), Google Play Billing (In-App Purchase), Google In-App Review

*(Yapay zeka destekli otonom yazılım ajanları ile iş birliği içinde üretilmiştir.)*

---

### 📦 Mağaza Bağlantıları

Uygulamanın derlenmiş canlı sürümüne aşağıdaki bağlantıdan ulaşabilirsiniz:

**[Google Play Store'da İnceleyin](https://play.google.com/store/apps/details?id=com.thisone.habittracker)**

---

> <small>*⚠️ **Tıbbi Yasal Uyarı ve Sorumluluk Reddi (Medical Disclaimer)**<br> ThisOne tıbbi bir cihaz değildir; herhangi bir hastalığı teşhis etmez, tedavi etmez, iyileştirmez veya önlemez. Uygulama yalnızca kişisel motivasyon, günlük alışkanlık takibi ve tasarruf hesaplama amacıyla sunulur. Sağlığınızla ilgili tıbbi kararlar için lütfen yetkili bir sağlık uzmanına danışın.*</small>
