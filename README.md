## Arch Linux VM Installation Documentation
### By Sajid Rahman

This .md chronicles the process of installing Arch Linux via VMWare, and mainly followed the Arch Install Wiki, 
with a prioritization on the basic commands and steps required to create a stable install of Arch Linux.

## Beginning the Arch Installation
### 1. Downloading the ISO image of your Arch Install

Through the [Arch Install wiki](https://archlinux.org/download/), navigate to the list of American mirrors and select any mirror:
For example, I downloaded it from the /archlinux/iso/2021.10.01/ through the ACM mirror.

It is necessary to ensure that the .iso being used for the Arch install is recent; you can do this by utilizing a Windows
command shell and checking with **_certutilhashfile archlinux-2021.10.01-x86_64.iso MD5_** which will print the MD5's.

### 2. Setting up VMWare for the Arch Install

Setting up a Arch Linux on VMWare is straightforward:
1. Click New Machine
2. Go through the wizard/setup process
3. Set the maximum disk size for Arch Linux to be 20 GB
4. Customize the hardware from the default 768 mb to become 2 GB (otherwise the installation process will fail due to insufficient vram)

After going through the wizard, it is necessary to go into where the configuration of Arch Linux is saved and edit the .vmx file with
a text editor to alter the boot settings of the VM:

- firmware = "efi" - correct quotation marks 
- bios.bootdelay = 5000

You can check that these settings are correct when you boot up the VM with **_ls /sys/firmware/efi/efivars_**, which should echo a path
directory IF efi has been successfully enabled by editing the .vmx file; if not, nothing will print as efi isn't enabled and the path
was never created.

### 3. Disk Partitions

The formatting of the disk partitions is necessary to mount a partition later on; not to mention a dedicated EFI partition for
a boot loader to operate off of later on once the install is completed. This involves separately mounting one partition as the
EFI mount and the other as the dedicated mount.

- _**cfdisk /dev/sda**_ - cfdisk is used to create/modify/delete any form of partition in a dedicated GUI, which is a partition table

Two system partitions are created:

- SDA1; EFI system partition
- SDA2; Regular file system

### 3.1. Formatting Partitions and Mounting

The formatting of the disk partitions is necessary to mount a partition later on; not to mention a dedicated EFI partition for
a boot loader to operate off of later on once the install is completed. This involves separately mounting one partition as the
EFI mount and the other as the dedicated mount.

- _**mkfs.ext4 /dev/sda2**_ - Formats the partition with a that of an Ext4 file system on /dev/root_partition

- _**mount /dev/sda2 /mnt**_ - Mounts the main partition that is 16G
- _**mkdir -p /mnt/boot/efi**_ - Creates the efi boot directory that will be mounted in the EFI system partition
- _**sudo mount /dev/sda1 /boot/efi**_ - Mounts the efi boot directory onto the EFI system partition

### 4 Updating the System Clock

This one is pretty self explanatory and directly from the Arch installation wiki; in order for the system 
to not be internally confused about what time it is or even what day it is - the system clock is essentially
synchronized.

- _**timedatectl set-ntp true**_ - Automatically sets/syncs the timezone and date for the system clock

### 5. Importation of Installation Files

- _**pacstrap /mnt base linux linux-firmware**_

- _**pacstrap /mnt base linux linux-firmware nano grub dhcpcd efibootmgr**_

### 6. Configuring the System:

Once the installation of the essential packages are complete from the disk onto the VM, it'll be necessary to configure the system.
This is done by generation of an fstab file, a file that defines how the partitions within the disk are mounted within the file system.
Utilizing the genfstab command and a -U as its flag to sort by labels, we direct its standard output into a new file within where the
configuration files are located (/etc) and create a new file called "fstab" that contains the aforementioned information.

- _**genfstab -U /mnt >> /mnt/etc/fstab**_
- _**cat /mnt/etc/fstab**_

#### changing root directory (Cont):
One of the most important steps after beginning the finalization of the installation process, is to change the location Root within the system,
which is also known as change root. This is relevant to our lecture in class about containers and was even mentioned by Professor West. 
Once the root directory is changed, its' also important that you should also set a new password for root.

- _**arch-chroot /mnt**_

#### Configuring; time zone (Cont).
- _**ln -sf /usr/share/zoneinfo/Region/City /etc/localtime**_

- _**hwclock --systohc**_ - sets system time to UTC

#### localization

- **_nano /etc/locale.gen**_
- uncomment en_US.UTF-8 UTF-8

**_locale-gen**_
**_touch /etc/locale.conf**_
**_nano /etc/locale.conf**_
paste in LANG=en_US.UTF-8

#### network configuration
- _**touch /etc/hostname**_
- _**nano /etc/hostname**_
typed in 24sajidrahman as hostname

- _**nano /etc/hosts**_ - 
127.0.0.1	localhost
::1		localhost
127.0.1.1	24sajidrahman
 
#### initramfs 
Although not required, better to be safe than sorry
- _**mkinitcpio -P - recreate the iniramfs image**_

### 7. Final Configurations; once the user chroots into the new system"

sof-firmware (Sound Open Firmware), man-pages (Utility that allows reading of man pages)

- _**pacman -S man-pages texinfo sudo man-db sof-firmware dosfstools amd-ucode

#### Network Manager installation
In order to install the NetworkManager before reboot, we must update the packages that Arch has came preinstalled with.

- _**sudo pacman -Syu (updates the packages)

- _**sudo pacman -S wpa_supplicant wireless_tools networkmanager
- _**sudo pacman -S nm-connection-editor network-manager-applet

- _**sudo systemctl enable NetworkManager.service - enables NetworkManager
- _**sudo systemctl enable wpa_supplicant.service - enables wireless connections and the services related to it

#### Changing the Password
- _**passwd

#### Boot Loader
- _**sudo grub-install
- _**grub-mkconfig -o /boot/grub/grub.cfg - Required due to installation of AMD microcodes


- _**mount /dev/sda1 /mnt
- _**grub-install --efi-directory=/dev/sda1

Grub was imported earlier for the purpose of a dedicated boot loader

### 8. Rebooting
Pressing Ctrl+D allows the user to exit the chroot environment

#### Logging in
Hopefully, if the installation process was followed correctly, you will be greeted with a new boot environment allowing you to boot into Arch Linux;
all the blood, sweat, and tears that went into this installation process now finally 

- _**sudo systemctl disable dhcpcd.service - disabling the dhcpc daemon is required due to the conflict between 
NetworkManager and the daemon when it comes to configuration and default settings of the network

Immediately enable NetworkManager.service with "sudo systemctl start NetworkManager.service"
- _**sudo systemctl start NetworkManager.service - starts the engine on the NetworkManager service; cannot be started in chroot

### 9. Installation V2; General recommendations
Once our Arch install is completed, it is highly recommended to save it via a Snapshot feature that is offered thru VMWare; which essentially saves a copy
of your virtual machine at that current stage.

#### Creating Users
Required by the assignment; 3 users, one for the user, Codi, and Sal are to be created and given passwords that will be changed after they login for the first time

- _**sudo useradd -m sajid
- _**sudo passwd sajid 

login - sajid; pw - 1556954

- _**sudo useradd -m codi
- _**sudo passwd codi

pw - GraceHopper1906

- _**sudo useradd -m sal
- _**sudo passwd sal

pw - GraceHopper1906

To force Codi's and Sal's passwords to be changed upon login, all we need to do is set them to be expired

- _**sudo passwd -e codi
- _**sudo passwd -e sal

#### Giving all created users sudo perms
In /etc/sudoers it is revealed that users in the sudoers group can execute ALL commands; 
all you would have to do is just add these 3 users to the group to grant them sudo persm.

- _**sudo groupadd sudo

- _**sudo usermod -a -G sudo sajid
- _**sudo usermod -a -G sudo codi
- _**sudo usermod -a -G sudo sal

- _**less /etc/passwd - checks the passwd file to make sure that all 3 users actually exist

#### Checking for Updates
As is typical after booting up for the first time, it's highly recommended and a good practice to see if the Arch installation needs any updates; to achieve this,
pacman -Syu applies any updates if applicable.

- _**pacman -Syu

#### Installing Boot Environment & Xorg
Once finished updating the Arch install, a Window System is necessary for a Desktop Environment to function. Xorg is one of those Window Systems; highly recommended due 
to its role as a display server and a requisite for most GUI applications to operate on Linux, it is also required due to the intention of installing GNOME as our DE. 

- _**pacman -S xorg (press enter when prompted to install all packages that come with it)
- _**pacman -S xorg-server

#### Installing Desktop Environment (KDE)

* KDE Plasma Desktop Environment
* Wayland session for KDE Plasma
* KDE applications group (consists of KDE specific applications including the Dolphin manager and other useful apps)

- _**pacman -S plasma plasma-meta (press enter to install components when prompted)

Once KDE is installed

- _**pacman-S plasma-wayland-session kde-applications 


#### Enabling Services
Once all services necessary for KDE Plasma to run are installed,
enable them using:

- _**systemctl enable sddm.service

### 10. Rebooting into a Desktop Environment

- _**shutdown

You'll be greated with a desktop environment, login to your account (one of three displayed on the right of the screen)
and boot into the O.S.

### 11. Final Touches

#### Changing the Shell

- _**sudo pacman -S zsh

chsh -s /usr/bin/zsh # will ask for password to user

The shell must also be configured to allow zsh to fully function; with several hotkeys allowing the user to customize things like
an automatic cd, how the history is saved, etc. Once the configuration is finished, the console automatically switches from bash
to zsh.

#### Customizing zsh

In order to customize the Zsh shell, I decided to install "Oh My Zsh," that allows for more configuration

It does require installing it from git, which will require us to install git onto the VM

- sudo pacman -S git

And using a command for the installation, we download it from the git mirror, which will then clone it and automatically install through the shell

- sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
- touch ~/.zshrc # creates the zsh config file if not already there; usually created when oh my zsh is installed
- nano ~/.zshrc # enter the config file using nano to edit text

- PS1="%{$fg[red]%}%n%{$reset_color%}@%{$fg[blue]%}%m %{$fg[yellow]%}%(5~|%-1~/.../%3~|%4~) %{$reset_color%}%% " # Changes the colors of "$" and "%"; along with several others

change the theme from "ZSH_THEME=robbyrussell" to "ZSH_THEME=eastwood"

It is also possible to change the profile of the terminal:

Settings --> Edit Current Profile --> Appearance

Finally, changing the color of certain characters:


- source ~/.zshrc # Applies any different changes made in the zsh configuration file to the terminal

- installing a browser

I decided to utilize the browser that came with KDE Plasma (Konquerer, which honestly felt very clunky) and manually installed firefox.
It was downloaded as a archive (specifically bzip), and had to be extracted to my downloads folder

After extracting and running the Firefox.exe; I attached it to my taskbar and made it the default browser

#### Installing SSH

sudo Pacman -s Putty # will install putty, which is what we will use to SSH into the class gateway

- ssh -p53997 sar9727@129.244.245.21
- ssh -p53997 sar9727@192.168.2.

- Creating aliases

- nano ~/.zshrc

alias clr='clear'
alias c='clear'
alias rb='sudo reboot'
alias ping='ping -c 5' # very useful for wanting to test connectivity without realizing that you're about to unlease what seems to be an infinite # of packet requests
alias bestclass='echo codi's section is the best'
alias ls='ls --color=auto'

