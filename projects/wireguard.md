## WireGuard Installation Documentation
### By Sajid Rahman

This .md chronicles the process of installing WireGuard through a DigitalOcean droplet

## Beginning the WireGuard Installation
### 1. Using DigitalOcean

Through the email that has been kindly forwarded to us by our professors, set up a DigitalOcean account using the referral link that will allow us up to credit worth $100 on DigitalOcean (cloud infra).

After setting up your account, navigate to either the hotbar on the top where you can directly create a droplet, or to 'Droplets' on the sidebar to the left of the screen.

### 2. Creating a Droplet via DigitalOcean

Upon creating a Droplet, there are certain settings you will want to configure for your instance. For starters, you'll want to choose the cheapest option - specifically the basic plan with a regular CPU that will only cost you $5 USD a month; formatted below is the configuration required.

```
Ubuntu 20.04
Basic
Regular Intel CPU
```

During the creation process, you will be asked on how you want to secure your droplet, in which there are two options:
- SSH Key: A more secure authentication method
- Password: Create a root password to access Droplet (less secure)

Utilizing a SSH key rather than a password is a preferable option as it ensures security and decreases any sort of risks when it comes to managing your droplet.

### 2.1 Generating a SSH key 

DigitalOcean actually provides a guide on how to generate SSH keys through a window on their website, as well as a link to a tutorial on how to utilize Putty to generate the
keys.

