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



##  Step 1 — Installing Node.js
Let’s begin by installing the latest LTS release of Node.js, using the NodeSource package archives.

First, install the NodeSource PPA in order to get access to its contents. Make sure you’re in your home directory, and use curl to retrieve the installation script for the most recent LTS version of Node.js from its archives.

cd ~
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
You can inspect the contents of this script with nano or your preferred text editor:

nano nodesource_setup.sh
When you’re done inspecting the script, run it under sudo:

sudo bash nodesource_setup.sh
The PPA will be added to your configuration and your local package cache will be updated automatically. After running the setup script from Nodesource, you can install the Node.js package:

sudo apt install nodejs
To check which version of Node.js you have installed after these initial steps, type:

node -v
Output
v14.4.0
Note: When installing from the NodeSource PPA, the Node.js executable is called nodejs, rather than node.

The nodejs package contains the nodejs binary as well as npm, a package manager for Node modules, so you don’t need to install npm separately.

npm uses a configuration file in your home directory to keep track of updates. It will be created the first time you run npm. Execute this command to verify that npm is installed and to create the configuration file:

npm -v
Output
6.14.5
In order for some npm packages to work (those that require compiling code from source, for example), you will need to install the build-essential package:

sudo apt install build-essential
You now have the necessary tools to work with npm packages that require compiling code from source.

With the Node.js runtime installed, let’s move on to writing a Node.js application.

### Step 2 — Creating a Node.js Application
Let’s write a Hello World application that returns “Hello World” to any HTTP requests. This sample application will help you get Node.js set up. You can replace it with your own application — just make sure that you modify your application to listen on the appropriate IP addresses and ports.

First, let’s create a sample application called hello.js:

cd ~
nano hello.js
Insert the following code into the file:

~/hello.js
const http = require('http');

const hostname = 'localhost';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World!\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
Save the file and exit the editor.

This Node.js application listens on the specified address (localhost) and port (3000), and returns “Hello World!” with a 200 HTTP success code. Since we’re listening on localhost, remote clients won’t be able to connect to our application.

To test your application, type:

node hello.js
You will receive the following output:

Output
Server running at http://localhost:3000/
Note: Running a Node.js application in this manner will block additional commands until the application is killed by pressing CTRL+C.

To test the application, open another terminal session on your server, and connect to localhost with curl:

curl http://localhost:3000
If you get the following output, the application is working properly and listening on the correct address and port:

Output
Hello World!
If you do not get the expected output, make sure that your Node.js application is running and configured to listen on the proper address and port.

Once you’re sure it’s working, kill the application (if you haven’t already) by pressing CTRL+C.

### Step 3 — Installing PM2
Next let’s install PM2, a process manager for Node.js applications. PM2 makes it possible to daemonize applications so that they will run in the background as a service.

Use npm to install the latest version of PM2 on your server:

sudo npm install pm2@latest -g
The -g option tells npm to install the module globally, so that it’s available system-wide.

Let’s first use the pm2 start command to run your application, hello.js, in the background:

pm2 start hello.js
This also adds your application to PM2’s process list, which is outputted every time you start an application:

Output
...
[PM2] Spawning PM2 daemon with pm2_home=/home/sammy/.pm2
[PM2] PM2 Successfully daemonized
[PM2] Starting /home/sammy/hello.js in fork_mode (1 instance)
[PM2] Done.
┌────┬────────────────────┬──────────┬──────┬───────────┬──────────┬──────────┐
│ id │ name               │ mode     │ ↺    │ status    │ cpu      │ memory   │
├────┼────────────────────┼──────────┼──────┼───────────┼──────────┼──────────┤
│ 0  │ hello              │ fork     │ 0    │ online    │ 0%       │ 25.2mb   │
└────┴────────────────────┴──────────┴──────┴───────────┴──────────┴──────────┘
As indicated above, PM2 automatically assigns an App name (based on the filename, without the .js extension) and a PM2 id. PM2 also maintains other information, such as the PID of the process, its current status, and memory usage.

