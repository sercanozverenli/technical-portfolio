# Kavramsal Çerçeve ve Literatürdeki Temel Sorun

Günümüzde ister yapay zekâ (AI) ve makine öğrenmesi (ML) algoritmaları olsun, ister sensörlerle çalışan uçtan uca endüstriyel otomasyon sistemleri olsun; veriye dayalı karar mekanizmalarının önemli bir bölümü ortak bir yapısal sınırlılığa sahiptir. Bu sistemlerin çoğu, karar sürecinden önce verinin güvenilirliğini bağımsız olarak değerlendiren bir mekanizmaya sahip değildir. Bunun yerine, kendilerine sunulan girdiden mümkün olduğunca bir çıktı üretmeye odaklanırlar. Başka bir ifadeyle, sistemler çoğu durumda "Bu veriyle güvenilir bir karar veremem." diyerek karar üretmekten kaçınabilecek bir üst karar katmanına sahip değildir.

Bu yapısal sınırlılığın gerçek dünyadaki etkileri yalnızca teorik değildir; otomasyon ve yapay zekâ tarihindeki önemli olaylar bu problemi açık biçimde ortaya koymaktadır.

**Sensör ve Otomasyon Faciası (Boeing 737 MAX MCAS):** Uçağın hücum açısını (AoA) ölçen sensörlerden gelen hatalı veri nedeniyle MCAS sistemi yanlış düzeltme komutları üretmiştir. Sistem, sensör verisinin güvenilirliğini bağımsız olarak sorgulayan veya veriyi güvenilmez kabul ederek kontrolü pilota devreden bir karar mekanizmasına sahip olmadığından, hatalı veriyi geçerli kabul etmiş ve defalarca burnu aşağı yönlendiren otomatik müdahalelerde bulunmuştur.

**Yapay Zekâ Sınıflandırma Körlüğü (Otonom Araç Kazaları):** Otonom sürüş literatüründe yer alan bazı kazalarda (örneğin 2016 yılında gerçekleşen beyaz tır kazası), görüntü işleme sistemi parlak gökyüzü ile yol üzerindeki beyaz tır dorsesini ayırt etmekte başarısız olmuştur. Dağılım dışı (Out-of-Distribution) özellikler taşıyan bu veriyle karşılaşan model, veriyi güvenilmez olarak işaretleyip güvenli moda geçmek yerine, girdiyi yanlış biçimde yorumlamış ve buna bağlı olarak frenleme gerçekleştirmemiştir.

Yapay zekâ literatüründe **Sahte Kesinlik (False Confidence)** olarak tartışılan bu olgu, modelin güvenilir olmayan veya eğitim dağılımı dışındaki veriler karşısında kendi belirsizliğini doğru biçimde ifade edememesinden kaynaklanmaktadır. Bu çalışmada ise söz konusu durum, verinin istatistiksel anlamını büyük ölçüde yitirdiği kritik eşik **Bilgi Çöküşü (Information Collapse)** kavramı çerçevesinde ele alınmaktadır. Bilgi Çöküşü anlarında sistem, güvenilir bir karar üretecek istatistiksel temelden yoksun olmasına rağmen tahmin üretmeye devam edebilir. Özellikle yüksek riskli uygulamalarda bu durumun maliyeti, sistemin güvenli biçimde kendini durdurması, daha ihtiyatlı bir karar mekanizmasına geçmesi veya insan operatörden yardım istemesinden çok daha ağır sonuçlar doğurabilmektedir.

Bu çalışmada önerilen **Amplify Core (DRDRS)**, literatürdeki bu boşluğu hedefleyen modelden bağımsız (model-agnostic) bir karar yönlendirme mimarisidir. Buradaki modelden bağımsızlık; ana karar vericinin derin öğrenme tabanlı bir yapay zekâ modeli, klasik bir istatistiksel yöntem veya basit bir sensör tabanlı kural motoru olmasından bağımsız olarak mimarinin aynı prensiple çalışabilmesini ifade etmektedir. Amplify Core, veriyi ana karar mekanizmasına iletmeden önce bağımsız bir **İstatistiksel Güvenlik Kontrol Noktası** gibi davranarak verinin güvenilirliğini değerlendirir ve elde edilen sonuca göre uygun karar rejimini belirler.

Sistemin temel yaklaşımı, algoritmaların her koşulda bir tahmin üretmesini varsayan anlayışı terk ederek, karar sürecinden önce verinin kendisine şu temel soruyu yöneltmektir:

> **"Mevcut veri kalitesiyle güvenilir bir karar üretmek istatistiksel olarak ne kadar güvenlidir?"**

---

# Operasyonel Analoji: Karar Verme Rejimleri

Amplify Core'un verinin durumuna göre benimsediği karar rejimleri, dört aşamalı bir otonom sürüş senaryosu ile özetlenebilir:

**Temiz Veri (Güneşli Hava):** Görüş açıktır. Veri güvenilirdir. Sistem ana modeli kullanarak normal performansla çalışmaya devam eder.

**Gürültülü Veri (Yağmurlu / Çamurlu Cam):** Veride geçici bozulmalar bulunmaktadır. Stabilizasyon Katmanı devreye girerek eksiklikleri mümkün olduğunca giderir ve veri yeniden ana modele yönlendirilir.

**Bozuk Veri (Yoğun Sis):** Sorun artık yalnızca yüzeysel gürültü değildir; çevreden elde edilen bilginin güvenilirliği önemli ölçüde azalmıştır. Bu durumda sistem ana modeli devreden çıkarır ve daha muhafazakâr, kural tabanlı veya güvenlik odaklı bir **İhtiyatlı Yedek Model (Fallback Model)** ile çalışmaya devam eder.

**Bilgi Çöküşü (Zifiri Karanlık ve Arızalı Farlar):** Mevcut veri, güvenilir herhangi bir karar üretmeye yetecek istatistiksel bilgiyi artık taşımamaktadır. Böyle bir durumda sistem tahmin üretmek yerine **Karar Kaçınması (Abstention)** stratejisini uygular; işlemi güvenli biçimde durdurur, insan operatöre devreder veya daha üst düzey bir kontrol mekanizmasından destek ister. Çünkü bu koşullarda yanlış bir karar üretmek, hiç karar üretmemekten daha yüksek risk taşımaktadır.

*(Buraya otonom sürüş analojisini özetleyen Şekil-1 diyagramı eklenecektir.)* 

  ![Şekil 1](../../../../../docs/diagrams/amplify-core/sekil-1.PNG ':size=35%') 


