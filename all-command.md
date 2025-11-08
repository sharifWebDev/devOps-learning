# 🌐 Ultimate Server Management & DevOps Commands 

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
````

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
npm run dev
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
sudo nginx -t
sudo systemctl restart nginx
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

## 1️⃣4️⃣ Docker & DevOps

```bash
docker -v
docker-compose -v
docker ps
docker start container_name
docker stop container_name
docker-compose up -d
docker logs container_name
```

---

## 1️⃣5️⃣ Misc / Utilities

```bash
# Check system uptime
uptime

# Disk usage
df -h
du -sh /path/to/folder

# Memory usage
free -h

# Search in files
grep -R "search_term" /path/to/folder
``` 