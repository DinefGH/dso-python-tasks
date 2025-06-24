# V Server configuration

## Contents
1. [Create SSH keys](#1-create-ssh-keys)  
2. [Copy SSH public key to server](#2-copy-ssh-public-key-to-server)  
3. [Login with SSH on V server](#3-login-with-ssh-on-v-server)  
4. [Disable password-based login](#4-disable-password-based-login)  
5. [Test key-only login](#5-test-key-only-login)  
6. [Update package lists](#6-update-package-lists)  
7. [Install Nginx](#7-install-nginx)  
8. [Test Nginx in browser](#8-test-nginx-in-browser)  
9. [Create folder and HTML for alternative site](#9-create-folder-and-html-for-alternative-site)  
10. [Configure Nginx to serve on port 8081](#10-configure-nginx-to-serve-on-port-8081)  

---

## 1. Create SSH keys

Generate an Ed25519 keypair and save it under your project folder:
```bash
ssh-keygen -t ed25519 \
  -f ~/folder/folder/key_ed25519 \
  -C "demo@VServer"

Press Enter to accept the file path.

Optionally enter a passphrase for extra security.


2. Copy SSH public key to server
Copy your new public key onto the V server’s authorized_keys:

ssh-copy-id \
  -i ~/folder/folder/key_ed25519.pub \
  demo@111.111.111.11

This appends your key to ~/.ssh/authorized_keys on the server.

3. Login with SSH on V server
Connect using your key:

ssh demo@111.111.111.11


4. Disable password-based login
Edit the SSH daemon config on the server:

sudo nano /etc/ssh/sshd_config

Find and set:
PasswordAuthentication no

sudo systemctl restart ssh


5. Test key-only login
Verify that password auth is disabled:

ssh -o PubkeyAuthentication=no demo@111.111.111.11

You should see Permission denied, confirming only key-based login works.

6. Update package lists
Always refresh APT before installing:

sudo apt update


7. Install Nginx
Install the web server:
sudo apt install -y nginx

Enable and start it:
sudo systemctl enable --now nginx


8. Test Nginx in browser
On your workstation, open:
http://111.111.111.11

You should see the default Nginx landing page.


9. Create folder and HTML for alternative site
On the server:

sudo mkdir -p /var/www/alternatives
sudo tee /var/www/alternatives/alternate-index.html > /dev/null << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Alternative Site</title>
</head>
<body>
  <h1>Alternative HTML on 8081</h1>
  <p>This page is served on port 8081.</p>
</body>
</html>



10. Configure Nginx to serve on port 8081
Create a new server block:


sudo tee /etc/nginx/sites-available/alternatives > /dev/null << 'EOF'
server {
    listen 8081 default_server;
    listen [::]:8081 default_server;

    root /var/www/alternatives;
    index alternate-index.html;

    location / {
        try_files \$uri \$uri/ =404;
    }
}


Enable and reload:

sudo ln -s /etc/nginx/sites-available/alternatives /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx


Now browse to:
http://192.168.178.30:8081


and you’ll see your alternate-index.html.