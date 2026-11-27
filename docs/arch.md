# Arch Linux Kurulum Rehberi

Arch Linux, "KISS" (Keep It Simple, Stupid) felsefesiyle tasarlanmış, minimal ve rolling release bir dağıtımdır.
Kurulum terminalden yapılır; her şey kullanıcının kontrolündedir.
Paket yöneticisi **pacman**, kullanıcı deposu **AUR**'dur.

> **Özet:** Arch Linux'u `archinstall` ile veya elle kurmayı, masaüstü ortamı seçmeyi, AUR üzerinden VS Code kurmayı, `pacman` komutlarını ve eski bilgisayarlar için ipuçlarını öğrenirsin.

## ✅ Gereksinimler

- 512 MB RAM (minimum), 2 GB+ önerilir
- 20 GB disk alanı
- 1 GB USB bellek
- İnternet bağlantısı (kablolu önerilir, Wi-Fi için `iwctl` gerekir)

## 💿 1. ISO İndirme

1. [archlinux.org/download](https://archlinux.org/download/) adresine git
2. En güncel ISO'yu indir (aylık güncellenir)
3. Ventoy veya Rufus ile USB'ye yaz

> Arch, canlı masaüstü yerine **minimal** bir kurulum ortamı sunar. Kurulum terminalden yapılır.

## 🧭 2. Kurulum Yöntemi Seç

Arch iki kurulum yöntemi sunar:

- **`archinstall` (TUI/GUI):** Menü tabanlı, yeni başlayanlar için kolay.
- **Manuel kurulum:** Her adımı elle yaparsın, ileri seviye.

Aşağıda her iki yöntem de ayrı ayrı anlatılmıştır.

---

## 🧰 2A. `archinstall` ile Kurulum (Menü Tabanlı)

`archinstall`, resmi ISO'da gelen menü tabanlı kurulum aracıdır. Her adımı görsel menüden seçersin.

### 2A.1 USB'den Başlat

Bilgisayarı USB'den boot et. Arch ISO açılır.

### 2A.2 Klavye Düzeni

Türkçe klavye:

```bash
loadkeys trq
```

### 2A.3 İnternet Bağlantısı

Kablolu bağlantı otomatik çalışır. Wi-Fi için:

```bash
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect "SSID"
exit
```

Bağlantıyı doğrula:

```bash
ping -c 3 archlinux.org
```

### 2A.4 `archinstall` Başlat

```bash
archinstall
```

### 2A.5 Menü Adımları

> Menü adları `archinstall` sürümüne göre biraz değişebilir.

`archinstall` menüsünde sırayla:

1. **Language:** Türkçe seç
2. **Mirrors:** Türkiye aynalarını seç
3. **Disk configuration:** `Best-effort default partition layout` seç (otomatik)
4. **Disk encryption:** İsteğe bağlı
5. **Bootloader:** `GRUB` seç
6. **Unified kernel images:** Hayır
7. **Hostname:** `archlinux` yaz
8. **Root password:** Root şifresi belirle
9. **User account:** Kullanıcı oluştur, `sudo` yetkisi ver
10. **Profile:** Masaüstü ortamı seç (XFCE, GNOME, KDE, minimal)
11. **Audio:** `pipewire` seç (modern)
12. **Kernels:** `linux` seç (varsayılan)
13. **Network configuration:** `NetworkManager` seç
14. **Timezone:** `Europe/Istanbul`
15. **NTP:** `true`
16. **Install:** Kurulumu başlat

### 2A.6 Yeniden Başlat

Kurulum bitince:

```bash
reboot
```

> `archinstall` ile kurulum ~10 dakika sürer. Her adım menüden seçilir, komut ezberlemene gerek yok.

---

## ⌨️ 2B. Manuel Kurulum (Terminal)

Her adımı elle yaparsın. Arch Wiki'deki resmi yöntemdir.

### 2B.1 USB'den Başlat

Bilgisayarı USB'den boot et.

### 2B.2 Klavye Düzeni

`loadkeys trq` (bkz. 2A.2).

### 2B.3 İnternet Bağlantısı

Kablolu bağlantı otomatiktir. Wi-Fi için 2A.3'teki `iwctl` adımlarını uygula ve bağlantıyı `ping` ile doğrula.

### 2B.4 Saat Senkronizasyonu

```bash
timedatectl set-ntp true
```

### 2B.5 Disk Bölümleme

Diskini bölümle:

```bash
fdisk /dev/sda
```

Örnek bölümleme (UEFI):

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 512 MB | EFI | `/efi` |
| `/dev/sda2` | 2 GB | swap | — |
| `/dev/sda3` | Kalan | ext4 | `/` |

> `fdisk` içinde `g` ile yeni GPT tablosu oluştur, EFI bölümünün türünü **EFI System**, swap'ı **Linux swap** yap ve `w` ile kaydet. Disk adını `lsblk` ile kontrol et (NVMe'de `/dev/nvme0n1p1` gibi).

