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

- firmware ="efi"
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

- _**pacstrap /mnt base linux linux-firmware**_ - Create a new system installation from scratch on the mounted partition

- _**pacstrap /mnt base linux linux-firmware nano grub dhcpcd efibootmgr**_ - Arch Linux install with the addition of useful packages;
nano as a text editor, efibootmgr to manage boot entries, grub to manage boot, dhcpcd as a DHCP client

### 6. Configuring the System:

Once the installation of the essential packages are complete from the disk onto the VM, it'll be necessary to configure the system.
This is done by generation of an fstab file, a file that defines how the partitions within the disk are mounted within the file system.
Utilizing the genfstab command and a -U as its flag to sort by labels, we direct its standard output into a new file within where the
configuration files are located (/etc) and create a new file called "fstab" that contains the aforementioned information.

- _**genfstab -U /mnt >> /mnt/etc/fstab**_ - generates the fstab file for the VM
- _**cat /mnt/etc/fstab**_ - read contents of the fstab file to make sure previous command worked

#### changing root directory (Cont):
One of the most important steps after beginning the finalization of the installation process, is to change the location Root within the system,
which is also known as change root. This is relevant to our lecture in class about containers and was even mentioned by Professor West. 
Once the root directory is changed, its' also important that you should also set a new password for root.

- _**arch-chroot /mnt**_ - chroots into the main/root partition that is mounted

#### Configuring; time zone (Cont).
- _**ln -sf /usr/share/zoneinfo/Region/City /etc/localtime**_

- _**hwclock --systohc**_ - sets system time to UTC

#### Localization; language localization

_**locale-gen**_ - Generates the locales 
- **_nano /etc/locale.gen**_ e - Edit the locales in NANO
- uncomment en_US.UTF-8 UTF-8 as it is needed

_**touch /etc/locale.conf**_ - Creates configuration file for locale
_**nano /etc/locale.conf**_ - Edits locale.conf via nano text editor
- paste in LANG=en_US.UTF-8

#### Network configuration
- _**touch /etc/hostname**_ - Creates the hostname file
- _**nano /etc/hostname**_ - Edits the hostname file in NANO editor
- typed in 24sajidrahman as hostname - Paste in host name

- _**nano /etc/hosts**_ - Edits the hosts files in NANO editor
- Paste in the following:
- 127.0.0.1	localhost
  ::1		localhost
  127.0.1.1	24sajidrahman
 
#### initramfs 
Although not required, better to be safe than sorry
- _**mkinitcpio -P**_ - recreate the initramfs image; mainly used to create an initial ramdisk environment

### 7. Final Configurations; once the user chroots into the new system"


- _**pacman -S man-pages texinfo sudo man-db sof-firmware dosfstools amd-ucode**_
- man-pages (utility that allows reading of man pages)
- texinfo (format for documentation)
- sudo (ability to run commands as root user)
- man-db (database for man pages)
- sof-firmware (Sound Open Firmware) 
- dosfstools (consists of the programs mkfs.fat, fsck.fat and fatlabel to create, check and label file systems of the FAT family)
- amd-ucode (necessary processor micro-code due to laptop software)

#### Network Manager installation
In order to install the NetworkManager before reboot, we must update the packages that Arch has came preinstalled with.

- _**sudo pacman -Syu**_ - Checks the repositories of the package manager and synchronizes its package list with the master server incase its outdated

- _**sudo pacman -S wpa_supplicant wireless_tools networkmanager**_ - Necessary tools required for managing a wireless internet connection
- _**sudo pacman -S nm-connection-editor network-manager-applet**_ - app to add, remove, and modify connections stored by NetworkManager

- _**sudo systemctl enable NetworkManager.service**_ - enables NetworkManager
- _**sudo systemctl enable wpa_supplicant.service**_ - enables wireless connections and the services related to it

#### Changing the Password
- _**passwd**_ - Changes the password of the currently logged in 

