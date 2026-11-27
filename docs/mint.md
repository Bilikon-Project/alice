# Linux Mint Kurulumu

Linux Mint, yeni başlayanlar için en kolay Linux dağıtımlarından biridir.
Ubuntu tabanlıdır; `apt` paket yöneticisi kutudan çıkar.

> **Özet:** Linux Mint'i USB'ye yazıp kurmayı, kurulum sonrası güncellemeyi, VLC, VS Code, Git ve htop kurulumunu, `apt` komutlarını ve eski bilgisayarlar için ayarları öğrenirsin.

## ✅ Gereksinimler

- 2 GB RAM (minimum)
- 20 GB disk alanı
- 4 GB USB bellek
- İnternet bağlantısı

## 💿 1. ISO İndirme

1. [linuxmint.com](https://linuxmint.com) adresine git
2. İstediğin sürümü indir
3. Ventoy veya Rufus ile USB'ye yaz

### Sürüm Seçimi

RAM miktarına göre sürüm seç:

| RAM | Önerilen Sürüm |
|-----|----------------|
| En çok 2 GB | XFCE veya MATE |
| 2 GB'tan fazla, 4 GB'tan az | XFCE |
| 4 GB ve üzeri | Cinnamon |

> XFCE ile MATE arasında karar veremiyorsan **XFCE** ile devam et.
> MATE hâlâ geliştiriliyor ancak XFCE daha hafif ve daha aktif.

## 🚀 2. Kurulum

1. **USB'den başlat:** Bilgisayarı USB'den boot et.
2. **Kurulumu başlat:** Masaüstündeki **Install Linux Mint** simgesine tıkla.
3. **Dil ve klavye:** Türkçe dil ve klavye düzenini seç.
4. **Disk bölümleme:** Temiz kurulum için "Diski sil ve Linux Mint'i kur" seçeneğini seç.

> ⚠️ Kuruluma başlamadan önce verilerini yedekle.

## 🔧 3. Kurulum Sonrası

Sistemi güncelle ve temizle:

```bash
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

## 📦 4. Uygulama Kurulumu

### VLC

Medya oynatıcı:

```bash
sudo apt install vlc
```

### Visual Studio Code

VS Code, Mint depolarında yok — Microsoft'un resmi deposundan kurulur.

**1. GPG anahtarını ekle:**

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
rm -f packages.microsoft.gpg
```

**2. Depoyu ekle:**

```bash
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
```

**3. Kur ve kontrol et:**

```bash
sudo apt update
sudo apt install code
code --version
```

### Git

Sürüm kontrol sistemi:

```bash
sudo apt install git
```

### htop

Gelişmiş sistem monitörü:

```bash
sudo apt install htop
```

## 📖 5. APT Referansı

Linux Mint, Ubuntu tabanlı olduğu için `apt` paket yöneticisi kurulu gelir.
Aşağıdaki tablo, en sık kullanılan `apt` komutlarını **tek yerde** toplar.

| Komut | Ne yapar? |
|-------|-----------|
| `sudo apt update` | Paket listesini günceller |
| `sudo apt upgrade` | Kurulu paketleri yükseltir |
| `sudo apt full-upgrade` | Gerekirse paket ekler/kaldırır, agresif yükseltir |
| `sudo apt install <paket>` | Paket kurar |
| `sudo apt install <p1> <p2>` | Birden fazla paket kurar |
| `sudo apt install ./dosya.deb` | Yerel `.deb` dosyasını kurar |
| `sudo apt install --reinstall <paket>` | Paketi yeniden kurar |
| `sudo apt remove <paket>` | Paketi kaldırır (ayarlar kalır) |
| `sudo apt purge <paket>` | Paketi ve ayarlarını kaldırır |
| `sudo apt autoremove` | Gereksiz bağımlılıkları temizler |
| `sudo apt autoremove --purge` | Gereksizleri ayarlarıyla temizler |
| `sudo apt clean` | İndirilen `.deb` önbelleğini siler |
| `sudo apt autoclean` | Eski `.deb` önbelleğini siler |
| `apt search <kelime>` | Paket arar |
| `apt show <paket>` | Paket detayını gösterir |
| `apt list --installed` | Kurulu paketleri listeler |
| `apt list --upgradable` | Güncellenebilir paketleri listeler |
| `sudo apt install -f` | Bozuk bağımlılıkları düzeltir |
| `sudo dpkg --configure -a` | Yarım kalan kurulumları tamamlar |
| `sudo apt --fix-broken install` | Bozuk paketleri onarır |

### Örnek Senaryolar

**Paketi tamamen kaldır:**

```bash
sudo apt purge vlc
sudo apt autoremove -y
```

## 🐢 6. Eski Bilgisayarlar İçin

2010 öncesi veya düşük RAM'li (2 GB altı) bilgisayarlarda kurulum sonrası kasma yaşanabilir. Aşağıdaki ayarlar masaüstü ortamına göre değişir.

### 6.1 Cinnamon

- **Efektleri azalt:** Menü → Sistem Ayarları → **Efektler** → "Efektleri etkinleştir" kapat veya "Basit" modunu seç.
- **Pencere animasyonlarını kapat:** Menü → Sistem Ayarları → **Pencereler** → "Pencere animasyonlarını etkinleştir" kapat. (Animasyonlar takılıyorsa ilk çözüm budur.)
- **Başlangıç uygulamalarını kıs:** Menü → Sistem Ayarları → **Başlangıç Uygulamaları** → gereksizleri kapat.
- **Güç yönetimi:** Menü → Sistem Ayarları → **Güç Yönetimi** → uyku/ekran kapatma sürelerini uzat.
- **Takas (swap) alanı:** 2 GB RAM varsa en az 2 GB swap önerilir (`sudo swapon --show` ile kontrol et).

> Cinnamon, 4 GB altı RAM'li bilgisayarlarda XFCE'ye göre belirgin şekilde daha yavaş çalışır. Mümkünse XFCE tercih et.

### 6.2 XFCE

- **Efektleri azalt:** Menü → Ayarlar → **Görünüm** → **Efektler** sekmesi → "Efektleri etkinleştir" kapat.
- **Pencere yöneticisi efektleri:** Menü → Ayarlar → **Pencere Yöneticisi (Gelişmiş)** → "Bileşikleyici" sekmesi → kapat.
- **Başlangıç uygulamalarını kıs:** Menü → Ayarlar → **Oturum ve Başlangıç** → "Uygulama Otomatik Başlatma" sekmesi → gereksizleri kaldır.
- **Güç yönetimi:** Menü → Ayarlar → **Güç Yöneticisi** → uyku/ekran kapatma sürelerini uzat.
- **Panel sayısını azalt:** Birden fazla panel varsa tek panele düşür (sağ tık → Panel → Kaldır).
- **Takas (swap) alanı:** 2 GB RAM varsa en az 2 GB swap önerilir.

> XFCE, 2 GB RAM'li bilgisayarlarda bile akıcı çalışır. En güvenli seçim.

### 6.3 MATE

- **Efektleri azalt:** Menü → Tercihler → **MATE Ayarları** → **Görünüm** → **Efektler** sekmesi → "Efektleri etkinleştir" kapat.
- **Pencere yöneticisi efektleri:** Menü → Tercihler → **MATE Ayarları** → **Pencere Yöneticisi** → "Bileşikleyici" kapat.
- **Başlangıç uygulamalarını kıs:** Menü → Tercihler → **Başlangıç Uygulamaları** → gereksizleri kapat.
- **Güç yönetimi:** Menü → Tercihler → **Güç Yönetimi** → uyku/ekran kapatma sürelerini uzat.
- **Panel sayısını azalt:** Birden fazla panel varsa tek panele düşür (sağ tık → Panel → Kaldır).
- **Takas (swap) alanı:** 2 GB RAM varsa en az 2 GB swap önerilir.

> MATE hâlâ geliştiriliyor ancak XFCE kadar hafif değil. 2 GB RAM'li bilgisayarlarda XFCE daha iyi sonuç verir.

## 🛠️ 7. Sık Karşılaşılan Sorunlar

### 7.1 Wi-Fi görünmüyor

Bazı Wi-Fi kartları (özellikle Broadcom) kurulumdan sonra tanınmaz. **Sürücü Yöneticisi**'ni aç, önerilen sürücüyü kur ve yeniden başlat.

Sorun devam ederse:

```bash
sudo apt install firmware-b43-installer
```

Cihazı tanımlamak için:

```bash
lspci -nn | grep -i network
```

### 7.2 APT kilidi hatası (`/var/lib/dpkg/lock-frontend`)

Bu hata, başka bir `apt` işlemi (ör. Güncelleme Yöneticisi) çalışırken yeni komut verildiğinde görülür. Önce o işlemin bitmesini bekle. Hâlâ takılıysa çalışan işlemi bul:

```bash
sudo lsof /var/lib/dpkg/lock-frontend
```

İşlem gerçekten takılmışsa normal şekilde sonlandır, ardından yarım kalanı tamamla:

```bash
sudo kill <PID>
sudo dpkg --configure -a
```

> ⚠️ `kill -9` kullanma; paket veritabanını bozabilir.

### 7.3 Kurulum sonrası siyah ekran / boot sorunu

Bazı eski NVIDIA kartlarda GRUB menüsünden **nomodeset** parametresiyle başlatmak gerekir.

1. GRUB menüsünde `e` tuşuna bas.
2. `linux` ile başlayan satırın sonuna `nomodeset` ekle.
3. `Ctrl+X` ile başlat.
4. Kalıcı çözüm için sürücüyü kurduktan sonra parametreyi kaldır.

### 7.4 Ses çıkmıyor

`alsamixer` komutunu terminalde çalıştır:

```bash
alsamixer
```

- `F6` ile doğru ses kartını seç.
- `M` tuşuyla mute durumunu kontrol et.
- `↑` / `↓` ile ses seviyesini artır.

### 7.5 XFCE'de panel donuyor / yanıt vermiyor

Terminalde paneli yeniden başlat:

```bash
xfce4-panel -r
```

Sorun devam ederse panel yapılandırmasını sıfırla:

```bash
rm -rf ~/.config/xfce4/panel
xfce4-panel &
```

### 7.6 MATE'de tema / simge bozuldu

Temayı sıfırla: Menü → Tercihler → **MATE Ayarları** → **Görünüm** → varsayılan temayı seç. Simgeler için **Arayüz** sekmesinden varsayılan simge setini seç.

### 7.7 Ekran parlaklığı ayarlanmıyor

Bazı eski laptoplarda parlaklık tuşları çalışmaz. Çözüm:

```bash
sudo apt install brightnessctl
brightnessctl set 50%
```

Kalıcı çözüm için `/etc/default/grub` dosyasına `acpi_backlight=vendor` ekle:

```bash
sudo nano /etc/default/grub
# GRUB_CMDLINE_LINUX_DEFAULT satırına acpi_backlight=vendor ekle
sudo update-grub
```

---

**Son güncelleme:** 2026-11-27
**Kaynaklar:**
- [Linux Mint Resmi Dokümantasyon](https://linuxmint.com/documentation.php)
- [Linux Mint Kurulum Rehberi](https://linuxmint-installation-guide.readthedocs.io/)
