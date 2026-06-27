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

> **⚠️ Telif Hakkı ve Yasal Uyarı (Disclaimer)**
> Bu proje tamamen **eğitim, öğrenme ve kişisel portföy** sergileme amacıyla, bir "Proof of Concept" (Kavram Kanıtı) niteliğinde açık kaynaklı olarak geliştirilmiştir. Uygulama içinde yer alan ve üçüncü taraflara ait olan karakter görsellerinin (IP'ler) **tüm telif hakları asıl sahiplerine aittir** ve bu proje kapsamında tarafımca hiçbir **ticari gelir veya amaç güdülmemektedir**.
> 
> **Sorumluluk Reddi:** Bu deponun kaynak kodlarını indiren, kopyalayan (fork) veya kendi projelerinde kullanan **üçüncü şahıslar**, proje içerisindeki üçüncü parti görsellerin telif hakkı lisans gereksinimlerinden **tamamen kendileri sorumludur**. Projenin geliştiricisi olarak ben; bu kaynak kodların veya içeriklerin başkaları tarafından ticari faaliyetlerde kullanılmasından, izinsiz dağıtımından veya telif hakkı ihlali doğuracak herhangi bir kötüye kullanımından doğabilecek **hiçbir hukuki ve cezai sorumluluğu kabul etmiyorum**. Projeyi veya kodları indiren/kullanan herkes bu şartları peşinen kabul etmiş sayılır.