Applications that are running under PM2 will be restarted automatically if the application crashes or is killed, but we can take an additional step to get the application to launch on system startup using the startup subcommand. This subcommand generates and configures a startup script to launch PM2 and its managed processes on server boots:

pm2 startup systemd
The last line of the resulting output will include a command to run with superuser privileges in order to set PM2 to start on boot:

Output
[PM2] Init System found: systemd
sammy
[PM2] To setup the Startup Script, copy/paste the following command:
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u sammy --hp /home/sammy
Run the command from the output, with your username in place of sammy:

sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u sammy --hp /home/sammy
As an additional step, we can save the PM2 process list and corresponding environments:

pm2 save
You have now created a systemd unit that runs pm2 for your user on boot. This pm2 instance, in turn, runs hello.js.

Start the service with systemctl:

sudo systemctl start pm2-sammy
If at this point you encounter an error, you may need to reboot, which you can achieve with sudo reboot.

Check the status of the systemd unit:

systemctl status pm2-sammy
For a detailed overview of systemd, please review Systemd Essentials: Working with Services, Units, and the Journal.

In addition to those we have covered, PM2 provides many subcommands that allow you to manage or look up information about your applications.

Stop an application with this command (specify the PM2 App name or id):

pm2 stop app_name_or_id
Restart an application:

pm2 restart app_name_or_id
List the applications currently managed by PM2:

pm2 list
Get information about a specific application using its App name:

pm2 info app_name
The PM2 process monitor can be pulled up with the monit subcommand. This displays the application status, CPU, and memory usage:

pm2 monit
Note that running pm2 without any arguments will also display a help page with example usage.

Now that your Node.js application is running and managed by PM2, let’s set up the reverse proxy.

### Step 4 — Setting Up Nginx as a Reverse Proxy Server
Your application is running and listening on localhost, but you need to set up a way for your users to access it. We will set up the Nginx web server as a reverse proxy for this purpose.

In the prerequisite tutorial, you set up your Nginx configuration in the /etc/nginx/sites-available/example.com file. Open this file for editing:

sudo nano /etc/nginx/sites-available/example.com
Within the server block, you should have an existing location / block. Replace the contents of that block with the following configuration. If your application is set to listen on a different port, update the highlighted portion to the correct port number:

/etc/nginx/sites-available/example.com

server {
...
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
...
}


This configures the server to respond to requests at its root. Assuming our server is available at example.com, accessing https://example.com/ via a web browser would send the request to hello.js, listening on port 3000 at localhost.

You can add additional location blocks to the same server block to provide access to other applications on the same server. For example, if you were also running another Node.js application on port 3001, you could add this location block to allow access to it via https://example.com/app2:


