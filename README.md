# headless
#### volume proc

**disk layout root**
| partition | list  | group | name |  mount                    | format |
| --------- | ----  | ----- | ---- |  -------------------------| ------ |
| 2         | 1     | proc  | root |  /mnt                     | ext4   |
| 2         | 3     | proc  | vars |  /mnt/var                 | ext4   |
| 2         | 6     | proc  | vlog |  /mnt/var/log             | ext4   |
| 2         | 7     | proc  | vaud |  /mnt/var/log/audit       | ext4   |
| 2         | 8     | proc  | vtmp |  /mnt/var/tmp             | ext4   |
| 2         | 9     | proc  | vpac |  /mnt/var/cache/pacman    | ext4   |
| 2         | 8     | proc  | flat |  /mnt/var/lib/flatpak     | ext4   |
| 2         | 9     | proc  | swap |  [swap]                   | ext4   |

**disk layout data**

| partition | list  | group | name |  mount                    | format |
| --------- | ----  | ----- | ---- |  -------------------------| ------ |
| 2         | 1     | data  | home |  /mnt/home                | ext4   |
| 2         | 1     | data  | nets |  /mnt/var/lib/telnet      | ext4   |


**1. encrypt disk**
```
cryptsetup luksFormat --sector-size=4096 /dev/sda2
```
```
cryptsetup luksFormat --sector-size=4096 /dev/sda3
```
```
cryptsetuo luksOpen /dev/sda2 [nama]
```
```
cryptsetuo luksOpen /dev/sda3 [nama]
```
**2. create lvm**
```
pvcreate /dev/mapper/proc
```
```
vgcreate proc /dev/mapper/proc
```
```
pvcreate /dev/mapper/data
```
```
vgcreate data /dev/mapper/data
```

**3. root**
```
lvcreate -L 20G proc -n root
```
```
mkfs.ext4 -b 4096 /dev/proc/root
```
```
mount /dev/proc/root /mnt
```
**4. vars**
```
lvcreate -L 5G proc -n vars
```
```
mkfs.ext4 -b 4096 /dev/proc/vars
```
```
mkdir /mnt/var
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/proc/vars /mnt/var
```
**5. vlog**
```
lvcreate -L 2G proc -n vlog
```
```
mkfs.ext4 -b 4096 /dev/proc/vlog
```
```
mkdir /mnt/var/log
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/proc/vlog /mnt/var/log
```
**6. vaud**
```
lvcreate -L 512M proc -n vaud
```
```
mkfs.ext4 -b 4096 /dev/proc/vaud
```
```
mkdir /mnt/var/log/audit
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/proc/vaud /mnt/var/log/audit
```
**7. vtmp**
```
lvcreate -L 2G proc -n vtmp
```
```
mkfs.ext4 -b 4096 /dev/proc/vtmp
```
```
mkdir /mnt/var/tmp
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/proc/vtmp /mnt/var/tmp
```
**8. vpac**
```
lvcreate -L 2G proc -n vpac
```
```
mkfs.ext4 -b 4096 /dev/proc/vpac
```
```
mkdir -p /mnt/var/cache/pacman
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/proc/vpac /mnt/var/cache/pacman
```
**9. flat**
```
lvcreate -L 10G proc -n flat
```
```
mkfs.ext4 -b 4096 /dev/proc/flat
```
```
mkdir -p /mnt/var/lib/flatpak
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/proc/vpac /mnt/var/lib/flatpak
```
**10. swap**
```
lvcreate -L 4G proc -n swap
```
```
mkswap /dev/proc/swap
```
```
swapon /dev/proc/swap
```
**11. create lvm**
```
pvcreate /dev/mapper/data
```
```
vgcreate data /dev/mapper/data
```

