# Spaces — Görev Tasarım Simülatörü

NASA Space Apps için uçtan uca bir uzay görevi tasarım oyunu. Brifingi seç, fırlatma aracını ve bilim yükünü tasarla, fırlatma penceresini yakala, inişi kurtar, yüzeyde bilim yap ve veriyi Dünya'ya ulaştır.

**Oynamak için:** [nasa.ataberk.dev](https://nasa.ataberk.dev)

## Çalıştırma

```bash
# yerel D1 şeması (ilk sefer)
npx wrangler d1 execute DB --local --file worker/schema.sql

# tam geliştirme ortamı (Workers + D1 + statik arayüz)
npx wrangler dev --port 8789
```

Tarayıcıdan `http://localhost:8789` açılır. `.dev.vars` içinde `SESSION_SECRET` ve `CHALLENGE_SECRET` bulunur.

## Oyun modları

- **Serbest görev:** rastgele seed ile brifing; sonuç yalnızca yerel rekor (`localStorage`) olarak kaydedilir.
- **Resmî görev:** sabit seed (`1337`) ile tüm oyunculara aynı görev. Skor, sunucu tarafında aksiyon replay'i ile yeniden oynatılır ve doğrulanır; hesap gerektirir.

## Hesap ve skor altyapısı

- Supabase/Google bağımlılığı yoktur; her şey Cloudflare D1 üzerindedir.
- Hesaplar e-posta + şifre (PBKDF2-SHA256, 100k tur), oturumlar HttpOnly çerezle yönetilir.
- `POST /api/register`, `/api/login`, `/api/logout`, `GET /api/me`, `POST /api/name`
- `GET /api/challenge` (imzalı, 15 dk geçerli), `GET /api/leaderboard`, `POST /api/submit`
- `submit`: oturum + imza + saatlik hız limiti (8/IP) + deterministik replay denetimi yapar; puan yalnızca sunucunun kendi simülasyonundan kabul edilir.

## Kontroller

| İşlem | Tuş |
|---|---|
| Fırlatma | `Boşluk` / `Enter` / FIRLAT |
| İniş (tam itki) | `W` / `↑` / `Boşluk` |
| İniş (yarım itki) | `S` / `↓` |
| İniş (yan itki) | `A` / `←` , `D` / `→` |
| Rover | `A` / `←` geri, `D` / `→` ileri |
| Yüzey | Enstrüman seç, `Sölü çalıştır`, `Şimdi ilet` |

## Puan

Brifing ödülü + fırlatma hassasiyeti + iniş hassasiyeti + bilim (iletilen veri) + bütçe ekonomisi — üst sınır 400. Rozetler: ilk iniş, tüm söller, 100+ veri, S sınıfı.

## Yapı

```
app/
  index.html   arayüz iskeleti (8 perde + pencereler)
  style.css    tasarım dili (kâğıt/mürekkep "görev dosyası" paleti)
  game.js      giriş modülü (replay ihracı + tarayıcıda ui başlatımı)
  sim.js       deterministik çekirdek: veri, fizik, puan, replay
  ui.js        ekranlar, tuval çizimleri, giriş, hesap/liderlik
  assets/      PNG çizimler
worker/
  index.mjs    Cloudflare Workers API (auth, challenge, skor, replay)
  schema.sql   D1 şeması
```

Teknik: saf HTML/CSS/JS + Canvas 2D, derleme adımı yok. Oyun çekirdeği (`sim.js`) DOM'dan bağımsızdır; canlı oyunda ve sunucu replay'inde aynı `ADIM = 1/60` sabit adım kullanılır.

## Katkı

Yeni gök cismi, brifing, donanım veya atölye tarifi eklemek için `app/sim.js` içindeki veri dizilerini düzenleyin. Kod değişikliklerinde replay determinizmi bozulmamalı; `node --check` ile sözdizimini denetleyin.

## Lisans

MIT — bkz. [LICENCE](LICENCE).