/etc/nginx/sites-available/example.com — Optional
server {
...
    location /app2 {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
...
}
Once you are done adding the location blocks for your applications, save the file and exit your editor.

sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/

Make sure you didn’t introduce any syntax errors by typing:

sudo nginx -t
Restart Nginx:


sudo systemctl restart nginx
Assuming that your Node.js application is running, and your application and Nginx configurations are correct, you should now be able to access your application via the Nginx reverse proxy. Try it out by accessing your server’s URL (its public IP address or domain name).

Now it’s time to start both our Node.js application and the Nginx service to trigger the recent changes. But first, let’s check the status of Nginx to confirm that the configuration is working properly:

sudo nginx -t 
The output upon running the above command would look like this:

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
The above output confirms that our configuration was successful. Next, restart Nginx to enable your changes:

sudo systemctl restart nginx
With Nginx running again, let’s allow full access through the Nginx firewall:

sudo ufw allow 'Nginx Full'
Next, navigate to the directory of the Node.js application:

cd ~/nginx_server_project
Start your Node.js server application using the following command:

node server.js
Node.js Server Running And Listening For A Request

Open your browser and access the Node.js application using your-domain.com:

Accessing The Node.js Application

Now we can navigate to the address your-domain.com/overview on the browser and access the /overview endpoint of our application:

Accessing The /Overview Endpoint

To further test if every other path we defined is working, let’s try the last path, your-domain.com/api:

Accessing The /api Endpointq

Advanced Nginx configuration options
Node.js has become popular for creating online applications that are fast and scalable. However, it is essential to optimize the web server infrastructure to take advantage of its possibilities. This section will explore how to optimize the scalability and security of Node.js apps by covering advanced Nginx configuration options including SSL termination, load balancing, and more.

SSL termination with Nginx
It is essential to secure communication between clients and the Node.js server, and SSL/TLS encryption is a key component in making this happen. Nginx performs well when it comes to SSL termination.


By terminating HTTPS traffic from clients, Nginx relieves the computational burden of SSL/TLS encryption on your upstream web and application servers:

server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /path/to/ssl_certificate.crt;
    ssl_certificate_key /path/to/ssl_certificate.key;

    # SSL settings
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    location / {
        proxy_pass http://node_app_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
The configuration file above:

Sets up Nginx to listen on port 443 for SSL connections
Specifies the SSL certificate and key paths for SSL termination
Configures SSL protocols and ciphers to ensure secure communication
Uses the proxy_pass directive to forward incoming requests to the backend Node.js servers
Load balancing with Nginx
As Node.js applications develop in size to accommodate an increasing number of users, it becomes important to allocate incoming traffic among several backend servers to guarantee efficiency and dependability.

Nginx provides a variety of algorithms to help with load balancing. These algorithms determine how loads are distributed among the available servers:

http {
  upstream node_app_servers {
      # no load balancing method is specified for Round Robin
      server api1.backend.com;
      server api2.backend.com;
  }

  server {
      listen 80;
      server_name example.com;

      location / {
          proxy_pass http://node_app_servers;
          proxy_set_header Host $host;
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto $scheme;
      }
  }
}
The upstream directive defines a group of backend Node.js servers for load balancing
Nginx distributes incoming requests among the specified servers using a default round-robin algorithm
#The proxy_pass directive forwards requests to the backend servers within the upstream group

# Here is an example of an nginx configuration file that sets up a reverse proxy for a Node.js application under the /api path:

# 🔧 Sample Nginx Config (node-api.conf or inside nginx.conf)

server {
    listen 80;
    server_name yourdomain.com;

    location /api/ {
        proxy_pass http://localhost:3000/;  # Assuming Node.js is running on port 3000
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;

        # Optionally remove the /api prefix before sending to Node.js
        rewrite ^/api/?(.*)$ /$1 break;
    }

    # Optional: Serve static frontend from a directory
    location / {
        root /var/www/html;
        index index.html;
    }
}
# 📌 Explanation
location /api/ matches any URL starting with /api/.

rewrite ^/api/?(.*)$ /$1 break; removes the /api prefix so your Node.js routes can remain clean (e.g., /users instead of /api/users).

proxy_pass http://localhost:3000/; proxies the request to your local Node.js server.

# ✅ Make sure to:
Update yourdomain.com to your actual domain or IP.

# Restart Nginx after saving config:

sudo nginx -t     # Test config

sudo systemctl reload nginx

Conclusion
Congratulations! You now have your Node.js application running behind an Nginx reverse proxy on an Ubuntu 20.04 server. This reverse proxy setup is flexible enough to provide your users access to other applications or static web content that you want to share.


# configure Jenkins in Ubuntu

✅ Step-by-Step Jenkins Deployment on Ubuntu VPS
Step 1: Install Jenkins on Ubuntu VPS
Update your packages:

bash
Copy
Edit
sudo apt update
Install Java (Jenkins requires Java):

bash
Copy
Edit
sudo apt install openjdk-11-jdk -y
Add Jenkins repository:

bash
Copy
Edit
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
Install Jenkins:

bash
Copy
Edit
sudo apt update
sudo apt install jenkins -y
Start and enable Jenkins:

bash
Copy
Edit
sudo systemctl start jenkins
sudo systemctl enable jenkins
Open firewall (if UFW is active):

bash
Copy
Edit
sudo ufw allow 8080
Step 2: Access Jenkins Web UI
Open a browser and go to http://your_server_ip:8080

Get the initial admin password:

bash
Copy
Edit
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Paste it into the web UI, then follow the setup wizard:

Install suggested plugins

Create your admin user

Step 3: Configure Your Jenkins Job
Install required plugins (Manage Jenkins > Plugins):

Git

SSH plugin (for remote deployment)

Pipeline (for scripted/Declarative pipelines)

Create a New Job:

Go to Dashboard > New Item

Name it (e.g., MyAppDeploy)

Choose Freestyle Project or Pipeline and click OK

Configure Source Code Management:

Choose Git

Enter your repo URL (and credentials if private)

Step 4: Define Deployment Process
Option A: Using a Freestyle Project
Add Build Step > "Execute shell"

Enter your shell script to build/deploy:

bash
Copy
Edit
git pull origin main
npm install
pm2 restart app
Option B: Using a Pipeline Script
Choose Pipeline project

In the pipeline section, write a Jenkinsfile:

groovy
Copy
Edit
pipeline {
  agent any

  stages {
    stage('Pull Code') {
      steps {
        git credentialsId: 'your-cred-id', url: 'git@github.com:user/repo.git'
      }
    }
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }
    stage('Deploy') {
      steps {
        sh 'pm2 restart app'
      }
    }
  }
}
Step 5: Automate with Webhooks (Optional)
If using GitHub/GitLab:

Add webhook in your repo to point to:

bash
Copy
Edit
http://your_server_ip:8080/github-webhook/
In Jenkins job:

Under Build Triggers, check GitHub hook trigger for GITScm polling

Step 6: Secure Your Jenkins Server
Use HTTPS (via reverse proxy with Nginx + SSL)

Restrict access with user roles

Keep Jenkins and plugins up to date

Example Deployment Scenario
Assume you're deploying a Node.js app:

Code pulled from GitHub repo

Runs npm install and npm run build

Deploys to /var/www/myapp using rsync or direct script

Restarts app with pm2

✅ Summary
Step	Task
1	Install Jenkins
2	Access and configure UI
3	Create job or pipeline
4	Define build & deploy steps
5	(Optional) Add webhook
6	Secure Jenkins

Would you like a sample Jenkinsfile for a specific language or framework (Node.js, Python, Java, etc.)?


# jenkins installation


Linux 
Table of Contents
Prerequisites
Debian/Ubuntu
Long Term Support release
Weekly release
Installation of Java
Fedora
Long Term Support release
Weekly release
Start Jenkins
Red Hat Enterprise Linux and derivatives
Long Term Support release
Weekly release
Start Jenkins
Post-installation setup wizard
Unlocking Jenkins
Customizing Jenkins with plugins
Creating the first administrator user
Jenkins installers are available for several Linux distributions.

Debian/Ubuntu

Fedora

Red Hat Enterprise Linux and derivatives

Prerequisites
Minimum hardware requirements:

256 MB of RAM

1 GB of drive space (although 10 GB is a recommended minimum if running Jenkins as a Docker container)

Recommended hardware configuration for a small team:

4 GB+ of RAM

50 GB+ of drive space

Comprehensive hardware recommendations:

Hardware: see the Hardware Recommendations page

Software requirements:

Java: see the Java Requirements page

Web browser: see the Web Browser Compatibility page

For Windows operating system: Windows Support Policy

For Linux operating system: Linux Support Policy

For servlet containers: Servlet Container Support Policy

Debian/Ubuntu
On Debian and Debian-based distributions like Ubuntu you can install Jenkins through apt.

How To Install Jenkins on Ubuntu 24.04

You need to choose either the Jenkins Long Term Support release or the Jenkins weekly release.

Long Term Support release
A LTS (Long-Term Support) release is chosen every 12 weeks from the stream of regular releases as the stable release for that time period. It can be installed from the debian-stable apt repository.

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
Weekly release
A new release is produced weekly to deliver bug fixes and features to users and plugin developers. It can be installed from the debian apt repository.

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
Beginning with Jenkins 2.335 and Jenkins 2.332.1, the package is configured with systemd rather than the older System V init. More information is available in "Managing systemd services".

The package installation will:

Setup Jenkins as a daemon launched on start. Run systemctl cat jenkins for more details.

Create a ‘jenkins’ user to run this service.

Direct console log output to systemd-journald. Run journalctl -u jenkins.service if you are troubleshooting Jenkins.

Populate /lib/systemd/system/jenkins.service with configuration parameters for the launch, e.g JENKINS_HOME

Set Jenkins to listen on port 8080. Access this port with your browser to start configuration.

If Jenkins fails to start because a port is in use, run systemctl edit jenkins and add the following:

[Service]
Environment="JENKINS_PORT=8081"
Here, "8081" was chosen but you can put another port available.

Installation of Java
Jenkins requires Java to run, yet not all Linux distributions include Java by default. Additionally, not all Java versions are compatible with Jenkins.

There are multiple Java implementations which you can use. OpenJDK is the most popular one at the moment, we will use it in this guide.

Update the Debian apt repositories, install OpenJDK 21, and check the installation with the commands:

sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version
openjdk version "21.0.3" 2024-04-16
OpenJDK Runtime Environment (build 21.0.3+11-Debian-2)
OpenJDK 64-Bit Server VM (build 21.0.3+11-Debian-2, mixed mode, sharing)
Why use apt and not apt-get or another command? The apt command has been available since 2014. It has a command structure that is similar to apt-get but was created to be a more pleasant experience for typical users. Simple software management tasks like install, search and remove are easier with apt.

Fedora
You can install Jenkins through dnf. You need to add the Jenkins repository from the Jenkins website to the package manager first.

Long Term Support release
A LTS (Long-Term Support) release is chosen every 12 weeks from the stream of regular releases as the stable release for that time period. It can be installed from the redhat-stable yum repository.

sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo dnf upgrade
# Add required dependencies for the jenkins package
sudo dnf install fontconfig java-21-openjdk
sudo dnf install jenkins
sudo systemctl daemon-reload
Weekly release
A new release is produced weekly to deliver bug fixes and features to users and plugin developers. It can be installed from the redhat yum repository.

sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io-2023.key
sudo dnf upgrade
# Add required dependencies for the jenkins package
sudo dnf install fontconfig java-21-openjdk
sudo dnf install jenkins
Start Jenkins
You can enable the Jenkins service to start at boot with the command:

sudo systemctl enable jenkins
You can start the Jenkins service with the command:

sudo systemctl start jenkins
You can check the status of the Jenkins service using the command:

sudo systemctl status jenkins
If everything has been set up correctly, you should see an output like this:

Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor preset: enabled)
Active: active (running) since Tue 2018-11-13 16:19:01 +03; 4min 57s ago
If you have a firewall installed, you must add Jenkins as an exception. You must change YOURPORT in the script below to the port you want to use. Port 8080 is the most common.

