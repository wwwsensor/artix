# Preamble
- This guide is for a dinit install.
- You must know to partition, format and mount drives.
- To set keyboard layout for shell session: _loadkeys us-acentos_
- Code style language
  - Brackets: Contingent
  - Arrows: Variable
  - Bars: Optional
- [How to manage Wi-Fi](https://wiki.archlinux.org/title/Network_configuration/Wireless)
- [Dinit docs](https://github.com/davmac314/dinit)
# Install
### 1 - Setup drives
System uses UEFI, which W1X needs, if _/sys/firmware/efi_ dir exists.
### 2 - Install base system
_ucode_ packages are not free SW. _os-prober_ detects other OSs at Grub deployment.
```
basestrap <partition> opendoas man artix-archlinux-support base linux \
                      connman-dinit efibootmgr grub neovim base-devel \
                      linux-firmware [ os-prober ] [ amd/intel-ucode ]
fstabgen -U <partition> ><partition>/etc/fstab
artix-chroot <partition> bash
```
### 3 - Configure base system
Uncomment _en\_US.UTF-8 UTF-8_ for en\_US in _/etc/locale.gen_ then run the commands:
```
ln -sf /usr/share/zoneinfo/<Region>/<City> /etc/localtime
ln -sf /etc/dinit.d/connmand /etc/dinit.d/boot.d/.
echo permit nopass :wheel >/etc/doas.conf
echo KEYMAP=<keymap> >/etc/vconsole.conf
echo LANG=en_US.UTF-8 >/etc/locale.conf
echo <hostname> >/etc/hostname
hwclock --systohc
[ useradd -mG <group> <user> ]
passwd [ <user> ]
chsh [ <user> ]
locale-gen
ln -sf $(which doas) /usr/bin/sudo
```
_/etc/hosts_ :
```
127.0.0.1  localhost
::1        localhost
127.0.1.1  <hostname>.localdomain <hostname>
```
### 4 - Configure Grub (optional)
_/etc/default/grub_
### 5 - Install Grub
```
# For BIOS systems
grub-install --recheck <drives with OSs>
# For UEFI systems
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
# Always
grub-mkconfig -o /boot/grub/grub.cfg
```
### 6 - Autologin tty (optional)
_/etc/dinit.d/config/agetty-tty1.conf_ :
```
#!/bin/sh
GETTY_ARGS="--autologin <user>"
```
### 6 - End
```
exit; reboot
```
