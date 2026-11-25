# Gentoo Kurulum Rehberi

Gentoo, her şeyi kaynak koddan derleyen ileri seviye bir Linux dağıtımıdır.
Paket yöneticisi **Portage**, komutu **`emerge`**'dir.

Bu sayfa iki ayrı rehber içerir:

- **64-bit (amd64):** Modern bilgisayarlar için.
- **32-bit (x86):** Eski veya 32-bit donanımlar için.

İhtiyacın olan mimarinin bölümüne git.

---

# 64-BIT (amd64) KURULUMU

## Gereksinimler (64-bit)

- 4 GB RAM (derleme için minimum)
- 20 GB disk alanı (kullanım amacına göre 50 GB+ önerilir)
- 4 GB USB bellek
- İnternet bağlantısı
- Sabır (derleme uzun sürebilir)

## 1. ISO İndirme (64-bit)

1. [gentoo.org/downloads](https://www.gentoo.org/downloads/) adresine git
2. **amd64** mimarisi için **minimal installation CD** indir (`install-amd64-minimal`)
3. Ventoy veya Rufus ile USB'ye yaz

> Gentoo, canlı masaüstü yerine **minimal** bir kurulum ortamı sunar. Kurulum terminalden yapılır.

## 2. Disk Hazırlığı (64-bit)

Diskini bölümle:

```bash
parted /dev/sda
```

Örnek bölümleme (UEFI):

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 512 MB | EFI | `/efi` |
| `/dev/sda2` | 2 GB | swap | — |
| `/dev/sda3` | Kalan | ext4 | `/` |

Dosya sistemlerini oluştur ve bağla:

```bash
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda3
mkswap /dev/sda2
swapon /dev/sda2
mount /dev/sda3 /mnt/gentoo
mkdir /mnt/gentoo/efi
mount /dev/sda1 /mnt/gentoo/efi
```

## 3. Stage3 Tarball (64-bit)

**amd64** Stage3 arşivini indir ve çıkar:

```bash
cd /mnt/gentoo
tar xpvf stage3-amd64-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

## 4. Portage Yapılandırması (64-bit)

`make.conf` dosyasını düzenle:

```bash
nano /mnt/gentoo/etc/portage/make.conf
```

Örnek:

```bash
COMMON_FLAGS="-O2 -pipe -march=native"
MAKEOPTS="-j$(nproc)"
```

## 5. Chroot Ortamına Geç (64-bit)

```bash
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --bind /run /mnt/gentoo/run
chroot /mnt/gentoo /bin/bash
source /etc/profile
export PS1="(chroot) ${PS1}"
```

## 6. Kernel Derleme (64-bit)

```bash
emerge --ask sys-kernel/gentoo-sources
cd /usr/src/linux
make menuconfig
make && make modules_install
make install
```

## 7. Bootloader (64-bit)

GRUB'u UEFI için kur:

```bash
emerge --ask sys-boot/grub
grub-install --target=x86_64-efi --efi-directory=/efi
grub-mkconfig -o /boot/grub/grub.cfg
```

## 8. Kurulum Sonrası (64-bit)

### 8.1 Kök Şifresi ve Kullanıcı

```bash
passwd
useradd -m -G wheel -s /bin/bash kullanici
passwd kullanici
```

### 8.2 Temel Paketler

```bash
emerge --ask app-admin/sudo net-misc/networkmanager
```

`sudo` için `wheel` grubunu yetkilendir:

```bash
visudo
# %wheel ALL=(ALL:ALL) ALL satırının başındaki # işaretini kaldır
```

### 8.3 Güncelleme

```bash
emerge --sync
emerge --ask --update --deep --newuse @world
```

## 9. Uygulama Kurulumu (64-bit)

### 9.1 VLC

```bash
emerge --ask media-video/vlc
```

### 9.2 Visual Studio Code

VS Code, Gentoo resmi deposunda **yoktur**. Üç seçenek vardır:

**Seçenek 1 — Flatpak (önerilen):**

```bash
emerge --ask sys-apps/flatpak
flatpak install flathub com.visualstudio.code
```

**Seçenek 2 — Overlay (VSCodium):**

```bash
emerge --ask app-eselect/eselect-repository
eselect repository enable guru
emerge --sync
emerge --ask app-editors/vscodium
```

**Seçenek 3 — Resmi `.tar.gz`:**

```bash
cd /opt
wget "https://code.visualstudio.com/sha/download?build=stable&os=linux-x64" -O code.tar.gz
tar -xzf code.tar.gz
ln -s /opt/VSCode-linux-x64/bin/code /usr/local/bin/code
```

### 9.3 Git

```bash
emerge --ask dev-vcs/git
```

### 9.4 htop

```bash
emerge --ask sys-process/htop
```

## 10. Portage Referansı (64-bit)

| Komut | Ne yapar? |
|-------|-----------|
| `emerge --sync` | Depo listesini günceller |
| `emerge --ask <paket>` | Paket kurar |
| `emerge --ask <p1> <p2>` | Birden fazla paket kurar |
| `emerge --ask --update --deep --newuse @world` | Sistemi günceller |
| `emerge --ask --depclean` | Gereksiz bağımlılıkları temizler |
| `emerge --search <kelime>` | Paket arar |
| `emerge --info <paket>` | Paket bilgisini gösterir |
| `emerge --ask --unmerge <paket>` | Paketi kaldırır |
| `emerge --ask --oneshot <paket>` | Paketi `@world`'e eklemeden kurar |
| `emerge --ask --pretend <paket>` | Kurulumu simüle eder, gerçekten kurmaz |
| `revdep-rebuild` | Bozuk bağımlılıkları onarır |

### Örnek Senaryolar (64-bit)

**Sistemi güncelle:**

```bash
emerge --sync
emerge --ask --update --deep --newuse @world
```

**Paketi kaldır ve temizle:**

```bash
emerge --ask --unmerge vlc
emerge --ask --depclean
```

## 11. Eski Bilgisayarlar İçin (64-bit)

Gentoo, derleme süresi ve RAM açısından ağırdır. 2 GB RAM'li bilgisayarlarda önerilmez.

- **`MAKEOPTS` ayarı:** Düşük RAM'li cihazlarda `-j1` veya `-j2` kullan, aksi halde derleme sırasında bellek yetersizliği (OOM) olabilir.
- **`COMMON_FLAGS`:** `-march=native` yerine `-march=x86-64` kullan (daha hızlı derleme).
- **Binary paketler:** Derleme yerine `--usepkg` ile binary paket kullanmayı dene.
- **Takas (swap) alanı:** En az 4 GB swap önerilir.

> Gentoo, düşük donanımlı bilgisayarlar için **uygun değildir**. Hafif bir masaüstü ortamı (XFCE gibi) tercih edilmeli.

## 12. Sık Karşılaşılan Sorunlar (64-bit)

### 12.1 `emerge` "command not found"

Chroot ortamına geçmemiş olabilirsin. `/mnt/gentoo` dizininde `chroot` komutunu tekrar çalıştır.

### 12.2 Derleme sırasında bellek yetersizliği (OOM)

`MAKEOPTS` değerini düşür:

```bash
nano /etc/portage/make.conf
# MAKEOPTS="-j2" veya "-j1" yap
```

### 12.3 `emerge --sync` çok yavaş

Alternatif ayna kullan:

```bash
mirrorselect -i -o >> /etc/portage/make.conf
```

### 12.4 Kernel derlemesi başarısız

Eksik bağımlılık olabilir:

```bash
emerge --ask --oneshot sys-devel/bc sys-devel/flex sys-devel/bison
```

### 12.5 GRUB açılışta görünmüyor

EFI bölümünün doğru bağlandığından emin ol:

```bash
efibootmgr -v
```

Gerekirse GRUB'u yeniden kur:

```bash
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=Gentoo
grub-mkconfig -o /boot/grub/grub.cfg
```

---

# 32-BIT (x86) KURULUMU

> Gentoo, 32-bit desteğini hâlâ sürdüren nadir dağıtımlardan biridir. Eski bilgisayarlar için uygundur.

## Gereksinimler (32-bit)

- 2 GB RAM (derleme için minimum)
- 10 GB disk alanı (kullanım amacına göre 20 GB+ önerilir)
- 2 GB USB bellek
- İnternet bağlantısı
- Sabır (32-bit donanımda derleme daha uzun sürer)

## 1. ISO İndirme (32-bit)

1. [gentoo.org/downloads](https://www.gentoo.org/downloads/) adresine git
2. **x86** mimarisi için **minimal installation CD** indir (`install-x86-minimal`)
3. Ventoy veya Rufus ile USB'ye yaz

> İndirme sayfasında **x86** sekmesini seçtiğinden emin ol. amd64 ile karıştırma.

## 2. Disk Hazırlığı (32-bit)

Diskini bölümle:

```bash
parted /dev/sda
```

Örnek bölümleme (BIOS/MBR — eski donanım):

| Bölüm | Boyut | Tür | Bağlama Noktası |
|-------|-------|-----|-----------------|
| `/dev/sda1` | 2 GB | swap | — |
| `/dev/sda2` | Kalan | ext4 | `/` |

> 32-bit eski donanımda genellikle UEFI yoktur, BIOS/MBR kullanılır.

Dosya sistemlerini oluştur ve bağla:

```bash
mkfs.ext4 /dev/sda2
mkswap /dev/sda1
swapon /dev/sda1
mount /dev/sda2 /mnt/gentoo
```

## 3. Stage3 Tarball (32-bit)

**x86 (i686)** Stage3 arşivini indir ve çıkar:

```bash
cd /mnt/gentoo
tar xpvf stage3-i686-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

> 32-bit Stage3 dosya adı `stage3-i686-*.tar.xz` şeklindedir (amd64 değil).

## 4. Portage Yapılandırması (32-bit)

`make.conf` dosyasını düzenle:

```bash
nano /mnt/gentoo/etc/portage/make.conf
```

Örnek (32-bit için `-march` daha muhafazakâr):

```bash
COMMON_FLAGS="-O2 -pipe -march=i686"
MAKEOPTS="-j2"
```

> 32-bit donanımda `-march=native` yerine `-march=i686` veya `-march=pentium4` kullan. `MAKEOPTS` değerini düşük tut (RAM az).

## 5. Chroot Ortamına Geç (32-bit)

```bash
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --bind /run /mnt/gentoo/run
chroot /mnt/gentoo /bin/bash
source /etc/profile
export PS1="(chroot) ${PS1}"
```

## 6. Kernel Derleme (32-bit)

```bash
emerge --ask sys-kernel/gentoo-sources
cd /usr/src/linux
make menuconfig
make && make modules_install
make install
```

> 32-bit için kernel yapılandırmasında `Processor type and features → High Memory Support` ayarını **4GB** veya **off** yap (donanıma göre).

## 7. Bootloader (32-bit)

BIOS/MBR için GRUB'u kur:

```bash
emerge --ask sys-boot/grub
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

> 32-bit eski donanımda `--target=i386-pc` kullanılır (UEFI değil).

## 8. Kurulum Sonrası (32-bit)

### 8.1 Kök Şifresi ve Kullanıcı

```bash
passwd
useradd -m -G wheel -s /bin/bash kullanici
passwd kullanici
```

### 8.2 Temel Paketler

```bash
emerge --ask app-admin/sudo net-misc/networkmanager
```

`sudo` için `wheel` grubunu yetkilendir:

```bash
visudo
# %wheel ALL=(ALL:ALL) ALL satırının başındaki # işaretini kaldır
```

### 8.3 Güncelleme

```bash
emerge --sync
emerge --ask --update --deep --newuse @world
```

## 9. Uygulama Kurulumu (32-bit)

### 9.1 VLC

```bash
emerge --ask media-video/vlc
```

### 9.2 Metin Editörü

32-bit sistemde VS Code resmi binary **yoktur**. Hafif editör önerilir:

```bash
emerge --ask app-editors/vim
```

veya:

```bash
emerge --ask app-editors/neovim
```

### 9.3 Git

```bash
emerge --ask dev-vcs/git
```

### 9.4 htop

```bash
emerge --ask sys-process/htop
```

## 10. Portage Referansı (32-bit)

| Komut | Ne yapar? |
|-------|-----------|
| `emerge --sync` | Depo listesini günceller |
| `emerge --ask <paket>` | Paket kurar |
| `emerge --ask <p1> <p2>` | Birden fazla paket kurar |
| `emerge --ask --update --deep --newuse @world` | Sistemi günceller |
| `emerge --ask --depclean` | Gereksiz bağımlılıkları temizler |
| `emerge --search <kelime>` | Paket arar |
| `emerge --info <paket>` | Paket bilgisini gösterir |
| `emerge --ask --unmerge <paket>` | Paketi kaldırır |
| `emerge --ask --oneshot <paket>` | Paketi `@world`'e eklemeden kurar |
| `emerge --ask --pretend <paket>` | Kurulumu simüle eder, gerçekten kurmaz |
| `revdep-rebuild` | Bozuk bağımlılıkları onarır |

### Örnek Senaryolar (32-bit)

**Sistemi güncelle:**

```bash
emerge --sync
emerge --ask --update --deep --newuse @world
```

**Paketi kaldır ve temizle:**

```bash
emerge --ask --unmerge vlc
emerge --ask --depclean
```

## 11. Eski Bilgisayarlar İçin (32-bit)

32-bit donanım zaten eski olduğu için derleme süresi uzundur.

- **`MAKEOPTS` ayarı:** `-j1` veya `-j2` kullan. 32-bit cihazlarda RAM genelde azdır.
- **`COMMON_FLAGS`:** `-march=native` yerine `-march=i686` kullan.
- **Binary paketler:** Derleme yerine `--usepkg` ile binary paket kullanmayı dene.
- **Takas (swap) alanı:** En az 2 GB swap önerilir.

> 32-bit Gentoo, sabır işidir. Derleme saatler sürebilir. Mümkünse binary paket kullan.

## 12. Sık Karşılaşılan Sorunlar (32-bit)

### 12.1 `emerge` "command not found"

Chroot ortamına geçmemiş olabilirsin. `/mnt/gentoo` dizininde `chroot` komutunu tekrar çalıştır.

### 12.2 Derleme sırasında bellek yetersizliği (OOM)

`MAKEOPTS` değerini düşür:

```bash
nano /etc/portage/make.conf
# MAKEOPTS="-j1" yap
```

### 12.3 `emerge --sync` çok yavaş

Alternatif ayna kullan:

```bash
mirrorselect -i -o >> /etc/portage/make.conf
```

### 12.4 Kernel derlemesi başarısız

Eksik bağımlılık olabilir:

```bash
emerge --ask --oneshot sys-devel/bc sys-devel/flex sys-devel/bison
```

### 12.5 GRUB açılışta görünmüyor

BIOS/MBR kurulumunu kontrol et:

```bash
fdisk -l /dev/sda
```

Gerekirse GRUB'u yeniden kur:

```bash
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### 12.6 32-bit Stage3 bulunamıyor

Gentoo, 32-bit için `stage3-i686-*.tar.xz` dosyasını sunar. `amd64` ile karıştırma. İndirme sayfasında **x86** sekmesini seç.

---

**Kaynaklar:**
- [Gentoo Handbook (amd64)](https://wiki.gentoo.org/wiki/Handbook:AMD64)
- [Gentoo Handbook (x86)](https://wiki.gentoo.org/wiki/Handbook:X86)
- [Gentoo Wiki — Portage](https://wiki.gentoo.org/wiki/Portage)
- [Gentoo Wiki — Installation](https://wiki.gentoo.org/wiki/Installation)