YOURPORT=8080
PERM="--permanent"
SERV="$PERM --service=jenkins"

firewall-cmd $PERM --new-service=jenkins
firewall-cmd $SERV --set-short="Jenkins ports"
firewall-cmd $SERV --set-description="Jenkins port exceptions"
firewall-cmd $SERV --add-port=$YOURPORT/tcp
firewall-cmd $PERM --add-service=jenkins
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
Red Hat Enterprise Linux and derivatives
You can install Jenkins through yum on Red Hat Enterprise Linux, AlmaLinux, Rocky Linux, Oracle Linux, CentOS, and other Red Hat based distributions.

How To Install Jenkins on Rocky Linux 9

You need to choose either the Jenkins Long Term Support release or the Jenkins weekly release.

Long Term Support release
A LTS (Long-Term Support) release is chosen every 12 weeks from the stream of regular releases as the stable release for that time period. It can be installed from the redhat-stable yum repository.

sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum upgrade
# Add required dependencies for the jenkins package
sudo yum install fontconfig java-21-openjdk
sudo yum install jenkins
sudo systemctl daemon-reload
Weekly release
A new release is produced weekly to deliver bug fixes and features to users and plugin developers. It can be installed from the redhat yum repository.

sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io-2023.key
sudo yum upgrade
# Add required dependencies for the jenkins package
sudo yum install fontconfig java-21-openjdk
sudo yum install jenkins
Start Jenkins
You can enable the Jenkins service to start at boot with the command:

