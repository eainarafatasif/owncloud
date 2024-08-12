# owncloud
Owncloud is an open-source cloud platform for storing, sharing, and managing files on your own server. It offers a secure, self-hosted alternative to services like Google Drive, giving you full control over your data. With customizable apps, it supports collaboration, making it ideal for both personal and business use.



# ownCloud Core

[![Build Status](https://drone.owncloud.com/api/badges/owncloud/core/status.svg?branch=master)](https://drone.owncloud.com/owncloud/core)
[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=owncloud_core&metric=alert_status)](https://sonarcloud.io/dashboard?id=owncloud_core)
[![Security Rating](https://sonarcloud.io/api/project_badges/measure?project=owncloud_core&metric=security_rating)](https://sonarcloud.io/dashboard?id=owncloud_core)
[![Coverage](https://sonarcloud.io/api/project_badges/measure?project=owncloud_core&metric=coverage)](https://sonarcloud.io/dashboard?id=owncloud_core)
[![Design](https://contribute.design/api/shield/owncloud/core)](https://contribute.design/owncloud/core)

**[ownCloud](http://ownCloud.com) offers file sharing and collaboration trusted by 200+ million users worldwide regardless of device or location.**

![](https://github.com/owncloud/screenshots/blob/master/files/sidebar_1.png)

## Why Is This so Awesome?
* :file_folder: **Access your Data** You can store your files, contacts, calendars and more on a server of your choosing.
* :package: **Sync your Data** You keep your files, contacts, calendars and more synchronized amongst your devices.
* :arrows_counterclockwise: **Share your Data** You share your data with others, and give them access to your latest photo galleries, your calendar or anything else you want them to see.
* :rocket: **Expandable with dozens of Apps** ...like Calendar, Contacts, Mail or News.
* :cloud: **All Benefits of the Cloud** ...on your own Server.
* :lock: **Encryption** You can encrypt data in transit with secure https connections. You can enable the encryption app to encrypt data on storage for improved security and privacy.
* ...

## Installation Instructions
For installing ownCloud, see the official
[ownCloud 10](https://doc.owncloud.com/server/latest/admin_manual/installation/) installation manual.


Here's a step-by-step guide to installing ownCloud using Nginx, MySQL, and an SSL certificate on Ubuntu 22.04:

### 1. **Update the System**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

### 2. **Install Required Packages**
   Install necessary dependencies, including Nginx, MySQL, PHP, and other required packages.
   ```bash
   sudo apt install nginx mysql-server php-fpm php-mysql php-zip php-gd php-intl php-curl php-mbstring php-xml php-ldap php-apcu php-redis php-imagick unzip curl -y
   ```

### 3. **Secure MySQL**
   Secure your MySQL installation by running the following command:
   ```bash
   sudo mysql_secure_installation
   ```
   Follow the prompts to set a root password, remove anonymous users, disallow root login remotely, remove test databases, and reload the privilege tables.

### 4. **Create a MySQL Database and User**
   Log in to the MySQL shell and create a database and user for ownCloud:
   ```bash
   sudo mysql -u root -p
   ```
   Run the following commands to create a database and user:
   ```sql
   CREATE DATABASE owncloud;
   CREATE USER 'ownclouduser'@'localhost' IDENTIFIED BY 'strongpassword';
   GRANT ALL PRIVILEGES ON owncloud.* TO 'ownclouduser'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

### 5. **Download ownCloud**
   Navigate to the `/var/www/` directory and download the latest version of ownCloud:
   ```bash
   cd /var/www/
   sudo wget https://download.owncloud.org/community/owncloud-complete-latest.zip
   sudo unzip owncloud-complete-latest.zip
   sudo chown -R www-data:www-data owncloud/
   sudo chmod -R 755 owncloud/
   ```

### 6. **Configure PHP**
   Edit the PHP configuration file for `php-fpm`:
   ```bash
   sudo nano /etc/php/8.1/fpm/php.ini
   ```
   Ensure the following settings are configured:
   ```ini
   memory_limit = 512M
   upload_max_filesize = 200M
   post_max_size = 200M
   max_execution_time = 360
   ```
   Then, restart PHP-FPM:
   ```bash
   sudo systemctl restart php8.1-fpm
   ```

### 7. **Configure Nginx**
   Create an Nginx configuration file for ownCloud:
   ```bash
   sudo nano /etc/nginx/sites-available/owncloud
   ```
   Add the following configuration:
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com;
       return 301 https://$server_name$request_uri;
   }

   server {
       listen 443 ssl;
       server_name yourdomain.com;

       ssl_certificate /etc/ssl/certs/yourdomain.crt;
       ssl_certificate_key /etc/ssl/private/yourdomain.key;

       root /var/www/owncloud;
       index index.php index.html /index.php$request_uri;

       client_max_body_size 200M;

       location / {
           try_files $uri $uri/ /index.php$request_uri;
       }

       location ~ ^/\.well-known/acme-challenge/ {
           allow all;
       }

       location ~ \.php$ {
           include snippets/fastcgi-php.conf;
           fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
           fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
           include fastcgi_params;
       }

       location ~ /\.ht {
           deny all;
       }
   }
   ```
   Enable the site and restart Nginx:
   ```bash
   sudo ln -s /etc/nginx/sites-available/owncloud /etc/nginx/sites-enabled/
   sudo systemctl restart nginx
   ```

### 8. **Set Up SSL Certificate**
   You can use Let's Encrypt to generate a free SSL certificate:
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d yourdomain.com
   ```
   Follow the prompts to set up your SSL certificate.

### 9. **Complete ownCloud Installation**
   Open your browser and navigate to `https://yourdomain.com`. You should see the ownCloud setup page.

   - Enter your preferred admin username and password.
   - Enter the database details (e.g., database name, user, password).
   - Complete the installation.

### 10. **Secure ownCloud**
   For added security, configure your firewall:
   ```bash
   sudo ufw allow OpenSSH
   sudo ufw allow 'Nginx Full'
   sudo ufw enable
   ```

This will complete the installation of ownCloud on your Ubuntu 22.04 server with Nginx, MySQL, and an SSL certificate.



## Contribution Guidelines
https://owncloud.com/contribute/

## Commit Messages
To ease bringing commits into context, a CI job check that the commit message satisfies a specification for adding human and machine readable meaning to commit messages. For details see: [Conventional Commits](www.conventionalcommits.org/). Note that if conventional commits are not satisfied, CI will not be green. In this case, you need to rewrite the git commit history to meet the requirement.

You must at least provide a `type` + `description` as described in the [Examples](https://www.conventionalcommits.org/en/v1.0.0/#examples) section.

For a quickstart, the following types can be used:

`fix:`, `feat:`, `build:`, `chore:`, `ci:`, `docs:`, `style:`, `refactor:`, `perf:`, `test:`


## Support
Learn about the different ways you can get support for ownCloud: https://owncloud.com/support/

## Get in Touch
* :clipboard: [Forum](https://central.owncloud.org)
* :hash: [IRC channel](https://web.libera.chat/?channels=#owncloud)
* :busts_in_silhouette: [Facebook](https://facebook.com/ownclouders)
* :hatching_chick: [Twitter](https://twitter.com/ownCloud)

## Important Notice on Translations
Please submit translations via Transifex:
https://explore.transifex.com/owncloud-org/

See the detailed information about [translations](https://doc.owncloud.com/server/latest/developer_manual/core/translation.html) here.
