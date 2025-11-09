## **`test-app1` + `test-app2` Laravel projects** একসাথে **Localhost + SSL** সহ ready-to-use **step-by-step guide**

---

# **Linux Ubuntu – Multiple Laravel Projects Setup with SSL (Localhost)**

## **Pre-requisites**

* PHP, Nginx, MySQL, Composer, Node.js/NPM, mkcert সব ইনস্টল করা আছে।
* দুইটি প্রজেক্ট:

  ```
  /var/www/test-app1
  /var/www/test-app2
  ```

---

## **Step 1: Hosts File Entry**

```bash
sudo nano /etc/hosts
```

Add:

```
127.0.0.1   test-app1.local
127.0.0.1   test-app2.local
```

Save → `Ctrl+O`, Exit → `Ctrl+X`

---

## **Step 2: Generate SSL Certificates (mkcert)**

1. Mkcert install (যদি না থাকে):

```bash
sudo apt install -y mkcert libnss3-tools
mkcert -install
```

2. Certificates তৈরি করো `/etc/ssl` ফোল্ডারে:

```bash
cd /etc/ssl 
sudo mkcert test-app2.local
```

Certificates তৈরি হবে:

``` 
test-app2.local.pem
test-app2.local-key.pem
```

---

## **Step 3: Clone & Configure Projects**

### **3.1 Clone test-app2**

```bash
cd /var/www/
git clone git@github.com:yourusername/test-app2.git
cd test-app2
```

### **3.2 Environment setup**

```bash
cp .env.example .env
nano .env
```

Set DB and other settings:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel_db2
DB_USERNAME=sharif_user
DB_PASSWORD=root_password
```

---

### **3.3 Install dependencies**

```bash
composer install
//or
composer update --ignore-platform-reqs
php artisan key:generate
npm install
npm run dev
npm run build
```

---

### **3.4 Permissions**

```bash
sudo chown -R www-data:www-data /var/www/test-app2
sudo chmod -R 775 /var/www/test-app2/storage /var/www/test-app2/bootstrap/cache
```
 
---

## **Step 4: Nginx Configuration**

### **4 test-app2 SSL Config**

```bash
sudo nano /etc/nginx/sites-available/test-app2.conf
```

```nginx
server {
    listen 80;
    server_name test-app2.local;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name test-app2.local;

    ssl_certificate     /etc/ssl/test-app2.local.pem;
    ssl_certificate_key /etc/ssl/test-app2.local-key.pem;

    root /var/www/test-app2/public;
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

---

## **Step 5: Enable Sites & Reload Nginx**

```bash 
sudo ln -sf /etc/nginx/sites-available/test-app2.conf /etc/nginx/sites-enabled/

sudo nginx -t
sudo systemctl reload nginx
```

---

## **Step 6: Verify Permissions**

```bash
sudo chown -R $USER:www-data /var/www/app_name
sudo chown -R www-data:www-data /var/www/test-app2 
sudo chmod -R 775 /var/www/test-app2/storage /var/www/test-app2/bootstrap/cache
```

---

## **Step 7: Access in Browser**

* Test App2: `https://test-app2.local`

> Both apps should work with SSL.

---

## **Tips for Multiple Projects**

1. প্রতিটি project এর জন্য **unique `server_name`** + **SSL certificate**।
2. HTTP → HTTPS redirect config রাখা ভালো।
3. Database আলাদা হলে project conflict কম হয়।

---

এইভাবে তুমি একই সার্ভারে **অসীম সংখ্যক Laravel projects** Localhost + SSL সহ run করতে পারো।

---

যদি চাও, আমি তোমার জন্য একটি **single Nginx master config** বানাতে পারি যেখানে দুইটি প্রজেক্ট একসাথে থাকবে, যাতে future project add করা আরও সহজ হয়।

চাও কি আমি করি?