**12. home**
```
lvcreate -L 10G data -n home
```
```
mkfs.ext4 -b 4096 /dev/data/home
```
```
mkdir /mnt/home
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/data/home /mnt/home
```
**13. nets**
```
lvcreate -L 2G data -n nets
```
```
mkfs.ext4 -b 4096 /dev/data/nets
```
```
mkdir /mnt/var/lib/telnet
```
```
mount -o rw,nodev,noexec,nosuid,relatime /dev/data/nets /mnt/var/lib/telnet
```
**14.volume boot**
```
mkfs.vfat -F32 -S 4096 -n BOOT [ partition path ]
```
```
mkdir /mnt/boot
```
```
mount -o uid=0,gid=0,fmask=0077,dmask=0077 [ path boot partition ] /mnt/boot
```
**15.installation**
```
pacstrap /mnt linux-hardened linux-hardened-headers linux-firmware-atheros linux-firmware-intel linux-firmware-nvidia base base-devel firejail lvm2 mkinitcpio less git iwd firewalld hyprland uwsm hypridle hyprshot hyprpicker hyprlock hyprpolkitagent xdg-desktop-portal-hyprland qt5-waylan qt6-wayland wl-clipboard cliphist mailcap mako waybar wofi pipewire pipewire-pulse pipewire-jack wireplumber pamixer mpd mpc mpv yt-dlp libsecret flatpak gnome-keyring kitty ttf-jetbrains-mono-nerd ttf-droid superfile libpwquality perl-image-exiftool cracklib apparmor qrencode btop rsync go hugo neovim brightnessctl intel-ucode
```
**16. fstab**
```
genfstab -U /mnt > /mnt/etc/fstab
```

**17. network**
 - ethernet
```
cp /etc/systemd/network/* /mnt/etc/systemd/network/
```

### chroot
```
arch-chroot /mnt
```
**initramfs configuration**
```
cd /boot
```
```
cd /kernel
```
```
rm initramfs-linux-hardened.img
```
```
cd ..
```
```
rm initramfs-linux-hardened.img intel-ucode.img vmlinuz-linux-hardened
```
```
cd ..
```


## 5. postconfig

### locatime 
```
ln -sf /usr/share/zoneinfo/Asia/Jakarta /etc/localtime
```
```
hwclock --systohc
```

## locale 
```
nvim /etc/locale.conf
```
> uncommanting en_US
```
locale-gen && locale > /etc/locale.conf
```
### hostname 
```
echo 'nama_hostname' > /etc/hostname
```
### user
```
useradd -d /var/lib/telnet/ -u 23 telnet
```
```
passwd telnet
```
```
nvim /etc/passwd
```
> change uid and gid to 23 and move it up for trick the cracker
```
nvim /etc/group
```
> change uid and gid to 23
```
chown -R telnet:telnet /var/lib/telnet
```
```
useradd -m [user]
```
```
passwd [user]
```
```
nvim /etc/sudoers
```
> enter [user] ALL=(ALL:ALL) ALL under root ALL=(ALL:ALL) ALL

### kernel parameter
```
mkdir /etc/cmdline.d
```
```
 touch /etc/cmdline.d/{01-boot.conf,02-mods.conf,03-secs.conf,04-perf.conf,05-nets.conf,06-misc.conf}
```
```
echo "rd.luks.name=$(blkid -s UUID -o value /dev/[proc physical partition name)=root root=/dev/proc/root" > /etc/cmdline.d/01-boot.conf
```
```
echo "data UUID=$(blkid -s UUID -o value /dev/sda3) none" >> /etc/crypttab
```
```
mv /etc/mkinitcpio.conf /etc/mkinitcpio.d/default.conf
```
```
nvim /etc/mkinitcpio.d/default.conf
```
> in HOOKS add sdencrypt lvm2
```
touch /etc/vconsole.conf
```
```
nvim /etc/mkinitcpio.d/linux-hardened.preset
```
```
# mkinitcpio preset file for the 'linux-zen' package

ALL_config="/etc/mkinitcpio.d/default.conf"
ALL_kver="/boot/kernel/vmlinuz-linux-hardened"
ALL_kerneldest="/boot/kernel/vmlinuz-linux-hardened"

PRESETS=('default')
#PRESETS=('default' 'fallback')

#default_config="/etc/mkinitcpio.conf"
#default_image="/boot/initramfs-linux-zen.img"
default_uki="/boot/efi/linux/arch-linux-hardened.efi"
#default_options="--splash /usr/share/systemd/bootctl/splash-arch.bmp"

#fallback_config="/etc/mkinitcpio.conf"
#fallback_image="/boot/initramfs-linux-zen-fallback.img"
#fallback_uki="/efi/EFI/Linux/arch-linux-zen-fallback.efi"
#fallback_options="-S autodetect"
```
```
mkinitcpio -P
```
**fstab**
```
nvim /etc/fstab
```
> commanting boot
** enable wifi **
```
systemctl enable systemd-networkd.socket
```
**user config**
```
cd /tmp
```
```
cd conf
```
```
cp -fr .bash* .config/ /home/user/
```
```
nvim /home/user/.bash_profile
```
