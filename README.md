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

## Hardware 
Before starting, ensure the following:
- A Raspberry Pi is available.
- The Raspberry Pi OS is flashed onto an SD card. You can use the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to flash the OS image.

## Perperation

### Raspberry Py and VS Code

#### Enabled SSH on the Raspberry Py
   Update the configuration:

   ```bash
   sudo raspi-config  -->  Interface Options > SSH > Enable
   sudo reboot
   ```

#### Setting Up the LAMP Server
Follow these steps to set up the LAMP server on your Raspberry Py<br>
Update the package list and install required software:

   ```bash
   sudo apt update
   sudo apt upgrade -y
   sudo apt install apache2 php libapache2-mod-php -y
   ```
   After this, your server is reachable via `http://<your-ip>`.
   
   Create the default `index.php` file:
   
   ```bash
   sudo rm /var/www/html/index.html
   sudo nano /var/www/html/index.php
   ```
<b>Note:</b> /var/www/html/ is the root location from ypu LAMP project.

#### Adapt Permissions (Upload Files via FTP/SFTP)

   Set file permissions:

   ```bash
   sudo chown -R <username>:www-data /var/www/html
   # Your user can edit files
   # www-data: Apache Webserver can execute and read files
   sudo chmod -R 775 /var/www/html
   ```


### Install plugin SSH - Remote on VS Code

This extension can be used to code directly on the Raspberry Py.

1. Install the extension ``Remote - SSH`` in VS code.
2. Add a new host ``ssh <name>@<ipaddress>`` via the input field on top of VS code.
3. Add a configuration (use the first one).
4. VS code will connect to the host.
5. When you click on open, you will see the folder structure. Here you can select the specific folder.

## GitHub

### Seting up the config

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

4. Set the Git-Remote Settings for teh Project 
   ```bash
   git remote set-url origin git@github.com:rerdm/RaspberryPyHomeserver.git
   ``` 

### Acessing the GitHub project via Token

- Generate a token in [Github](https://github.com/settings/tokens).
- When you push your code you have to enter the GitHub ``username`` and ``passwort`` (GitHubToken)<br>
<b>Note</b>: with this setting you have to enter the credentials after every push.

### Establish connection via Github SSH key

By setting up SSH-Key connection between the Py and github , you are not requested to 
Enter your credentials after every push.

1. **Generate an SSH key** on your Raspberry Pi:
   ```bash
   ssh-keygen -t ed25519 -C "<your-email>@github.com"
   ```
   - When prompted for a file to save the key, press `Enter` to use the default location (`~/.ssh/id_ed25519`).
   - When asked for a passphrase, press `Enter` to leave it empty (no password).

2. **Add the SSH key to the SSH agent**:
   ```bash
   eval "$(ssh-agent -s)"
   ssh-add ~/.ssh/id_ed25519
   ```
   This step ensures that your SSH key is loaded into the SSH agent, which manages your private keys. By doing this, you won't need to repeatedly enter the passphrase for the SSH key during your Git operations or other SSH-based connections.

3. **Copy the public key**:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
   - Copy the output of this command to your clipboard.

4. **Add the SSH key to your GitHub account**:
   - Go to [GitHub SSH settings](https://github.com/settings/keys).
   - Click on **New SSH key**.
   - Paste the public key you copied earlier into the "Key" field.
   - Give it a title (e.g., "Raspberry Pi").
   - Click **Add SSH key**.

5. **Test the SSH connection**:
   ```bash
   ssh -T git@github.com
   ```
   - If successful, you will see a message like:  
     `Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.`

6. **Update your Git remote to use SSH**:
   ```bash
   git remote set-url origin git@github.com:yourName/raspberry-webserver.git
   ```

Upload files to Github:

   ```bash
   git add .
   git commit -m "initial commit"
   git push 
   ```

## Seting Up a Static IP adredd in the Raspberry Py

If you work remotly on the Py you shudl set a static ip adress. Because after restart it can be that the 
py select another ip adress. In this case you have make the config again.
In this case i have set the stati ip adress for my wlan ``Wlan0`` and set it to ip adrerss ``192.168.2.146``

   1. Setting up the static IP-adress

   ```bash
   nmcli connection modify "WLAN-500605" ipv4.addresses 192.168.2.146/24
   nmcli connection modify "WLAN-500605" ipv4.gateway 192.168.2.1
   nmcli connection modify "WLAN-500605" ipv4.dns 192.168.2.1
   nmcli connection modify "WLAN-500605" ipv4.method manual
   ```
   2. Restart connection 
   ```bash
   nmcli connection down "WLAN-500605" && nmcli connection up "WLAN-500605"
   ```



## Security
Ensure sensitive files like `creds/token.txt` are ignored by Git using `.gitignore`. Never share sensitive tokens publicly.

## Tools 


- Install `WinSCP` to transvere Data to the Raspberry Py.


