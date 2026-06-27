# Oyunlaştırılmış Çarpım Tablosu Aracı

Eğitim teknolojileri odaklı geliştirilen bu proje, ilköğretim seviyesindeki kullanıcılar için sıkıcı ezber yöntemlerini ortadan kaldırarak çarpma işlemini eğlenceli ve etkileşimli hale getiren web ve mobil tabanlı bir oyunlaştırma (gamification) uygulamasıdır. 

### 🎮 Oyun Mekaniği ("Kimim Ben?")
Uygulama etkileşimli bir ödül mantığı üzerine kuruludur:

* **Zorluk Seçimi:** Kullanıcı, pratik yapmak istediği seviyeye uygun çarpım tablosu zorluk derecesini belirler.
  
  ![Zorluk Seçimi](../../../docs/assets/gamified-multiplication-tool/multiplication_1.png ':size=35%')

* **Dinamik Soru Havuzu ve Hata Kontrolü:** Çarpım tablosu soruları rastgele karıştırılarak (randomized) ekrana getirilir. Test bitiminde "Kimim Ben?" butonuna tıklandığında, arka planda çalışan algoritma cevapları denetler. Eğer hata varsa, kullanıcı uyarılır ve doğruları bulmaya teşvik edilir.
  
  ![Soru Ekranı ve Hata Kontrolü](../../../docs/assets/gamified-multiplication-tool/multiplication_2.png ':size=35%')

* **Ödül Sistemi:** Tüm sorular hatasız tamamlandığında; seçilen zorluk seviyesine özel olarak havuzdan çekilen gizli bir karakterin kimlik bilgilerini içeren özel bir ödül sayfasına yönlendirme yapılır.
  
  ![Ödül Ekranı](../../../docs/assets/gamified-multiplication-tool/multiplication_3.png ':size=35%')


### Kullanılan Teknolojiler (Tech Stack)
* **Arayüz ve Tasarım:** HTML5, CSS3, Bootstrap 4.0
* **JavaScript Ekosistemi ve Oyun Motoru:** Vanilla JS, jQuery 3.5.1, Fabric.js *(Uygulama mantığının kurgulanması, DOM manipülasyonları ve Canvas üzerindeki karakter yönetim süreçleri GPT-3.5 yapay zeka asistanı desteğiyle entegre edilmiştir.)*
* **Mobil Dağıtım:** HTML/JS mimarisi APK Builder aracı kullanılarak dağıtıma hazır Android (`.apk`) formatına dönüştürülmüştür.

---

### Kaynak Kodları ve Kurulum
Uygulamanın web tabanlı kaynak kodlarına ve derlenmiş Android sürümüne aşağıdaki bağlantılardan ulaşabilirsiniz:

**[GitHub Kaynak Kodları](https://github.com/sercanozverenli/web_and_mobile_apps/tree/main/Gamified_Multiplication_Tool/HTML_Source)** <br>
**[Android APK Dosyası](https://github.com/sercanozverenli/web_and_mobile_apps/blob/main/Gamified_Multiplication_Tool/Android_Build/Gamified_Multiplication_Tool_1_1.0.apk)**

---

> <small>*⚠️ **Yasal Uyarı ve Sorumluluk Reddi (Disclaimer)**<br> Bu proje tamamen eğitim, araştırma ve kişisel portföy sergileme amacıyla ticari bir amaç güdülmeden geliştirilmiştir. Proje içeriğinde yer alabilecek üçüncü taraflara ait her türlü materyalin (görsel, ses, metin vb.) telif ve fikri mülkiyet hakları asıl sahiplerine aittir.*</small>
> 
> <small>*Projeyi veya kaynak kodlarını kullanan, çoğaltan veya kendi çalışmalarına entegre eden üçüncü şahıslar, ilgili materyallerin lisans ve telif gereksinimlerine uymakla yegâne sorumludur. Proje geliştiricisi; içeriklerin veya kodların yetkisiz ticari kullanımından, dağıtımından veya doğabilecek herhangi bir telif hakkı ihlalinden dolayı hiçbir yasal sorumluluk kabul etmez. Projeyi kullanan herkes bu şartları peşinen kabul etmiş sayılır.*</small>
