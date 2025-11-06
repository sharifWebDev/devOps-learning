<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Local PC</title>
</head>
<body>
    এখানে সমস্ত প্রক্রিয়া সঠিকভাবে পদক্ষেপে সাজানো হলো যাতে তুমি পর্যায়ক্রমে সহজে সেটআপ করতে পারো।

---

### 1️⃣ **SSH Key Generation এবং GitHub এ যোগ করা**

#### 1.1 **SSH Key Generate করা:**

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""
```

এটি একটি নতুন SSH key তৈরি করবে। তোমার **[email@example.com](mailto:email@example.com)** এই অংশটি তোমার GitHub এর email দিয়ে replace করতে হবে।

#### 1.2 **SSH Agent চালু এবং Key যোগ করা:**

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

#### 1.3 **Public Key কপি করা:**

```bash
cat ~/.ssh/id_ed25519.pub
```

এই আউটপুট কপি করে GitHub-এ যুক্ত করো।

#### 1.4 **GitHub এ SSH Key যোগ করা:**

1. GitHub → **Settings** → **SSH and GPG keys** → **New SSH key**
2. Title: `Ubuntu Server` (বা তোমার ইচ্ছামত)
3. Public key পেস্ট করে **Add SSH Key** চাপো।

#### 1.5 **SSH কানেকশন পরীক্ষা করা:**

```bash
ssh -T git@github.com
```

এই কমান্ডটি সফল হলে:

```
Hi sharifWebDev! You've successfully authenticated, but GitHub does not provide shell access.
```

#### 1.6 **GitHub থেকে Repository ক্লোন করা:**

```bash
git clone git@github.com:sharifWebDev/pos-sell-scv-vue.git
```

---

### 2️⃣ **PHP, Nginx, MySQL, Composer ইনস্টলেশন**

#### 2.1 **প্রয়োজনীয় সফটওয়ার ইনস্টল করা:**

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
```

#### 2.2 **PHP 8.4, Nginx, MySQL এবং Composer ইনস্টল করা:**

```bash
sudo apt install -y nginx php8.4 php8.4-cli php8.4-fpm php8.4-mysql php8.4-xml php8.4-curl php8.4-zip php8.4-mbstring unzip git curl
```

#### 2.3 **PHP ভার্সন চেক করা:**

```bash
php -v
```

---

### 3️⃣ **Composer ইনস্টলেশন**

#### 3.1 **পুরনো Composer মুছে ফেলা (যদি থাকে):**

```bash
sudo rm -f /usr/local/bin/composer
```

#### 3.2 **Composer ইনস্টল করা:**

