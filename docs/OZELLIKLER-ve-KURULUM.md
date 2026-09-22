# Pazarova — Özellikler ve Kurulum Kılavuzu

> Çok-satıcılı, emanet (escrow) korumalı oyun & e-pin pazar yeri yazılımı
> Canlı demo: **https://pazarova.epinsoft.com.tr**

---

## 1. Ürün Özeti

**Pazarova**, kullanıcıların kendi mağazalarını açıp ilan yayınladığı; alışverişin **emanet (escrow)** güvencesiyle yürüdüğü, **komisyon bazlı** çok-satıcılı bir pazar yeri yazılımıdır. Oyun içi eşya, hesap, e-pin ve dijital ürün satışı için tasarlanmıştır.

- **Kime hitap eder?** Oyun eşyası / e-pin / dijital ürün pazar yeri kurmak isteyen girişimciler, mevcut e-pin bayilerini çok-satıcılı modele taşımak isteyen işletmeler.
- **Konum:** itemavm, itemsatis gibi çok-satıcılı oyun pazar yerlerine muadil; emanet, komisyon kademeleri ve oyunlaştırma ile fark yaratır.
- **Model:** Alıcı öder → para **emanette** tutulur → satıcı teslim eder → alıcı onaylar → emanet satıcıya **serbest bırakılır**. Uyuşmazlıkta yönetici iade/serbest bırakma kararı verir.

---

## 2. Özellikler

### 2.1 Pazar Yeri Çekirdeği
| Özellik | Açıklama |
|---|---|
| Mağaza açma | Üyeler kendi mağazasını açar, mağaza sayfası + kapak/avatar + doğrulama rozetleri |
| İlan sihirbazı | 4 adımlı ilan yayınlama (Kategori → Detaylar → Doping/öne çıkarma → Sözleşme), zengin metin editörü |
| Emanet (escrow) | Ödeme emanette tutulur; teslimat onayında satıcıya serbest bırakılır. Emanet süresi ürün tipine göre ayarlanabilir |
| Uyuşmazlık yönetimi | Alıcı "Sorun Var" ile itiraz açar; yönetici panelinden alıcıya iade / satıcıya serbest bırakma |
| Komisyon kademeleri (tier) | Satıcı seviyelerine göre komisyon oranı; satış hacmine göre otomatik yükseltme; komisyon fatura anında dondurulur |
| Kupon sistemi | Platform kuponu + satıcı kuponu; kapsam bazlı (ürün/kategori/satıcı), kullanım limiti |
| Cüzdan / çift bakiye | Harcanabilir bakiye + çekilebilir kazanç bakiyesi; cüzdan hareket defteri |
| Para çekme | Satıcı çekim talebi + yönetici onayı akışı |

### 2.2 Teslimat
- **Otomatik teslimat:** Stok kodu havuzundan, ödeme sonrası anında (çift-satış önleyen atomik stok sahiplenme).
- **Manuel (C2C) teslimat:** Satıcı ödeme sonrası panelden teslim eder; alıcı "Teslim Aldım" ile onaylar. Manuel teslim süre aşımında otomatik iade.

### 2.3 Mesajlaşma / CRM
- Sipariş içi alıcı–satıcı mesajlaşma.
- Müşteri ilişkileri araçları (satıcı panelinde sipariş/müşteri yönetimi).

### 2.4 Entegrasyon (Satıcı API + Webhook)
- **Satıcı API'si:** Token korumalı REST; satıcı kendi ürün / stok / sipariş / bakiye verisini yönetir. Yetki izolasyonu (satıcı yalnız kendi verisine erişir).
- **Outbound webhook:** `order.paid / delivered / confirmed / disputed / refunded` olayları için **HMAC-SHA256 imzalı** POST bildirimi; kuyruk + tekrar deneme (retry) + SSRF koruması.

### 2.5 Oyunlaştırma
- **Şans Çarkı:** Sunucu taraflı ağırlıklı çekiliş; günlük hak sınırı; puan/kupon ödülleri (gerçek kredi).
- **Gece Pazarı:** Zaman kısıtlı, kilitli "tezgah" mekaniği; gün boyu sabit dizilim, her gece 00:00'da yenilenir.

### 2.6 Yönetici Paneli
- Mağaza vitrin yönetimi (doğrulama, kimlik onayı, mağaza kademesi, kapak yükleme).
- Satıcı seviyesi (tier) yönetimi ve komisyon oranları.
- Oyunlaştırma yönetimi (ödül CRUD, çark/gece pazarı ayarları).
- Uyuşmazlık paneli.
- Ürün / kategori / kupon / kampanya / bayilik / referans yönetimi.
- Raporlar (ciro, kâr/marj).

### 2.7 Diğer
- SEO uyumlu yapı + blog motoru.
- Mobil uyumlu (responsive) arayüz.
- Cloudflare uyumlu.

---

## 3. Teknik Mimari

