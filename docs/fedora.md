# Fedora Kurulum Rehberi

Fedora, Red Hat tarafından desteklenen, yenilikçi ve modern bir Linux dağıtımıdır.
Wayland, PipeWire, GNOME gibi yeni teknolojileri ilk benimseyenlerdendir.
Paket yöneticisi **dnf**, paket formatı **`.rpm`**'dir.

## Gereksinimler

- 2 GB RAM (minimum), 4 GB+ önerilir
- 20 GB disk alanı
- 4 GB USB bellek
- İnternet bağlantısı

## 1. ISO İndirme

1. [fedoraproject.org/workstation/download](https://fedoraproject.org/workstation/download) adresine git
2. **Fedora Workstation** (GNOME) veya **Fedora Spins** (XFCE, KDE, MATE vb.) seç
3. ISO'yu indir
4. Fedora Media Writer veya Ventoy ile USB'ye yaz

> Fedora, **Workstation** (GNOME), **KDE Plasma**, **XFCE**, **MATE**, **Cinnamon** gibi farklı sürümler sunar. Yeni başlayanlar için Workstation önerilir.

## 2. Kurulum Yöntemi Seç

Fedora, **Anaconda** adlı kurulum aracını kullanır. İki modu vardır:

- **GUI (Graphical):** Görsel sihirbaz, yeni başlayanlar için önerilir.
- **TUI (Text):** Metin tabanlı, terminal sevenler için.

Aşağıda her iki yöntem de ayrı ayrı anlatılmıştır.

---

## 2A. GUI ile Kurulum (Anaconda Graphical)

### 2A.1 USB'den Başlat

Bilgisayarı USB'den boot et. Fedora kurulum ekranı açılır.

### 2A.2 Kurulumu Başlat

**Install to Hard Drive** seçeneğine tıkla.

### 2A.3 Dil ve Klavye

Türkçe dil ve klavye düzenini seç.

### 2A.4 Kurulum Hedefi

**Installation Destination** bölümünde diski seç. Yeni başlayanlar için **Automatic** (otomatik bölümleme) en kolayıdır.

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 600 MB | EFI | `/boot/efi` |
| `/dev/sda2` | 1 GB | ext4 | `/boot` |
| `/dev/sda3` | Kalan | btrfs | `/` |

> Fedora, varsayılan olarak **Btrfs** dosya sistemini kullanır (snapshot desteği için).

### 2A.5 Ağ ve Hostname

Hostname belirle, Wi-Fi varsa bağlan.

### 2A.6 Root ve Kullanıcı

Root şifresi (veya root'u kilitle) ve normal kullanıcı oluştur. Kullanıcıyı **Administrator** yap (sudo için).

### 2A.7 Kurulumu Tamamla

Yeniden başlat. İlk açılışta GNOME kurulum sihirbazı çıkar.

---

## 2B. Terminal ile Kurulum (Anaconda Text)

### 2B.1 USB'den Başlat

Bilgisayarı USB'den boot et. GRUB menüsünde **Install Fedora in text mode** seçeneğini seç.

### 2B.2 Dil ve Klavye

Metin menüsünde Türkçe dil ve klavye seç.

### 2B.3 Kurulum Hedefi

Menüden **Installation Destination** seç. Disk seçimi yap:

- **Automatic:** Otomatik bölümleme (önerilir)
- **Manual:** Elle bölümleme

### 2B.4 Ağ Yapılandırması

**Network & Hostname** menüsünden ağ bağlantısını yapılandır.

### 2B.5 Root ve Kullanıcı

**Root Password** ve **User Creation** menülerinden ayarları yap.

### 2B.6 Yazılım Seçimi

**Software Selection** menüsünden masaüstü ortamı seç:

- **Fedora Workstation** (GNOME)
- **Fedora KDE Plasma Desktop**
- **Fedora XFCE Desktop**
- **Fedora MATE Desktop**
- **Minimal Install** (masaüstü olmadan)

### 2B.7 Kurulumu Başlat

**Begin Installation** seç ve kurulumun bitmesini bekle.

### 2B.8 Yeniden Başlat

Kurulum bitince **Reboot** seç.

---

## 3. Kurulum Sonrası (her iki yöntem için ortak)

### 3.1 Güncelleme

```bash
sudo dnf upgrade --refresh
```

### 3.2 RPM Fusion Depoları

Fedora, telif hakkı nedeniyle bazı paketleri (codec, sürücü) resmi depoda sunmaz. **RPM Fusion** ekle:

```bash
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### 3.3 Codec Kurulumu

Video/ses codec'leri:

```bash
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin
sudo dnf groupupdate sound-and-video
```

### 3.4 Flatpak (varsayılan olarak gelir)

Flathub'ı ekle:

```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## 4. Uygulama Kurulumu

### 4.1 VLC

```bash
sudo dnf install vlc
```

> VLC, RPM Fusion `free` deposunda bulunur. Önce RPM Fusion'ı eklemiş olmalısın.

### 4.2 Visual Studio Code

VS Code, Fedora resmi deposunda **yoktur** — Microsoft'un resmi deposundan kurulur.

**1. GPG anahtarını ekle:**

```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
```

**2. Depoyu ekle:**

```bash
sudo tee /etc/yum.repos.d/vscode.repo > /dev/null <<'EOF'
[code]
name=Visual Studio Code
baseurl=https://packages.microsoft.com/yumrepos/vscode
enabled=1
autorefresh=1
type=rpm-md
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc
EOF
```

**3. Kur:**

```bash
sudo dnf check-update
sudo dnf install code
code --version
```

### 4.3 Git

```bash
sudo dnf install git
```

### 4.4 htop

```bash
sudo dnf install htop
```

## 5. DNF Referansı

Fedora'nın paket yöneticisi **dnf**'dir.

| Komut | Ne yapar? |
|-------|-----------|
| `sudo dnf check-update` | Güncellemeleri kontrol eder |
| `sudo dnf upgrade` | Sistemi günceller |
| `sudo dnf install <paket>` | Paket kurar |
| `sudo dnf install <p1> <p2>` | Birden fazla paket kurar |
| `sudo dnf install ./dosya.rpm` | Yerel `.rpm` dosyasını kurar |
| `sudo dnf remove <paket>` | Paketi kaldırır |
| `sudo dnf autoremove` | Gereksiz bağımlılıkları temizler |
| `dnf search <kelime>` | Paket arar |
| `dnf info <paket>` | Paket bilgisini gösterir |
| `dnf list installed` | Kurulu paketleri listeler |
| `sudo dnf group list` | Paket gruplarını listeler |
| `sudo dnf group install "<grup>"` | Paket grubu kurar |
| `sudo dnf clean all` | Önbelleği temizler |
| `sudo dnf history` | İşlem geçmişini gösterir |
| `sudo dnf history undo <id>` | Son işlemi geri alır |

### Örnek Senaryolar

**Sistemi güncelle ve temizle:**

```bash
sudo dnf upgrade --refresh
sudo dnf autoremove
sudo dnf clean all
```

**Paketi tamamen kaldır:**

```bash
sudo dnf remove vlc
sudo dnf autoremove
```

**Son işlemi geri al:**

```bash
sudo dnf history
sudo dnf history undo 5
```

## 6. Eski Bilgisayarlar İçin

Fedora, GNOME ile ağır olabilir. Hafif sürümler tercih edilmeli.

- **Hafif sürüm:** Fedora **XFCE**, **LXQt** veya **MATE** spin'lerini kullan (GNOME ağır).
- **`btrfs` yerine `ext4`:** Kurulumda ext4 seç (daha az disk/CPU kullanır).
- **Gereksiz servisleri kapat:** `systemctl disable <servis>`.
- **Swap:** 2 GB RAM varsa en az 2 GB swap (veya zram) önerilir.
- **`zram`:** Fedora varsayılan olarak zram kullanır; düşük RAM'de avantaj.

> Fedora, GNOME ile 4 GB RAM altında kasar. XFCE/LXQt spin'leri eski cihazlar için daha uygun.

## 7. Sık Karşılaşılan Sorunlar

### 7.1 `dnf` "failed to synchronize cache"

Depo bağlantısı sorunlu olabilir:

```bash
sudo dnf clean all
sudo dnf makecache
```

### 7.2 RPM Fusion eklenmiyor

Fedora sürümünü kontrol et:

```bash
rpm -E %fedora
```

Ardından doğru sürüm için RPM Fusion URL'sini kullan.

### 7.3 Wi-Fi görünmüyor

Firmware eksik olabilir:

```bash
sudo dnf install linux-firmware
sudo dnf install akmod-wl   # Broadcom için
```

### 7.4 NVIDIA sürücüsü

RPM Fusion `nonfree` ekledikten sonra:

```bash
sudo dnf install akmod-nvidia
sudo dnf install xorg-x11-drv-nvidia-cuda
```

Yeniden başlat.

### 7.5 Wayland sorun çıkarıyor

Giriş ekranında oturum türünü **X11** olarak değiştir (GNOME giriş ekranında dişli ikonu).

### 7.6 `dnf upgrade` çok yavaş

En hızlı aynayı seç:

```bash
sudo dnf install dnf-plugins-core
sudo dnf config-manager --setopt=fastestmirror=True --save
```

### 7.7 Flatpak uygulaması açılmıyor

Flatpak'ı güncelle:

```bash
flatpak update
```

### 7.8 Anaconda "No disks selected" hatası

Disk görünmüyorsa RAID/BIOS ayarlarını kontrol et. Gerekirse kurulum sırasında:

```bash
lsblk
```

ile diskleri listele ve doğru diski seç.

---

**Son güncelleme:** 2026-11-26
**Kaynaklar:**
- [Fedora Docs — Installation Guide](https://docs.fedoraproject.org/en-US/fedora/latest/install-guide/)
- [Fedora Docs — Anaconda](https://docs.fedoraproject.org/en-US/anaconda/)
- [Fedora Docs — DNF](https://docs.fedoraproject.org/en-US/quick-docs/dnf/)
- [Fedora Docs — RPM Fusion](https://docs.fedoraproject.org/en-US/quick-docs/setup_rpmfusion/)
