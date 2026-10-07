# ⚡ Faster Website in 10 Checks: a ready-to-use AI prompt

A copy-paste prompt you can give to **any AI coding assistant that can open your project**
(Claude Code, Cursor, Copilot, etc.) so it audits and fixes the 10 most common reasons a
website loads slowly: images, fonts, JavaScript, compression, CDN and caching.

It works the way a performance engineer would: **measure first, fix one item at a time,
measure again**. It never invents numbers, never deletes anything without asking, and tells
you step by step what to do for the things it cannot reach (hosting, server settings).

## How to use

Open your project in your AI assistant (it needs access to the files; a plain chat window
cannot change your site), then copy everything in the code block below and paste it in.

🇹🇷 Türkçe sürüm aşağıda: [Türkçe](#-türkçe-sürüm)

---

```markdown
# TASK: Audit and fix my website's loading speed in 10 checks

You are an experienced web performance engineer. Check the 10 items below in this project one
by one and fix what is wrong. The goal is a measurable improvement: measure, fix, measure again.

## 0. Discovery
Before touching any code, inspect the project and briefly tell me:
- Framework and version (Next.js, Nuxt, Astro, Vite + React, WordPress, plain HTML...)
- Build tool and build command
- Where the site is hosted (Vercel, Netlify, Cloudflare, own server...). Infer it from config
  files; if you cannot, ask me.
- How to run the project locally in production mode

## 1. Baseline measurement
Make a production build and measure it. If you can run Lighthouse:
    npx lighthouse <url> --only-categories=performance --output=json --output-path=./lighthouse-before.json
Record: LCP, CLS, TBT, total transfer size, JavaScript downloaded on first load.
If you cannot measure, ask me for the PageSpeed Insights (pagespeed.web.dev) result.
Never write a number you did not measure. Do not estimate.

## 2. The 10 checks
For each item, in order: check it, fix it if needed, run the build, tell me what you changed.

1. Image format: serve JPEG and PNG images as WebP or AVIF. If the framework has an image
   component (e.g. Next.js `next/image`, Astro `<Image>`), use it; otherwise convert the files.
   Keep the originals.
2. Image dimensions: every `<img>` and image component has width and height (or a CSS
   aspect-ratio), so content does not shift while the page loads (CLS).
3. Lazy loading: add `loading="lazy"` to images that are not visible on the first screen.
   Do NOT lazy load the main image on the first screen (usually the LCP element); give it
   `fetchpriority="high"` instead.
4. Fonts: preload the critical font and use `font-display: swap`. Self-host fonts where possible
   (Next.js: `next/font`). Remove font weights that are not used.
5. Code splitting: code is split per page (route). Heavy components that are not needed on the
   first screen (maps, charts, editors, modals) are loaded later with dynamic import.
6. Unused code: find unused dependencies (e.g. `npx knip`) and dead code, and list the largest
   packages. Show me the list before deleting anything; do not delete until I approve.
7. Compression: on the live site, check the `Content-Encoding` header of static files
   (`curl -sI -H "Accept-Encoding: br, gzip" <file url>`). If neither Brotli nor gzip is on,
   explain how to enable it; if the config lives in this repo, fix it.
8. CDN: check the response headers to see whether images, CSS and JS are served from a CDN.
   If the host already provides one (Vercel, Netlify, Cloudflare), write "already done".
9. Caching: give files with a hash in their name (e.g. `app.3f9a2c.js`) the header
   `Cache-Control: public, max-age=31536000, immutable`. Do NOT give long caching to HTML or to
   files without a hash, otherwise visitors keep seeing old files after an update.
10. Measure again: repeat the baseline measurement under the same conditions. Targets (Google
    Core Web Vitals): LCP under 2.5 s, INP under 200 ms, CLS under 0.1. Lighthouse cannot
    measure INP in the lab (TBT is its proxy), so explain how I check it on the live site with
    PageSpeed Insights.

## Rules
- Do not change how the site looks or behaves.
- Do not add new packages or delete anything without asking me.
- If something needs hosting or server access you do not have, do not try to change it; write
  the steps I need to take.
- If an item is already fine, write "already done" and change nothing.
- Run the build after every item. If it breaks, fix or revert that item before moving on.
- Only report numbers that come from a measurement.

## Final report
1. Table: item | state before | what was done | what I need to do
2. Before vs after: LCP, CLS, TBT, JavaScript size, total size
3. List of changed files
```

---

## 🇹🇷 Türkçe sürüm

Projenizi açtığınız yapay zekaya verin (dosyalara erişmesi gerekir; sohbet penceresine
yapıştırırsanız sitenizde değişiklik yapamaz). Aşağıdaki bloğun tamamını kopyalayın.

```markdown
# GÖREV: Sitemin açılış hızını 10 maddede kontrol et ve düzelt

Sen deneyimli bir web performans mühendisisin. Bu projede aşağıdaki 10 maddeyi tek tek kontrol
et ve sorunlu olanları düzelt. Amaç ölçülebilir iyileşme: önce ölç, sonra düzelt, sonra yeniden ölç.

## 0. Keşif
Koda dokunmadan önce projeyi incele ve bana kısaca şunları söyle:
- Framework ve sürümü (Next.js, Nuxt, Astro, Vite + React, WordPress, düz HTML...)
- Build aracı ve build komutu
- Site nerede barındırılıyor (Vercel, Netlify, Cloudflare, kendi sunucu...). Bunu config
  dosyalarından çıkar; çıkaramıyorsan bana sor.
- Projeyi yerelde production modunda nasıl çalıştırırım

## 1. Başlangıç ölçümü
Production build al ve ölç. Lighthouse çalıştırabiliyorsan:
    npx lighthouse <adres> --only-categories=performance --output=json --output-path=./lighthouse-once.json
Şunları kaydet: LCP, CLS, TBT, toplam indirilen boyut, ilk açılışta inen JavaScript boyutu.
Ölçemiyorsan bana PageSpeed Insights (pagespeed.web.dev) sonucunu sor.
Ölçmediğin hiçbir sayıyı yazma, tahmin etme.

## 2. On madde
Her maddede sırayla: kontrol et, sorun varsa düzelt, build al, neyi değiştirdiğini yaz.

1. Görsel formatı: JPEG ve PNG görselleri WebP veya AVIF olarak sun. Framework'ün görsel
   bileşeni varsa (ör. Next.js `next/image`, Astro `<Image>`) onu kullan; yoksa dosyaları
   dönüştür. Orijinalleri silme.
2. Görsel boyutu: her `<img>` ve görsel bileşeninde width ve height (ya da CSS aspect-ratio)
   olsun, sayfa yüklenirken içerik kaymasın (CLS).
3. Lazy loading: ilk ekranda görünmeyen görsellere `loading="lazy"` ver. İlk ekrandaki ana
   görsele (genelde LCP öğesi) lazy loading VERME; ona `fetchpriority="high"` ekle.
4. Fontlar: kritik fontu preload et, `font-display: swap` kullan. Mümkünse fontu kendi
   sunucundan sun (Next.js'te `next/font`). Kullanılmayan font ağırlıklarını çıkar.
5. Code splitting: kod sayfa (route) bazında bölünsün. İlk ekranda gerekmeyen ağır bileşenler
   (harita, grafik, editör, modal) dinamik import ile sonradan yüklensin.
6. Kullanılmayan kod: kullanılmayan paketleri (ör. `npx knip`) ve ölü kodu bul, en büyük
   paketleri listele. Silmeden önce listeyi bana göster, ben onay vermeden silme.
7. Sıkıştırma: canlı sitede statik dosyaların `Content-Encoding` başlığına bak
   (`curl -sI -H "Accept-Encoding: br, gzip" <dosya adresi>`). Brotli ya da gzip yoksa nasıl
   açılacağını yaz; ayar bu repodaysa düzelt.
8. CDN: görseller, CSS ve JS bir CDN üzerinden mi sunuluyor, yanıt başlıklarından kontrol et.
   Barındırma zaten CDN veriyorsa (Vercel, Netlify, Cloudflare) "zaten tamam" yaz.
9. Önbellek: adında hash olan dosyalara (ör. `app.3f9a2c.js`)
   `Cache-Control: public, max-age=31536000, immutable` ver. HTML'e ve hash'siz dosyalara uzun
   önbellek VERME; yoksa güncellemeden sonra ziyaretçi eski dosyayı görmeye devam eder.
10. Yeniden ölç: başlangıç ölçümünü aynı koşullarda tekrarla. Hedefler (Google Core Web
    Vitals): LCP 2,5 saniyenin, INP 200 ms'nin, CLS 0,1'in altında. Lighthouse INP'yi
    laboratuvarda ölçemez (yerine TBT'ye bakılır); canlı sitede PageSpeed Insights ile nasıl
    kontrol edeceğimi yaz.

## Kurallar
- Sitenin görünüşünü ve davranışını değiştirme.
- Bana sormadan yeni paket ekleme, hiçbir şey silme.
- Hosting ya da sunucu ayarı gibi erişemediğin bir şey varsa değiştirmeye çalışma; benim
  yapmam gereken adımları yaz.
- Bir maddede sorun yoksa "zaten tamam" yaz, hiçbir şey değiştirme.
- Her maddeden sonra build al. Build kırılırsa o maddeyi düzelt ya da geri al, kırık bırakıp
  sonrakine geçme.
- Yalnız ölçümden gelen sayıları yaz.

## Son rapor
1. Tablo: madde | önceki durum | ne yapıldı | benim yapmam gereken
2. Önce ve sonra: LCP, CLS, TBT, JavaScript boyutu, toplam boyut
3. Değişen dosyaların listesi
```

---

⭐ the repo if it saved you time.
