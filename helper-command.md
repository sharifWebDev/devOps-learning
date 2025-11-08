````markdown
# Laravel Messenger Server Setup Guide

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

> Add your server block configuration inside (example for messenger.test):

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
```

### Check Nginx status

```bash
sudo systemctl status nginx
```

---

## 3️⃣ Database

* Local MySQL / MariaDB
* Database: `laravel_db`
* User: `sharif_user`
* Password: `root_password`
* `.env` file:

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

## 10️⃣ Get Server Public IP

```bash
curl ifconfig.me
```

---

## 11️⃣ Useful Nginx Commands

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl restart nginx
sudo systemctl status nginx
sudo nginx -t
```

---

## 12️⃣ Notes

* Always give storage & bootstrap/cache write permission.
* Make sure `.env` session settings match your server_name.
* Use `session.cookie_secure=false` for HTTP dev environment.
* For production with HTTPS, set `session.cookie_secure=true`.
* Use `sudo` if file edits fail in VS Code or nano.

```