#### Boot Loader
- _**sudo grub-install**_ - Finalizes the install of the GRUB boot loader that was downloaded earlier via pacstrap
- _**grub-mkconfig -o /boot/grub/grub.cfg**_ - Required due to the AMD architecture present on laptop; updates the processor microcodes


- _**mount /dev/sda1 /mnt**_ - Mounts the boot disk as the mounted partition
- _**grub-install --efi-directory=/dev/sda1**_ - Installs a EFI directory to the boot disk via GRUB 

Admittedtly, this was probably the most difficult part of the installation for me, as I had confused myself with mounting both SDA1 (EFI boot partition) and SDA2 as mkfs.ext4 rather than actually just the main drive. It is still something that confuses me but I believe I resolved it when I unmounted SDA1 and made no alterations to it; only setting SDA1 as a EFI through manually creating its EFI directory and then installing installing grub with sudo; which may have forced it to install to SDA1 as originally intended.

### 8. Rebooting

Pressing Ctrl+D allows the user to exit the chroot environment and back into the regular user

Once there, type in _**reboot**_ to restart the machine and relaunch it

#### Logging in
Hopefully, if the installation process was followed correctly, you will be greeted with a new boot environment allowing you to boot into Arch Linux;
all the blood, sweat, and tears that went into this installation process now finally 

- _**sudo systemctl disable dhcpcd.service**_ - disabling the dhcpc daemon is required due to the conflict between 
NetworkManager and the daemon when it comes to configuration and default settings of the network

Immediately enable NetworkManager.service with "sudo systemctl start NetworkManager.service"
- _**sudo systemctl start NetworkManager.service**_ - starts the engine on the NetworkManager service; cannot be started in chroot

### 9. Installation V2; General recommendations
Once our Arch install is completed, it is highly recommended to save it via a Snapshot feature that is offered thru VMWare; which essentially saves a copy
of your virtual machine at that current stage.

#### Creating Users
Required by the assignment; 3 users, one for the user, Codi, and Sal are to be created and given passwords that will be changed after they login for the first time

- _**sudo useradd -m sajid**_ - Creates a user 'sajid' with a home dir
- _**sudo passwd sajid**_ - Changes the password of user 'sajid'

- _**sudo useradd -m codi**_ - Creates a user 'sajid' with a home dir
- _**sudo passwd codi**_ - Changes the password of user 'codi'

pw - GraceHopper1906

- _**sudo useradd -m sal**_ - Creates a user 'sajid' with a home dir 
- _**sudo passwd sal**_ - Changes the password of user 'sal'

pw - GraceHopper1906

To force Codi's and Sal's passwords to be changed upon login, all we need to do is set them to be expired

- _**sudo passwd -e codi**_ - Sets password of user 'codi' into an expired status
- _**sudo passwd -e sal**_**_ - Sets password of user 'codi' into an expired status

#### Giving all created users sudo perms
In /etc/sudoers it is revealed that users in the sudoers group can execute ALL commands; 
all you would have to do is just add these 3 users to the group to grant them sudo persm.

- _**sudo groupadd sudo**_ - Cerates a group called 'sudo'

- _**sudo usermod -a -G sudo sajid**_ - Adds user 'sajid' to the group 'sudo'
- _**sudo usermod -a -G sudo codi**_ - Adds user 'codi' to the group 'sudo'
- _**sudo usermod -a -G sudo sal**_ - Adds user 'sal' to the group 'sudo'

- _**less /etc/passwd**_ - echos the last 10 lines of the passwd file (where user information is stored) to make sure that all 3 users actually exist

#### Checking for Updates
As is typical after booting up for the first time, it's highly recommended and a good practice to see if the Arch installation needs any updates; to achieve this,
pacman -Syu applies any updates if applicable.

- _**pacman -Syu**_ - Checks the repositories of the package manager and synchronizes its package list with the master server incase its outdated

#### Installing Boot Environment & Xorg
Once finished updating the Arch install, a Window System is necessary for a Desktop Environment to function. Xorg is one of those Window Systems; highly recommended due 
to its role as a display server and a requisite for most GUI applications to operate on Linux, it is also required due to the intention of installing GNOME as our DE. 

