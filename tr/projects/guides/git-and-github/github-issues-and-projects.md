# Proje Yönetimi, Hata Takibi ve Takım Organizasyonu

Bu doküman; kod yazımından ziyade bir yazılım projesinin nasıl yönetileceğine, görev dağılımlarının nasıl yapılacağına, yazılım hatalarının (bug) nasıl raporlanıp takip edileceğine ve GitHub'ın yerleşik Çevik (Agile) yönetim araçları olan Issues ve Projects sistemlerinin kullanımına odaklanmaktadır.

---

## 1. GitHub Issues Nedir ve Ne Amaçla Kullanılır?

**GitHub Issues (Sorunlar/Görevler)**, bir deponun yerleşik hata takip ve bilet (ticket) sistemidir. İsmine "Sorun" denilse de sadece hatalar için kullanılmaz. Yazılım geliştirme döngüsündeki her türlü iş kalemi bir Issue olarak açılır.

**Temel Kullanım Senaryoları:**
* **Hata Raporlama (Bug Tracking):** "Giriş ekranında şifremi unuttum butonu çalışmıyor."
* **Yeni Özellik Talebi (Feature Request):** "Uygulamaya karanlık mod (dark mode) desteği eklenmeli."
* **Görev Listesi (Task Management):** "Veritabanı şemalarının dökümantasyonunun yazılması."
* **Soru ve Tartışma:** "Arama algoritması için ElasticSearch mü yoksa Redis mi kullanmalıyız?"

Bir Issue oluşturulduğunda sistem ona benzersiz bir ID atar (Örn: `#12`). Depo içindeki tüm commit'ler ve Pull Request'ler (PR) bu ID üzerinden Issue'ya atıfta bulunabilir.

---

## 2. Issue Yönetim Parametreleri (Metadata)

İçerisinde yüzlerce görev barındıran bir projede kaybolmamak için GitHub sağ taraftaki panelde güçlü sınıflandırma ve filtreleme araçları sunar:

### A. Assignees (Görev Atamaları)
Bir işi kimin veya kimlerin yapacağını belirler. Bir Issue sahipsiz olmamalıdır. Issue'yu birine atadığınızda (Assign), o kişinin e-posta adresine ve GitHub bildirim paneline uyarı gider. İşin sorumlusu belli olur.

### B. Labels (Etiketler)
Görevleri kategorize etmeyi ve filtrelemeyi sağlayan renkli etiketlerdir. GitHub varsayılan olarak birkaç etiket sunar ancak projeye özel etiketler oluşturulabilir:
* `bug`: Bir şey çalışmıyor. (Kırmızı)
* `enhancement`: Yeni özellik veya geliştirme. (Açık Mavi)
* `documentation`: Kod değil, belge güncellemesi gerekiyor. (Mavi)
* `good first issue`: Açık kaynak projelere yeni katılacaklar için kolay, ısınma amaçlı görevler.
* `help wanted`: Proje sahibinin dışarıdan yardıma veya uzmanlığa ihtiyaç duyduğu konular.

### C. Milestones (Kilometre Taşları)
Birbiriyle ilişkili birden fazla Issue'yu belirli bir hedefe (Sprint veya Sürüm/Release) bağlamak için kullanılır. 
* **Örnek Milestone:** `v1.0.0 Beta Sürümü` veya `Ekim Ayı Sprinti`.
* Bir Milestone'a bitiş tarihi (Due Date) verilebilir. İçine eklenen 10 Issue'dan 6'sı kapatıldığında sistem otomatik olarak `%60 Tamamlandı` şeklinde bir ilerleme çubuğu (progress bar) gösterir.

---

## 3. Issue Şablonları (Issue Templates) Yapılandırması

Özellikle Public (açık kaynak) depolarda, dışarıdan gelen kullanıcıların yetersiz hata bildirimleri ("Uygulama çöktü, düzeltin") yapmasını engellemek için Issue Şablonları kullanılır.

Deponuzun kök dizininde `.github/ISSUE_TEMPLATE/` klasörü oluşturup içine Markdown formatında şablonlar eklerseniz, yeni bir Issue açmak isteyen kullanıcıya doğrudan bir form sunulur.

**Örnek bir `bug_report.md` şablonu yapısı:**
```markdown
---
name: Hata Raporu
about: Bir şeylerin bozuk olduğunu bildirmek için kullanın
title: '[BUG] '
labels: bug
assignees: ''
---

**Hata Açıklaması**
Hatanın ne olduğunu açık ve net bir şekilde belirtin.

**Nasıl Tekrar Üretilir (Steps to Reproduce)**
Hata ekranına ulaşmak için adımları yazın:
1. '...' sayfasına git.
2. '...' butonuna tıkla.
3. Çıkan hatayı gör.

**Beklenen Davranış**
Bozuk olan kısmın aslında nasıl çalışması gerektiğini açıklayın.

**Çevre (Environment):**
 - İşletim Sistemi: [Örn. iOS, Windows]
 - Tarayıcı [Örn. Chrome, Safari]
 - Versiyon [Örn. 22]
```

---

## 4. Akıllı Otomasyon: Commit ile Issue Kapatma

GitHub'ın en güçlü özelliklerinden biri, geliştiricinin GitHub arayüzüne girmesine gerek kalmadan, sadece terminalden kod göndererek (push) Issue'ları otomatik kapatabilmesidir.

Eğer bir commit mesajında belirli anahtar kelimeler ve Issue numarası (`#ID`) birlikte kullanılırsa, o commit ana branch'e (`main`) merge edildiği anda GitHub ilgili Issue'yu otomatik olarak "Closed" (Kapatıldı) statüsüne çeker.

**Geçerli Anahtar Kelimeler:** `close`, `closes`, `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves`, `resolved`

**Terminal Örneği:**
```bash
git add .

# Bu commit sunucuya ulaştığında 42 numaralı Issue otomatik olarak kapanır
git commit -m "fix: şifre sıfırlama butonundaki çökme giderildi. Fixes #42"

git push origin main
```

---

## 5. GitHub Projects (Agile/Kanban Panosu)

Büyük projelerde Issue'ları liste halinde okumak yerine görselleştirmek için **GitHub Projects** kullanılır. Jira veya Trello kullandıysanız, GitHub Projects birebir aynı mantıkta çalışan ve doğrudan deponuzdaki kodlar ve Issue'larla entegre olan bir sistemdir.

**Temel Mimari (Kanban Sütunları):**
Proje panosu tipik olarak 3 ila 4 temel sütundan (kolondan) oluşur:
1. **To Do (Yapılacaklar):** Üzerinde henüz çalışılmaya başlanmamış Issue'ların veya notların olduğu yığın.
2. **In Progress (Devam Edenler):** Şu anda bir geliştiricinin aktif olarak kod yazdığı, ataması yapılmış işler.
3. **In Review (İncelemede):** Kodu yazılmış, Pull Request (PR) açılmış ve onay (Code Review) bekleyen işler.
4. **Done (Tamamlananlar):** Kodu ana branch'e başarıyla birleştirilmiş ve kapatılmış görevler.

**Yerleşik Otomasyon:**
GitHub Projects'in en büyük avantajı kod ile olan bağıdır. Panoya kurallar ekleyebilirsiniz:
* "Bir Issue için PR açıldığında, o kartı otomatik olarak 'In Progress' sütunundan alıp 'In Review' sütununa taşı."
* "PR merge edildiğinde (birleştirildiğinde) kartı otomatik olarak 'Done' sütununa taşı ve Issue'yu kapat."