sudo systemctl enable jenkins
You can start the Jenkins service with the command:

sudo systemctl start jenkins
You can check the status of the Jenkins service using the command:

sudo systemctl status jenkins
If everything has been set up correctly, you should see an output like this:

Loaded: loaded (/lib/systemd/system/jenkins.service; enabled; vendor preset: enabled)
Active: active (running) since Tue 2023-06-22 16:19:01 +03; 4min 57s ago
...
If you have a firewall installed, you must add Jenkins as an exception. You must change YOURPORT in the script below to the port you want to use. Port 8080 is the most common.

YOURPORT=8080
PERM="--permanent"
SERV="$PERM --service=jenkins"

firewall-cmd $PERM --new-service=jenkins
firewall-cmd $SERV --set-short="Jenkins ports"
firewall-cmd $SERV --set-description="Jenkins port exceptions"
firewall-cmd $SERV --add-port=$YOURPORT/tcp
firewall-cmd $PERM --add-service=jenkins
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
Post-installation setup wizard
After downloading, installing and running Jenkins using one of the procedures above (except for installation with Jenkins Operator), the post-installation setup wizard begins.

This setup wizard takes you through a few quick "one-off" steps to unlock Jenkins, customize it with plugins and create the first administrator user through which you can continue accessing Jenkins.

