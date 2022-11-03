---
layout: page
title: "Arch-Linux"
permalink: /archInstall
---
Downloading Arch
-	Downloaded Arch ISO from the URL provided
-	Verified SHA256 file hash with the following command in PowerShell
    -   `Get-FileHash <file path>`
-	Loaded ISO into vmware and started the install

INSTALLING ARCH
-	I did not need to change the keyboard layout as it was already set to US.
-	`ls /sys/firmware/efi/efivars` returns nothing. System booted in BIOS mode. This is an important note to keep track of. For the rest of the install, we are assuming     that we are installing in BIOS MBR mode.
-	Ensured that network adapter was bridged directly to my network card. NAT is not working in my vmware for some reason. Unsure. IP successfully obtained through DHCP in live image when running ip addr. Pinging arch linux site was successful. 
-	Checked timezone using timedatectl status. Confirmed that system clock was synced with UTC time.
-	Partitioning the disk…
    -	fdisk -l listed one disk and one partition. 20GB total.
    -	`fdisk /dev/sda`
    -   Option d – delete all existing partitions. Just to be safe..
    -	Option n – create a new partition
        -	Option e – create swap partition
        -	First sector 2048
        -   Last sector 2000000 – little bit bigger than 512mb to be safe.
        -	Option t – set partition type
        -	Select partition 2 
        -	Hexcode 82 for linux swap
    -	Option n – create new partition
        -	Option p
        -	Partition number 1
        -	Left defaults to take up rest of disk/dev
    -	Option w to write table to disk and exit
-	Create file systems and swap file on partitions
    -	`mkfs.ext4 /dev/sda1` – create root file system with ext4
    -	Reboot now so arch will recognize swap partition
    -	`mkswap /dev/sda2`
-	Mount partitions
    -	`mount /dev/sda1 /mnt`
    -	`swapon /dev/sda2 – enables swap file`
-	Install essential packages
        -	`pacstrap -K /mnt base linux linux-firmware`
            -	This took some time
-   Configure system
    -	Generate an fstab with: `genfstab -U /mnt >> /mnt/etc/fstab`
        -	Cat this new file to ensure it reflects the disk correctly
    -	chroot into new directory with: `arch-chroot /mnt`
    -	Set timezone with : `ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime`
    -	Generate /etc/adjtime with: `hwclock –systohc`
    -	Install vim with: `pacman -S vi`
    -	Edit locale.gen file and uncomment the en_us and any other locale needed. I had to manually add this, as it wasn’t there.
    -	Run locale-gen command to generate all uncommented locales.
    -	Create local.conf file with: `touch /etc/locale.conf`
    -	Edit locale.conf file: add lang variable with: `vi /etc/locale.conf` and add `LANG=en_US.UTF-8`
    -	Didn’t change keyboard layout, so didn’t need to make changes persistent in vconsole.conf

NETWORK CONFIG
-	Create hostname file. `touch /etc/hostname` to create it, then `vi /etc/hostname` and add whatever you want the hostname to be. I used "archsux"
-	DHCP provided all information, and I don’t care about deprecated nettools commands. Left defaults for now.

ROOT PASSWORD
-	Run passwd to change root password.

BOOTLOADER
-	`pacman -S grub`
-	`grub-install –target=i386-pc /dev/sda`
-	`grub-mkconfig -o /boot/grub/grub.cfg` : generates grub config file.

FINISHING UP
-	Install sudo with: `pacman -S sudo`
-	Install dhcpcd with: `pacman -S dhcpcd`. THIS IS CRUCIAL TO MAINTAIN SEEMLESS NETWORK CONNECTIVITY UPON RESTART!
-	Start dhcpcd service so it starts automatically on restart with: `sudo systemctl enable dhcpcd`
-	Exit the chroot environment with: `exit`
-	Reboot 
-	Remove install media

AFTER INSTALL…
-	Login as root
-	Bring network interface up with: `ip link set dev ens33 up`
    - Network connectivity SHOULD work if you installed dhcpcd. 
-	Install openssh. `pacman -S openssh`.
-	Install LXDE with: `pacman -Sy –noconfirm lxde lxdm`
-	Enable lxdm.service with: `systemctl enable lxdm`
-	Set lxdm as default session with: `sudo sed -I /etc/lxdm/lxdm.conf -e ‘s;^# session=/usr/bin/startlxde;session=/usr/bin/startlxde;g’`
-	Restart. Once restarted you should have a desktop environment.
-	Installed man so I can read documentation with: `pacman -S man-db`.
-	Create Codi and sean Account with: `useradd -m codi` and `useradd -m sean`
-	Change codi and sean password with: `passwd codi` and `passwd Sean`
    -	Enter provided password 
-	Give codi and my account sudo by editing sudoers file with visudo. Add these lines: 
    -	`codi ALL=(ALL:ALL) ALL`
    -	`sean ALL=(ALL:ALL) ALL`
    -	Look at the existing root entry for an example
-	Install zsh
    -	`pacman -S zsh`
-	Change default shell of sean user to zsh
    -	In `/etc/passwd` file change `/bin/bash` to `/bin/zsh`
    -	Switch user to sean: `su sean`
    -	Follow prompts to setup zsh.
    -	Left configuration file blank.
-	Change bash colors
    -	Append this to .bashrc file:
    -	 ![image](https://user-images.githubusercontent.com/70538441/197404784-90945a73-7417-40ec-84cf-68acb685b8f4.png)
    -	This makes part of the shell red.
-	While in .bashrc, add alias for `ls -lah` and `sudo` as seen below
    -	 ![image](https://user-images.githubusercontent.com/70538441/197404805-b9cd84b0-e24b-4276-a2ff-bb217f74ddcb.png)

COMPLETED INSTALL
![image](https://user-images.githubusercontent.com/70538441/199751372-8ba5ac97-c8ef-4ec8-8878-e7962c3aa430.png)

