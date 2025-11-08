### **Fix: Give Proper Ownership for Editing Code Files**

1. Project এর owner ঠিক করো, যাতে তুমি এবং Nginx/PHP-FPM উভয়ই access পাবে:

```bash
# Step 1: Change ownership to your user + www-data group
sudo chown -R $USER:www-data /var/www/messenger2

# Step 2: Give proper permissions
sudo find /var/www/messenger2 -type d -exec chmod 775 {} \;   # Directories: 775
sudo find /var/www/messenger2 -type f -exec chmod 664 {} \;   # Files: 664

# Step 3: Make sure storage and cache are fully writeable
sudo chmod -R 775 /var/www/messenger2/storage
sudo chmod -R 775 /var/www/messenger2/bootstrap/cache
```

2. Project ফোল্ডারে write permission ঠিক করো:

```bash
sudo chmod -R 775 /var/www/messenger2
```

* `775` মানে owner এবং group লিখতে পারবে, অন্যরা read-only।
* VS Code বা nano থেকে এখন কোন file edit করতে সমস্যা হবে না।

--- 

