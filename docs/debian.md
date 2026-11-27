# Debian Kurulum Rehberi

Debian, "rock solid" (kaya gibi sağlam) kararlılığıyla bilinen, en eski ve en köklü Linux dağıtımlarından biridir.
Ubuntu, Linux Mint, Pop!_OS gibi dağıtımlar Debian tabanlıdır.
Paket yöneticisi **apt**, paket formatı **`.deb`**'dir.

> **Özet:** Debian'ı USB'ye yazıp GUI veya terminal ile kurmayı, `sudo` ve depo ayarlarını, VLC, VS Code, Git ve htop kurulumunu, `apt` komutlarını ve eski bilgisayarlar için ipuçlarını öğrenirsin.

## ✅ Gereksinimler

- 512 MB RAM (masaüstü için 2 GB+ önerilir)
- 10 GB disk alanı (masaüstü için 20 GB+)
- 4 GB USB bellek
- İnternet bağlantısı

## 💿 1. ISO İndirme

1. [debian.org/download](https://www.debian.org/download) adresine git
2. **Stable** sürümü indir (kararlılık için önerilir)
3. **netinst** (küçük, internetten kurar) veya **DVD** (çevrimdışı) ISO seç
4. Ventoy veya Rufus ile USB'ye yaz

> Debian üç kanal sunar: **Stable** (kararlı), **Testing** (test), **Unstable/Sid** (geliştirme). Yeni başlayanlar **Stable** kullanmalı.

## 🧭 2. Kurulum Yöntemi Seç

Debian iki kurulum yöntemi sunar:

- **GUI (Graphical install):** Görsel sihirbaz, yeni başlayanlar için önerilir.
- **Terminal (Install):** Metin tabanlı, ileri seviye kullanıcılar için.

Aşağıda her iki yöntem de ayrı ayrı anlatılmıştır.

---

## 🖥️ 2A. GUI ile Kurulum (Graphical install)

### 2A.1 USB'den Başlat

Bilgisayarı USB'den boot et. Debian kurulum menüsü açılır.

### 2A.2 Graphical Install Seç

Menüden **Graphical install** seçeneğini seç.

### 2A.3 Dil ve Bölge

Türkçe dil, Türkiye bölge, `tr_TR.UTF-8` yerel ayarı seç.

### 2A.4 Klavye

Türkçe Q klavye seç.

### 2A.5 Ağ Yapılandırması

Hostname ve domain belirle. Wi-Fi varsa ağ seç.

### 2A.6 Kullanıcı ve Şifre

Root şifresi ve normal kullanıcı oluştur.

### 2A.7 Disk Bölümleme

**Guided — use entire disk** (tüm diski kullan) seçeneği yeni başlayanlar için en kolayıdır.

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 512 MB | EFI | `/boot/efi` |
| `/dev/sda2` | 2 GB | swap | — |
| `/dev/sda3` | Kalan | ext4 | `/` |

### 2A.8 Paket Seçimi

Kurulum sırasında masaüstü ortamı seç:

- **Debian desktop environment** (GNOME varsayılan)
- **GNOME**, **KDE Plasma**, **XFCE**, **LXQt**, **MATE**, **Cinnamon** seçenekleri
- **Standard system utilities** işaretli kalsın

### 2A.9 GRUB

GRUB bootloader'ı MBR'ye (veya UEFI'de EFI bölümüne) kur.

### 2A.10 Kurulumu Tamamla

Yeniden başlat.

---

## ⌨️ 2B. Terminal ile Kurulum (Install)

### 2B.1 USB'den Başlat

Bilgisayarı USB'den boot et. Menüden **Install** (metin tabanlı) seçeneğini seç.

### 2B.2 Dil, Klavye, Ağ ve Kullanıcı

Menü adımları 2A.3 – 2A.6 ile aynıdır; yalnızca arayüz metin tabanlıdır. Kablolu bağlantı önerilir (Wi-Fi firmware gerektirebilir).

### 2B.3 Disk Bölümleme

**Guided — use entire disk** seçeneği en kolayıdır. Elle bölümleme için **Manual** seç ve 2A.7'deki tabloyu kullan.

### 2B.4 Paket Seçimi

Masaüstü ortamı seç (GNOME, XFCE, KDE vb.) veya **"Debian desktop environment"** işaretini kaldırıp sadece **"Standard system utilities"** ile minimal kurulum yap.

### 2B.5 GRUB

GRUB'u MBR'ye (veya UEFI'de EFI bölümüne) kur.

### 2B.6 Kurulumu Tamamla

Yeniden başlat.

---

## 🔧 3. Kurulum Sonrası (her iki yöntem için ortak)

### 3.1 `sudo` Kurulumu

Kurulumda root şifresi belirlediysen Debian `sudo` ile gelmez. Root olarak kur:

```bash
su -
apt install sudo
usermod -aG sudo kullanici
exit
```

> `usermod` ile eklenen grup, oturumu kapatıp açınca (veya yeniden başlatınca) geçerli olur.

### 3.2 Güncelleme

```bash
sudo apt update
sudo apt upgrade -y
```

### 3.3 Depo Yapılandırması

Debian 13 "Trixie" ve sonrasında depolar `/etc/apt/sources.list.d/debian.sources` dosyasında (deb822 biçimi) tutulur. Dosya genellikle kurulumla hazır gelir; eksik bileşenleri eklemen yeterli:

```bash
sudo nano /etc/apt/sources.list.d/debian.sources
```

Örnek:

```text
Types: deb
URIs: http://deb.debian.org/debian
Suites: trixie trixie-updates
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Types: deb
URIs: http://security.debian.org/debian-security
Suites: trixie-security
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```

> `non-free-firmware` özellikle Wi-Fi ve ekran kartı sürücüleri için önemlidir.

> Debian 12 ve öncesinde klasik `/etc/apt/sources.list` dosyası kullanılır ve sürüm adı `bookworm` gibi yazılır.

### 3.4 Firmware Kurulumu

Wi-Fi veya ekran kartı sorunu varsa:

```bash
sudo apt install firmware-linux firmware-iwlwifi
```

## 📦 4. Uygulama Kurulumu

### 4.1 VLC

```bash
sudo apt install vlc
```

### 4.2 Visual Studio Code

VS Code, Debian resmi deposunda **yoktur** — Microsoft'un resmi deposundan kurulur.

**1. GPG anahtarını ekle:**

```bash
sudo apt install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
rm -f packages.microsoft.gpg
```

**2. Depoyu ekle:**

```bash
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
```

**3. Kur:**

```bash
sudo apt update
sudo apt install code
code --version
```

### 4.3 Git

```bash
sudo apt install git
```

### 4.4 htop

```bash
sudo apt install htop
```

## 📖 5. APT Referansı

Debian'ın paket yöneticisi **apt**'tir.

| Komut | Ne yapar? |
|-------|-----------|
| `sudo apt update` | Paket listesini günceller |
| `sudo apt upgrade` | Kurulu paketleri yükseltir |
| `sudo apt full-upgrade` | Gerekirse paket ekler/kaldırır |
| `sudo apt install <paket>` | Paket kurar |
| `sudo apt install <p1> <p2>` | Birden fazla paket kurar |
| `sudo apt install ./dosya.deb` | Yerel `.deb` dosyasını kurar |
| `sudo apt remove <paket>` | Paketi kaldırır (ayarlar kalır) |
| `sudo apt purge <paket>` | Paketi ve ayarlarını kaldırır |
| `sudo apt autoremove` | Gereksiz bağımlılıkları temizler |
| `apt search <kelime>` | Paket arar |
| `apt show <paket>` | Paket bilgisini gösterir |
| `apt list --installed` | Kurulu paketleri listeler |
| `sudo apt install -f` | Bozuk bağımlılıkları düzeltir |

### Örnek Senaryolar

**Paketi tamamen kaldır:**

```bash
sudo apt purge vlc
sudo apt autoremove -y
```

## 🐢 6. Eski Bilgisayarlar İçin

Debian, hafif masaüstü ortamlarıyla eski cihazlarda çalışır.

- **Hafif masaüstü:** XFCE, LXQt veya MATE seç (GNOME ağır).
- **Swap:** 2 GB RAM varsa en az 2 GB swap önerilir.
- **`non-free-firmware`:** Eski Wi-Fi kartları için firmware paketleri gerekebilir.
- **Gereksiz servisleri kapat:** `systemctl disable <servis>` ile kullanılmayanları kapat.
- **`apt clean`:** Paket önbelleğini düzenli temizle.

> Debian Stable, eski cihazlarda **çok kararlı** çalışır. Hafif masaüstü ile 1 GB RAM'de bile açılır.

## 🛠️ 7. Sık Karşılaşılan Sorunlar

### 7.1 `sudo: command not found`

`sudo` kurulu değil. Çözüm için 3.1'e bak.

### 7.2 Wi-Fi görünmüyor

`non-free-firmware` deposunu ekle ve firmware kur:

```bash
sudo apt install firmware-iwlwifi firmware-realtek
```

### 7.3 `apt update` GPG hatası

Depo anahtarı eksik veya eskimiş demektir. Anahtarı `apt-key` ile değil, `/etc/apt/keyrings/` altına koyarak ekle ve depo satırında `signed-by=` ile göster (4.2'deki VS Code adımları örnektir).

### 7.4 Ekran kartı sürücüsü yok

NVIDIA için:

```bash
sudo apt install nvidia-driver firmware-misc-nonfree
```

### 7.5 `apt upgrade` "kept back" paketler

`full-upgrade` kullan:

```bash
sudo apt full-upgrade
```

### 7.6 Kurulum sonrası siyah ekran

GRUB'da `nomodeset` parametresiyle başlat. Kalıcı çözüm için:

```bash
sudo nano /etc/default/grub
# GRUB_CMDLINE_LINUX_DEFAULT satırına nomodeset ekle
sudo update-grub
```

---

**Son güncelleme:** 2026-11-27
**Kaynaklar:**
- [Debian Resmi Kurulum Rehberi](https://www.debian.org/releases/stable/installmanual)
- [Debian Wiki — Apt](https://wiki.debian.org/Apt)
- [Debian Wiki — Firmware](https://wiki.debian.org/Firmware)
