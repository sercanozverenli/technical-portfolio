# Hipotenüs Hesaplayıcı (01-hypotenuse-calculator)

Seviye: Başlangıç
Amaç: Dik üçgenin iki dik kenarını kullanarak hipotenüsü hesaplamayı göstermek; Pisagor teoremi, sayısal adımlar ve basit giriş doğrulama örneği.

Teori
- Pisagor teoremi: Bir dik üçgende hipotenüs c ve dik kenarlar a, b için
  c = sqrt(a^2 + b^2)
- Hesaplama adımları:
  1) a ve b değerlerini alın (ondalıklı değerleri desteklemek için float kullanın).
  2) a ve b'nin karelerini hesaplayın: a**2, b**2.
  3) Kareleri toplayın: a**2 + b**2.
  4) Sonucun karekökünü alın: (a**2 + b**2)**0.5 veya math.sqrt(...).
- Sayısal not: Çok büyük değerlerde kare alma taşmaya neden olabilir; günlük kullanımda float yeterlidir.

Örnek kod
```python
# Kullanıcıdan iki dik kenar alınır ve float'a çevrilir
try:
    leg1 = float(input("Birinci dik kenarın uzunluğunu girin: "))
    leg2 = float(input("İkinci dik kenarın uzunluğunu girin: "))
except ValueError:
    print("Geçersiz giriş: Lütfen sayısal bir değer girin.")
else:
    # Basit giriş doğrulaması: negatif veya sıfır değerleri reddedelim
    if leg1 <= 0 or leg2 <= 0:
        print("Uzunluklar pozitif olmalıdır.")
    else:
        # Kareleri hesapla ve topla
        sum_of_squares = leg1 ** 2 + leg2 ** 2
        # Karekök alarak hipotenüsü hesapla
        hypotenuse = sum_of_squares ** 0.5
        print(f"Hipotenüs uzunluğu: {hypotenuse}")
```

Notlar
- Alternatif: math.sqrt kullanmak isterseniz `from math import sqrt` ile sqrt(sum_of_squares) çağrabilirsiniz; bu yalnızca okumayı kolaylaştırır.
- Girdi doğrulamasını artırmak için döngü içinde tekrar isteme veya özel hata mesajları ekleyebilirsiniz.
- Bu örnek öğretim amaçlıdır; gerçek uygulamalarda birim kontrolü (metrik/imperial) ve hassasiyet gereksinimlerini göz önünde bulundurun.
