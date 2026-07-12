# Projeler ve Yardımcı Konular

Bu sayfada başlangıç seviyesindeki bazı küçük projeleri ve günlük geliştirici ihtiyaçları için faydalı olan yardımcı konuları bir arada bulacaksınız. Anlatım doğal Türkçe ile yapılmıştır: önce konunun ne olduğunu, hangi problemleri çözdüğünü anlatıyorum, sonra gerekli kod örneklerini veriyorum. Bu dosya hem öğrenme hem de uygulama amaçlı tek bir kaynak olarak kullanılabilir.

Bu sayfada öğrenecekleriniz
- Tarih ve zaman ile temel işlemler: datetime modülü kullanımı, tarih parsing ve formatlama, gün hesaplama.
- Küçük proje örnekleri: Tic-Tac-Toe (oyun iskeleti), Basit Komut Satırı Not Defteri, Birim dönüşümü aracı.
- Proje düzeni ve pratik ipuçları: fonksiyonlaştırma, test etme, giriş doğrulama.

---

## Tarih ve zaman: datetime modülü

Python'da tarih ve saatle çalışmak için standart olarak `datetime` modülü kullanılır. Temel sınıflar:
- `datetime.date`: tarih (yıl, ay, gün)
- `datetime.time`: saat bilgisi (saat, dakika, saniye)
- `datetime.datetime`: tarih ve saat birlikte
- `datetime.timedelta`: iki tarih/saat arasındaki fark

Basit örnekler
```python
from datetime import datetime, date, timedelta

# şu anki tarih ve saat
now = datetime.now()
print(now)  # 2026-07-12 14:00:00.123456

# sadece tarih
today = date.today()
print(today)  # 2026-07-12

# tarih formatlama
print(now.strftime("%Y-%m-%d %H:%M"))  # 2026-07-12 14:00

# string -> datetime (parsing)
s = '2026-07-12 14:00'
dt = datetime.strptime(s, "%Y-%m-%d %H:%M")

# iki tarih arasındaki fark
tomorrow = today + timedelta(days=1)
diff = tomorrow - today
print(diff.days)  # 1
```

Pratik notlar
- Tarih formatları ülkeye göre farklı olabilir; kullanıcıdan tarih alırken açık format belirtin (örn. YYYY-MM-DD).
- Zaman dilimi (timezone) gereksinimi varsa `pytz` veya Python 3.9+'da gelen `zoneinfo` kullanılmalıdır.

---

## Proje 1 — Tic-Tac-Toe: oyun iskeleti (basit CLI versiyon)

Bu proje küçük ve tamamlanabilir bir oyun sunar. Amaç: oyun mantığını, tahtayı, kazanma kontrollerini ve kullanıcı etkileşimini öğrenmek.

Ana adımlar
1. 3x3'lük bir oyun tahtası (liste listesi veya tek liste) oluşturun.
2. Oyuncu girdilerini alıp tahtaya yerleştirin.
3. Her hamleden sonra kazanma/draw kontrolü yapın.
4. Oyun bittiğinde kazananı veya beraberliği bildirin.

Basit bir örnek iskelet
```python
# basit bir Tic-Tac-Toe iskeleti

def create_board():
    return [' ' for _ in range(9)]

def print_board(b):
    for i in range(0, 9, 3):
        print(f" {b[i]} | {b[i+1]} | {b[i+2]} ")
        if i < 6:
            print('---+---+---')

def check_winner(b, mark):
    wins = [
        (0,1,2),(3,4,5),(6,7,8),
        (0,3,6),(1,4,7),(2,5,8),
        (0,4,8),(2,4,6)
    ]
    return any(all(b[i]==mark for i in combo) for combo in wins)

def is_draw(b):
    return all(cell != ' ' for cell in b)

def play():
    board = create_board()
    current = 'X'
    while True:
        print_board(board)
        try:
            pos = int(input(f"{current} için pozisyon (0-8): "))
        except ValueError:
            print('Geçersiz pozisyon, tekrar deneyin.')
            continue
        if not (0 <= pos < 9) or board[pos] != ' ':
            print('Geçersiz veya dolu pozisyon, tekrar deneyin.')
            continue
        board[pos] = current
        if check_winner(board, current):
            print_board(board)
            print(f"Kazanan: {current}")
            break
        if is_draw(board):
            print_board(board)
            print('Berabere')
            break
        current = 'O' if current == 'X' else 'X'

if __name__ == '__main__':
    play()
```

