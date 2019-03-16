#Arch Linux Installation steps

---

## Instaling the OS

###Copy arch in a usb with
```bash
dd if=/path/to/iso.iso of=/path/to/usb status="progress"
```

###Set timedate 
timedatectl set-ntp true

###Pluggin usb in pc and enter arch to create partitions
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

###check the new partitons 
lsblk

###make filesystems
```bash
mkfs.ext4 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
```

###mount the root partitions in the system  
```bash
mount /dev/sda3 /mnt
```

###create folder for the rest of the partitions
```bash
mkdir /mnt/home
mkdir /mnt/boot

mount /dev/sda1 /mnt/boot
mount /dev/sda4 /mnt/home
```

###use pacstrap to install arch in the mounted drive with the some extra tools
```bash
pacstrap /mnt base base-devel vim 
```

###create an fstab file with all the partitions to be auto loaded on startup
```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

###jump inside the arch installation files in your drive
```bash
arch-chroot /mnt
```

###install a network manager, arch does not install it so no internet after reboot 
```bash
pacman -S networkmanager
```

###make the system start network manager on boot
```bash
systemctl enable NetworkManager
```

###install grub to manage the linux OS's
```bash
pacman -S grub
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

###setup password
```bash
passwd
```

###setup locals
```bash
vim /etc/locale.gen #uncomment the your locals
vim /etc/locale.conf #set the LANG variable
#LANG=en-US.UTF-8
ln -sf /usr/share/zoneinfo/<country>/<timezone> /etc/localtime
#ln -sf /usr/share/zoneinfo/Mexico/BajaNorte /etc/localtime
```

###set a hostname
```bash
vim /etc/hostname #write the name
```

###reboot
```bash
exit #go to the usb system
umount -R /mnt #unmount everything
reboot #and then remove the usb
```

###Update pacman
```bash
pacman -Syu
```

###install fonts
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

## Create the xinit file in the home of the user
```bash
vim ~/.xinitrc
#write the code that will run the graphic environment
exec i3
```

### Install i3
```bash
pacman -S i3-wm dmenu
```

