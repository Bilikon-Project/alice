# Gentoo Kurulum Rehberi

Gentoo, her şeyi kaynak koddan derleyen ileri seviye bir Linux dağıtımıdır.
Paket yöneticisi **Portage**, komutu **`emerge`**'dir. Bu rehber varsayılan init sistemi **OpenRC** ile kurulumu anlatır.

> **Özet:** Gentoo'yu 64-bit (amd64) veya 32-bit (x86) donanıma terminalden kurmayı, kernel ve bootloader ayarlarını, `emerge` komutlarını ve eski bilgisayarlar için ipuçlarını öğrenirsin. Mimariye göre değişen yerler tabloda ve ayrı alt başlıklarda gösterilir.

## 🧭 1. Mimari Seç

İki mimari aynı adımları izler; farklar aşağıdaki tabloda toplanmıştır. Bu tabloya aşağıdaki bölümlerden geri dönersin, tekrar yazılmaz.

| Özellik | 64-bit (amd64) | 32-bit (x86) |
|---------|----------------|--------------|
| ISO | `install-amd64-minimal` | `install-x86-minimal` |
| Stage3 | `stage3-amd64-openrc-*.tar.xz` | `stage3-i686-openrc-*.tar.xz` |
| Genelde boot modu | UEFI | BIOS/MBR |
| `-march` | `native` | `i686` |
| `MAKEOPTS` (başlangıç) | `-j4` | `-j2` |
| Resmi binary paketler | Var | Yok |
| VS Code / VSCodium | Var | Yok |

> Boot modu mimariye değil bilgisayara bağlıdır. Canlı ortamda `ls /sys/firmware/efi` çalıştır: dizin varsa **UEFI**, yoksa **BIOS** kullan.

## ✅ 2. Gereksinimler

| Özellik | 64-bit | 32-bit |
|---------|--------|--------|
| RAM (derleme için minimum) | 4 GB | 2 GB |
| Disk | 20 GB (50 GB+ önerilir) | 10 GB (20 GB+ önerilir) |
| USB bellek | 4 GB | 2 GB |

- İnternet bağlantısı
- Sabır (32-bit donanımda derleme daha uzun sürer)

## 💿 3. ISO İndirme