Geliştirme fikirleri
- Basit bir bilgisayar oynatıcı (random hamle veya min-max ile gelişmiş) ekleyin.
- Hamleleri kaydetme/geri alma özelliği ekleyin.
- Birim testleri ile oyun mantığını doğrulayın.

---

## Proje 2 — Basit Komut Satırı Not Defteri

Bu küçük uygulama dosyaya not ekleme, listeleme ve silme gibi temel işlevleri sağlayacak. Amaç: dosya I/O, kullanıcıyla etkileşim ve basit hata yönetimi öğrenmek.

Basit örnek
```python
# notepad.py - çok basit bir CLI not defteri

FILENAME = 'notes.txt'

def add_note(text):
    with open(FILENAME, 'a', encoding='utf-8') as f:
        f.write(text.replace('\n', ' ') + '\n')

def list_notes():
    try:
        with open(FILENAME, 'r', encoding='utf-8') as f:
            for i, line in enumerate(f, 1):
                print(i, line.strip())
    except FileNotFoundError:
        print('Henüz not bulunmuyor.')

def delete_note(index):
    try:
        with open(FILENAME, 'r', encoding='utf-8') as f:
            lines = f.readlines()
        if not (1 <= index <= len(lines)):
            print('Geçersiz indeks')
            return
        lines.pop(index - 1)
        with open(FILENAME, 'w', encoding='utf-8') as f:
            f.writelines(lines)
    except FileNotFoundError:
        print('Henüz not bulunmuyor.')

# basit komut satırı arayüzü
if __name__ == '__main__':
    while True:
        cmd = input('(a)dd, (l)ist, (d)elete, (q)uit: ')
        if cmd == 'a':
            t = input('Not metni: ')
            add_note(t)
        elif cmd == 'l':
            list_notes()
        elif cmd == 'd':
            try:
                idx = int(input('Silinecek not numarası: '))
            except ValueError:
                print('Geçersiz sayı')
                continue
            delete_note(idx)
        elif cmd == 'q':
            break
        else:
            print('Bilinmeyen komut')
```

Geliştirme fikirleri
- Notlara etiket ekleme, JSON veya CSV formatında saklama.
- Basit arama (kelime içeriyor mu) özelliği ekleme.

---

## Proje 3 — Birim Dönüşümü Aracı

Küçük bir araç ile farklı birimler arasında dönüşüm yapabilirsiniz (ör. sıcaklık, uzunluk). Bu proje fonksiyon tasarımı ve test yazmayı teşvik eder.

Örnek fonksiyonlar
```python
def celsius_to_fahrenheit(c):
    return c * 9/5 + 32

def km_to_miles(km):
    return km * 0.621371

# kullanım
print(celsius_to_fahrenheit(0))  # 32.0
print(km_to_miles(5))            # ~3.106855
```

Öneriler
- Kullanıcıdan birim ve değer alıp uygun dönüşümü seçen bir CLI yazın.
- Birimlerin doğrulanması için sözlük (`dict`) kullanın: `{'km': km_to_miles, ...}` ve dinamik çağrı yapın.

---

## Proje düzeni ve pratik ipuçları

Küçük projelerde bile temiz bir yapı önemlidir. Tavsiyeler:
- Fonksiyonlaştırın: Tekrin eden mantıkları fonksiyonlara alın.
- Modüller: Benzer işlevleri aynı dosyada tutmak yerine modüllere ayırın (ör. `utils.py`).
- Testler: `pytest` veya Python'un `unittest` modülü ile temel testler yazın.
- Girdi doğrulama: Her kullanıcı girdisini doğrulayın; beklenmeyen veri uygulamayı bozabilir.
- Sürüm kontrolü: Git kullanın ve anlamlı commit mesajları yazın.

---

## İpuçları ve dikkat edilmesi gerekenler

- Küçük projeler öğrenme için mükemmeldir; önce basit bir çalışır versiyon yapın, sonra özellik ekleyin.
- Kodunuzu küçük parçalara ayırın; bu sayede test etmek ve hata ayıklamak kolaylaşır.
- Dokümantasyon: her fonksiyon için kısa bir docstring yazın; proje büyüdükçe okuyanlara büyük kolaylık sağlar.

