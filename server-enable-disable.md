### ✅ Step 1 — Follow the standard Nginx  server enable disable

```
/etc/nginx/sites-available/   → সব project config রাখবে  
/etc/nginx/sites-enabled/     → যেগুলো চালু থাকবে তাদের symbolic link রাখবে
```

---

### ⚙️ Step 2 — Check your current enabled projects

```bash
ls /etc/nginx/sites-enabled/
```
All active project list show:

```
default
messenger.conf
system_builder.conf 
```
 
### 🧹 Step 3 — Disable all except one project

ধরা যাক তুমি শুধু `system_builder.conf` চালু রাখতে চাও।
```bash
sudo find /etc/nginx/sites-enabled/ -type l ! -name "system_builder.conf" -exec sudo rm {} \;
```
--- 
### 🚀 Step 5 — Test & Reload Nginx

```bash
sudo nginx -t && sudo systemctl reload nginx
```
 
---

### 🧰 Step 6 — (Optional) Enable/Disable later easily

* **Enable new project:**

  ```bash
  sudo ln -s /etc/nginx/sites-available/messenger.conf /etc/nginx/sites-enabled/
  sudo systemctl reload nginx
  ```

* **Disable project:**

  ```bash
  sudo rm /etc/nginx/sites-enabled/messenger.conf
  sudo systemctl reload nginx
  ```
 
 
 
