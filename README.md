# Alice

**Herkes için Linux wiki** 🐇

Linux'u ve Linux'taki her şeyi sıfırdan öğrenmek isteyenler için Türkçe, açık ve ücretsiz bir wiki.

**Links:** [wiki](https://alice-wiki.example.com)

---

## Yerelde çalıştır

Wiki, `docs/` klasörünü `fetch` ile okur; bu yüzden dosyayı çift tıklayıp açmak yerine yerel sunucu kullan:

```bash
python3 -m http.server 8000
```

Sonra tarayıcıda `http://localhost:8000` adresini aç.

## Yeni konu ekle

1. `docs/yeni-konu.md` oluştur
2. `index.html` içindeki `TOPIC_FILES`'a ekle
3. Bitti.

Detaylı rehber: [`docs/templates.md`](docs/templates.md)

## Katkı

Fork'la, konu ekle, PR gönder. Kurallar için `docs/templates.md`.

## Lisans

MIT
