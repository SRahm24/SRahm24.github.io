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

### 2.1 Generating a SSH key 

Opted to utilize a SSH key rather than a password due to issues concerning security
Generated a public and private key utilizing Putty and its Key Generator; added an additional passphrase for security (two fruits and a # sequence with a special char)
Once key was generated, added it to the Droplet

Edit the .yml file with your choice of text editor

(You can also create a new .yml by utilizing the command: 
- _**#echo "basic" > docker-compose.yml**_ - Echo is used to direct a single string "basic" into a new .yml file since touch is unusable on a Windows command prompt shell)

Edit the docker-compose.yml by navigating to its directory in the file editor with your choice of text editor due to the fact that command prompt only has "copy con" as a text editor (Copy con <filename> does work but absolutely inefficiently as it takes time to write to the file):

### 4. Installing Docker on Droplet

Once the .yml is created and set up, execute it through a command prompt:
  
- _**sudo apt install apt-transport-https ca-certificates curl software-properties-common -y**_ - Creates the necessary containers for the WordPress installation to function; will take time to pull the image and set up WordPress.
  
- _**sudo apt install apt-transport-https ca-certificates curl software-properties-common -y**_
  
Installation of the WordPress image will take a couple minutes; once it is complete, head to the url "http://localhost:8000/" to access your WordPress installation and set up a website. (The docker-compose.yml set up a WordPress container on a local host, specifically port 8000, which allows us to access it).

Run through the install wizard to set up your very own WordPress website: a website name, your username, and password.
  
## End of Installation

Once a user and password are created, you can then log in, and will be greeted with the WordPress admin interface hosted from your very own Docker container.

![Screenshot_9](https://user-images.githubusercontent.com/54213991/141718414-357e3028-654b-45a1-9d86-9daf0d4c4a0f.png)
  
  
### Resources:

https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/
  
https://thematrix.dev/setup-wireguard-vpn-server-with-docker/ 