```bash
curl -sS https://getcomposer.org/installer -o composer-setup.php
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

#### 3.3 **Composer ভার্সন চেক করা:**

```bash
composer --version
```

---

### 4️⃣ **Node.js ইনস্টলেশন**

#### 4.1 **NVM ইনস্টল করা:**

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

#### 4.2 **NVM লোড করা (শেল রিস্টার্ট করার পরিবর্তে):**

```bash
\. "$HOME/.nvm/nvm.sh"
```

#### 4.3 **Node.js এবং NPM ইনস্টল করা:**

```bash
nvm install 24
```

#### 4.4 **Node.js এবং NPM ভার্সন চেক করা:**

```bash
node -v   # v24.11.0 হবে
npm -v    # 11.6.1 হবে
```

---

### 5️⃣ **MySQL ইনস্টলেশন ও কনফিগারেশন**

#### 5.1 **MySQL ইনস্টল করা:**

```bash
sudo apt update
sudo apt install mysql-server -y
```

#### 5.2 **MySQL লগইন করা:**

```bash
sudo mysql
```

#### 5.3 **ডাটাবেজ ও ইউজার তৈরি করা:**

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
FLUSH PRIVILEGES;

CREATE DATABASE laravel_db;
CREATE USER 'sharif_user'@'localhost' IDENTIFIED BY 'root_password';
GRANT ALL PRIVILEGES ON laravel_db.* TO 'sharif_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

### 6️⃣ **Laravel প্রজেক্ট ডিপ্লয় করা**

#### 6.1 **GitHub থেকে Laravel প্রজেক্ট ক্লোন করা:**

```bash
cd /var/www/
git clone https://github.com/yourusername/your-laravel-project.git
cd your-laravel-project
```

#### 6.2 **.env ফাইল কনফিগার করা:**

```bash
cp .env.example .env
nano .env
```

এখানে ডাটাবেজ কনফিগারেশনটি দিন।

#### 6.3 **ডিপেনডেন্সি ইনস্টল করা:**

```bash
php artisan key:generate
composer install
npm install
npm run dev
npm run build
```

#### 6.4 **পারমিশন সেট করা:**

```bash
chown -R www-data:www-data /var/www/your-laravel-project
chmod -R 775 storage bootstrap/cache
```

---

### 7️⃣ **Nginx কনফিগারেশন (Laravel)**

#### 7.1 **Nginx কনফিগারেশন ফাইল তৈরি করা:**

```bash
sudo nano /etc/nginx/sites-available/laravel.conf
```

এখানে নীচের কনফিগারেশনটি পেস্ট করুন:

```nginx
server {
    listen 80;
    server_name your_domain.com;
    root /var/www/your-laravel-project/public;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.4-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

#### 7.2 **সাইট একটিভ করা:**

```bash
sudo ln -s /etc/nginx/sites-available/laravel.conf /etc/nginx/sites-enabled/
sudo nginx -t && systemctl restart nginx
```

---

### 8️⃣ **Firewall (UFW) কনফিগার করা**

```bash
ufw allow OpenSSH
ufw allow 'Nginx Full'
ufw enable
```

---

### 9️⃣ **SSL (mkcert) সেটআপ (Local Domain)**

#### 9.1 **mkcert ইনস্টল করা:**

```bash
sudo apt install -y mkcert libnss3-tools
mkcert -install
```

#### 9.2 **SSL সার্টিফিকেট তৈরি করা:**

```bash
cd /etc/ssl
sudo mkcert private-messenger.com
```

#### 9.3 **Nginx কনফিগারেশন (SSL) আপডেট করা:**

```bash
sudo nano /etc/nginx/sites-available/laravel.conf
```

```nginx
server {
    listen 443 ssl;
    server_name private-messenger.com;

    ssl_certificate     /etc/ssl/private-messenger.com.pem;
    ssl_certificate_key /etc/ssl/private-messenger.com-key.pem;

    root /var/www/messenger/public;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

#### 9.4 **Nginx রিলোড করা:**

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

### 🔟 **Real Domain HTTPS সেটআপ (Let's Encrypt)**

#### 10.1 **DNS রেকর্ড আপডেট করা:**

```
A record for @ and www pointing to your server's IP
```

#### 10.2 **Let's Encrypt SSL (Certbot) ইনস্টল করা:**

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

#### 10.3 **HTTP থেকে HTTPS রিডাইরেক্ট:**

* Certbot এর মধ্যে যখন HTTP → HTTPS redirect এর অপশন আসে, তখন **Yes** সিলেক্ট করতে হবে।

#### 10.4 **Auto Renew চেক করা:**

```bash
sudo systemctl status certbot.timer
sudo certbot renew --dry-run
```

---

এখন তোমার Laravel + Nginx + PHP + MySQL + SSL (Let's Encrypt) ready। 🎉

---

**VPS Management Commands:**

* **Server update:**

  ```
  ```


bash
apt update && apt upgrade -y

````
- **Restart server:**
```bash
reboot
````

* **Restart Nginx service:**

  ```bash
  systemctl restart nginx
  ```
* **Check logs:**

  ```bash
  tail -f /var/log/nginx/error.log
  ```
* **Fix permissions:**

  ```bash
  chmod -R 775 storage bootstrap/cache
  php artisan optimize:clear
  ```

</body>
</html>
 
