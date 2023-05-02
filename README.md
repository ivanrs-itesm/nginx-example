# Installing Node

    curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
    sudo bash nodesource_setup.sh
    sudo apt install nodejs
    node -v
    sudo apt install build-essential
    sudo apt install npm

Create server file <code>app.js</code> and run with <code> node app.js </code>

# Installing PM2

    sudo npm install pm2@latest -g
    pm2 start app.js
    pm2 startup systemd

# Installing NIGNX

    sudo apt update
    sudo apt install nginx

## Configure firewall
    sudo ufw app list
    sudo ufw allow 'Nginx HTTP’
    sudo ufw status
    Sudo ufw enable
    
## Check nginx web server
    systemctl status nginx

Test <code>http://your_server_ip</code>

# Setting Default Block NGINX

Inside **/etc/nginx/site-available/default**

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


## Setting up NGINX (Blocks)

    sudo mkdir -p /var/www/itesm502.online/html
    sudo chown -R $USER:$USER /var/www/itesm502.online/html
    sudo chmod -R 755 /var/www/itesm502.online
    sudo nano /var/www/itesm502.online/html/index.html

HTML:

    <html>
        <head>
            <title>Welcome to ITESM!</title>
        </head>
        <body>
            <h1>Success!  The ITESM server block is working!</h1>
        </body>
    </html>

Configuration

    sudo nano /etc/nginx/sites-available/itesm502.online

config file:

    server {
            listen 80;
            listen [::]:80;

            root /var/www/itesm502.online/html;
            index index.html index.htm index.nginx-debian.html;

            server_name itesm502.online www.itesm502.online;

            location / {
                    try_files $uri $uri/ =404;
            }
    }

A link to the sites-enabled

    sudo ln -s /etc/nginx/sites-available/itesm502.online /etc/nginx/sites-enabled/

Remove the comment on <code>server_names_hash_bucket_size</code>

    sudo nano /etc/nginx/nginx.conf

Test NGINX

    sudo nginx -t

# SSL with Let's Encrypt

    sudo apt install certbot python3-certbot-nginx
    sudo nano /etc/nginx/sites-available/itesm502.online
    sudo nginx –t
    sudo systemctl reload nginx
    sudo certbot --nginx -d itesm502.online -d www.itesm502.online

# Reverse Proxy

sudo nano /etc/nginx/sites-available/itesm502.online

Replace location/ with

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


Then:

    sudo nginx -t
    sudo systemctl restart nginx
