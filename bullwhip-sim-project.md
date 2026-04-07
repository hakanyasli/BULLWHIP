# Bullwhip Effect Simulator — Project Master File

> Hakan + Claude ortak çalışması | Son güncelleme: Nisan 2026  
> **Bu dosyayı her Cline oturumuna context olarak ver.**  
> Kalın yazılar = Hakan'ın kararları. Değiştirme.

---

## 🎯 Ürün Özeti

Bir direksiyon gecikme simülatörü. Kullanıcı arabayı sürer, delay slider'ı gecikmeyi ayarlar. Yüksek gecikme = momentum ve inertia ile tekerlek input'u takip etmez = bullwhip etkisi fiziksel olarak hissedilir.

**İki kimlik (aynı motor, farklı sarmalama):**
- 🎓 **Eğitim modu:** "Bullwhip Effect Simulator" | alt başlık: "Delay & Control" → Yeditepe Üniversitesi, lojistik dersleri. **MVP budur. Şu an buradayız.**
- 🍺 **Eğlence modu:** "Drunk Driver Simulator" | alt başlık: "How Impaired Are You?" → POST-MVP. Henüz kodlama yapma.

**İkinci ürün (ayrı proje):** Tepside yuvarlanan bilye, delay efektli. Ayrı sohbet, ayrı dosyalar.

---

## ✅ Tamamlananlar (v0.4 → v0.5)

### Fizik & Oyun Motoru
- Momentum bazlı fizik: `viscousSteer + steerVelocity`
- Seeded road: `ROAD_SEED=42` — her run aynı yol, adil karşılaştırma
- Step-based ◀ ▶ butonlar (spring-back yok, kalıcı pozisyon)
- Oyun başlayınca tüm sliderlar kilitlenir
- Araç canvas'ın altında (`CAR_Y_FRAC=0.88`), yol yukarı akar

### Görsel
- Asphalt grey yol yüzeyi, yeşil pasture (yanlış: eski versiyonda her ikisi de karanlıktı)
- Ghost forecast line kaldırıldı. Yerine: **car path trace** — yeşil=yolda, kırmızı=dışarıda
- Skid marks kırmızı, 200 item limit
- Araç flash'ı yoldan çıkınca kırmızı
- Canvas ortasında tıklanabilir ▶ play butonu (DOM element değil, canvas üzerine çizili)
- Orange fog overlay = bilinmeyen gelecek talebi, headlight slider ufku ayarlar