- _**pacman -S xorg**_ - Installs display server package (press enter when prompted to install all packages that come with it)
- _**pacman -S xorg-server**_ - Installs display server package

#### Installing Desktop Environment (KDE)

The following are the required components for a successful KDE Plasma install:
* KDE Plasma Desktop Environment
* Wayland session for KDE Plasma
* KDE applications group (consists of KDE specific applications including the Dolphin manager and other useful apps)

- _**pacman -S plasma plasma-meta**_ Installs KDE Plasma package (press enter to install components when prompted)

Once KDE is installed

- _**pacman-S plasma-wayland-session kde-applications**_ - Installs the Wayland small display server protocol for KDE Plasma

#### Enabling Services
Once all services necessary for KDE Plasma to run are installed,
enable them using:

- _**systemctl enable sddm.service**_ - Enables the Simple Desktop Display Manager that the KDE Plasma DE requires for logging in

### 10. Rebooting into a Desktop Environment

- _**shutdown**_ - Shutdowns the virtual machine

You'll be greated with a desktop environment, login to your account (one of three displayed on the right of the screen)
and boot into the O.S.

### 11. Final Touches

#### Changing the Shell

- _**sudo pacman -S zsh**_ Installs the Zsh shell package

- _**chsh -s /usr/bin/zsh**_ - Sets the default shell to the location of the zsh shell in the user's bin; will ask for password to user

The shell must also be configured to allow zsh to fully function; with several hotkeys allowing the user to customize things like
an automatic cd, how the history is saved, etc. Once the configuration is finished, the console automatically switches from bash
to zsh.

#### Customizing zsh

In order to customize the Zsh shell, I decided to install "Oh My Zsh," that allows for more configuration

It does require installing it from git, which will require us to install git onto the VM

- _**sudo pacman -S git**_ Installs Git, a version control system that manages Arch repository packages

And using a command for the installation, we download it from the git mirror, which will then clone it and automatically install through the shell

- _**sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"**_
- _**touch ~/.zshrc**_ - creates the zsh config file if not already there; usually created when oh my zsh is installed
- _**nano ~/.zshrc**_ - enter the config file using nano to edit text

- _**PS1="%{$fg[red]%}%n%{$reset_color%}@%{$fg[blue]%}%m %{$fg[yellow]%}%(5~|%-1~/.../%3~|%4~) %{$reset_color%}%%"**_ - Changes the colors of "$" and 
  "%"; along with several others

change the theme from "ZSH_THEME=robbyrussell" to "ZSH_THEME=eastwood" through nano

It is also possible to change the profile of the terminal:

Settings --> Edit Current Profile --> Appearance

Finally, changing the color of certain characters:


- _**source ~/.zshrc**_ - Applies any different changes made in the zsh configuration file to the terminal

#### Installing a Browser

I decided to utilize the browser that came with KDE Plasma (Konquerer, which honestly felt very clunky) and manually installed firefox.
It was downloaded as a archive (specifically bzip), and had to be extracted to my downloads folder

After extracting and running the Firefox.exe; I attached it to my taskbar and made it the default browser

#### Installing SSH

- _**sudo Pacman -S Putty**_ - Will install putty, which is what we will use to SSH into the class gateway

- ssh -p53997 sar9727@129.244.245.21
- ssh -p53997 sar9727@192.168.2.36

#### Creating aliases

- _**nano ~/.zshrc**_ - Open up the zsh configuration file in nano

- _**alias clr='clear'**_ - command to clear terminal of previous entries
- _**alias c='clear'**_ - alternate  command to clear terminal of previous entries
- _**alias rb='sudo reboot'**_ - short command for easy reboots
- _**alias ping='ping -c 5'**_ - very useful for wanting to test connectivity without realizing that you're about to unlease what seems to be an infinite # of packet requests
- _**alias bestclass='echo codi's section is the best'**_ - testing aliases out; echos a statement that is probably true