Unlocking Jenkins
When you first access a new Jenkins controller, you are asked to unlock it using an automatically-generated password.

Browse to http://localhost:8080 (or whichever port you configured for Jenkins when installing it) and wait until the Unlock Jenkins page appears.

Unlock Jenkins page

From the Jenkins console log output, copy the automatically-generated alphanumeric password (between the 2 sets of asterisks).

Copying initial admin password
Note:

The command: sudo cat /var/lib/jenkins/secrets/initialAdminPassword will print the password at console.

If you are running Jenkins in Docker using the official jenkins/jenkins image you can use sudo docker exec ${CONTAINER_ID or CONTAINER_NAME} cat /var/jenkins_home/secrets/initialAdminPassword to print the password in the console without having to exec into the container.

On the Unlock Jenkins page, paste this password into the Administrator password field and click Continue.
Note:

The Jenkins console log indicates the location (in the Jenkins home directory) where this password can also be obtained. This password must be entered in the setup wizard on new Jenkins installations before you can access Jenkins’s main UI. This password also serves as the default administrator account’s password (with username "admin") if you happen to skip the subsequent user-creation step in the setup wizard.

Customizing Jenkins with plugins
After unlocking Jenkins, the Customize Jenkins page appears. Here you can install any number of useful plugins as part of your initial setup.

Click one of the two options shown:

Install suggested plugins - to install the recommended set of plugins, which are based on most common use cases.

Select plugins to install - to choose which set of plugins to initially install. When you first access the plugin selection page, the suggested plugins are selected by default.

If you are not sure what plugins you need, choose Install suggested plugins. You can install (or remove) additional Jenkins plugins at a later point in time via the Manage Jenkins > Plugins page in Jenkins.
The setup wizard shows the progression of Jenkins being configured and your chosen set of Jenkins plugins being installed. This process may take a few minutes.

Creating the first administrator user
Finally, after customizing Jenkins with plugins, Jenkins asks you to create your first administrator user.

When the Create First Admin User page appears, specify the details for your administrator user in the respective fields and click Save and Finish.

When the Jenkins is ready page appears, click Start using Jenkins.
Notes:

This page may indicate Jenkins is almost ready! instead and if so, click Restart.

If the page does not automatically refresh after a minute, use your web browser to refresh the page manually.

If required, log in to Jenkins with the credentials of the user you just created and you are ready to start using Jenkins!



# step to setup CD Pipeline using jenkins

# to give permission
 sudo chown -R jenkins:jenkins /var/www/html/API
