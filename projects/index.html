## WordPress Installation Documentation
### By Sajid Rahman

This .md chronicles the process of installing WordPress through Docker on WIndows

## Beginning the WordPress Installation
### 1. Install Docker on Windows

Downloading the executable is easy enough, but for those who are not familiar with their PCs, they may find one of the necessary requirements difficult:
This mainly concerns having to enable virtualization for your CPU through BIOS settings (accessible on startup of PC by pressing F12/del)
After running the install wizard for Docker and enabling virtualization, installing/updating Windows Subsystem for Linux (WSL) is also necessary for Docker to function (it
should warn the user upon first launch to install this resource and then restart their PCs).

### 2. Preparing WordPress Install

By opening your command prompt ater a successful installation of Docker, you can immediately get to work to installing Wordpress

- _**docker-compose --version**_ - Ensure that docker compose is installed (it is by default on all Windows installs) by checking its version
# Prints Docker Compose version v2.1.1


mkdir Wordpress # Create a new directory for your wordpress installation

cd Wordpress # change your directory to the new wordpress installation

Next, navigate through file explorer to the install directory, and create a new .yml file

Edit the .yml file with your choice of text editor

(You can also create a new .yml by utilizing the command #echo "basic" > docker-compose.yml; echo is used to direct a single string "basic"
into a new .yml file since touch is unusable on a Windows command prompt shell)

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

### 4. Updating the System Clock

This one is pretty self explanatory and directly from the Arch installation wiki; in order for the system 
to not be internally confused about what time it is or even what day it is - the system clock is essentially
synchronized.

- _**timedatectl set-ntp true**_ - Automatically sets/syncs the timezone and date for the system clock

## End of Installation

Hopefully, by following these instructions and a good enough prowess to use Google to resolve issues, you'll have successfully installed Arch Linux.
It was definitely an interesting experience albeit one that is tough for newcomers to the Linux scene.
