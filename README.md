# Arch Linux Installation steps

---

## Instaling the OS

### Copy arch in a usb with
```bash
dd if=/path/to/iso.iso of=/path/to/usb status="progress"
```

### Set timedate 
timedatectl set-ntp true

### Pluggin usb in pc and enter arch to create partitions
```bash
lsblk	#check partitons
fdisk /dev/sda
p #print partitions
n #new partition

#boot partition +200M
#swap partition +{1.5xRAM}
#root partition +15G or more
#home partition +open

w #write to disk
```

### check the new partitons 
lsblk

### make filesystems
```bash
mkfs.ext4 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
```

### mount the root partitions in the system  
```bash
mount /dev/sda3 /mnt
```

### create folder for the rest of the partitions
```bash
mkdir /mnt/home
mkdir /mnt/boot

mount /dev/sda1 /mnt/boot
mount /dev/sda4 /mnt/home
```

### use pacstrap to install arch in the mounted drive with the some extra tools
```bash
pacstrap /mnt base base-devel vim 
```

### create an fstab file with all the partitions to be auto loaded on startup
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

### jump inside the arch installation files in your drive
```bash
arch-chroot /mnt
```

### install a network manager, arch does not install it so no internet after reboot 
```bash
pacman -S networkmanager
```

### make the system start network manager on boot
```bash
systemctl enable NetworkManager
```

### install grub to manage the linux OS's
```bash
pacman -S grub
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

If you have a system with EFI you will need to do this instead:

```bash
# install the efibootmgr package
sudo pacman -S grub efibootmgr
# Mount the EFI partition in /boot/efi (default grub loction for efi files)
sudo mount /dev/sda2 /boot/efi
# If this is the first time setting EFI do this:
sudo grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi --removable
# Else check the EFI/Grub config i ok
sudo grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
# Build/apply your grub config
sudo grub-mkconfig -o /boot/grub/grub.cfg
# Reboot your machine
sudo reboot 
```

### setup password
```bash
passwd
```

### setup locals
```bash
vim /etc/locale.gen #uncomment the your locals
vim /etc/locale.conf #set the LANG variable
#LANG=en-US.UTF-8
ln -sf /usr/share/zoneinfo/<country>/<timezone> /etc/localtime
#ln -sf /usr/share/zoneinfo/Mexico/BajaNorte /etc/localtime

#you have to set the LC_* variable in /etc/locale.conf
locale | cat > /etc/locale.conf
#it is not necesary but can help to solve problems
vim /etc/profile
#add this at the end
export LC_ALL=
#export LC_ALL="en_US.UTF_8"
```


### set a hostname
```bash
vim /etc/hostname #write the name
```

### reboot
```bash
exit #go to the usb system
umount -R /mnt #unmount everything
reboot #and then remove the usb
```

### Update pacman
```bash
pacman -Syu
```

### install fonts
```bash
pacman -S noto-fonts
```

---

## Creating a sudo user
```bash
useradd -m -g wheel <username>
#useradd -m -g wheel ruben
passwd <username>
#passwd ruben
```

## Give the user sudo access
```bash
vim /etc/sudoers
#uncomment the lines
%wheel ALL=(ALL) ALL
```

---
## Installing and configuring Window Manager

## Install Xorg server
```bash
pacman -S xorg-server xorg-xinit
```

### Install i3 window manager, status bar, terminal and menu
```bash
pacman -S i3-gaps i3-status rxvt-unicode dmenu
```

## Create the xinit file in the home of the user
```bash
vim ~/.xinitrc
#write the code that will run the graphic environment
exec i3
```

Once i3 is started it will allow you to create a config file.
Edit the created config file saved in ~/.config/i3/config

```bash
vim ~/.config/i3/config
# add gaps with
gaps inner <px>
gaps outer <px>
# check the config file to see the keybinding
```


## Install a web browser
The two mayor web browsers in the official arch community repo are
- chromium
- Firefox

```bash
sudo pacman -S chromiun 
sudo pacman -S firefox
```

## Install display manager
```bash
sudo pacman -S lightdm lightdm-gtk-greeter
sudo systemctl enable lightdm.service
```


## Install Variety Wallpaper Manager
```bash
vim ~/.bashrc
## Add the system variable to tell you are using i3 if not defined already
export XDG_CURRENT_DESKTOP=i3

# save and install variety, feh is needed by variety
sudo pacman -S feh variety
# if using display manager you need to start variety with the xserver
vim ~/.xprofile
# add variety to the xprofile
variety &   #<--- the & is necesary 
```
