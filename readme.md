## Linux Ubuntu Local PC Environment Setup With a Project run 

---
## Github Setup

### 1️⃣ **SSH Key Generation & add into GitHub**

#### 1.1 **SSH Key Generate করা:**

```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/id_ed25519 -N ""
```

#### 1.2 **SSH Agent start and Key add:**

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
2. Title: `Ubuntu Server` (বা ইচ্ছামত)
3. Public key পেস্ট করে **Add SSH Key** চাপো।

#### 1.5 **SSH কানেকশন পরীক্ষা করা:**

```bash
ssh -T git@github.com
```

Success message:
```
Hi sharifWebDev! You've successfully authenticated, but GitHub does not provide shell access.
```

#### 1.6 **GitHub থেকে Repository ক্লোন করা:**

```bash
git clone git@github.com:sharifWebDev/pos-sell-scv-vue.git
```

---

### 2️⃣ **PHP, Nginx, MySQL, Composer Install**

#### 2.1 **nesseary software & tools:**

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
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
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

### 9️⃣ **SSL (mkcert) সেটআপ (Localhost Domain)**

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

Now Laravel + Nginx + PHP + MySQL + SSL (Let's Encrypt) ready। 🎉

---

চমৎকার প্রশ্ন ✅
তুমি এখন local test domain (`private-messenger.com` → `/etc/hosts` → 127.0.0.1) এ কাজ করছো, কিন্তু **যদি real domain (live server)** ব্যবহার করতে চাও, তাহলে নিচের ধাপগুলো আলাদা হবে 👇

---

## 🌐 যদি domain “real” (public) হয়

উদাহরণ: `example.com`
তাহলে তোমাকে ৩টি প্রধান জিনিস ঠিকমতো করতে হবে 👇

---

### **① Domain DNS → Server IP তে point করতে হবে**
 সার্ভারের **public IP** দেখো:

```bash
curl ifconfig.me
```

ধরা যাক IP হলো 👉 `103.41.114.119`

এখন domain provider (Namecheap, GoDaddy, Cloudflare etc) এ গিয়ে:
➡️ “DNS Settings” বা “Manage DNS” এ যাও
➡️ নিচের রেকর্ডগুলো যোগ করো:

| Type | Name | Value (IP / Target) | TTL  |
| ---- | ---- | ------------------- | ---- |
| A    | @    | 103.41.114.119      | Auto |
| A    | www  | 103.41.114.119      | Auto |

> 💡 এটা করতে 10–15 মিনিট (বা সর্বোচ্চ 24 ঘণ্টা) propagate হতে পারে।

---

### **② Nginx Configuration (real domain)**

```bash
sudo nano /etc/nginx/sites-available/laravel.conf
```

পুরো config হবে এইরকম 👇

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/messenger/public;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.4-fpm.sock; # PHP version অনুযায়ী
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Save → `Ctrl + O`, Enter, `Ctrl + X`

Test:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

### **③ Free SSL (Let's Encrypt / Certbot) সেটআপ করো**

(যেহেতু এটা real domain, তুমি Let's Encrypt use করতে পারো — এটা ফ্রি ✅)

```bash
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```

তারপর নির্দেশনা অনুসারে:

* Email দেবে
* Agree করবে
* “Redirect all traffic to HTTPS” (Option 2) বেছে নেবে

---

### **④ SSL Renew (every 90 days)**

Let's Encrypt সার্টিফিকেট 90 দিনের জন্য বৈধ।
Auto renew নিশ্চিত করতে cron job ব্যবহার করো 👇

```bash
sudo crontab -e
```

নিচে যোগ করো:

```
0 3 * * * certbot renew --quiet
```

এটা প্রতিদিন ভোর ৩টায় renew চেক করবে।

---

### ✅ একদম সংক্ষিপ্ত Version (live domain setup)

```bash
# 1. Point domain DNS -> Server IP
# 2. Configure nginx
sudo nano /etc/nginx/sites-available/laravel.conf

# 3. Enable & reload
sudo ln -sf /etc/nginx/sites-available/laravel.conf /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx

# 4. Install SSL
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```
---

🔒 ফলাফল: ওয়েবসাইট `https://example.com` (SSL সহ) খুলবে এবং auto-renew থাকবে।

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
  ``


To see all the registered domains and their associated IPs (for local and external networks), you can use various tools depending on your requirements. Below are some commands that might help:

### 1. **List of Domains (DNS Records) for your server (local/remote domains)**

#### 1.1 **List Domains from `/etc/hosts` (Local System)**

The `/etc/hosts` file contains mappings of hostnames to IP addresses. You can see all the locally registered domain names here.

```bash
cat /etc/hosts
```

Example output:

```
127.0.0.1    localhost
127.0.1.1    dev-server
```

#### 1.2 **DNS Records Lookup for Domains**

If you want to find out the IP addresses of a registered domain, you can use `nslookup` or `dig`.

**Using `nslookup`:**

```bash
nslookup <domain_name>
```

Example:

```bash
nslookup example.com
```

**Using `dig`:**

```bash
dig <domain_name>
```

Example:

```bash
dig example.com
```

These will give you the IP address(es) associated with the domain name.

### 2. **List of Registered Domains on Your Server (Apache/Nginx Virtual Hosts)**

#### 2.1 **For Nginx:**

To list all the domain configurations registered in Nginx, you can check the `sites-available` and `sites-enabled` directories.

```bash
ls /etc/nginx/sites-available/
```

This will show you the domain configurations for your web server (Nginx).

To list which domains are currently active (i.e., enabled):

```bash
ls /etc/nginx/sites-enabled/
```

#### 2.2 **For Apache (if used):**

You can list all the enabled virtual hosts for Apache by checking the `sites-enabled` directory.

```bash
ls /etc/apache2/sites-enabled/
```

### 3. **Listing IP Addresses (Network Interfaces)**

To see the IP addresses of your system (local and external), use:

#### 3.1 **Using `ip` command:**

```bash
ip a
```

This will display all the network interfaces and their IP addresses.

#### 3.2 **Using `ifconfig` (older command, might not be installed by default):**

```bash
ifconfig
```

#### 3.3 **Using `hostname -I` (shows all IPs associated with the system):**

```bash
hostname -I
```

### 4. **List All Domains Registered to a Specific IP Address**

If you want to know all domains associated with an IP (this would be from DNS reverse lookups), you can use `host` or `dig`:

```bash
dig -x <ip_address>
```

Example:

```bash
dig -x 103.41.114.119
``` 

### Summary of Commands:

1. **Local domains:**

   ```bash
   cat /etc/hosts
   ```

2. **DNS record lookup:**

   ```bash
   nslookup <domain_name>
   dig <domain_name>
   ```

3. **Nginx registered domains:**

   ```bash
   ls /etc/nginx/sites-available/
   ls /etc/nginx/sites-enabled/
   ```

4. **Apache registered domains:**

   ```bash
   ls /etc/apache2/sites-enabled/
   ```

5. **IP addresses of your system:**

   ```bash
   ip a
   hostname -I
   ```

6. **Reverse DNS lookup for an IP:**

   ```bash
   dig -x <ip_address>
   ```
 