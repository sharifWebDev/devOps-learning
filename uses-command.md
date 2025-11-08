### Laravel Server Setup Guide

## 1️⃣ Project Ownership & Permissions

Give proper ownership and permissions to Laravel project:

```bash
# Change ownership to your user and www-data group
sudo chown -R $USER:www-data /var/www/messenger2

# Directories: 775
sudo find /var/www/messenger2 -type d -exec chmod 775 {} \;

# Files: 664
sudo find /var/www/messenger2 -type f -exec chmod 664 {} \;

# Make storage & cache fully writeable
sudo chmod -R 775 /var/www/messenger2/storage
sudo chmod -R 775 /var/www/messenger2/bootstrap/cache
````

---

## 2️⃣ Nginx Setup

### Create server block

```bash
sudo nano /etc/nginx/sites-available/laravel.conf
```

> Example server block for `messenger.test`:

```nginx
server {
    listen 80;
    server_name messenger.test;

    root /var/www/messenger2/public;
    index index.php index.html index.htm;

    access_log /var/log/nginx/laravel_access.log;
    error_log  /var/log/nginx/laravel_error.log;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;

        # Session & Cookie support
        fastcgi_param PHP_VALUE "session.save_handler = files
session.save_path = /var/lib/php/sessions
session.cookie_httponly = 1
session.cookie_secure = 0";
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

### Enable & reload Nginx

```bash
sudo ln -s /etc/nginx/sites-available/laravel.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl status nginx
```

---

## 3️⃣ Database

* MySQL / MariaDB
* Database: `laravel_db`
* User: `sharif_user`
* Password: `root_password`

`.env` file:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=sharif_user
DB_PASSWORD=root_password
```

---

## 4️⃣ Laravel Commands

```bash
php artisan migrate --seed
php artisan serve --port=8000
php artisan reverb:start --host=127.0.0.1 --port=5173
php artisan queue:listen

npm install
npm run dev
```

---

## 5️⃣ `.env` Session Fix

```dotenv
SESSION_DRIVER=file
SESSION_LIFETIME=120
SESSION_DOMAIN=messenger.test
SESSION_SECURE_COOKIE=false
SESSION_COOKIE=messenger_session
SESSION_ENCRYPT=false
SESSION_PATH=/
SESSION_SAME_SITE=strict
```

---

## 6️⃣ Check Open Ports

```bash
sudo ss -tuln
sudo netstat -tulnp
sudo lsof -i :80
```

---

## 7️⃣ Local Domains (/etc/hosts)

```bash
sudo nano /etc/hosts
```

Add:

```
127.0.0.1   messenger.test
127.0.0.1   proptinize.test
127.0.0.1   fast-chat.duckdns.org
```

---

## 8️⃣ Firewall (UFW)

```bash
sudo ufw allow 'Nginx Full'
sudo ufw status
sudo ufw enable
```

---

## 9️⃣ Certbot / SSL (optional)

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot -y
sudo apt update
sudo apt install -y certbot python3-certbot-nginx

# Generate SSL
sudo certbot --nginx -d private-messenger.com

# Test auto-renew
sudo certbot renew --dry-run
```

---

## 🔟 Get Server Public IP

```bash
curl ifconfig.me
```

---

# Ultimate Ubuntu Server Command Reference

## 1️⃣ User & Permission Management

```bash
whoami
sudo -i
sudo adduser username
sudo usermod -aG groupname username
sudo chown -R user:group /path/to/folder
sudo chmod 775 /path/to/folder
sudo chmod 664 /path/to/file
ls -la /path/to/folder
groups username
```

---

## 2️⃣ File & Directory Handling

```bash
ls -la
ls -lh
cp file.txt /destination/
cp -r folder/ /destination/
mv oldname newname
mv file.txt /destination/
rm file.txt
rm -rf folder/
mkdir foldername
nano filename
sudo nano filename
cat filename
less filename
tail -f filename
```

---

## 3️⃣ Nginx Management

```bash
nginx -v
sudo nginx -t
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl enable nginx
sudo systemctl status nginx
ls /etc/nginx/sites-enabled/
sudo nano /etc/nginx/sites-available/example.conf
sudo ln -s /etc/nginx/sites-available/example.conf /etc/nginx/sites-enabled/
```

---

## 4️⃣ PHP & Laravel Commands

```bash
php -v
php artisan serve --port=8000
php artisan migrate
php artisan migrate --seed
php artisan db:seed
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear
php artisan queue:listen
php artisan reverb:start --host=127.0.0.1 --port=5173
composer install
composer update
composer dump-autoload
```

---

## 5️⃣ NodeJS & Frontend

```bash
node -v
npm -v
npm install
npm run dev
npm run build
```

---

## 6️⃣ System Monitoring & Process Management

```bash
ps aux
top
htop
sudo ss -tuln
sudo netstat -tulnp
sudo lsof -i :80
sudo lsof -i :443
sudo kill -9 PID
tail -f /var/log/nginx/access.log
tail -f /var/log/nginx/error.log
tail -f /var/log/syslog
tail -f /var/www/messenger2/storage/logs/laravel.log
```

---

## 7️⃣ Networking

```bash
ip a
ifconfig
ping google.com
traceroute google.com
nslookup example.com
dig example.com
curl ifconfig.me
sudo ss -tuln
sudo ufw status
```

---

## 8️⃣ Firewall (UFW)

```bash
sudo ufw allow 'Nginx Full'
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw allow 80/tcp
sudo ufw enable
sudo ufw status verbose
```

---

## 9️⃣ MySQL / MariaDB Commands

```bash
mysql -u username -p
SHOW DATABASES;
USE database_name;
SHOW TABLES;
EXIT;
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON database_name.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
```

---

## 🔟 SSL / Certbot

```bash
sudo apt update
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
sudo certbot renew --dry-run
sudo systemctl status certbot.timer
```

---

## 1️⃣1️⃣ Git / Version Control

```bash
git clone https://github.com/username/repo.git
git status
git add .
git commit -m "Commit message"
git push origin branch
git pull origin branch
```

---

## 1️⃣2️⃣ System Updates / Package Management

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install package_name
sudo apt remove package_name
sudo apt purge package_name
```

---

## 1️⃣3️⃣ Debugging Laravel Issues

```bash
sudo chown -R $USER:www-data /var/www/messenger2
sudo chmod -R 775 /var/www/messenger2/storage
sudo chmod -R 775 /var/www/messenger2/bootstrap/cache
SESSION_DRIVER=file
SESSION_DOMAIN=yourdomain.test
SESSION_SECURE_COOKIE=false
php artisan config:clear
php artisan cache:clear
php artisan route:clear
php artisan view:clear
```

---

## 1️⃣4️⃣ Docker & DevOps (optional)

```bash
docker -v
docker-compose -v
docker ps
docker start container_name
docker stop container_name
docker-compose up -d
docker logs container_name
```