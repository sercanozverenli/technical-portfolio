# Karar Güven Skoru (Self-Confidence Score - SCS)

## Bu katman ne işe yarar?

Sistemin önceki katmanları verinin sağlıklı olup olmadığını (DRS) ve verinin hangi rejime gitmesi gerektiğini (Yönlendirme Motoru) belirledi. Ancak bir yapay zeka sisteminin gerçek dünyada güvenle çalışabilmesi için son bir soruya daha cevap vermesi gerekir:

> "Veri işlendi ve model bir tahmin üretti; peki bu nihai tahmine ne kadar güvenebiliriz ve bu kararı insansız (otonom) olarak uygulayabilir miyiz?"

**Karar Güven Skoru (SCS)**, sistemin ürettiği her bir tahmine eklenen 0.0 ile 1.0 arasında bir "otonomi ve güven etiketidir". Sistem bu skor sayesinde kendi ürettiği kararın arkasında durur: Skor yüksekse kararı otonom olarak uygular; skor orta seviyedeyse insan onayına sunar; skor düşükse çıktıyı "güvenilmez" olarak etiketler.

---

## SCS nasıl hesaplanır? — İki bileşenli dengeli skor

SCS, sadece verinin kalitesine veya sadece modelin kendine olan güvenine bakmaz. Bu ikisini mantıksal bir dengede birleştirir:

$$SCS = 0.70 \times DRS + 0.30 \times G$$

- **DRS (Veri Güvenilirliği Skoru — %70 ağırlık):** Kararın üzerine inşa edildiği verinin istatistiksel temeli ve kalitesidir.
- **G (Güven Sinyali / Confidence Signal — %30 ağırlık):** Tahmin modelinin ürettiği çıktının kendi içsel kesinlik derecesidir.

**Neden %70 Veri Kalitesi, %30 Model Güveni?**  
Yapay zeka modelleri bazen tamamen yanlış bir tahminde bulunurken bile kendilerinden son derece emin görünebilirler (sahte kesinlik / false confidence). Bu yüzden modelin kendi beyanına (%30) sınır konulmuş, kararın ana temeli (%70) verinin nesnel kalitesine bağlanmıştır. Kötü bir veriden gelen tahmin, model ne kadar emin görünürse görünsün yüksek bir güven skoru alamaz.

---

## Güven Sinyali ($G$): Sınıflandırma ve regresyonda nasıl çalışır?

Güven Sinyali ($G$), farklı problem türlerinde her zaman **"yüksek değer = yüksek güven"** mantığıyla [0, 1] aralığında hesaplanır:

1. **Sınıflandırma Modellerinde (UJIIndoorLoc, Yahoo Finance):** Modelin en yüksek olasılık verdiği birinci sınıf ile ikinci sınıf arasındaki fark ($Prob_{max1} - Prob_{max2}$) esas alınır. İki sınıf arasındaki fark ne kadar açılırsa, model o kadar kararlıdır.
2. **Regresyon Modellerinde (Olist):** Tahmin için hesaplanan Bootstrap Güven Aralığı genişliği kullanılır. Ters çevirme işlemi formülün kendisine değil, sinyalin tanımına dahil edilir:
   $$G_{ham} = 1 - \text{normalize}(\text{güven aralığı oranı})$$
   Böylece güven aralığı daraldıkça (belirsizlik azaldıkça) Güven Sinyali ($G_{ham}$) 1'e yaklaşır.

---

## İhtiyatlı Yedek Model ve Yapısal Güvenlik Tavanı ($G_{cap} = 0.90$)

Bozuk rejimden gelen (ham DRS: 0.25–0.49) veya stabilizasyondan çıkıp Temiz eşiğine ulaşamayan (`Recovered DRS`: 0.25–0.799) veriler **İhtiyatlı Yedek Modele (Fallback Model)** yönlendirilir. 

İhtiyatlı Yedek Model mantığı gereği daha basit ve temkinli bir yapıdır. Bu modelden üretilen Güven Sinyali'ne, model ne kadar iddialı bir çıktı verirse versin **$G_{cap} = 0.90$ yapısal tavanı** uygulanır:

$$G = \min(G_{ham}, 0.90)$$

### Matematiksel Güvenlik Garantisi

İhtiyatlı Modele giren bir verinin ulaşabileceği en yüksek `Recovered DRS` değeri $0.799$'dur. Bu durumda bir Yedek Model çıktısının alabileceği **teorik maksimum SCS skoru**:

$$SCS_{max} = (0.70 \times 0.799) + (0.30 \times 0.90) = 0.5593 + 0.2700 = 0.8293$$

$$SCS_{max} = 0.8293 < 0.85$$

**Bu tavanın anlamı nedir?**  
İhtiyatlı Yedek Model çıktısı hangi yoldan gelirse gelsin ve model ne kadar emin görünürse görünsün, ürettiği karar **matematiksel olarak asla Otonom Karar eşiğini ($\ge 0.85$) aşamaz.** Sistem, bozulmuş bir veriden gelen kararın insan onayı olmadan otonom çalışmasına yapısal olarak izin vermez.

---

## Üç Otonomi Seviyesi — Eşik Değerleri ve Karar Bölgeleri

Hesaplanan nihai SCS değerine göre sistem veriye üç farklı karardan birini atar:

| SCS Aralığı | Otonomi Seviyesi | Ne Anlama Gelir / Ne Yapılır? |
|---|---|---|
| **≥ 0.85** | **Otonom Karar** | Veri kaliteli, model emin. Tahmin insansız olarak doğrudan işleme alınır ve uygulanır. |
| **0.60 – 0.849** | **Manuel Onay Önerilir** | Tahmin üretilmiştir ancak tam otonomi için risklidir. Karar bir operatörün/insanın onayına sunulur. |
| **< 0.60** | **Güvenilmez Çıktı** | Tahmin üretilmiş olsa dahi belirsizlik çok yüksektir. Çıktı reddedilir, işlem kilitlenir. |

---

## Özel Durum: Fatura İşleme Ajanı Modülü Muafiyeti

Amplify Core mimarisinde **Fatura İşleme Ajanı** modülünün çalışma mantığı diğer modellerden farklıdır:

- Fatura Ajanı'nda DRS değerlendirmesinin ötesinde ayrı bir tahmin modeli (regresyon veya sınıflandırma) bulunmaz. 
- DRS kararının kendisi (Temiz / Gürültülü / Karar Kaçınması) zaten sistemin **nihai çıktısıdır.**
- SCS mekanizması, tanımı gereği bir tahmin modelinin çıktısını ölçtüğü için **Fatura İşleme Ajanı'nda SCS hesaplanmaz.** 
- Faturadaki bazı göstergelerin uygulanamadığı durumlar ($m_i=0$ maskelemesi) SCS raporunda değil, doğrudan DRS meta-veri kaydında saklanır.

---

## Neden bu tasarım tercih edildi?

- **Neden Güven Kapısı ($\tau$) geçilmeden SCS hesaplanmıyor?** Güven Kapısı, Post-inference aşamasında kalitesiz tahminleri eleyen canlı bir filteredir. Kapıdan geçemeyen bir kayıt doğrudan Karar Kaçınması'na (Safe Standby) gittiği için gereksiz SCS hesaplama maliyeti oluşmaz.
- **Neden ampirik tavan değil de formüle dayalı $G_{cap}=0.90$?** Yapay zeka güvenlik standartlarında sezgisel (heuristic) sınırlamalar yerine matematiksel ispatlar aranır. $0.8293 < 0.85$ eşitsizliği, sistemin güvenlik garantisini ampirik testlere değil, teorik bir ispata bağlar.
- **Şeffaflık ve Denetlenebilirlik:** Her tahmin çıktısının yanında `DRS`, `Güven Sinyali (G)`, `confidence_signal_source` ve `confidence_signal_capped` bilgisi saklanır. Böylece kararın neden otonom veya neden insan onaylı olduğu geriye dönük tam olarak izlenebilir.

---

← [Karar Kaçınması Mekanizması](tr/projects/systems/amplify-core/architecture/abstention-mechanism.md)
