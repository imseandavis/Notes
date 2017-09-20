Update the system
```
[user]$ sudo apt-get update && sudo apt-get -y upgrade
```

Install MariaDB 10.0
```
[user]$ sudo apt-get install -y mariadb-server
```

Create Database In MariaDB (Default password is blank) <br>
- Replace 'cachet' on the create database line with your desired db name. <br>
- Replace 'cachetuser' with your desired db admin username. <br>
- Replace 'password' with your desired password. <br>
```
[user]$ sudo mysql -u root -p
MariaDB [(none)]> CREATE DATABASE cachet;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON cachet.* TO 'cachetuser'@'localhost' IDENTIFIED BY 'password';
MariaDB [(none)]> FLUSH PRIVILEGES;
MariaDB [(none)]> \q
```

Install Apache2 web server
```
[user]$ sudo apt-get install apache2
```

Install PHP and required PHP modules
```
[user]$ sudo apt-get install php7.0 libapache2-mod-php7.0 php7.0-mbstring php7.0-curl php7.0-zip php7.0-gd php7.0-mysql php7.0-mcrypt php7.0-xml curl
```

Enable the Apache2 rewrite module if it is not already done:
```
[user]$ sudo a2enmod rewrite
```

In order to activate the new configuration, restart Apache:
```
[user]$ sudo service apache2 restart
```

Download the source code with Git
- Replace 2.3.12 with latest version number found here: https://github.com/CachetHQ/Cachet/releases
```
[user]$ sudo cd /var/www/html/
[user]$ sudo git clone https://github.com/cachethq/Cachet.git
[user]$ sudo cd Cachet
[user]$ sudo git checkout v2.3.12
```

Set File Ownership
```
[user]$ sudo chown www-data:www-data -R /var/www/html/Cachet/
```

Configuring a database
Rename the .env.example file to .env using the following command:
```
[user]$ sudo mv .env.example .env
```

Open the .env file and change the following lines:
```
[user]$ sudo nano .env
```
- APP_URL=http://localhost -> APP_URL=http://your-domain-name
- DB_DATABASE=cachet -> DB_DATABASE=your-database-name
- DB_USERNAME=homestead -> DB_USERNAME=your-database-username
- DB_PASSWORD=secret -> DB_PASSWORD=your-database-password

Download and Install Composer
Composer is a dependency manager for PHP with which you can install packages. Composer will pull in all the required libraries and dependencies you need for your project.
```
[user]$ sudo curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer 
[user]$ sudo composer install --no-dev -o
```

Set the application key (This is used for all encryption used in Cachet)
```
sudo php artisan key:generate
```

Run Cachet installer
```
sudo php artisan app:install
```

Create a new virtual host directive in Apache.
```
[user]$ sudo touch /etc/apache2/sites-available/cachet.conf
[user]$ sudo ln -s /etc/apache2/sites-available/cachet.conf /etc/apache2/sites-enabled/cachet.conf
```

Open The new config file:
```
[user]$ sudo nano /etc/apache2/sites-available/cachet.conf
```

Add the following lines to the config file:
- Replace 'your-domain.com' with your actual DNS name
- Replace the serverAlias with the same thing you used for ServerName
```
<VirtualHost *:80>
ServerName your-domain.com
# Or whatever you want to use
ServerAlias www.your-domain.com
# Make this the same as ServerName
DocumentRoot "/var/www/html/Cachet/public"
<Directory "/var/www/html/Cachet/public">
    Require all granted 
    # Used by Apache 2.4
    Options Indexes FollowSymLinks
    AllowOverride All
    Order allow,deny
    Allow from all
</Directory>
</VirtualHost>
```

Restart Apache:
```
[user]$ sudo service apache2 restart
```

Navigate to http://your-domain.com/ <br>
Finish Configuration...<br>
Profit $$$
