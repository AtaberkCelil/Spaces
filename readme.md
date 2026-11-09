# MenSpace — Görev Tasarım Simülatörü

NASA Space Apps için hazırlanmış, uçtan uca bir uzay görevi tasarlama oyunu. Fırlatma aracından bilime, inişten yüzey operasyonlarına kadar her karar görev sonucunu etkiler.

## Çalıştırma

```bash
# yerel önizleme (kimlik doğrulama çalışmaz)
python3 -m http.server 8899 --directory app

# tam geliştirme ortamı (Workers + D1)
npx wrangler dev --port 8789
```

## Oyun modları

- **Serbest görev:** rastgele brifing ve hedef gök cismi; sonuç yalnızca yerel rekor (`localStorage`) olarak kaydedilir.
- **Official Challenge:** sabit seed (`1337`) ile tüm oyunculara aynı görev verilir. Skor sunucu tarafında replay ile doğrulanır ve liderlik tablosunda gösterilir. Google OAuth gerekir.

## Kontroller

| İşlem | Tuş |
|---|---|
| Fırlatma | `Boşluk` / `W` / `FIRLAT` |
| İniş (tam itki) | `W` / `↑` / `Boşluk` |
| İniş (yarım itki) | `S` / `↓` |
| İniş (yan itki) | `A` / `←` , `D` / `→` |
| Yüzey | Enstrümanları işaretle, `Sölü başlat`, `Veriyi Dünya'ya gönder` |
| Rover | `A` / `←` geri, `D` / `→` ileri |

## Yapı

```
app/
  index.html   arayüz iskeleti (7 ekran)
  style.css    tasarım dili (kağıt/mürekkep paleti)
  game.js      oyun durumu, fazlar, brifing, canvas çizimi, ses
  assets/      PNG çizimler (64×64 / 128×128)
worker/
  index.mjs    Cloudflare Workers (auth, leaderboard, challenge API)
```

Teknik: saf HTML/CSS/JS + Canvas 2D, harici kütüphane veya build adımı yok.

## Katkı

Yeni gezegen, brifing, donanım veya atölye tarifi eklemek için ilgili diziyi `game.js`'de düzenleyin; yeni varlık için `app/assets/` içine şeffaf PNG koyun. Kod değişiklikleri `node --check` ile test edilmeli ve replay deterministik kalmalıdır.

## Lisans

MIT — bkz. [LICENCE](LICENCE).