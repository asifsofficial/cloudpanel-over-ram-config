
# 🛠️ PHP-FPM & MariaDB RAM Optimization Guide for VPS

This guide helps you troubleshoot and optimize your VPS when PHP-FPM pools or MariaDB consumes too much RAM. It also includes instructions to stop unnecessary PHP versions from running in the background.

---

## 📌 System Requirements (Example Used)

- **OS**: Ubuntu 24.04  
- **Panel**: CloudPanel  
- **Web Server**: Nginx  
- **DB**: MariaDB  
- **PHP**: 8.4 (active), others optional

---

## ✅ 1. Check Running PHP-FPM Processes

```bash
ps aux --sort=-%mem | grep php
```

Look for lines like:

```
php-fpm: pool yourdomain.com
php-fpm: master process (...)
```

---

## 🚫 2. Stop & Disable Unused PHP Versions

If you're only using PHP 8.4, disable others to free RAM:

```bash
# All in one command
for version in 7.1 7.2 7.3 7.4 8.0 8.1 8.2 8.3; do
    systemctl stop php${version}-fpm
    systemctl disable php${version}-fpm
done
```

> 🔍 List all PHP-FPM services:

```bash
systemctl list-units | grep php
```

---

## 🧠 3. Optimize Active PHP Pool (e.g., shopeybd.com)

Edit pool config:

```bash
nano /etc/php/8.4/fpm/pool.d/shopeybd.com.conf
```

Set recommended values:

```ini
pm = ondemand
pm.max_children = 10
pm.process_idle_timeout = 10s
pm.max_requests = 100
php_admin_value[memory_limit] = 256M
```

---

## 🔁 4. Reload PHP-FPM Service

```bash
systemctl reload php8.4-fpm
```

---

## 📊 5. Check RAM Usage

```bash
free -h
top -o %MEM
```

---

## 🩺 6. Check MariaDB Status

```bash
systemctl status mariadb
```

---

## 🧾 7. Monitor Logs for Errors

```bash
tail -n 50 /var/log/php8.4-fpm.log
tail -n 50 /var/log/mysql/error.log
```

---

## 🧹 8. (Optional) Remove Unused PHP Versions

⚠️ Only do this if you're sure:

```bash
apt remove php7.4 php7.4-fpm
apt remove php8.1 php8.1-fpm
apt autoremove
```

---

## ✅ Bonus: WP Plugin Monitoring

Use WP-CLI to list active plugins:

```bash
wp plugin list --status=active --fields=name,version
```

Or install **Query Monitor** for deeper insight from WP Dashboard.

---

## 📦 Future Upgrade

Create a shell script to automate:
- Disabling unused PHP versions
- Monitoring high RAM usage
- Restarting services if MariaDB stops

Let me know if you want this script added.

---

## 🔒 Credits

Maintained by [Asif](https://shopeybd.com) 💻  
Server environment powered by **Leaseweb**, tuned via **CloudPanel**.
