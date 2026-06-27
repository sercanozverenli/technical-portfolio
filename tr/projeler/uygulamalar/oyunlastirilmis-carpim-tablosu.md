# Oyunlaştırılmış Çarpım Tablosu Aracı

Eğitim teknolojileri odaklı geliştirilen bu proje, ilköğretim seviyesindeki kullanıcılar için sıkıcı ezber yöntemlerini ortadan kaldırarak çarpma işlemini eğlenceli ve etkileşimli hale getiren web ve mobil tabanlı bir oyunlaştırma (gamification) uygulamasıdır. 

### 🎮 Oyun Mekaniği ("Kimim Ben?")
Uygulama etkileşimli bir ödül mantığı üzerine kuruludur:
* **Zorluk Seçimi:** Kullanıcı, pratik yapmak istediği seviyeye uygun çarpım tablosu zorluk derecesini belirler.
* **Dinamik Soru Havuzu:** Çarpım tablosu soruları rastgele karıştırılarak (randomized) ekrana getirilir.
* **Hata Kontrolü:** Test bitiminde "Kimim Ben?" butonuna tıklandığında, arka planda çalışan algoritma cevapları denetler. Eğer hata varsa, kullanıcı uyarılır ve doğruları bulmaya teşvik edilir.
* **Ödül Sistemi:** Tüm sorular hatasız tamamlandığında; seçilen zorluk seviyesine özel olarak havuzdan çekilen gizli bir karakterin kimlik bilgilerini içeren özel bir ödül sayfasına yönlendirme yapılır.

### Uygulama İçi Görseller
*(Not: Ekran görüntülerini portföy dizinindeki `docs/assets/` klasörü altına ekleyebilirsiniz.)*

<div style="display: flex; gap: 10px; margin-top: 15px;">
  <img src="docs/assets/gamified-multiplication-tool/multiplication_1.png" alt="Zorluk Seçimi" width="30%">
  <img src="docs/assets/gamified-multiplication-tool/multiplication_2.png" alt="Soru Ekranı" width="30%">
  <img src="docs/assets/gamified-multiplication-tool/multiplication_3.png" alt="Ödül Ekranı" width="30%">
</div>

### Kullanılan Teknolojiler (Tech Stack)
* **Arayüz ve Tasarım:** HTML5, CSS3, Bootstrap 4.0
* **JavaScript Ekosistemi ve Oyun Motoru:** Vanilla JS, jQuery 3.5.1, Fabric.js *(Uygulama mantığının kurgulanması, DOM manipülasyonları ve Canvas üzerindeki karakter yönetim süreçleri GPT-3.5 yapay zeka asistanı desteğiyle entegre edilmiştir.)*
* **Mobil Dağıtım:** HTML/JS mimarisi APK Builder aracı kullanılarak dağıtıma hazır Android (`.apk`) formatına dönüştürülmüştür.

---

> **⚠️ Telif Hakkı ve Feragatname**
> Bu uygulama tamamen **eğitim ve kişisel portföy** amaçlı olarak, bir "Proof of Concept" (Kavram Kanıtı) niteliğinde geliştirilmiştir. "Kimim Ben?" ödül mekanizması havuzunda kullanılan karakter görselleri açık internet kaynaklarından temin edilmiş olup **hiçbir ticari amaç güdülmemektedir**.

---

### Kaynak Kodları ve Kurulum
Uygulamanın web tabanlı kaynak kodlarına ve derlenmiş Android sürümüne aşağıdaki bağlantılardan ulaşabilirsiniz:

👉 **[GitHub Üzerinden HTML Kaynak Kodlarını İncele](https://github.com/sercanozverenli/web_and_mobile_apps/tree/main/Gamified_Multiplication_Tool/HTML_Source)** 👉 **[Kurulabilir Android APK Dosyasını İndir](https://github.com/sercanozverenli/web_and_mobile_apps/blob/main/Gamified_Multiplication_Tool/Android_Build/Gamified_Multiplication_Tool_1_1.0.apk)**