A key-pair (public and private keys) utilizing Putty and its Key Generator is created, and an additional passphrase for added security is also recommended by the tutorial (my passphrase consists of a color, a fruit, and a # sequence with a special char - just for reminders sake)

Once the key-pair is generated, you can add it to Droplet by clicking 'New SSH Key' upon which you can copy and paste the public key into the text box that is prompted.
Give this key a dedicated name, whether it be 'Key1' or 'Key2.'

After implementing the SSH key method, continue on to installing Docker onto your Droplet.

### 4. Installing Docker on Droplet

The linked tutorial on how to install Docker and Docker compose on your Droplet essentially speedruns the entire install - it's a quick and efficient guide.

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y # Installs the necessary components required for the foundation of the install

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - # Creates and adds the Docker key

sudo add-apt-repository \ # Downloads Docker repository; choose the correct one from the tutorial linked at the bottom of the page for the correct variant required
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
   
apt-cache policy docker-ce # Switches to the correct repository

sudo apt install docker-ce -y # Begins the installation process of Docker

sudo usermod -aG docker ${USER} # This is in case the primary user of the Droplet is not able to access Docker without sudo perms; however, Droplet's primary users are root and can access Docker and its commands without any issues. It's ultimately an optional command.

sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose # Begins the installation process of Docker Compose; required for running and managing Containers

sudo chmod +x /usr/local/bin/docker-compose # Grants executable perms to the Docker Compose folder
```

Once Docker/Docker Compose is installed, you are free to begin installing WireGuard
  
### 5. Installing WireGuard

Again, speedrun via the commands already given via the tutorial links

```
mkdir -p ~/wireguard/ # Create a directory dedicated for the WireGuard install, -p will create any parents specified that do not exist in the path to the intended directory
mkdir -p ~/wireguard/config/ # Creates a config directory in the WireGuard folder
nano ~/wireguard/docker-compose.yml # Goes to edit 'docker-compose.yml' with the NANO text editor; if non-existent, it'll create a new file with that same name, which it does.
```

After the YML is edited, configure it with these settings by copy and pasting it into the .txt file.
Certain things such as the timezone (TZ), the server IP address (SERVERURL, which can be found on the DigitalOcean dashboard for your Droplet), and the PEERS, which are the number of user-config-files to generate, or the names of user-config-files (which will generate those respectively).

```
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=CST6CDT
      - SERVERURL=209.97.156.25
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```

After copying and pasting this into the .yml and saving it; run this command to actually start installing Wireguard

- _**cd ~/wireguard/**_ - Changes directory to previously created WireGuard directory
- _**docker-compose up -d**_ - Starts building the server for WireGuard

A message will appear once WireGuard is finished buildings its server, 'Creating wireguard   ... done'

### 6. Proving Wireguard works on your Smartphone
After the installation is complete; you will utilize your smartphone to test the integrity of your VPN

- _**docker-compose logs -f wireguard**_ - Generates execution log, and QR codes of Wireguard VPN connection settings

This command generates a QR image code that you will utilize thru the Wireguard VPN app; you will definitely want to install it to your phone first. Before moving any further - through your smartphone's browser, go to ipleak.net and take an image of your current IP addess/the page. After installing the Wireguard app, scan the generated QR and activate the VPN that it creates on your phone. You can do this by tapping the + button, and selecting 'Create from QR code.' From there you can scan the QR code and connect to the tunnel.

Prove the VPN is working successfully by going back to your browser and once again going to ipleak.net. The IP address should have changed.

![image](https://user-images.githubusercontent.com/54213991/144139342-b613b73b-1cda-4622-a76f-6e5fb961ec92.png)

Image prior to switching the WireGuard VPN on

![image](https://user-images.githubusercontent.com/54213991/144139366-5ef0903d-ce9f-402b-8e27-36f41515cfb5.png)

Image after switching the WireGuard VPN on and confusing U.S. authorities as to how I was able to magically teleport to New Jersey to buy a philly-cheesesteak sandwich.

### 7. Proving Wireguard works on your Laptop

According to the tutorial, to test out our WireGuard connection to a laptop, we had to delve into the config files for WireGuard and retrieve a .conf file to use a tunnel for the desktop app. The only issue is, is that ~/wireguard/configs/{username} didn't exist, and the only .conf files available were 'wg0.conf,' and that of the peer.conf files as well. I did try utilizing peer_pc2.conf in particular, but for some odd reason, it would not work correctly. I can only attribute this to the different format of the .conf files when compared to that of the powerpoint guide.

You will want to install the WireGuard desktop app, and then create a .conf file on your desktop
Into this file, copy and paste the contents of whichever peer.conf you selected into the file.

Launch Wireguard desktop and create a new tunnel, selecting the .conf file and activate the new tunnel

Check the IP address; it appears to not have changed, and after a quick search, it may just be due to the fact that hosting a VPN on a machine that's connected to your local IP address, which may just be an error on my end as mentioned above.

I tried copying and pasting both the contents of wg0.conf and the peers located in the console

wg0.conf:

```
[Interface]
Address = 10.0.0.1
ListenPort = 51820
PrivateKey = UM2/MGjnzswLO8/+W+vOtVWMGC0ua/KB521TuAeL3ks=
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTIN>
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUT>

[Peer]
# peer_pc1
PublicKey = 2hTAOV9iQHuxfgTDuVZwP2Kp9pF1+dBrtatRfLyEdiw=
AllowedIPs = 10.0.0.2/32

[Peer]
# peer_pc2
PublicKey = 3R+tFCTWETLhzfAFNOI9Wg6s2M2jXtZtIvAWHSq25AU=
AllowedIPs = 10.0.0.3/32

[Peer]
# peer_phone1
PublicKey = u8iVt3IZRjw/gpJmX44YR1BPu5TfbcDPHZ8gtO9ybns=
AllowedIPs = 10.0.0.4/32
```

peer_pc2.conf:

```
[Interface]
Address = 10.0.0.3
PrivateKey = UKIOD5jY3ZJLi4SYSXogGv1XmxOMpm/725wRor6JAWw=
ListenPort = 51820
DNS = 10.0.0.1

[Peer]
PublicKey = Jj2Kt2Szb5TUDge8GDvGOlegUEZ+bR6l9u6oItIHzCY=
Endpoint = 209.97.156.25:51820
AllowedIPs = 0.0.0.0/0, ::/0
```

End Result:

![0](https://user-images.githubusercontent.com/54213991/144146012-58c3f04d-cd28-4ce4-b4cd-795f5d7b5451.png)

## End of Installation

Overall, it was an interesting project concerning the usage of Cloud infra to manage a VPN without having to set up anything too tricky besides just the Wireguard installation and configuration. I am curious as to why the VPN failed to work on my laptop but I can only attribute that error due to the different format of the peer.conf files.

### Resources:
  
https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/create-with-putty/

https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/
  
https://thematrix.dev/setup-wireguard-vpn-server-with-docker/ 
