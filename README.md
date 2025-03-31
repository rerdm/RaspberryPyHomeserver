# RaspberryPyHomeserver

## Project Description
The RaspberryPyHomeserver project aims to build a home server using a Raspberry Pi. This server will be used to store data and will be accessible via a LAMP server.

**What is LAMP?**  
LAMP is an acronym for a stack of software used to host dynamic websites and servers:
- **L**inux: The operating system.
- **A**pache: The web server.
- **M**ySQL (or MariaDB): The database management system.
- **P**HP: The programming language for server-side scripting.

## Table of Contents
1. [Preconditions](#preconditions)<br>
   1.1 [Enabled SSH](#enabled-ssh)<br>
2. [Setting Up the LAMP Server](#setting-up-the-lamp-server)<br>
   2.1 [Update the Package List](#update-the-package-list)<br>
   2.2 [Create index.php](#create-indexphp)<br>
3. [Adapt Permissions (Upload Files via FTP/SFTP)](#adapt-permissions-upload-files-via-ftpsftp) <br>
   3.1 [Set File Permissions](#set-file-permissions) <br>
   3.2 [Transfer Files Using WinSCP](#transfer-files-using-winscp)<br>
4. [Install VS Code SSH - Remote](#install-vs-code-ssh---remote)<br>
5. [Upload the Project to Github](#upload-the-project-to-github)<br>
6. [Security](#security)

## Preconditions
Before starting, ensure the following:
- A Raspberry Pi is available.
- The Raspberry Pi OS is flashed onto an SD card. You can use the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to flash the OS image.

### Enabled SSH
1. Update the configuration:

   ```bash
   sudo raspi-config  -->  Interface Options > SSH > Enable
   sudo reboot
   ```

## Setting Up the LAMP Server
Follow these steps to set up the LAMP server on your Raspberry Pi:

### Update the Package List
1. Update the package list and install required software:
   ```bash
   sudo apt update
   sudo apt upgrade -y
   sudo apt install apache2 php libapache2-mod-php -y
   ```
   After this, your server is reachable via `http://<your-ip>`.

### Create index.php
2. Create the default `index.php` file:
   ```bash
   sudo rm /var/www/html/index.html
   sudo nano /var/www/html/index.php
   ```

## Adapt Permissions (Upload Files via FTP/SFTP)

### Set File Permissions
1. Set file permissions:

   ```bash
   sudo chown -R <username>:www-data /var/www/html
   # Your user can edit files
   # www-data: Apache Webserver can execute and read files
   sudo chmod -R 775 /var/www/html
   ```

### Transfer Files Using WinSCP
2. Open `WinSCP` for transferring and syncing the data.

## Install VS Code SSH - Remote

This extension can be used to code directly on the Raspberry Pi.

1. Install the extension ``Remote - SSH`` in VS code.
2. Add a new host ``ssh <name>@<ipaddress>`` via the input field on top of VS code.
3. Add a configuration (use the first one).
4. VS code will connect to the host.
5. When you click on open, you will see the folder structure. Here you can select the specific folder.

## Upload the Project to Github

1. Install Git on the Raspberry Pi:

   ```bash
   sudo apt update
   sudo apt install git -y
   ``` 
2. Set Git information:

   ```bash
   git config --global user.name "user name"
   git config --global user.email "<your-email>@github.com"
   ``` 

3. Initialize the project:

   ```bash
   git init
   ``` 

4. Generate a token in [Github](https://github.com/settings/tokens).

5. Upload files to Github:

   ```bash
   git remote add origin https://github.com/yourName/raspberry-webserver.git
   git branch -M master
   git add .
   git commit -m "initial commit"
   git push -u origin master  --> will request you to add username and password/GitHubToken 
   ```

## Security
Ensure sensitive files like `creds/token.txt` are ignored by Git using `.gitignore`. Never share sensitive tokens publicly.


