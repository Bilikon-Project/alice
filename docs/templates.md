# Yeni Wiki Sayfası Nasıl Yazılır

Bu sayfa, Alice Wiki için yeni bir konu dosyasının nasıl yazılacağını anlatır.
**Kural:** Bir konu = `docs/` klasöründe bir `.md` dosyası.

> **Özet:** Bu sayfa yeni bir konu dosyasının nasıl oluşturulacağını, nasıl yazılacağını ve yayınlamadan önce neleri kontrol edeceğini anlatır.

## 📝 1. Dosyayı Oluştur

`docs/` klasöründe küçük harfli bir isimle yeni bir dosya oluştur
(örnek: `opensuse.md`). Ardından dosya adını `index.html` içindeki
`TOPIC_FILES` dizisine ekle:

```js
const TOPIC_FILES = [
  "arch.md",
  "debian.md",
  "fedora.md",
  "gentoo.md",
  "mint.md",
  "templates.md",
];
```

> Dosya adı **küçük harf**, **boşluksuz** ve **Türkçe karaktersiz** olmalı; birden fazla kelimeyi `-` ile ayır (`opensuse.md` ✅, `linux-temelleri.md` ✅, `OpenSUSE.md` ❌, `linux temelleri.md` ❌).

## 🧱 2. Sayfa Yapısı

Her sayfa tek bir `# Başlık` ile başlar (sayfa başlığı).
Bölümler için `##`, alt bölümler için `###` kullan. Bu başlıklar otomatik
olarak sayfanın sağ tarafındaki içindekiler menüsüne eklenir.

```markdown
# Konu Başlığım

Kısa giriş paragrafı.

> **Özet:** Bu sayfa X'i anlatır. Y'yi öğreneceksin.

## Genel Bakış

Buraya daha fazla metin.

### Alt Bölüm

Detaylar buraya.
```

> `#` sadece **1 tane** olmalı. Sayfa başlığı zaten sayfanın üstünde gösterildiği için gövdede tekrar çıkmaz. `##` ve `###` içindekiler menüsüne eklenir (geniş ekranlarda sağda görünür).

## 💻 3. Kod Blokları

### Satır içi kod

Kısa kod parçalarını tek backtick ile sar:

```markdown
Paket listesini `sudo apt update` ile güncelle.
```

### Kod bloğu

Kodun önüne ve arkasına üç backtick koy. İlk üçlüden hemen sonra
**dil adını** yaz (bash, python, js, json, html vb.):

```bash
#!/bin/bash
echo "Merhaba, Alice"
ls -la /home
```

```python
def selamla(isim):
    print(f"Merhaba, {isim}")

selamla("Alice")
```

```js
const konular = ["mint", "gentoo", "templates"];
konular.forEach(k => console.log(k));
```

```json
{
  "isim": "Alice",
  "tur": "wiki"
}
```

```html
<div class="card">
  <h3>Konu</h3>
</div>
```

```
Dil etiketi yok — düz metin olarak görünür.
```

### Liste içinde kod

Kod bloğunu liste maddesiyle aynı hizada girintile:

````markdown
1. Terminali aç.
2. Şu komutu çalıştır:

   ```bash
   sudo apt update
   ```

3. Yeniden başlat.
````

> İçinde ``` geçen bir örnek göstermek için dıştaki bloğu dört backtick ile aç ve kapat.

## ✨ 4. Diğer Markdown Özellikleri

### Kalın ve italik

```markdown
Bu **kalın** ve bu *italik*.
```

### Listeler

```markdown
- Birinci madde
- İkinci madde
- Üçüncü madde
```

### Alıntı (not veya uyarı)

```markdown
> **Not:** Bu bir bilgi notu.

> **Uyarı:** Bu adımı atlarsan sorun çıkabilir.
```

### Tablo

```markdown
| Komut | Açıklama        |
|-------|-----------------|
| `ls`  | Dosyaları listele |
| `cd`  | Dizin değiştir  |
```

### Bağlantı

```markdown
[Linux Mint](https://linuxmint.com)
```

### Yatay çizgi

```markdown
---
```

### Desteklenmeyenler

Wiki'nin okuyucusu Markdown'un basit bir alt kümesini destekler. Şunları kullanma:

- İç içe (girintili) listeler
- Resimler (`![]()`) ve ham HTML
- Dipnotlar
- Paragraf içinde satır sonu: her satır ayrı paragraf olarak gösterilir

## 🔚 5. Sayfa Sonu

Her sayfanın sonunda **tarih ve kaynak** belirt:

```markdown
---

**Son güncelleme:** 2026-11-27
**Kaynaklar:**
- [Resmi Dokümantasyon](https://example.com/docs)
```

## 🎨 6. Güzellik Kuralları

Bu kurallar sayfayı profesyonel gösterir:

- **Tekrar yok:** Aynı bilgi 2 kere yazılmaz. Bir komut bir yerde geçer, başka yerde "yukarıya bak" denir.
- **Bölüm başlıklarında 1 emoji:** `## ✅ Gereksinimler`, `## 🚀 Kurulum` gibi.
- **Uyarılar blockquote ile:** `> **Önemli:**` veya `> ⚠️` kullan.
- **Tablolar karşılaştırma için:** Seçenekleri tablo ile göster.
- **Kod bloklarına dil etiketi:** `` ```bash ``, `` ```python `` gibi.
- **Sayfa sonunda kaynak:** Sadece **resmi** dokümantasyon.
- **Her konu kendi dağıtımına özel:** `gentoo.md` içinde `apt` geçmez, `mint.md` içinde `emerge` geçmez.

## ✅ 7. Yayınlama Öncesi Kontrol Listesi

- [ ] Dosya `docs/` içinde ve `.md` ile bitiyor
- [ ] Dosya adı küçük harfli, boşluksuz ve Türkçe karaktersiz
- [ ] İlk satır tek bir `# Başlık`
- [ ] Dosya adı `index.html` içindeki `TOPIC_FILES`'a eklendi
- [ ] Kod blokları üç backtick ve dil etiketiyle yazıldı
- [ ] `> **Özet:**` girişi var
- [ ] `##` başlıklarında 1 emoji var
- [ ] Sayfa sonunda tarih + resmi kaynak var
- [ ] Aynı bilgi 2 kere yazılmadı
- [ ] `python3 -m http.server 8000` ile yerelde önizlendi

---

**Son güncelleme:** 2026-11-27
**Kaynaklar:**
- [CommonMark — Markdown Rehberi](https://commonmark.org/help/)
- [GitHub Docs — Markdown Yazımı](https://docs.github.com/en/get-started/writing-on-github)
