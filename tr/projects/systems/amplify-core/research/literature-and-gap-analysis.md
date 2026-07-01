# Amplify Core:
# Veri Güvenilirliği ve Karar Yönlendirme Sistemi 
(Data Reliability & Decision Routing Systemi)


Veri güvenilirliği ve makine öğrenmesi (ML) model sağlığı konuları, günümüzde literatürde ve endüstride çoğunlukla birbirinden bağımsız disiplinler altında ele alınmaktadır. Ehrlinger ve Zhou gibi araştırmacıların veri kalitesi (Data Quality) üzerine yaptıkları çalışmalar da göstermektedir ki; verinin teknik doğruluğunu değerlendirmek ile o verinin karar mekanizması üzerindeki etkisini öngörmek farklı problemler olarak ele alınmaktadır. Başka bir ifadeyle, veri kalitesinin yüksek olması her zaman güvenilir bir karar üretileceği anlamına gelmemektedir.

Üretim ortamlarında (production) karar sistemlerini güvence altına almak amacıyla geliştirilen mevcut çözümler genel olarak üç ana yaklaşım altında toplanabilir. Ancak bu yaklaşımların her biri, aşağıda açıklanan yapısal sınırlılıklar (Gap) nedeniyle Amplify Core'un hedeflediği karar yönlendirme yaklaşımını tam olarak karşılayamamaktadır.

### 1. Klasik Veri Kalitesi (Data Quality) Araçları
**Örnek Sistemler:** Apache Griffin, AWS Deequ, Great Expectations

Bu araçlar, sisteme ulaşan verinin şemaya uygunluğunu, eksik (null) değer içerip içermediğini, veri tiplerini ve temel bütünlük kurallarını önceden tanımlanmış doğrulama kurallarıyla denetler. Amaçları, bozuk veya hatalı biçimlendirilmiş verilerin sisteme girişini engellemektir.

#### Sistemdeki Boşluk (Gap)
Klasik veri kalitesi araçları **modele kördür (model-blind)**. Sisteme ulaşan bir veri teknik olarak tamamen geçerli olabilir; örneğin sensörden gelen sıcaklık değeri beklenen veri tipindedir, eksik değildir ve tüm doğrulama kurallarını başarıyla geçmiştir. Buna rağmen aynı veri, ana karar modelini yanıltabilecek ciddi bir istatistiksel sapma (drift) veya gürültü (noise) taşıyor olabilir.

Bu araçlar yalnızca verinin yapısal bütünlüğünü değerlendirir; verinin karar mekanizması açısından taşıdığı istatistiksel güvenilirliği veya Bilgi Çöküşü riskini değerlendirmez. Dolayısıyla, teknik olarak geçerli ancak karar açısından riskli verileri ayırt edemezler.

### 2. Makine Öğrenmesi Gözlemleme (ML Observability) Platformları
**Örnek Sistemler:** Arize AI, Evidently AI

Bu platformlar, üretime alınmış makine öğrenmesi modellerinin performansını, veri dağılımındaki değişimleri (data drift), tahmin doğruluğunu ve sistem sağlığını sürekli izlemek amacıyla geliştirilmiştir. Modern MLOps süreçlerinin önemli bileşenlerinden biridir.

#### Sistemdeki Boşluk (Gap)
ML Observability platformları modeli gerçek zamanlı olarak izleyebilseler de, karar sürecine doğrudan müdahale etmezler. Temel amaçları problemi önlemekten ziyade problemi görünür hâle getirmektir.

Model hatalı kararlar üretmeye başladığında çeşitli metrikler, log kayıtları ve uyarılar (alerts) oluşturarak mühendislere bilgi sağlarlar. Ancak uçtan uca otomasyon sistemlerinde, finansal işlemlerde veya otonom sistemlerde problemin yalnızca raporlanması yeterli değildir. Bu tür sistemlerde ihtiyaç duyulan yaklaşım; hatalı karar üretildikten sonra sistemi analiz etmek değil, karar üretilmeden önce güvenilir olmayan veriyi tespit ederek daha güvenli bir karar rejimine yönlendirmektir.

Başka bir ifadeyle, bu platformlar sistemi korumaktan çok gözlemlerler.

### 3. Dağılım Dışı (Out-of-Distribution - OOD) Tespit Algoritmaları
Dağılım dışından gelen örnekleri, beklenmeyen girişleri veya yanıltıcı verileri (adversarial inputs) tespit etmek amacıyla makine öğrenmesi literatüründe geliştirilen özel algoritmalardır. Softmax eşikleme, Bayesian Sinir Ağları ve çeşitli belirsizlik (uncertainty) tahmin yöntemleri bu yaklaşımın yaygın örnekleridir.

#### Sistemdeki Boşluk (Gap)
OOD algoritmaları büyük ölçüde **modele bağımlıdır (model-dependent)**. Kullanılabilmeleri için çoğu zaman ana model mimarisine doğrudan müdahale edilmesi, özel katmanlar eklenmesi veya modelin belirli yöntemlerle yeniden eğitilmesi gerekir.

Bu nedenle çözüm, doğrudan belirli bir modele bağlıdır. Ana karar modeli değiştirildiğinde veya derin öğrenme modeli yerine klasik bir makine öğrenmesi algoritması ya da basit bir kural tabanlı sistem (rule-engine) kullanıldığında aynı yaklaşım çoğu zaman yeniden tasarlanmak zorundadır.

Dolayısıyla bu yöntemler, farklı karar sistemleri arasında ortak kullanılabilecek bağımsız bir güvenlik katmanı sunmaz.

---

## Amplify Core (DRDRS) Bu Boşluğu Nasıl Dolduruyor?

Mevcut literatür incelendiğinde, veriyi yalnızca yapısal olarak doğrulayan, yalnızca karar sonrasında sistemi izleyen veya doğrudan ana modele bağımlı çalışan birbirinden kopuk çözümler görülmektedir. Amplify Core, bu üç yaklaşımın kesişim noktasındaki boşluğu doldurmak amacıyla tasarlanmıştır.

* **Klasik veri kalitesi araçları gibi** karar öncesinde (pre-inference) veriyi değerlendirir; ancak yalnızca yapısal doğrulama yapmakla yetinmez, verinin istatistiksel güvenilirliğini ve karar üretmeye uygunluğunu da analiz eder.
* **ML Observability platformları gibi** sistem sağlığını takip eder; ancak problemi yalnızca raporlamak yerine, güvenilir olmayan veriyi karar üretilmeden önce tespit ederek uygun karar rejimine yönlendirir. Böylece izleme (monitoring) yaklaşımını, aktif karar yönlendirmesine (routing) dönüştürür.
* **OOD tespit algoritmalarından farklı olarak** modelden bağımsızdır (model-agnostic). İster derin öğrenme tabanlı bir yapay zekâ modeli, ister klasik bir makine öğrenmesi algoritması, isterse basit bir kural tabanlı sistem kullanılsın; Amplify Core mevcut karar mekanizmasına dışarıdan entegre edilebilen bağımsız bir denetim noktası (checkpoint) olarak çalışabilir.

Sonuç olarak Amplify Core, mevcut çözümlerin güçlü yönlerini korurken; karar öncesi müdahale (pre-inference), model bağımsızlığı (model-agnostic) ve karar yönlendirme (decision routing) yaklaşımlarını tek bir mimari altında birleştirerek literatürdeki temel boşluğu hedeflemektedir.