Dosya sistemlerini oluştur ve bağla:

```bash
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda3
mkswap /dev/sda2
swapon /dev/sda2
mount /dev/sda3 /mnt
mkdir /mnt/efi
mount /dev/sda1 /mnt/efi
```

### 2B.6 Ayna Listesi

Türkiye aynalarını öne al:

```bash
reflector --country Turkey --latest 5 --sort rate --save /etc/pacman.d/mirrorlist
```

### 2B.7 Base Paketler

```bash
pacstrap -K /mnt base linux linux-firmware nano sudo networkmanager grub efibootmgr
```

> `linux` yerine `linux-lts` de kurabilirsin (daha kararlı).

> CPU'na göre mikro kod paketini de ekle: Intel için `intel-ucode`, AMD için `amd-ucode`.

### 2B.8 fstab Oluştur

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

### 2B.9 Chroot Ortamına Geç

```bash
arch-chroot /mnt
```

### 2B.10 Saat Dilimi

```bash
ln -sf /usr/share/zoneinfo/Europe/Istanbul /etc/localtime
hwclock --systohc
```

### 2B.11 Yerel Ayarlar

```bash
nano /etc/locale.gen
# tr_TR.UTF-8 UTF-8 ve en_US.UTF-8 UTF-8 satırlarının başındaki # kaldır

locale-gen
echo "LANG=tr_TR.UTF-8" > /etc/locale.conf
```

### 2B.12 Klavye (Kalıcı)

```bash
echo "KEYMAP=trq" > /etc/vconsole.conf
```

### 2B.13 Hostname

```bash
echo "archlinux" > /etc/hostname
```

### 2B.14 Root Şifresi ve Kullanıcı

```bash
passwd
useradd -m -G wheel -s /bin/bash kullanici
passwd kullanici

EDITOR=nano visudo
# %wheel ALL=(ALL:ALL) ALL satırının başındaki # kaldır
```

### 2B.15 Ağ Yöneticisi

```bash
systemctl enable NetworkManager
```

### 2B.16 Bootloader

```bash
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=Arch
grub-mkconfig -o /boot/grub/grub.cfg
```

### 2B.17 Çıkış

```bash
exit
umount -R /mnt
reboot
```

---

## 🔧 3. Kurulum Sonrası (her iki yöntem için ortak)

### 3.1 Güncelleme

```bash
sudo pacman -Syu
```

### 3.2 Masaüstü Ortamı (manuel kurulumda)

`archinstall` ile masaüstü seçtiysen bu adımı atla. Manuel kurulumda:

XFCE:

```bash
sudo pacman -S xfce4 xfce4-goodies lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm
```

GNOME:

```bash
sudo pacman -S gnome gnome-extra
sudo systemctl enable gdm
```

KDE Plasma:

```bash
sudo pacman -S plasma-meta kde-applications
sudo systemctl enable sddm
```

## 📦 4. Uygulama Kurulumu

### 4.1 VLC

```bash
sudo pacman -S vlc
```

### 4.2 Visual Studio Code (AUR)

VS Code, resmi depoda **yoktur** — AUR'dan kurulur. Önce bir AUR yardımcısı gerekir:

```bash
sudo pacman -S --needed git base-devel
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

> `makepkg -si` komutunu root ile değil, normal kullanıcıyla çalıştır.

Sonra:

```bash
yay -S visual-studio-code-bin
```

Alternatif — açık kaynak derleme **Code - OSS** (resmi depoda; eklenti mağazası olarak Open VSX kullanır):

```bash
sudo pacman -S code
```

### 4.3 Git

```bash
sudo pacman -S git
```

### 4.4 htop

```bash
sudo pacman -S htop
```

## 📖 5. Pacman Referansı

Arch'ın paket yöneticisi **pacman**'dir.

| Komut | Ne yapar? |
|-------|-----------|
| `sudo pacman -Syu` | Sistemi günceller (sync + upgrade) |
| `sudo pacman -S <paket>` | Paket kurar |
| `sudo pacman -S <p1> <p2>` | Birden fazla paket kurar |
| `sudo pacman -R <paket>` | Paketi kaldırır |
| `sudo pacman -Rs <paket>` | Paketi ve gereksiz bağımlılıklarını kaldırır |
| `sudo pacman -Rns <paket>` | Paketi, bağımlılıklarını ve ayarlarını kaldırır |
| `pacman -Ss <kelime>` | Paket arar |
| `pacman -Si <paket>` | Paket bilgisini gösterir |
| `pacman -Q` | Kurulu paketleri listeler |
| `pacman -Qe` | Açıkça kurulan paketleri listeler |
| `sudo pacman -Scc` | Paket önbelleğini tamamen siler (rutin için önerilmez) |
| `sudo paccache -r` | Önbellekte son 3 sürümü bırakıp eskileri siler (`pacman-contrib` gerekir) |
| `sudo pacman -S --needed <paket>` | Zaten kuruluysa atlar |

### AUR Yardımcıları

| Komut | Ne yapar? |
|-------|-----------|
| `yay -S <paket>` | AUR'dan paket kurar |
| `yay -Syu` | Resmi depo + AUR'u günceller |
| `yay -Ss <kelime>` | Resmi + AUR'da arar |
| `yay -Rns <paket>` | AUR paketini kaldırır |

### Örnek Senaryolar

**Paketi tamamen kaldır:**

```bash
sudo pacman -Rns vlc
```

## 🐢 6. Eski Bilgisayarlar İçin

Arch minimal olduğu için hafif masaüstü ortamıyla eski cihazlarda çalışır.

- **Hafif masaüstü:** XFCE, LXQt veya i3 tercih et (GNOME/KDE ağır).
- **`linux-lts`:** `linux` yerine `linux-lts` kernel kullan.
- **Swap:** 2 GB RAM varsa en az 2 GB swap önerilir.
- **Gereksiz servisleri kapat:** `systemctl disable <servis>`.
- **Önbellek temizliği:** `sudo paccache -r` ile eski paket sürümlerini düzenli temizle (`pacman-contrib` gerekir).

> Arch, eski cihazlarda **hafif masaüstü** ile çalışır. GNOME/KDE gibi ağır ortamlar 2 GB RAM'de kasar.

## 🛠️ 7. Sık Karşılaşılan Sorunlar

### 7.1 `pacman -Syu` "failed to commit transaction"

Önce disk doluluğunu kontrol et:

```bash
df -h
```

Hata "exists in filesystem" gibi çakışan bir dosyayı gösteriyorsa, yalnızca o yola izin ver:

```bash
sudo pacman -Syu --overwrite '/yol/dosya'
```

> ⚠️ Her şeye izin veren `--overwrite '*'` kullanma; sistem dosyalarını bozabilir. Çözüm genelde [archlinux.org/news](https://archlinux.org/news/) duyurusunda yazar.

### 7.2 AUR paketi derlenmiyor

`base-devel` ve `git` kurulu mu kontrol et:

```bash
sudo pacman -S --needed base-devel git
```

### 7.3 Wi-Fi bağlanmıyor

`iwctl` ile tekrar bağlan veya `NetworkManager` servisini kontrol et:

```bash
systemctl status NetworkManager
```

### 7.4 GRUB açılışta görünmüyor

EFI bölümünü kontrol et:

```bash
efibootmgr -v
```

Gerekirse GRUB'u yeniden kur:

```bash
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=Arch
grub-mkconfig -o /boot/grub/grub.cfg
```

### 7.5 Anahtar halkası hatası (`invalid or corrupted package`)

Anahtarları yenile (`-Sy` ve `-Su` birlikte çalıştırılmalı; tek başına `-Sy` kısmi güncellemedir):

```bash
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman -Sy archlinux-keyring && sudo pacman -Su
```

### 7.6 `archinstall` disk bölümlemede hata

> ⚠️ Aşağıdaki komut diskteki **tüm veriyi siler**.

Diskte eski bölümler varsa temizle:

```bash
wipefs -a /dev/sda
```

---

**Son güncelleme:** 2026-11-27
**Kaynaklar:**
- [Arch Wiki — Installation guide](https://wiki.archlinux.org/title/Installation_guide)
- [Arch Wiki — Archinstall](https://wiki.archlinux.org/title/Archinstall)
- [Arch Wiki — Pacman](https://wiki.archlinux.org/title/Pacman)
- [Arch Wiki — Arch User Repository](https://wiki.archlinux.org/title/Arch_User_Repository)
