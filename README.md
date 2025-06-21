# ServerConfiguration

# Connecting to the VPS
To connect your VPS server, you can use your server IP, you can create a root password and enter the server with your IP address and password credentials. But the more secure way is using an SSH key.

# Creating SSH Key
For MAC OS / Linux / Windows 10 (with openssh)
Launch the Terminal app.

ssh-keygen -t rsa

Press ENTER to store the key in the default folder /Users/lamadev/.ssh/id_rsa).

Type a passphrase (characters will not appear in the terminal).

Confirm your passphrase to finish SSH Keygen. You should get an output that looks something like this:

Your public key has been saved in /Users/lamadev/.ssh/id_rsa.pub.
The key fingerprint is:
ae:89:72:0b:85:da:5a:f4:7c:1f:c2:43:fd:c6:44:30 lamadev@mac.local
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|         .       |
|        E .      |
|   .   . o       |
|  o . . S .      |
| + + o . +       |
|. + o = o +      |
| o...o * o       |
|.  oo.o .        |
+-----------------+ 
Copy your public SSH Key to your clipboard using the following code: pbcopy < ~/.ssh/id_rsa.pub
# For Windows
Download PuTTY and PuTTYgen.
Open up PuTTYgen and click the Generate.

Copy your key.
Enter a key passphrase and confirm.

Save the private key.
# Connection
After copying the SSH Key go the to hosting service provider dashboard and paste your key and save. After,

For MAC OS / Linux
ssh root@<server ip address> 

For Windows
Open the PuTTY app.

After installing it, follow these steps to connect to your server:

Launch PuTTY.
Copy your server IP address and SSH port from hPanel. The default SSH port value is 22.
Paste them into the respective fields. Click Open to proceed.
Enter your VPS root username and password. For security reasons, the password won’t show when you type it.


# First Configuration
 ## Deleting apache server

systemctl stop apache2

systemctl disable apache2

apt remove apache2
# to delete related dependencies:

apt autoremove

# Cleaning and updating server

apt clean all && sudo apt update && sudo apt dist-upgrade

rm -rf /var/www/html

# Installing Nginx
apt install nginx

# Installing and configure Firewall

apt install ufw
ufw enable
ufw allow "Nginx Full"

# First Page

# Delete the default server configuration

 rm /etc/nginx/sites-available/default
 
 rm /etc/nginx/sites-enabled/default
 

# First configuration

# create new folder
sudo nano /etc/nginx/sites-available/damansarachiropractic.com

# update your configuration to point your new Location 


server {
    listen 80;
    server_name damansarachiropractic.com
 www.damansarachiropractic.com;


    root /var/www/damansarachiropractic.com/html;

    index index.html index.htm index.nginx-debian.html;


    location / {
        try_files $uri $uri/ =404;
    }
}

# create HTML Directory

sudo mkdir -p /var/www/damansarachiropractic.com/html

# Add permission to Directory
sudo chown -R $USER:$USER /var/www/damansarachiropractic.com/html

sudo chmod -R 755 /var/www

# create new File 
echo "Hello from NGINX on my Youtube Channel TechnophileFirdous! we had learn to create our own Page in Nginx server" | sudo tee /var/www/damansarachiropractic.com/html/index.html

# Create a symbolic link to your domain’s configuration file in the sites-enabled directory:

sudo ln -sf /etc/nginx/sites-available/damansarachiropractic.com /etc/nginx/sites-enabled//damansarachiropractic.com

# test your configuration

sudo nginx -t

# restart your nginx server

sudo systemctl restart nginx



# Uploading Apps Using Git

apt install git

mkdir damansara

cd damansara

git clone <your repository>

# Nginx Configuration for new apps

nano /etc/nginx/sites-available/damansara


location /api {
        proxy_pass http://45.90.108.107:8800;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
  }

  
If you check the location /api you are going to get "502" error which is good. Our configuration works. The only thing we need to is running our app

apt install nodejs
apt install npm
cd api
npm install
nano .env
Copy and paste your env file
node index.js
But if you close your ssh session here. It's gonna kill this process. To prevent this we are going to need a package which is called pm2
npm i -g pm2
Let's create a new pm2 instance

pm2 start --name api index.js   
pm2 startup ubuntu 
React App Deployment
cd ../client
nano .env
Paste your env file.

npm i
Let's create the build file

npm run build
Right now, we should move this build file into the main web file

rm -rf /var/www/netflix/*
mkdir /var/www/netflix/client
cp -r build/* /var/www/netflix/client
Let's make some server configuration

 location / {
        root /var/www/netflix/client/;
        index  index.html index.htm;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        try_files $uri $uri/ /index.html;
  }

Adding Domain
1 - Make sure that you created your A records on your domain provider website.

2 - Change your pathname from Router

3 - Change your env files and add the new API address

4 - Add the following server config

server {
 listen 80;
 server_name safakkocaoglu.com www.safakkocaoglu.com;

location / {
 root /var/www/netflix/client;
 index  index.html index.htm;
 proxy_http_version 1.1;
 proxy_set_header Upgrade $http_upgrade;
 proxy_set_header Connection 'upgrade';
 proxy_set_header Host $host;
 proxy_cache_bypass $http_upgrade;
 try_files $uri $uri/ /index.html;
}
}

server {
  listen 80;
  server_name api.safakkocaoglu.com;
  location / {
    proxy_pass http://45.90.108.107:8800;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    }
}

server {
  listen 80;
  server_name admin.safakkocaoglu.com;
  location / {
    root /var/www/netflix/admin;
    index  index.html index.htm;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    try_files $uri $uri/ /index.html;
  }
}
SSL Certification
apt install certbot python3-certbot-nginx
Make sure that Nginx Full rule is available

ufw status
certbot --nginx -d example.com -d www.example.com
Let’s Encrypt’s certificates are only valid for ninety days. To set a timer to validate automatically:

systemctl status certbot.timer


## for running nestjs application

Method 1: Using PM2

Install NestJS CLI

sudo npm i -g @nestjs/cli
Install PM2

sudo npm install pm2@latest -g
Clone Code Repository

git clone http://coderepo.com/projectname.git
cd projectname
npm install
Build Project

npm run build
Run Project with PM2

pm2 start dist/main.js --name <application_name>
pm2 startup systemd
pm2 save
This method ensures your application runs continuously and restarts automatically after system reboots
1
.

Method 2: Using Nginx as a Reverse Proxy

Install NestJS CLI and Create Project

npm i -g @nestjs/cli

nest new <project-name>

cd <project-name>
##  install git 

 sudo apt update
## Install dependencies:

sudo apt install libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-

## login you git

## git login
git login

// git clone gitURL

npm install
npm run build
Install PM2 and Start Application

npm install -g pm2
pm2 start dist/main.js --name <application_name>
pm2 startup systemd
pm2 save
Install Nginx and Configure Reverse Proxy

sudo apt install nginx
sudo nano /etc/nginx/sites-available/<your_domain>
Add the following configuration:

server {
server_name your_domain www.your_domain;
location / {
proxy_pass http://localhost:3000;
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection 'upgrade';
proxy_set_header Host $host;
proxy_cache_bypass $http_upgrade;
}
}
Create a symlink and restart Nginx:

sudo ln -s /etc/nginx/sites-available/<your_domain> /etc/nginx/sites-enabled/
sudo unlink /etc/nginx/sites-enabled/default
sudo systemctl restart nginx
Add SSL Using Let’s Encrypt (Optional)

sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d <your_domain> -d <www.your_domain>
This method provides an additional layer of security by using Nginx as a reverse proxy and adding SSL