| Bileşen | Teknoloji |
|---|---|
| Dil / Çerçeve | PHP — CodeIgniter 3 (HMVC) |
| Veritabanı | MySQL / MariaDB |
| Web sunucusu | Apache veya LiteSpeed (`.htaccess` ile) |
| Zamanlanmış görevler | Cron (emanet serbest bırakma, otomatik iade, webhook teslimi) |
| CDN / Proxy | Cloudflare uyumlu |
| Önerilen PHP | PHP 7.4+ (8.x uyumlu) |

---

## 4. Kurulum ve Yapılandırma

### 4.1 Gereksinimler
- PHP 7.4 veya üzeri (önerilen), gerekli eklentiler: `mysqli/pdo_mysql`, `curl`, `mbstring`, `openssl`, `gd`.
- MySQL 5.7+ / MariaDB 10.3+.
- Apache (`mod_rewrite`) veya LiteSpeed; `.htaccess` desteği açık.
- Cron erişimi (veya cron-job.org gibi harici HTTP-cron servisi).

### 4.2 Dosyaların Yüklenmesi
```bash
# Kaynak kodu web köküne yükleyin (FTP/SFTP veya panel dosya yöneticisi)
# Örn. public_html/ ya da alan adı kök dizini
```
`.htaccess` dosyalarının (kök + `assets/`) yüklendiğinden emin olun — yeniden yazma (rewrite) ve güvenlik kuralları buna bağlıdır.

### 4.3 Veritabanı İçe Aktarma
```bash
# phpMyAdmin veya CLI ile şemayı içe aktarın
mysql -u KULLANICI -p VERITABANI < pazarova_schema.sql
```

### 4.4 Yapılandırma
`application/config/` altında:
- **Veritabanı** (`database.php`): host, kullanıcı, parola, veritabanı adı.
- **Base URL** (`config.php`): `$config['base_url']` = alan adınız.
- **Şifreleme anahtarı** (`config.php`): `$config['encryption_key']` — güçlü, benzersiz bir değer atayın (stok kodları şifreli saklanır).

> Kurulum sırasında gerçek bağlantı bilgileri (DB parolası, admin girişi, anahtarlar) size ayrıca iletilir; bu dökümanda yer almaz.

### 4.5 Cron Kurulumu
Emanet serbest bırakma + otomatik iade + tier yükseltme ve webhook teslimi için iki görev gerekir. Örnek (anahtarlar kurulumda verilir):
```cron
# Emanet serbest / otomatik iade / tier — her 10 dakikada
*/10 * * * * curl -s "https://ALANADINIZ/cron/pay_sellers?key=CRON_ANAHTARI" >/dev/null 2>&1

# Webhook teslimi — her 5 dakikada
*/5 * * * * curl -s "https://ALANADINIZ/cron/run_webhooks?key=CRON_ANAHTARI" >/dev/null 2>&1
```
Cron erişimi yoksa cron-job.org gibi harici HTTP-cron servisi de kullanılabilir.

### 4.6 Ödeme Geçitleri (ÖNEMLİ — Satış Avantajı)
> **Ödeme geçitleri PLACEHOLDER olarak gelir.** Yazılım, ödeme altyapısı size bağlı olacak şekilde tasarlanmıştır.

- Alıcı, **kendi POS / ödeme sağlayıcısını** (iyzico, PayTR, Stripe, Shopier vb.) yönetici panelinden bağlar.
- Kendi credential'larınızı (API anahtarı/merchant bilgisi) girene kadar sistem **demo modunda** çalışır.
- Bu sayede yazılım herhangi bir ödeme sağlayıcısına kilitli değildir; kendi anlaşmanızı kullanırsınız.

### 4.7 İlk Kullanım
1. **Yönetici girişi:** Kurulumda verilen yönetici adresi ve bilgileriyle giriş yapın.
2. **Kategori ekleme:** Yönetici → Kategoriler'den ilk kategorilerinizi oluşturun.
3. **İlk mağaza / ilan:** Bir üye hesabıyla mağaza açıp ilan sihirbazından ilk ilanı yayınlayın.
4. **Ödeme:** Kendi ödeme sağlayıcı bilgilerinizi girin (bkz. 4.6).
5. **Cron:** Emanet ve webhook görevlerini kurun (bkz. 4.5).

---

## 5. Teslim Paketi

- Tam **kaynak kod**.
- **Veritabanı şeması** (SQL).
- **Kurulum desteği** (yapılandırma + ilk ayağa kaldırma).

---

## 6. İletişim

| | |
|---|---|
| Web | [epinsoft.com.tr](https://epinsoft.com.tr) |
| E-posta | pazarlama@epinsoft.com.tr |
| Telefon | +90 850 255 18 01 |

> Canlı demoyu inceleyin: **https://pazarova.epinsoft.com.tr** — satın alma ve özel istekler için bize ulaşın.
