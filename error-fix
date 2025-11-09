## That `413 Request Entity Too Large` error means **Nginx is rejecting your upload because the file size exceeds its default upload limit (1MB)**.
  
### 🧱 Step 1 — Open your Laravel Nginx site config

```bash
sudo nano /etc/nginx/sites-available/laravel.conf
```
  

### ⚙️ Step 2 — Add proper directives inside the `server { ... }` block
 
```nginx
server {
    listen 80;
    server_name example.com;   # change to your domain or IP
    root /var/www/system_builder/public;

    index index.php index.html;

    # ✅ Prevent "413 Request Entity Too Large"
    client_max_body_size 100M;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.4-fpm.sock;  # use correct PHP version
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    access_log /var/log/nginx/laravel_access.log;
    error_log  /var/log/nginx/laravel_error.log;
}
```
 

### 🧾 Step 3 — Test and reload Nginx
 
```bash
sudo nginx -t
```  

```bash
sudo systemctl reload nginx
```

---

### ⚙️ Step 4 — Update PHP upload limits (optional but recommended)

Edit:

```bash
sudo nano /etc/php/8.4/fpm/php.ini
```

Find and set:

```ini
upload_max_filesize = 100M
post_max_size = 100M
```

Restart PHP-FPM:

```bash
sudo systemctl restart php8.4-fpm
```

---
 
If you often upload **very large files (100MB+)**, also add:

```nginx
proxy_read_timeout 300s;
fastcgi_read_timeout 300s;
``` 
 