### UI & i18n
- Dark mode default, ☀/☾ toggle sol üstte
- EN/TR toggle: hem ana sayfada hem welcome popup içinde
- **Cihaz dili auto-detect:** `navigator.language` → Türkçe sistem = Türkçe açılır
- Footer: [Prof. Dr. Erkut AKKARTAL](https://scholar.google.com/citations?user=O5LOOKYAAAAJ&hl=tr) | [Yeditepe Üniversitesi](https://yeditepe.edu.tr/tr) | 2026
- Session history tablosu (delay, speed, duration, headlight, on-road%, violations)
- Haptic feedback on violation (mobile)
- Ses: ihlalde beep, yol dışındayken sawtooth warning

### Mimari (v0.4'te temelden yenilendi)
- **Tek `G` objesi** = tüm game state. `draw()` sadece okur, asla yazmaz.
- `resizeCanvas()` → sadece CH ve canvas.height
- `updateFogOverlay()` → sadece CSS height
- `audio` objesi → izole
- Tüm JS bir IIFE içinde
- Off-road detection: loop'ta bir kez hesaplanır, `G.isOff`'a yazılır

---

## 🐛 Açık Buglar (v0.5 sonrası)

| # | Bug | Öncelik |
|---|-----|---------|
| 1 | `CX is not defined` — drawStatic'te yazım hatası, strict mode'da patlıyor | KRİTİK |
| 2 | Welcome popup ilk yüklemede çıkmıyor | Yüksek |
| 3 | Canvas play button iOS'te touch'a cevap vermiyor | Yüksek |
| 4 | Canvas boyutu popup kapanınca yeterince büyümüyor | Orta |
| 5 | Font boyutları hâlâ küçük görünüyor (değişiklik yansımamış olabilir) | Orta |
| 6 | Canvas play button altındaki "ayarları değiştir" yönlendirmesi eksik | Düşük |

---

## 📋 Sıradaki Görevler (Öncelik Sırası)

### Acil (Bug Fix)
1. `CX` değişken hatası → `var CX` veya `let CX` ekle
2. Welcome popup güvenilir şekilde ilk yüklemede açılsın
3. Canvas play button → iOS touch fix (`touchend` + `preventDefault`)
4. Canvas yüksekliği popup sonrası gerçek ekranı doldursun

### Kısa Vade (MVP tamamlama)
5. Play button altına "Ayarlar için aşağı kaydır" ipucu ekle
6. Canvas'a tıklayınca oyunu duraklat / devam ettir (pause/resume)
7. Font boyutları: welcome popup min 14px, genel min 12px
8. localStorage: dil tercihi, tema, "popup tekrar gösterme" kaydet

### Orta Vade
9. Difficulty progression: 30s sonra yol daha dar ve hızlı sallanır
10. Slider başına (i) info butonu — scenario'ya göre içerik
11. Ses: motor uğultusu, sert ihlalde lastik freni

---

## 🗺️ Büyük Özellikler (Post-MVP Backlog)

### Senaryo Sistemi
| Senaryo | Slider birimi | Gecikme = |
|---------|--------------|-----------|
| 📦 Logistics / Bullwhip | Hafta | Bilgi gecikmesi |
| 🍺 Drunk Driver | İçki sayısı | Kan alkol seviyesi |
| 😴 Sleep Deprivation | Uykusuz saat | Bilişsel yavaşlama |
| 💊 Medication | Doz | İlaç reaksiyon etkisi |
| 🎂 Aging Driver | Yaş (50→90) | Nörolojik yavaşlama |
| 📡 Drone / Remote | km | Sinyal gecikmesi |
| 🚀 Mars Rover | Milyon km | Işık hızı gecikmesi |

Senaryo seçince: slider birimi değişir, emoji animasyonu degrader, (i) butonları ilgili klinik bilgi gösterir.

### Gyroscope Steering
Telefonu eğerek direksiyon. iOS permission gerekir. Pedagojik etki güçlü — gecikmeyi ellerde hissediyorsun.

### Global Leaderboard
```
FinalScore = onRoad% × (1 + delay/15) × speedMultiplier
```
Koşullar: delay ≥ 5, süre ≥ 30s. Firebase veya Supabase free tier yeterli.
Viral paylaşım: "🍺🍺🍺 ile %73 yolda kaldım — sen yapabilir misin?"

### Diğer Backlog
- Path oscillation grafiği: centerline'dan sapma delta'sı göster (iyi fikir, MVP değil)
- İki oyunculu mod: biri direksiyona, biri delay'e
- Endless mod: yol bitmez, zorluk artar
- Sınıf modu: öğretmen dashboard'u, canlı skor görüntüleme

---

## 📲 Dağıtım Planı

| Faz | Durum | Notlar |
|-----|-------|--------|
| **1 — Stable HTML** | 🔄 Devam ediyor | Bug-free dosya → GitHub Pages → Yeditepe öğrencileri test eder |
| **2 — PWA** | Bekliyor | manifest.json + service worker + ikon tasarımı |
| **3 — App Store** | Bekliyor | Capacitor wrapper, iOS $99/yıl, Android $25 tek seferlik |
| **4 — Monetizasyon** | Bekliyor | Eğitim: kurumsal lisans $999/yıl | Tüketici: $3.99 veya freemium |

---

## 💰 Monetizasyon

- **B2B:** Üniversiteler, trafik güvenliği vakıfları → $500–2000/yıl kurumsal lisans
- **B2C:** $3.99 tek seferlik veya freemium (delay 0–5 ücretsiz, tam açılım ücretli)
- **"Buy Me a Coffee":** Hayır, yeterince ciddi değil

---

## 🏗️ Teknoloji Stack

| Katman | Teknoloji | Durum |
|--------|-----------|-------|
| Oyun | Canvas 2D API | ✅ Aktif |
| Ses | Web Audio API | ✅ Aktif |
| Fizik | Vanilla JS (IIFE) | ✅ Aktif |
| Gyroscope | DeviceOrientation API | ⏳ Eklenmedi |
| Leaderboard | Firebase / Supabase | ⏳ Eklenmedi |
| PWA | manifest + service worker | ⏳ Eklenmedi |
| Native | Capacitor | ⏳ İleride |

---

## 🗣️ Çalışma Tercihleri (Cline'a not)

- Direkt ve özlü iletişim. Laf kalabalığı yok.
- Değişiklik önerisinde önce özetle, onay al, sonra yap.
- Türkçe/İngilizce karışık — nasıl yazarsa öyle cevap ver.
- Yama değil sağlam temel. Tech debt biriktirme.
- `G` objesi tek state kaynağı. `draw()` asla state'e yazmaz.
- Versiyon geçmişi: v0.1 temel mekanik → v0.2 momentum fizik → v0.3 IIFE fix → v0.4 G objesi / temiz mimari → v0.5 play button / responsive / i18n fix

---

## 📌 Versiyon Geçmişi

| Versiyon | Yapılanlar |
|----------|-----------|
| v0.1 | Temel mekanik, delay queue |
| v0.2 | Momentum fizik, ghost line, skid marks, popup |
| v0.3 | IIFE fix, dark mode, canvas sizing |
| v0.4 | **Mimari yenileme:** G objesi, tek state kaynağı, off-road detection fix, asphalt/yeşil yüzey, path trace, footer linkler |
| v0.5 | **Eklenenler:** Canvas ortasına tıklanabilir ▶ play butonu (canvas üzerine çizili, DOM yok, hit-test `PLAY_BTN` objesi ile); welcome popup'a EN/TR toggle eklendi; `navigator.language` ile cihaz dili auto-detect; explanation grid `@media(max-width:480px)` altında 1 kolona düşüyor; footer'da isim tekrarı kaldırıldı, "Prof. Dr. Erkut AKKARTAL" ismi direkt Scholar'a link oldu; font boyutları artırıldı (overlay min 13px, wstep min 13px). **Açılan buglar:** `CX is not defined` — `drawStatic()` içinde `CX=CW/2` yazım hatası (`var` eksik), strict mode'da console hatası veriyor; welcome popup ilk yüklemede çıkmıyor; canvas play button iOS Safari'de touch'a cevap vermiyor (`touchend` handler yetersiz); canvas yüksekliği popup kapanınca hâlâ küçük kalıyor. |
| **v0.6** | ← Hedef: CX bug fix + popup fix + iOS touch fix + canvas height fix |

---

*"24 saat uykusuzluk = 1.0 promil alkol etkisi" — bu tek başına viral.*