1. [gentoo.org/downloads](https://www.gentoo.org/downloads/) adresine git
2. 1. bölümdeki tabloya göre **minimal installation CD** indir
3. Ventoy veya Rufus ile USB'ye yaz

> Gentoo, canlı masaüstü yerine **minimal** bir kurulum ortamı sunar. Kurulum terminalden yapılır. İndirme sayfasında doğru mimari sekmesini (amd64 / x86) seçtiğinden emin ol.

## 🚀 4. Kurulum

Tüm adımlar USB'den açılan minimal ortamda, root olarak yapılır.

### 4.1 Disk Hazırlığı

Disk adını `lsblk` ile bul, ardından bölümle:

```bash
parted /dev/sda
```

**UEFI için örnek bölümleme:**

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 512 MB | EFI | `/efi` |
| `/dev/sda2` | 2 GB | swap | — |
| `/dev/sda3` | Kalan | ext4 | `/` |

```bash
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda3
mkswap /dev/sda2
swapon /dev/sda2
mount /dev/sda3 /mnt/gentoo
mkdir /mnt/gentoo/efi
mount /dev/sda1 /mnt/gentoo/efi
```

**BIOS/MBR için örnek bölümleme:**

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 2 GB | swap | — |
| `/dev/sda2` | Kalan | ext4 | `/` |

```bash
mkfs.ext4 /dev/sda2
mkswap /dev/sda1
swapon /dev/sda1
mount /dev/sda2 /mnt/gentoo
```

### 4.2 Stage3 Tarball

Stage3 arşivini [gentoo.org/downloads](https://www.gentoo.org/downloads/) sayfasından indir (dosya adı için 1. bölümdeki tabloya bak), sonra çıkar:

```bash
cd /mnt/gentoo
tar xpvf stage3-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

> Dizinde yalnızca indirdiğin tek Stage3 dosyası olmalı; birden fazla varsa `*` yerine tam adı yaz.

### 4.3 Portage Yapılandırması

`make.conf` dosyasını düzenle:

```bash
nano /mnt/gentoo/etc/portage/make.conf
```

```bash
COMMON_FLAGS="-O2 -pipe -march=native"
MAKEOPTS="-j4"
```

> 32-bit için `-march=i686` yaz. `MAKEOPTS` sayısı çekirdek sayını ve iş başına ~2 GB RAM'i geçmemeli. `make.conf` komut çalıştırmaz, bu yüzden `$(nproc)` yazma; sayıyı elle gir.

### 4.4 Chroot Ortamına Geç

```bash
cp --dereference /etc/resolv.conf /mnt/gentoo/etc/
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev
mount --bind /run /mnt/gentoo/run
mount --make-slave /mnt/gentoo/run
chroot /mnt/gentoo /bin/bash
source /etc/profile
export PS1="(chroot) ${PS1}"
```

### 4.5 Portage ve Profil

Depo anlık görüntüsünü indir, profil seç ve sistemi güncelle:

```bash
emerge-webrsync
eselect profile list
eselect profile set <numara>
emerge --ask --update --deep --newuse @world
```

> Başlangıç için OpenRC kullanan, güncel (`23.0` gibi) varsayılan profili seç; masaüstü profilleri de listede olur. Son komut uzun sürebilir.

### 4.6 Saat Dilimi ve Dil

```bash
echo "Europe/Istanbul" > /etc/timezone
emerge --config sys-libs/timezone-data
nano /etc/locale.gen
# tr_TR.UTF-8 UTF-8 ve en_US.UTF-8 UTF-8 satırlarını ekle
locale-gen
eselect locale list
eselect locale set <numara>
env-update && source /etc/profile && export PS1="(chroot) ${PS1}"
```

### 4.7 Kernel

Önce firmware ve `installkernel` kur:

```bash
mkdir -p /etc/portage/package.license
echo "sys-kernel/linux-firmware linux-fw-redistributable no-source-code" > /etc/portage/package.license/linux-firmware
emerge --ask sys-kernel/linux-firmware sys-kernel/installkernel
```

**Seçenek A — Hazır kernel (önerilen, hızlı):**

```bash
emerge --ask sys-kernel/gentoo-kernel-bin
```

**Seçenek B — Elle derleme:**

```bash
emerge --ask sys-kernel/gentoo-sources
eselect kernel list
eselect kernel set 1
cd /usr/src/linux
make menuconfig
make -j"$(nproc)"
make modules_install
make install
```

> İpucu: Seçenek B'de initramfs kullanmayacaksan kök dosya sistemi (ext4) ve disk sürücüsünü modül değil, **yerleşik** (`=y`) seç. 32-bit için `Processor type and features → High Memory Support` ayarını RAM'ine göre **4GB** veya **off** yap.

### 4.8 Sistem Yapılandırması

`fstab` için bölüm UUID'lerini öğren ve dosyayı düzenle:

```bash
blkid
nano /etc/fstab
```

```text
UUID=<EFI-UUID>   /efi  vfat  umask=0077        0 2
UUID=<SWAP-UUID>  none  swap  sw                0 0
UUID=<ROOT-UUID>  /     ext4  defaults,noatime  0 1
```

> BIOS kullanıyorsan `/efi` satırını yazma.

Bilgisayar adı ve klavye:

```bash
echo 'hostname="gentoo"' > /etc/conf.d/hostname
echo 'keymap="trq"' > /etc/conf.d/keymaps
```

### 4.9 Kullanıcı ve Temel Paketler

```bash
passwd
emerge --ask app-admin/sudo net-misc/networkmanager
rc-update add NetworkManager default
useradd -m -G wheel -s /bin/bash kullanici
passwd kullanici
visudo
# %wheel ALL=(ALL:ALL) ALL satırının başındaki # işaretini kaldır
```

### 4.10 Bootloader

**UEFI:**

```bash
echo 'GRUB_PLATFORMS="efi-64"' >> /etc/portage/make.conf
emerge --ask sys-boot/grub sys-boot/efibootmgr
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=Gentoo
grub-mkconfig -o /boot/grub/grub.cfg
```

**BIOS/MBR:**

```bash
echo 'GRUB_PLATFORMS="pc"' >> /etc/portage/make.conf
emerge --ask sys-boot/grub
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### 4.11 Çıkış ve Yeniden Başlat

```bash
exit
cd
umount -l /mnt/gentoo/dev{/shm,/pts,}
umount -R /mnt/gentoo
reboot
```

## 🔧 5. Kurulum Sonrası

Kullanıcınla giriş yap ve ağı kontrol et:

```bash
ping -c 3 gentoo.org
```

Wi-Fi için `nmtui` kullan. Güncelleme ve paket yönetimi komutları 7. bölümdeki tabloda.

## 📦 6. Uygulama Kurulumu

### 6.1 VLC

```bash
emerge --ask media-video/vlc
```

### 6.2 Metin Editörü

**64-bit:** VSCodium resmi depoda bulunur:

```bash
emerge --ask app-editors/vscodium
```

Microsoft'un VS Code'unu istersen `app-editors/vscode` paketi lisans onayı ister; paket test (`~amd64`) olarak işaretliyse Portage keyword ekleme talimatı verir:

```bash
echo "app-editors/vscode Microsoft-vscode" > /etc/portage/package.license/vscode
emerge --ask app-editors/vscode
```

Flatpak ile de kurulabilir:

```bash
emerge --ask sys-apps/flatpak
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
flatpak install flathub com.visualstudio.code
```

**32-bit:** VS Code için 32-bit sürüm yoktur; hafif bir editör kur:

```bash
emerge --ask app-editors/vim
```

veya:

```bash
emerge --ask app-editors/neovim
```

### 6.3 Git

```bash
emerge --ask dev-vcs/git
```

### 6.4 htop

```bash
emerge --ask sys-process/htop
```

## 📖 7. Portage Referansı

| Komut | Ne yapar? |
|-------|-----------|
| `emerge-webrsync` | Depo anlık görüntüsünü indirir (ilk kurulumda) |
| `emerge --sync` | Depo listesini günceller |
| `emerge --ask <paket>` | Paket kurar |
| `emerge --ask <p1> <p2>` | Birden fazla paket kurar |
| `emerge --ask --getbinpkg <paket>` | Resmi binary paketi indirip kurar (yalnızca amd64) |
| `emerge --ask --update --deep --newuse @world` | Sistemi günceller |
| `emerge --ask --depclean` | Gereksiz bağımlılıkları temizler |
| `emerge --search <kelime>` | Paket arar |
| `emerge --info` | Sistemin derleme ayarlarını gösterir |
| `emerge --ask --unmerge <paket>` | Paketi kaldırır |
| `emerge --ask --oneshot <paket>` | Paketi `@world`'e eklemeden kurar |
| `emerge --pretend <paket>` | Kurulumu simüle eder, gerçekten kurmaz |
| `revdep-rebuild` | Bozuk bağımlılıkları onarır (`app-portage/gentoolkit` gerekir) |

### Örnek Senaryo

**Paketi kaldır ve temizle:**

```bash
emerge --ask --unmerge vlc
emerge --ask --depclean
```

## 🐢 8. Eski Bilgisayarlar İçin

Gentoo, derleme süresi ve RAM açısından ağırdır. 2 GB altı RAM'de önerilmez.

- **`MAKEOPTS`:** `-j1` veya `-j2` kullan, aksi halde derleme sırasında bellek yetersizliği (OOM) olabilir.
- **Takas (swap) alanı:** En az 2 GB, büyük paketler için 4 GB önerilir.
- **Binary paketler:** Resmi binary paket sunucusu yalnızca amd64 ve arm64 için vardır; 64-bit'te `--getbinpkg` ile derleme süresini kısaltabilirsin. 32-bit'te derleme gerekir (saatler sürebilir).
- **Hafif masaüstü:** XFCE gibi hafif bir ortam seç.

> Gentoo, düşük donanımlı bilgisayarlar için **uygun değildir**; mümkünse binary paket ve hafif masaüstü kullan.

## 🛠️ 9. Sık Karşılaşılan Sorunlar

### 9.1 `emerge` "command not found"

Chroot ortamına geçmemiş olabilirsin. 4.4'teki adımları tekrar uygula.

### 9.2 Derleme sırasında bellek yetersizliği (OOM)

`MAKEOPTS` değerini düşür:

```bash
nano /etc/portage/make.conf
# MAKEOPTS="-j1" yap
```

### 9.3 `emerge --sync` çok yavaş

Alternatif ayna seç:

```bash
emerge --ask app-portage/mirrorselect
mirrorselect -i -o >> /etc/portage/make.conf
```

### 9.4 Kernel derlemesi başarısız

Eksik bağımlılık olabilir:

```bash
emerge --ask --oneshot sys-devel/bc sys-devel/flex sys-devel/bison
```

### 9.5 GRUB açılışta görünmüyor

UEFI'de EFI girişini, BIOS'ta bölüm tablosunu kontrol et:

```bash
efibootmgr -v
fdisk -l /dev/sda
```

Gerekirse 4.10'daki GRUB adımlarını tekrar uygula.

### 9.6 Stage3 bulunamıyor

Mimarini karıştırmış olabilirsin. Doğru dosya adı için 1. bölümdeki tabloya bak ve indirme sayfasında doğru mimari sekmesini seç.

---

**Son güncelleme:** 2026-11-27
**Kaynaklar:**
- [Gentoo Handbook (amd64)](https://wiki.gentoo.org/wiki/Handbook:AMD64)
- [Gentoo Handbook (x86)](https://wiki.gentoo.org/wiki/Handbook:X86)
- [Gentoo Wiki — Portage](https://wiki.gentoo.org/wiki/Portage)
- [Gentoo Wiki — Binary package quickstart](https://wiki.gentoo.org/wiki/Binary_package_quickstart)
