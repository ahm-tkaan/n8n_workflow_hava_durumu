# Konya Günlük Hava Durumu İş Akışı

Bu n8n iş akışı, Konya şehri için günlük hava durumu bilgilerini OpenWeatherMap API'sinden alarak, bu bilgileri formatlı bir HTML e-posta olarak belirlenen alıcılara gönderir. Ayrıca iş akışının çalışma durumunu da ayrı bir e-posta olarak raporlar.

## İçindekiler

- [Genel Bakış](#genel-bakış)
- [Kurulum](#kurulum)
- [İş Akışı Mimarisi](#iş-akışı-mimarisi)
- [Düğüm (Node) Açıklamaları](#düğüm-node-açıklamaları)
- [Özelleştirme](#özelleştirme)
- [Sorun Giderme](#sorun-giderme)
- [Geliştirme](#geliştirme)

## Genel Bakış

Bu iş akışı, aşağıdaki temel işlevleri gerçekleştirir:

1. Her gün saat 12:00'de otomatik olarak çalışır
2. OpenWeatherMap API'sinden Konya şehri için güncel hava durumu verilerini alır
3. Hava durumu verilerini şık bir HTML e-posta formatına dönüştürür
4. Hava durumu bilgilerini belirtilen e-posta adresine gönderir
5. İş akışının tamamlanma durumunu ayrı bir e-posta olarak raporlar

## Kurulum

### Ön Koşullar

- n8n kurulumu (sürüm 0.214.0 veya daha yeni)
- Gmail hesabı (e-posta gönderimi için)
- OpenWeatherMap API anahtarı

### Adımlar

1. Bu iş akışını n8n'e içe aktarın
2. Gmail hesap kimlik bilgilerinizi n8n'e ekleyin
3. OpenWeatherMap API düğümünde API anahtarını kontrol edin:
   - Kendi API anahtarınızla değiştirin
4. E-posta düğümlerinde alıcı adresini kontrol edin ve güncelleyin:
   - Mevcut alıcı adresi: `dev.kaan.0@gmail.com`
5. İş akışını etkinleştirin

## İş Akışı Mimarisi

İş akışı aşağıdaki sırada çalışan düğümlerden oluşur:

1. **Cron** → 2. **OpenWeatherMap API** → 3. **Başarılı Durum Ayarla** → 
4. **Hava Durumu HTML Formatla** → 5. **Hava Durumu E-postası Gönder** → 
6. **İş Akışı Durum Raporu** → 7. **Durum Raporu E-postası Gönder**

## Düğüm (Node) Açıklamaları

### 1. Cron

- **İşlev**: İş akışını zamanlama
- **Ayarlar**: Her gün saat 12:00'de çalışacak şekilde yapılandırılmıştır

### 2. OpenWeatherMap API

- **İşlev**: Hava durumu verilerini alma
- **API Endpoint**: `https://api.openweathermap.org/data/2.5/weather`
- **Parametreler**:
  - `q=Konya` (şehir adı)
  - `API ANAHTARI` (API anahtarı)
  - `units=metric` (Celsius cinsinden sıcaklık)
  - `lang=tr` (Türkçe hava durumu açıklamaları)

### 3. Başarılı Durum Ayarla

- **İşlev**: API çağrısı başarılıysa, veri akışını işlemeye devam eder

### 4. Hava Durumu HTML Formatla

- **İşlev**: API'den alınan hava durumu verilerini şık HTML içeriğine dönüştürür
- **Özellikler**:
  - Sıcaklık, hissedilen sıcaklık, nem, rüzgar hızı ve hava durumu açıklaması dahil edilir
  - Hava durumu simgesi OpenWeatherMap'ten alınır
  - Sıcaklığa bağlı olarak otomatik öneriler sunar
  - Türkçe tarih ve saat formatlaması içerir

### 5. Hava Durumu E-postası Gönder

- **İşlev**: Hava durumu raporunu e-posta olarak gönderir
- **Alıcı**: `dev.kaan.0@gmail.com`
- **Konu**: `Konya Hava Durumu - [TARİH]`

### 6. İş Akışı Durum Raporu

- **İşlev**: İş akışının çalışma durumunu özetleyen HTML içeriği oluşturur
- **Özellikler**:
  - İş akışının başarılı/başarısız durumunu raporlar
  - Çalışma zamanını ekler
  - Bir sonraki çalışma zamanını belirtir

### 7. Durum Raporu E-postası Gönder

- **İşlev**: İş akışının durumunu e-posta olarak gönderir
- **Alıcı**: `dev.kaan.0@gmail.com`
- **Konu**: `İş Akışı Durum Raporu - [TARİH]`

## Özelleştirme

İş akışını aşağıdaki şekillerde özelleştirebilirsiniz:

### Şehir Değiştirme

OpenWeatherMap API düğümündeki URL'de `q=Konya` parametresini değiştirin:
```
https://api.openweathermap.org/data/2.5/weather?q=YENİ_ŞEHİR&appid=APİKEYdb&units=metric&lang=tr
```

### Zamanlama Değiştirme

Cron düğümünde tetikleme zamanını değiştirin.

### E-posta Alıcısını Değiştirme

Her iki e-posta düğümünde de `sendTo` parametresini güncelleyin.

### HTML Şablonları Değiştirme

JavaScript fonksiyon düğümlerindeki HTML içeriklerini değiştirerek e-posta tasarımını özelleştirebilirsiniz.

## Sorun Giderme

### Yaygın Sorunlar

1. **API Çağrısı Hatası**:
   - API anahtarının doğruluğunu kontrol edin
   - İnternet bağlantınızı kontrol edin
   - OpenWeatherMap servisinin çalışıp çalışmadığını kontrol edin

2. **E-posta Gönderim Hatası**:
   - Gmail kimlik bilgilerinizin güncel olduğunu kontrol edin
   - Gmail hesabınızda uygulama şifresi ayarlarını kontrol edin
   - Alıcı e-posta adresinin doğru olduğunu kontrol edin

## Geliştirme

Bu iş akışı, aşağıdaki şekillerde genişletilebilir:

1. **Çoklu Şehir Desteği**: Birden fazla şehir için hava durumu bilgileri eklenebilir
2. **Tahmin Desteği**: 5 günlük hava durumu tahmini eklenebilir
3. **Uyarı Sistemi**: Aşırı hava koşulları için özel uyarılar ve bildirimler eklenebilir
4. **Webhook Desteği**: Manuel tetikleme için webhook eklenebilir
5. **Veri Arşivleme**: Hava durumu verilerini zaman içinde izlemek için veri arşivleme eklenebilir

---

## Lisans

Yok.

## İletişim

Sorularınız veya önerileriniz için `dev.kaan.0@gmail.com` adresinden iletişime geçebilirsiniz.

---

*Son güncelleme: Nisan 2025*
