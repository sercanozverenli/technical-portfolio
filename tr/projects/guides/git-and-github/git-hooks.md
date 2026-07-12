# Git Hooks ve Yerel Otomasyon Scriptleri

Bu doküman; Git olaylarının (commit, push, merge vb.) hemen öncesinde veya sonrasında otomatik olarak tetiklenen bash/python scriptlerini (Hooks), kod kalitesini güvence altına alan `pre-commit` ve `pre-push` kancalarının kurulumunu ve bu yapılandırmaların ekip üyeleriyle nasıl paylaşılacağını ele almaktadır.

---

## 1. Git Hooks Nedir ve Nerede Bulunur?

**Git Hooks (Kancalar)**, belirli Git komutları çalıştırıldığında araya girerek özel işlemler yapmanızı sağlayan betiklerdir. İki ana kategoriye ayrılırlar:
* **İstemci Taraflı (Client-Side):** Sizin yerel bilgisayarınızda (commit atarken, branch değiştirirken) çalışan kancalar.
* **Sunucu Taraflı (Server-Side):** GitHub/GitLab gibi sunucularda (push geldiğinde) çalışan kancalar.

Yerel bir Git deposunda `.git/hooks/` dizinine giderseniz, Git'in sizin için önceden hazırladığı ancak aktif olmayan şablon dosyalarını görürsünüz. Bu dosyaların sonundaki `.sample` uzantısı silindiği an kanca aktif hale gelir.

```bash
# Projedeki kanca şablonlarını listeleme
ls -la .git/hooks/

# Çıktı Örneği:
# applypatch-msg.sample
# commit-msg.sample
# pre-commit.sample
# pre-push.sample
```

---

## 2. En Sık Kullanılan İstemci Taraflı Kancalar

### A. pre-commit
Bir commit oluşturulmadan hemen önce (siz `git commit` yazıp Enter'a bastığınız an, mesaj ekranı açılmadan önce) çalışır.
* **Kullanım Amacı:** Koda unutulmuş `print()` veya `breakpoint()` gibi hata ayıklama komutlarının sızmasını engellemek, kod formatlayıcıları (Black, Flake8) çalıştırmak, büyük boyutlu veri dosyalarının (.csv) yanlışlıkla eklenip eklenmediğini kontrol etmek.

### B. commit-msg
Siz commit mesajını yazıp kaydettikten sonra, commit tamamlanmadan hemen önce çalışır.
* **Kullanım Amacı:** Commit mesajlarının belirli bir standarda (Örn: Conventional Commits - `feat:`, `fix:`) uyup uymadığını kontrol etmek veya mesaja otomatik olarak Jira/Trello Issue numarasını eklemek.

### C. pre-push
`git push` komutu çalıştırıldığında, veriler uzak sunucuya aktarılmaya başlamadan hemen önce tetiklenir.
* **Kullanım Amacı:** Sunucuya bozuk kod gitmesini önlemek için yereldeki unit testleri (Örn: `pytest`) çalıştırmak. Testler fail verirse push işlemi iptal edilir.

---

## 3. Adım Adım Özel Bir Hook Oluşturma

Python tabanlı bir veri projesinde, commit atmadan önce kodların içinde unutulmuş bir `breakpoint()` fonksiyonu olup olmadığını kontrol eden bir `pre-commit` kancası yazalım.

**Adım 1:** Şablon dosyasının adını değiştirerek kancayı aktifleştirin.
```bash
mv .git/hooks/pre-commit.sample .git/hooks/pre-commit
```

**Adım 2:** Dosyaya çalıştırma (executable) izni verin (Linux/macOS için zorunludur).
```bash
chmod +x .git/hooks/pre-commit
```

**Adım 3:** Dosyanın içini bir metin editörüyle açıp aşağıdaki Bash scriptini yazın:
```bash
#!/bin/sh
# Projedeki değişiklik yapılan (staged) Python dosyalarını bul
STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep ".py$")

if [ -z "$STAGED_FILES" ]; then
    exit 0
fi

echo "🔍 Python dosyaları inceleniyor..."

# Dosyaların içinde 'breakpoint()' geçiyor mu kontrol et
for FILE in $STAGED_FILES; do
    if grep -q "breakpoint()" "$FILE"; then
        echo "❌ HATA: $FILE dosyasında unutulmuş bir 'breakpoint()' bulundu!"
        echo "Lütfen hata ayıklama kodlarını silin ve tekrar commit atın."
        exit 1 # İşlemi iptal et (Commit atılmaz)
    fi
done

echo "✅ Kod temiz, commit işlemine devam ediliyor."
exit 0 # İşlemi başarıyla tamamla
```

---

## 4. Kancaları Atlamak (Bypass)

Bazen acil bir durum olur ve `pre-commit` veya `commit-msg` kancalarındaki kurallara takılmadan doğrudan commit atmak isteyebilirsiniz (Örn: testleri henüz düzeltmediniz ama kodu yedeklemek istiyorsunuz).

Bunun için `--no-verify` parametresi kullanılır:

```bash
# Hook'ları yok sayarak zorla commit atma
git commit -m "wip: testler kırık ama kod yedeklendi" --no-verify

# Hook'ları yok sayarak pushlama
git push origin main --no-verify
```

---

## 5. Takım İçinde Hook'ları Paylaşmak (Entegre Etmek)

Git mimarisinin en büyük sorunlarından biri, `.git/hooks/` klasörünün versiyon kontrolüne dahil **olmamasıdır**. Yani sizin yazdığınız harika bir `pre-commit` scripti, projeyi klonlayan diğer takım arkadaşınıza gitmez.

Bunu çözmenin iki profesyonel yolu vardır:

### Yöntem A: Git Config ile Klasör Yolunu Değiştirmek
Projenin kök dizininde `.githooks` adında normal bir klasör oluşturulur. Tüm scriptler buraya konur ve bu klasör commitlenip sunucuya gönderilir. Takım arkadaşı projeyi indirdiğinde şu komutu çalıştırarak Git'e kancaları buradan okumasını söyler:

```bash
git config core.hooksPath .githooks
```

### Yöntem B: Framework Kullanmak (Önerilen)
Özellikle Python ekosisteminde, kanca yönetimini manuel bash scriptleriyle yapmak yerine `pre-commit` adlı harici paket kullanılır. Projeye bir `.pre-commit-config.yaml` dosyası eklenir.

```bash
# Framework'ü kur
pip install pre-commit

# Konfigürasyon dosyasına göre kancaları .git/hooks klasörüne otomatik inşa et
pre-commit install
```
*Bu sayede projeye dahil olan her geliştirici, `pre-commit install` komutunu bir kez çalıştırarak tüm standart test ve formatlama kurallarını otomatik olarak bilgisayarına kurmuş olur.*
