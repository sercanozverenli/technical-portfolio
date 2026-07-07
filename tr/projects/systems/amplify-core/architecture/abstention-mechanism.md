# Karar Kaçınması Mekanizması

![Karar Kaçınması Mekanizması Akışı](../../../../../docs/diagrams/amplify-core/sekil-8.png)

## Bu mekanizma ne işe yarar?

Yönlendirme Motoru veriyi Bilgi Çöküşü rejimine (DRS < 0.25) yönlendirdiğinde, sistem artık bir tahmin üretmeye çalışmaz. Bunun yerine bilinçli olarak durur ve **Güvenli Bekleme (Safe Standby)** moduna geçer.

Bu bir arıza değil, tasarım gereği bir güvenlik davranışıdır. Verinin istatistiksel temeli o kadar zayıflamıştır ki, bu noktada üretilecek herhangi bir tahmin gerçek bilgiden çok rastgele bir sonuçtan farksız olur. Sistem bunu bilir ve "bu veriyle güvenli bir karar veremem" der.

## Sistem durur mu, yoksa devam mı eder?

Kritik bir ayrım burada: **Güvenli Bekleme, tüm sistemin durması anlamına gelmez** — sadece o anki veri kaydı için karar üretiminin durması anlamına gelir. Sistem şu adımları izler:

1. Bilgi Çöküşü tespit edilir, Güvenli Bekleme moduna geçilir.
2. İlgili veri kaydı, **işlenemeyen kayıt günlüğüne (Dead Letter Log)** JSONL formatında yazılır — DRS skoru, hangi göstergelerin başarısız olduğu ve zaman damgası birlikte kaydedilir.
3. Aynı anda, ardışık kaç kez Bilgi Çöküşü yaşandığını izleyen bir **sayaç** artırılır.
4. Sistem akışı durdurmaz; bir sonraki veri kaydına geçer.
5. Yeni veri geldiğinde DRS otomatik olarak yeniden hesaplanır.

Yani sistem hiçbir zaman "kilitlenmiş" bir bekleme durumuna girmez — sürekli yeni veriyi değerlendirmeye devam eder, sadece güvenilir bir sinyal bulana kadar karar üretmekten kaçınır.

## İyileşme olursa ne olur, olmazsa ne olur?

Yeni veri için hesaplanan DRS skoru 0.25 eşiğini aşarsa, veri otomatik olarak ilgili rejime (Temiz, Gürültülü veya Bozuk) geri döner ve normal işleyişe devam eder.

Eşik aşılamazsa, sistem sayaç değerini kontrol eder. Sayaç belirli bir eşiği aşarsa (örnek değer: 10 ardışık çöküş), sistem **asenkron olarak** bir insan müdahalesi uyarısı tetikler. "Asenkron" olması önemlidir: bu uyarı, ana veri akışını bloklayan bir işlem değildir — sistem veriyi işlemeye devam ederken arka planda bir bildirim gönderilir. Sayaç eşiği aşılmamışsa, sistem sessizce izlemeye devam eder; hiçbir aksiyon tetiklenmez.

Sayaç eşiği (yukarıdaki diyagramda N=10 olarak gösterilmiştir) şu an için gösterge niteliğinde bir başlangıç değeridir — hangi veri ortamında kaç ardışık çöküşün gerçek bir anomaliye işaret ettiği, proje ilerledikçe veri tipine göre kalibre edilecektir.

## Neden akış hiç durdurulmuyor?

Alternatif bir tasarım, sistemin Bilgi Çöküşü anında tüm akışı durdurup insan onayı beklemesi olabilirdi. Bu tercih edilmedi, çünkü:

- **Tek bir bozuk kayıt, tüm sistemi bloke etmemeli.** Bilgi Çöküşü çoğu zaman geçici bir durumdur (örneğin bir sensörün anlık kesintisi); akışı durdurmak, sonraki sağlıklı verinin de işlenmesini geciktirir.
- **İnsan müdahalesi, ancak gerçekten gerektiğinde tetiklenmeli.** Sayaç mekanizması, tek seferlik bir çöküşü değil, ısrarlı bir örüntüyü (ardışık N kez) yakalamayı hedefler. Bu, gereksiz uyarı yorgunluğunu (alert fatigue) önler.
- **Loglama, durdurmadan da izlenebilirliği sağlar.** Dead Letter Log sayesinde hiçbir çöküş kaybolmaz — sistem durmasa bile, her olay geriye dönük incelenebilir.

## Sonraki katman

Karar Kaçınması, sadece Bilgi Çöküşü rejiminde devreye giren bir mekanizmadır. Ama sistemin **her** çıktısına eklenen, rejimden bağımsız ayrı bir güven etiketi de vardır — bu etiket, bir çıktının ne kadar otonom kabul edilebileceğini belirler:

→ [Karar Güven Skoru (SCS)](tr/projects/systems/amplify-core/architecture/self-confidence-score.md)
