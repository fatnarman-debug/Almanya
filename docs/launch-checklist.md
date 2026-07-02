# Yayın Öncesi Kontrol Listesi & İş Dağılımı — Aachener Zaun Center

Kaynak: 3 paralel uzman denetimi (Hukuk/DSGVO · Tasarım/Erişilebilirlik · Teknik/SEO), 02.07.2026.
Genel karar: **HENÜZ YAYINA HAZIR DEĞİL.** 6 statik HTML sayfa + robots/sitemap/favicon.

---

## P0 — YAYIN ENGELLERİ (bunlar bitmeden ONLINE OLMA)

### Hukuk / DSGVO (en kritik — Almanya'da Abmahnung riski)
- [ ] **`impressum.html` oluştur** (§5 DDG). Zorunlu: firma/rechtsform + sahip adı, adres (Wolfstr. 2, 52134 Herzogenrath), telefon+e-posta, USt-IdNr. (yoksa bölümü sil), HWK Aachen + Handwerksrolle no., meslek unvanı (örn. Metallbauermeister), VSBG §36 notu. **ODR/OS-linki EKLEME** (AB platformu 20.07.2025'te kapandı — ölü linke atmak ayrıca abmahnbar).
- [ ] **`datenschutz.html` oluştur** (Art. 13 DSGVO). Bölümler: Verantwortlicher, Betroffenenrechte, Beschwerderecht (LDI NRW), Server-Logs/Hosting, Kontaktformular, Google Maps, (fontlar self-host olunca o bölüm çıkar).
- [ ] **Footer + form linklerini düzelt** (6 dosyada): `aachenerzauncenter.de/impressum/` → `impressum.html`, `…/datenschutz/` → `datenschutz.html`. Şu an bunlar **ölü link**. (`kontakt.html:322` dahil)
- [ ] **Google Fonts self-host** — `fonts.googleapis.com`/`gstatic` linklerini 6 dosyadan kaldır, woff2'leri `/fonts`'a koy + `@font-face`. Material Symbols'ı da lokal host et veya inline SVG yap. *(LG München I, 3 O 17493/20 — abmahn dalgası)*
- [ ] **Google Maps → 2-Klick çözümü** (`kontakt.html:332`) veya iframe'i tamamen kaldırıp "Route planen" linkini bırak.

### Teknik / İçerik
- [ ] **Geçici görselleri değiştir** — `lh3.googleusercontent.com/aida/...` URL'leri kısa ömürlü, canlıda 404 olur. Sayısı: index 9 (hero bg dahil), tore 4, zaeune 4, sichtschutz 2. Hepsini lokal optimize (WebP/AVIF) görselle değiştir. Doğrula: `grep -rn googleusercontent *.html` boş dönmeli.
- [ ] **JSON-LD logo/image yolu 404** — `…/wp-content/uploads/2025/06/azc.webp` (index.html:174,196; kontakt.html:87). Bu statik sitede `wp-content/` yok. Gerçek logo yükle + yolu güncelle.
- [ ] **Canonical / sitemap / iç link uyumsuzluğu** — dosyalar `*.html` ama canonical+sitemap+schema temiz URL (`/zaeune/`, `/ueber-uns/`) gösteriyor. Karar ver: (a) sunucu rewrite `.html`→temiz URL, **veya** (b) tüm canonical/sitemap/schema'yı `*.html`'e çevir. Not: `ueber-uns/` (canonical) ≠ `ueber.html` (dosya).
- [ ] **sitemap.xml'e Über-uns eksik** — `/ueber-uns/` yok, ekle (URL şeması kararından sonra).
- [ ] **Tailwind Play CDN → build** — `cdn.tailwindcss.com` 6 dosyada; prodüksiyon için değil (FOUC, yavaş). DEPLOY.md §1'e göre statik minified CSS build et, CDN + inline config'i çıkar, `<link>` ekle.
- [ ] **İletişim formu çalışmıyor** — `kontakt.html:310` `action="#" onsubmit="return false;"` → hiçbir lead gitmiyor. Ya bir servise bağla (Web3Forms/Formspree/Netlify), ya kaldır. (P1 onay kutusu ile birlikte)

---

## P1 — ÖNEMLİ (yayınla birlikte / hemen sonrası)

- [ ] **Einwilligung-Checkbox** ekle (`kontakt.html`, önceden işaretsiz, `required`, datenschutz.html linki); yanıltıcı "…stimmen Sie zu" cümlesini kaldır.
- [ ] **Form endpoint bağlanınca** datenschutz.html'e ilgili servis bölümü + AV-Vertrag ekle.
- [ ] **og:image / twitter:image YOK** (6 sayfada) — sosyal paylaşımda görsel çıkmaz. 1200×630 mutlak URL ekle.
- [ ] **Başlık hiyerarşisi atlaması** — index "Vorteile" `h2`→`h4` (h3 atlanmış); footer başlıkları da `h4`. `h3`'e çevir (WCAG 1.3.1).
- [ ] **Görsellere width/height yok** → CLS. Tüm `<img>`'lere intrinsic boyut/aspect-ratio ekle.
- [ ] **Lokal JPEG'ler ağır** — zaun-projekt-1 (540KB, zaeune LCP), zaun-projekt-2 (869KB). WebP/AVIF, <150KB, LCP hero'ya `fetchpriority="high"` + lazy YOK.
- [ ] **Font yükleme** — `preconnect` yok; Material Symbols FOUT (ligatür metni yanıp sönüyor). Self-host + subset (self-host P0-4 ile örtüşür).
- [ ] **"5.0 / 252+ Bewertungen"** görünür metinde (index, ueber, tore, sichtschutz). JSON-LD'de AggregateRating YOK (iyi), ama UWG açısından gerçek/kanıtlanabilir olmalı — Google profiline linkle veya yumuşat.

---

## P2 — İYİLEŞTİRME / TUTARLILIK

- [ ] Nav ARIA yanlış: `role="menu"/menuitem"/aria-haspopup` kaldır, düz `<ul><li><a>` yap (veya gerçek disclosure `<button aria-expanded>`).
- [ ] Formda `aria-live="polite"` status alanı ekle (submit geri bildirimi + ilk hatalı alana odak).
- [ ] Yıldız (rating) kontrastı ~2.4:1 — rengi koyulaştır veya yanına "5,0" yaz.
- [ ] Breadcrumb `text-white/50` → `/70` (kontrast marjı).
- [ ] **Footer tutarsız** — index'te 3 sütun/rozet yok; diğer 5 sayfada 4 sütun+sosyal+rozet. Hepsini standartlaştır.
- [ ] Alt-sayfa schema `url` alanı ana sayfaya işaret ediyor → her sayfanın kendi canonical'ına.
- [ ] Alt-sayfa GeneralContractor bloklarına `image` ekle.
- [ ] Favicon: SVG dışında PNG/ICO fallback (eski istemciler).
- [ ] AGB (Werkvertrag/ödeme/garanti) — zorunlu değil, önerilir.
- [ ] Berufshaftpflicht'i Impressum'da gönüllü belirt (güven).
- [ ] Görsel lisanslarını belgele (DE'de resim abmahn'ı sık).
- [ ] Meta desc index ~175 karakter (biraz uzun, ~160'a çek).

---

## Karara bağlı sorular (sahibiyle netleştir)
1. **Rechtsform / sahip adı** (Einzelunternehmen / GbR / GmbH?) — Impressum'un temeli.
2. **USt-IdNr. var mı?** (Kleinunternehmer §19 ise bölüm silinir.)
3. **HWK Aachen Handwerksrolle no.** + meslek unvanı.
4. Öğle/açılış saatleri (Mo–Fr 08–18, Sa 09–14) gerçek mi?
5. "10+ yıl", "Meisterbetrieb", "5.0/252+" kanıtlanabilir mi?
6. Hoster adı + log saklama süresi (Datenschutz için).

---

## Değişmesi gereken dosya özeti
- **Yeni:** `impressum.html`, `datenschutz.html`, `/fonts/*` (self-host), gerçek görseller `/images/`, `assets/tailwind.css` (build)
- **6 HTML (hepsi):** footer/legal linkleri, Google Fonts linkleri kaldır, Tailwind CDN→build, og:image, canonical/URL şeması
- **kontakt.html:** form action/endpoint, consent checkbox, Maps 2-Klick, datenschutz linki
- **index.html:** başlık hiyerarşisi, footer standardı, wp-content logo yolu, hero görsel
- **sitemap.xml:** ueber-uns ekle + URL şeması
