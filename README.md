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
1. [Hardware](#1-hardware)<br>
2. [Preparation](#2-preparation)<br>
   2.1 [Raspberry Pi and VS Code](#21-raspberry-pi-and-vs-code)<br>
      2.1.1 [Enable SSH on the Raspberry Pi](#211-enable-ssh-on-the-raspberry-pi)<br>
      2.1.2 [Setting Up the LAMP Server](#212-setting-up-the-lamp-server)<br>
      2.1.3 [Adapt Permissions (Upload Files via FTP/SFTP)](#213-adapt-permissions-upload-files-via-ftpsftp)<br>
3. [Install VS Code SSH - Remote](#3-install-vs-code-ssh---remote)<br>
4. [GitHub](#4-github)<br>
   4.1 [Setting Up the Config](#41-setting-up-the-config)<br>
   4.2 [Accessing the GitHub Project via Token](#42-accessing-the-github-project-via-token)<br>
   4.3 [Establish Connection via GitHub SSH Key](#43-establish-connection-via-github-ssh-key)<br>
   4.4 [Upload Files to GitHub](#44-upload-files-to-github)<br>
5. [Setting Up a Static IP Address on the Raspberry Py](#5-setting-up-a-static-ip-address-on-the-raspberry-py)<br>
6. [Security](#6-security)<br>

## 1. Hardware
Before starting, ensure the following:
- A Raspberry Pi is available.
- The Raspberry Pi OS is flashed onto an SD card. You can use the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to flash the OS image.

## 2. Preparation

### 2.1 Raspberry Pi and VS Code

#### 2.1.1 Enable SSH on the Raspberry Pi
Update the configuration:

```bash
sudo raspi-config  -->  Interface Options > SSH > Enable
sudo reboot
```

#### 2.1.2 Setting Up the LAMP Server
Follow these steps to set up the LAMP server on your Raspberry Pi:<br>
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
**Note:** `/var/www/html/` is the root location for your LAMP project.

#### 2.1.3 Adapt Permissions (Upload Files via FTP/SFTP)

Set file permissions:

```bash
sudo chown -R <username>:www-data /var/www/html
# Your user can edit files
# www-data: Apache Webserver can execute and read files
sudo chmod -R 775 /var/www/html
```

## 3. Install VS Code SSH - Remote

This extension can be used to code directly on the Raspberry Pi.

1. Install the extension `Remote - SSH` in VS Code.
2. Add a new host `ssh <name>@<ipaddress>` via the input field on top of VS Code.
3. Add a configuration (use the first one).
4. VS Code will connect to the host.
5. When you click on open, you will see the folder structure. Here you can select the specific folder.

## 4. GitHub

### 4.1 Setting Up the Config

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

4. Set the Git-Remote Settings for the Project:
   ```bash
   git remote set-url origin git@github.com:rerdm/RaspberryPyHomeserver.git
   ``` 

### 4.2 Accessing the GitHub Project via Token

- Generate a token in [GitHub](https://github.com/settings/tokens).
- When you push your code, you have to enter the GitHub `username` and `password` (GitHubToken).<br>
**Note:** With this setting, you have to enter the credentials on every push.

### 4.3 Establish Connection via GitHub SSH Key

By setting up an SSH-Key connection between the Pi and GitHub, you are not required to enter your credentials after every push.

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

### 4.4 Upload Files to GitHub

```bash
git add .
git commit -m "initial commit"
git push 
```

## 5. Setting Up a Static IP Address on the Raspberry Py

If you work remotely on the Pi, you should set a static IP address. After a restart, the Pi might select another IP address, requiring reconfiguration. In this case, I have set the static IP address for my WLAN `Wlan0` to `192.168.2.146`.

1. Setting up the static IP address:

   ```bash
   nmcli connection modify "WLAN-500605" ipv4.addresses 192.168.2.146/24
   nmcli connection modify "WLAN-500605" ipv4.gateway 192.168.2.1
   nmcli connection modify "WLAN-500605" ipv4.dns 192.168.2.1
   nmcli connection modify "WLAN-500605" ipv4.method manual
   ```
2. Restart connection:
   ```bash
   nmcli connection down "WLAN-500605" && nmcli connection up "WLAN-500605"
   ```

## 6. Security
Ensure sensitive files like `creds/token.txt` are ignored by Git using `.gitignore`. Never share sensitive tokens publicly.




