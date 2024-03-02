# Introduction

This is a sample e-commerce application built for learning purposes.

## Deploy Pre-Requisites

```bash
# Install UFW (Uncomplicated Firewall)
sudo apt-get update
sudo apt-get install -y ufw
sudo ufw allow 22/tcp
sudo ufw enable
sudo ufw status

# Install MariaDB
sudo apt-get install -y mariadb-server
sudo systemctl start mariadb
sudo systemctl enable mariadb

# Configure firewall for Database
sudo ufw allow 3306/tcp
sudo ufw reload

# Configure Database
mysql -u root -p <<EOF
CREATE DATABASE ecomdb;
CREATE USER 'ecomuser'@'localhost' IDENTIFIED BY 'ecompassword';
GRANT ALL PRIVILEGES ON ecomdb.* TO 'ecomuser'@'localhost';
FLUSH PRIVILEGES;
EOF

# Load Product Inventory Information to the database
cat > db-load-script.sql <<-EOF
USE ecomdb;
CREATE TABLE products (id mediumint(8) unsigned NOT NULL auto_increment, Name varchar(255) default NULL, Price varchar(255) default NULL, ImageUrl varchar(255) default NULL, PRIMARY KEY (id)) AUTO_INCREMENT=1;

INSERT INTO products (Name, Price, ImageUrl) VALUES ("Laptop","100","c-1.png"),("Drone","200","c-2.png"),("VR","300","c-3.png"),("Tablet","50","c-5.png"),("Watch","90","c-6.png"),("Phone Covers","20","c-7.png"),("Phone","80","c-8.png"),("Laptop","150","c-4.png");
EOF

sudo mysql < db-load-script.sql

# Install required packages
sudo apt-get install -y apache2 php libapache2-mod-php php-mysql
sudo ufw allow 80/tcp
sudo ufw reload

# Configure Apache
sudo sed -i 's/index.html/index.php/g' /etc/apache2/apache2.conf

# Restart Apache
sudo systemctl restart apache2

# Download code
sudo apt-get install -y git
sudo git clone https://github.com/kodekloudhub/learning-app-ecommerce.git /var/www/html/

# Update index.php
sudo sed -i 's/172.20.1.101/localhost/g' /var/www/html/index.php

# Test
curl http://localhost
