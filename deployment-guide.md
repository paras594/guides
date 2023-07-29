
## Deploy on aws ec2

#### Quick Access
```bash
# nginx file for proxy
sudo nano /etc/nginx/sites-available/yo-api.parkmate.in 

# check disk usage on aws
diskusage
```

---

#### SSH into ec2 instance

- `.pem` file is required
- put this file in `~/.ssh`
- change the permission for the file:

```bash
# Linux
sudo chmod 400 file.pem

# Winodws

- icacls .\\private.key /inheritance:r
- icacls .\\private.key /grant:r "%username%":"(R)"
```

- in ec2 instance info, click on connect to get the connection info.
- from the ec2 instance info, get the public DNS URL and user name

username: `ubuntu`

dns url: `ec2-3-110-98-195.ap-south-1.compute.amazonaws.com`

- then use the following command to access the instance

```bash
ssh -i "parkmate.pem" ubuntu@ec2-3-110-98-195.ap-south-1.compute.amazonaws.com
```

- to exit instance, type `exit` and enter

---

#### Articles

aws and nodejs guide: [https://aws.plainenglish.io/deploying-a-nodejs-application-in-aws-ec2-c1618b9b3874](https://aws.plainenglish.io/deploying-a-nodejs-application-in-aws-ec2-c1618b9b3874)

vps and nodejs guide: [https://dev.to/christopherkapic/how-to-deploy-a-nodejs-server-with-nginx-on-a-vps-43mb](https://dev.to/christopherkapic/how-to-deploy-a-nodejs-server-with-nginx-on-a-vps-43mb)

pm2 and nodejs guide: [https://mxd.codes/articles/hosting-next-js-private-server-pm2-github-webhooks-ci-cd](https://mxd.codes/articles/hosting-next-js-private-server-pm2-github-webhooks-ci-cd)

nginx proxy_pass remove url substring: [https://serverfault.com/questions/699695/nginx-proxy-pass-remove-url-substring](https://serverfault.com/questions/699695/nginx-proxy-pass-remove-url-substring)

---

#### Setting up the instance/machine

**Install ubuntu updates**

```bash
sudo apt update 
sudo apt upgrade
```

**Install Nginx server, certbot and openssl**

```bash
sudo apt install nginx python3-certbot-nginx openssl
```

**Install Nodejs**

```bash
# install node version manager (nvm)
# get updated command from: 
<https://github.com/nvm-sh/nvm>

curl -o- <https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh> | bash
source ~/.bashrc

# install node
nvm install 16

# check installation
node -v
npm -v
```

**Install and setup git**

```bash
sudo apt-get install git
# check installation
git --version

git config --global user.name "pspotdev"
git config --global user.email "pspotdev@gmail.com"
```

**Install pm2 globally**

```bash
npm install pm2 -g
pm2 -v
```

**Clone Repositories in home directory**

```bash
cd ~
git clone <https://github.com/pspotdev/pspot-user-valet-api.git>
git clone <https://github.com/pspotdev/pspot-admin-api.git>
# add env files to both the repos
# install the packages
npm install
```

**Setup sub-domain for the aws server**

1. create elastic ip in aws and associate it with the instance
    1. [https://w3path.com/point-domain-to-aws-ec2-instance/](https://w3path.com/point-domain-to-aws-ec2-instance/)
2. go to cpanel, and in dns zone editor, add A name record with public ipv4 ip address for the ec2 instance, as specified in above post.

#### **Configure Nginx Proxy for the servers**

```bash
sudo nano /etc/nginx/sites-available/yo-api.parkmate.in 

# add the following configuration 
server {  
	listen 80;  
	server_name yo-api.parkmate.in;  
	location /main/ {    
		if ($request_uri ~* "/main/(.*)") { # this line strips the /main/ from url      
			proxy_pass <http://127.0.0.1:5000/$1>; # for user-valet-api    
		}  
	}  
	location /admin/ {    
		if ($request_uri ~* "/main/(.*)") {      
			proxy_pass <http://127.0.0.1:5003/$1>; # for admin api    
		}  
	}
}
# after saving the configuration, create symbolic link 
sudo ln -s /etc/nginx/sites-available/yo-api.parkmate.in /etc/nginx/sites-enabled

# check if configuration is ok or not
sudo nginx -t

# restart nginx to 
sudo systemctl restart nginx 

# make nginx run on system startup/reboot
sudo systemctl enable nginx
```

#### **Configure PM2**

```bash
cd ~ && pm2 init # this generated ecosystem.config.js

nano ecosystem.config.js 

# add the following
module.exports = {  
	apps: [    
		{      
			name: "pspot-user-valet-api",      
			cwd: "/home/ubuntu/pspot-user-valet-api",
      script: "npm",
      args: "start"
    },
    {
      name: "pspot-admin-api",
      cwd: "/home/ubuntu/pspot-admin-api",
      script: "npm",
      args: "start"
    }
  ]
};

# then run pm2 with that config
pm2 start ecosystem.config.js

# check running services
pm2 status

# Make PM2 run automatically on system startup/reboot
pm2 startup systemd

# copy paste the generated command and run it, 
# e.g command [DO NOT COPY PASTE THIS ONE]:
sudo env PATH=$PATH:/home/ubuntu/.nvm/versions/node/v16.14.0/bin /home/ubuntu/.nvm/versions/node/v16.14.0/lib/node_modules/pm2/bin/

pm2 startup systemd -u ubuntu --hp /home/ubuntu

# save the new process list
pm2 save
```

**Enable HTTPs for the subdomain using self generated ssl**

```bash
# this generates ssl certificate for the url and add it to nginx conf of that url
sudo certbot --nginx -d yo-api.parkmate.in

sudo nginx -t
sudo systemctl restart nginx
```

---

#### Extra command utilities

```bash
# kill service running on a port
sudo kill -9 $(lsof -t -i:3000)

# remove / flush pm2 logs
pm2 flush
```

`du -h` command lets you see all the files and their size in the current disk and shows all subdirectories too , to just see the first level :

---

## Deploy Node on vps

1. ssh to the server
2. install mysql. setup mysql_secure_installation
3. install nginx
4. to help nginx access port 80, install ufw
5. grant OpenSSH permission through ufw
6. allow permission for http
7. allow permission for Nginx HTTP
8. enable and activate ufw
9. stop and remove apache2
10. create directory in var/www and change owndership and chmod for the directory
11. create nginx configuration
12. restart nginx and test the ip if nginx is serving html
13. install wget and install node 


https://www.youtube.com/watch?v=D7GaDfXkFbU

**install package:**
```bash
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y nodejs build-essential mysql-server mysql-client
```

use `npm ci` instead of `npm install`

---


## Deploying Nodejs Mongodb on heroku 


**Setup Database on mongodb**

1. visit mongodb atlas, create account, and create cluster

2. create a database user

3. get the connection URI and add it to project

   ```js
   `mongodb+srv://${MONGO_USERNAME}:${MONGO_PASSWORD}@cluster0.zaceq.mongodb.net/${dbName}?retryWrites=true&w=majority`
   ```

**Deploying the api on heroku**

1. create a start script in package.json -> `node server.js`

2. add the following info in package.json

   ```
   "engines": {
       "node": ">=10.0.0",
       "npm": ">=6.0.0"
   },
   ```

   

3. login heroku -> `heroku login`

4. inside the project, create heroku project -> `heroku create project-name`

5. create a Procfile

   ```
   web: node server.js
   ```

6. commit all changes and push code to github 

7. after pushing to github push changes to heroku -> `git push heroku master`

8. login to heroku dashboard in browser and go to your project. inside it add config vars and add all your environment variables

9. to test the app you can open app from dashboard or run `heroku open` inside project