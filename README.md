# Değerin Korunumu web sitesi

Derleme adımı olmayan statik site. Cloudflare Pages bu klasörü olduğu gibi yayınlar.

```
index.html        Ana sayfa
stil.css          Bütün sayfaların ortak stili (renkler, başlık, kartlar, koyu tema)
araclar/dca.html  Düzenli alım (DCA) hesaplayıcı → /araclar/dca
veri/dca.json     Hesaplayıcının verisi: VT, VOO, QQQ, SMH, altın, Bitcoin; son 180 ayın aylık fiyatları
favicon.svg       Δ = 0 işareti
og.png            Bağlantı paylaşılınca görünen önizleme (1200×630, kanal/cikti/kapak_2560.png'den kırpıldı)
```

## Depo düzeni

Site, YouTube çalışma deposunda `site/` klasöründe yazılır. Yalnızca bu klasör
`github.com/degerinkorunumu/degerinkorunumu.github.io` deposuna gider (`site` uzak deposu, git subtree);
orada dosyalar kök dizindedir. Çalışma deposunun geri kalanı GitHub'a gitmez.

Değişiklikten sonra (çalışma deposunun kökünde):

```bash
git add site && git commit -m "Site: ..."
git subtree push --prefix=site site main
```

## GitHub Pages

Adres: https://degerinkorunumu.github.io/ (kullanıcı sitesi: `main` dalının kökü yayınlanır, derleme yok).
`.nojekyll` dosyası Jekyll işlemesini kapatır; dosyalar olduğu gibi sunulur. `main`'e her push birkaç dakikada
yayına çıkar. Uzantısız adresler (`/araclar/dca`) `.html` dosyasına gider.

2026-11-22'ye kadar site Cloudflare Pages'teydi (degerinkorunumu.pages.dev, eski depo
MehmetTheConqueror/degerinkorunumu); bazı operatörlerde sorun çıkabileceği için GitHub Pages'e taşındı.

Adres değişirse (kendi alan adı bağlanırsa) `index.html` ve `araclar/dca.html`'deki `canonical`, `og:url` ve
`og:image` değerlerini yeni adrese çevir; `og:image` tam adres olmalı, göreli yolu çoğu uygulama okumaz.

## Verinin güncellenmesi

`veri/dca.json`, çalışma deposundaki `araclar/site_dca_veri.py` ile üretilir (Yahoo Finance, anahtar gerekmez):

```bash
.venv/bin/python araclar/site_dca_veri.py      # site/veri/dca.json (bu ay dahil son 180 ay)
```

Her ayın ilk işlem günü fiyatı; ETF'lerde temettü düzeltilmiş kapanış. Ayda bir çalıştırıp commit + subtree push
yeterli; 15 yıllık pencere kendiliğinden kayar. `veri/aylik/` dosyalarına dokunmaz.

Hesaplayıcı adresi parametre alır, hesap paylaşılabilir (sayfa adresi seçimlere göre kendisi günceller):
- Toplam tutar ve dağılım: `/araclar/dca?mod=dagilim&toplam=500&dagilim=vt:40,altin:30,bitcoin:30&bas=2016-12&bit=2026-11`
- Her varlığa ayrı tutar: `/araclar/dca?mod=ayri&tutar=voo:200,altin:100,bitcoin:50`
- Grafik: `&gorunum=varliklar` (varsayılan sepet), `&olcek=log` (yalnız varlıklar görünümünde)

Yığılmış grafikteki sıra (`YIGIN`, dca.html) renk doğrulamasından geçmiş bir sıradır; yeni varlık eklenirse
renk ve sıra yeniden doğrulanmalı (dataviz validate_palette, açık #ffffff ve koyu #172030 zeminde).

## Yerel önizleme

Sayfalar `/stil.css`, `/veri/dca.json` gibi kökten yollar kullanır; dosyayı çift tıklayarak değil sunucuyla açın:
`python3 -m http.server 8765 --directory site` → http://127.0.0.1:8765/

## Yeni sayfa

Araç, veri ya da yazı sayfaları bu klasöre eklenir: `site/araclar/dca.html` → `/araclar/dca`.
