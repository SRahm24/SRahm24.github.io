## WordPress Installation Documentation
### By Sajid Rahman

This .md chronicles the process of installing WordPress through Docker on Windows

## Beginning the WordPress Installation
### 1. Install Docker on Windows

Downloading the executable is easy enough, but for those who are not familiar with their PCs, they may find one of the necessary requirements difficult:
This mainly concerns having to enable virtualization for your CPU through BIOS settings (accessible on startup of PC by pressing F12/del).

After running the install wizard for Docker and enabling virtualization, installing/updating Windows Subsystem for Linux (WSL) is also necessary for Docker to function (it should warn the user upon first launch to install this resource and then restart their PCs).

### 2. Preparing WordPress Install

By opening your command prompt ater a successful installation of Docker, you can immediately get to work to installing Wordpress

- _**docker-compose --version**_ - Ensure that docker compose is installed (it is by default on all Windows installs) by checking its version - Prints Docker Compose version v2.1.1

- _**mkdir Wordpress**_ - Create a new directory for your wordpress installation

- _**cd Wordpress**_ - change your directory to the new wordpress installation

### 3. Creating the docker-compose.yml 

Next, navigate through file explorer to the install directory, and create a new .yml file

Edit the .yml file with your choice of text editor

(You can also create a new .yml by utilizing the command #echo "basic" > docker-compose.yml; echo is used to direct a single string "basic"
into a new .yml file since touch is unusable on a Windows command prompt shell)

Edit the docker-compose.yml by navigating to its directory in the file editor with your choice of text editor due to the fact that command prompt only has "copy con" as a text editor (Copy con <filename> does work but absolutely inefficiently as it takes time to write to the file):
  
```
version: '3.3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}
```

### 4. Installing WordPress

Once the .yml is created and set up, execute it through a command prompt:
  
- _**docker-compose up -d**_ - Creates the necessary containers for the Wordpress installation to function; will take time to pull the image and set up Wordpress.
  
Installation of the WordPress image will take a couple minutes; once it is complete, head to the url "http://localhost:8000/" to access your Wordpress installation and set up a website. (The docker-compose.yml set up a Wordpress container on a local host, specifically port 8000, which allows us to access it).

Run through the install wizard to set up your very own Wordpress website: a website name, your username, and password.
  
## End of Installation

Once a user and password are created, you can then log in, and will be greeted with the Wordpress admin interface hosted from your very own Docker container.

![Screenshot_9](https://user-images.githubusercontent.com/54213991/141718414-357e3028-654b-45a1-9d86-9daf0d4c4a0f.png)
  
  
### Resources:

https://docs.docker.com/desktop/windows/install/

https://www.hostinger.com/tutorials/run-docker-wordpress
