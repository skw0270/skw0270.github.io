---
layout: page
title: "Arch-Linux"
permalink: /archInstall
---
Downloading Arch
-	Downloaded Arch ISO from the URL provided
-	Verified SHA256 file hash with the following command in PowerShell
    -   Get-FileHash <file path>
-	Loaded ISO into vmware and started the install

INSTALLING ARCH
-	I did not need to change the keyboard layout as it was already set to US.
-	Ls /sys/firmware/efi/efivars returns nothing. System booted in BIOS mode. This is an important note to keep track of. For the rest of the install, we are assuming     that we are installing in BIOS MBR mode.
-	Ensured that network adapter was bridged directly to my network card. NAT is not working in my vmware for some reason. Unsure. IP successfully obtained through         DHCP in live image when running ip addr. Pinging arch linux site was successful. 
-	Checked timezone using timedatectl status. Confirmed that system clock was synced with UTC time.
-	Partitioning the disk…
    o	Fdisk -l listed one disk and one partition. 20GB total.
    o	Fdisk /dev/sda
    o	Option d – delete all existing partitions. Just to be safe..
    o	Option n – create a new partition
        o	Option e – create swap partition
        o	First sector 2048
        o	Last sector 2000000 – little bit bigger than 512mb to be safe.
        o	Option t – set partition type
        o	Select partition 2 
        o	Hexcode 82 for linux swap
    o	Option n – create new partition
        o	Option p
        o	Partition number 1
        o	Left defaults to take up rest of disk/dev
    o	Option w to write table to disk and exit
-	Create file systems and swap file on partitions
    o	Mkfs.ext4 /dev/sda1 – create root file system with ext4
    o	Reboot now so arch will recognize swap partition
    o	Mkswap /dev/sda2
-	Mount partitions
    o	Mount /dev/sda1 /mnt
    o	Swapon /dev/sda2 – enables swap file
-	Install essential packages
        o	Pacstrap -K /mnt base linux linux-firmware
            o	This took some time
-   Configure system
    o	Generate an fstab with: Genfstab -U /mnt >> /mnt/etc/fstab 
        o	Cat this new file to ensure it reflects the disk correctly
    o	chroot into new directory with: Arch-chroot /mnt
    o	Set timezone with : ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
    o	Generate /etc/adjtime with: hwclock –systohc
    o	Install vim with: Pacman -S vi
    o	Edit locale.gen file and uncomment the en_us and any other locale needed. I had to manually add this, as it wasn’t there.
    o	Run locale-gen command to generate all uncommented locales.
    o	Create local.conf file with: touch /etc/locale.conf
    o	Edit locale.conf file: add lang variable with: Vi /etc/locale.conf and add LANG=en_US.UTF-8
    o	Didn’t change keyboard layout, so didn’t need to make changes persistent in vconsole.conf

NETWORK CONFIG
-	Create hostname file. Touch /etc/hostname. Vi /etc/hostname and add whatever you want the hostname to be. I used "archsux"
-	DHCP provided all information, and I don’t care about deprecated nettools commands. Left defaults for now.

ROOT PASSWORD
-	Run passwd to change root password.

BOOTLOADER
-	Pacman -S grub
-	Grub-install –target=i386-pc /dev/sda
-	Grub-mkconfig -o /boot/grub/grub.cfg: generates grub config file.

FINISHING UP
-	Install sudo with: pacman -S sudo
-	Install dhcpcd with: pacman -S dhcpcd. THIS IS CRUCIAL TO MAINTAIN SEEMLESS NETWORK CONNECTIVITY UPON RESTART!
-	Start dhcpcd service so it starts automatically on restart with: sudo systemctl enable dhcpcd
-	Exit the chroot environment with: Exit
-	Reboot 
-	Remove install media

AFTER INSTALL…
-	Login as root
-	Bring network interface up with: Ip link set dev ens33 up
    o Network connectivity SHOULD work if you installed dhcpcd. 
-	Install openssh. Pacman -S openssh.
-	Install LXDE with: pacman -Sy –noconfirm lxde lxdm
-	Enable lxdm.service with: systemctl enable lxdm
-	Set lxdm as default session with: sudo sed -I /etc/lxdm/lxdm.conf -e ‘s;^# session=/usr/bin/startlxde;session=/usr/bin/startlxde;g’
-	Restart. Once restarted you should have a desktop environment.
-	Installed man so I can read documentation with: Pacman -S man-db.
-	Create Codi and sean Account with: Useradd -m codi and useradd -m sean
-	Change codi and sean password with: Passwd codi and Passwd Sean
    o	Enter provided password 
-	Give codi and my account sudo by editing sudoers file with visudo. Add these lines: 
    o	codi ALL=(ALL:ALL) ALL
    o	sean ALL=(ALL:ALL) ALL
    o	Look at the existing root entry for an example
-	Install zsh
    o	Pacman -S zsh
-	Change default shell of sean user to zsh
    o	In /etc/passwd file change /bin/bash to /bin/zsh
    o	Switch user to sean: su sean
    o	Follow prompts to setup zsh.
    o	Left configuration file blank.
-	Change bash colors
    o	Append this to .bashrc file:
    o	 ![image](https://user-images.githubusercontent.com/70538441/197404784-90945a73-7417-40ec-84cf-68acb685b8f4.png)
    o	This makes part of the shell red.
-	While in .bashrc, add alias for ls -lah and sudo as seen below
    o	 ![image](https://user-images.githubusercontent.com/70538441/197404805-b9cd84b0-e24b-4276-a2ff-bb217f74ddcb.png)

