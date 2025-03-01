# Setup Laravel Project on Digital Ocean & AWS

## Prerequisites
Ensure you have the following before starting:
- A Digital Ocean Droplet or an AWS EC2 instance running Ubuntu
- SSH access to the server
- A GitHub and Bitbucket account (for repository hosting)
- A Laravel project ready to be deployed

## Step 1: Install Git and Setup SSH Key
```sh
sudo apt update && sudo apt install git -y
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```

### Add SSH Key to GitHub & Bitbucket
#### Bitbucket
1. Log in to Bitbucket.
2. Click on your avatar → **Personal Settings**.
3. Navigate to **SSH Keys** → Click **Add Key**.
4. Paste the SSH key from the previous step and save it.

#### GitHub
1. Log in to GitHub.
2. Go to **Settings** → **SSH and GPG Keys**.
3. Click **New SSH Key**, paste the key, and save it.

## Step 2: Install and Configure Nginx
### Install Nginx
```sh
sudo apt install nginx -y
```

### Start and Enable Nginx
```sh
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Check Nginx Status
```sh
sudo systemctl status nginx
```

### Allow Firewall Access
```sh
sudo ufw allow 'Nginx Full'
```

### Check Nginx Version
```sh
nginx -v
```

## Step 3: Configure Nginx for Laravel
### Create a New Configuration File
```sh
sudo nano /etc/nginx/sites-available/laravel
```

### Add the Following Configuration:
```nginx
server {
    listen 80;
    server_name your_domain_or_ip;

    root /var/www/html/new-cms-laravel/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock; # Change to 'php8.1-fpm.sock' if using PHP 8.1
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

### Enable the Configuration
```sh
sudo ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/
```

### Remove Default Configuration (If Needed)
```sh
sudo rm /etc/nginx/sites-enabled/default
```

### Reload Nginx
```sh
sudo systemctl reload nginx
sudo systemctl restart nginx
```

### If PHP is Not Fetching, Restart PHP Service
```sh
sudo systemctl restart php8.3-fpm
sudo systemctl status php8.3-fpm
```

## Step 4: Install phpMyAdmin
```sh
sudo apt install phpmyadmin
```

### Create a New MySQL User
```sql
CREATE USER 'cms'@'localhost' IDENTIFIED WITH mysql_native_password BY 'pf151214pf';
GRANT ALL PRIVILEGES ON *.* TO 'cms'@'localhost' WITH GRANT OPTION;
```

## Deploy Laravel on AWS
### Install AWS CLI
```sh
sudo apt install awscli -y
```

### Connect to AWS EC2 Instance
```sh
ssh -i /path/to/key.pem ubuntu@your-aws-instance-ip
```

### Clone Your Repository
```sh
git clone git@github.com:yourusername/your-repo.git /var/www/html/your-laravel-project
```

### Set Permissions
```sh
sudo chown -R www-data:www-data /var/www/html/your-laravel-project
sudo chmod -R 775 /var/www/html/your-laravel-project/storage /var/www/html/your-laravel-project/bootstrap/cache
```

### Run Laravel Setup
```sh
cd /var/www/html/your-laravel-project
composer install
cp .env.example .env
php artisan key:generate
```

### Configure Nginx on AWS (Same as Digital Ocean)
Follow the same Nginx setup mentioned above.

### Restart Services
```sh
sudo systemctl restart nginx
sudo systemctl restart php8.3-fpm
```

## Conclusion
Your Laravel project should now be set up on Digital Ocean and AWS using GitHub & Bitbucket. If you run into any issues, check the logs using:
```sh
sudo journalctl -u nginx --no-pager | tail -n 50
sudo tail -f /var/log/nginx/error.log
```

## Author
Developed by Hasan Raza

📧 Contact: hasanraz562@gmail.com

## Tags
Digital Ocean, AWS, Laravel, Ubuntu, Nginx, Deployment, Server Setup, GitHub, Bitbucket, SSH, phpMyAdmin

