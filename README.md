
<h1>Server Deploy Ubuntu-phpmyadmin-django</h1>

<h2>UBUNTU 22.04</h2>

$ sudo apt install wget nano vim apache2 wget python3-pip libapache2-mod-wsgi-py3 python3-venv lynx

$ sudo systemctl enable apache2

$ sudo systemctl restart apache2

$ sudo apt install mariadb-server mariadb-client

$ sudo systemctl enable --now mariadb

<h2>SECURE MARIADB INSTALLATION</h2>

$ sudo mysql_secure_installation

Enter current password for root (enter for none): Press ENTER.

Switch to unix_socket authentication? Press N, then ENTER.

Change the root password? Press Y, then ENTER.

Remove anonymous users? Press Y, then ENTER.

Disallow root login remotely? Press Y, then ENTER.

Remove test database and access to it? Press Y, then ENTER.

Reload privilege tables now? Press Y, then ENTER.

<h2>INSTALL PHP AND DOWNLOAD phpmyadmin</h2>

$ sudo apt install php php-{fpm,mbstring,bcmath,xml,mysql,common,gd,cli,curl,zip}

$ sudo systemctl enable php8.1-fpm --now

$ wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.tar.gz

$ tar xvf phpMyAdmin-*-all-languages.tar.gz

$ sudo mv phpMyAdmin-*/ /var/www/html/phpmyadmin

$ sudo mkdir -p /var/www/html/phpmyadmin/tmp

$ sudo cp /var/www/html/phpmyadmin/config.sample.inc.php /var/www/html/phpmyadmin/config.inc.php

<h2>KEY GENERETION FOR config.inc.php</h2>

$ sudo nano /var/www/html/phpmyadmin/config.inc.php

$cfg[‘blowfish_secret’] = ‘your-key‘; /* YOU MUST FILL IN THIS FOR COOKIE AUTH! */


Also, scroll down to the end and add this line.

$cfg['TempDir'] = '/var/www/html/phpmyadmin/tmp';

<b>After that save your file by pressing Ctrl+O, hit the Enter key, and then exit the file editor- Ctrl+X.</b>

$ sudo chown -R www-data:www-data /var/www/html/phpmyadmin

$ sudo find /var/www/html/phpmyadmin/ -type d -exec chmod 755 {} \;

$ sudo find /var/www/html/phpmyadmin/ -type f -exec chmod 644 {} \;

<b>Now, create Apache Vhost configuration file, phpmyadmin.conf</b>

$ sudo a2enconf phpmyadmin.conf

$ sudo systemctl restart apache2

Open your browser

http://localhost/phpmyadmin

Note: If you get this notification at the footer – The phpMyAdmin configuration storage is not completely configured, some extended features have been deactivated. Find out why. Or alternately go to the ‘Operations’ tab of any database to set it up there.

Then simply click on the Find out why link and click the “Create” link to automatically create phpmyadmin database.

<h2>DJANGO</h2>

$ adduser gerardo

$ usermod -aG sudo Gerardo

$ mkdir ~/project

$ cd ~/project

$ python3 -m venv venv

$ source venv/bin/activate

$ pip install django

$ django-admin startproject project .

$ vim project/settings.py

<h3>ADD</h3>

import os

ALLOWED_HOSTS = ['server_domain_or_IP','localhost', '0.0.0.0'']

STATIC_URL = 'static/'

STATIC_ROOT = os.path.join(BASE_DIR, 'static/')

$ ./manage.py makemigrations

$ ./manage.py collectstatic

$ ./manage.py runserver 0.0.0.0:8000

<h3>In the terminal</h3>

lynx http://localhost:8000

Ctrl + c

q

$ deactivate

$ sudo chown :www-data -R project/

$ chmod 664 ~/project/db.sqlite3

$ sudo adduser www-data $(whoami)


$ chmod 775 -R project

Create project.conf

$ sudo vim /etc/apache2/conf-available/project.conf

<h3>Add the virtual host</h3>

$ sudo a2enconf project.conf

$ sudo apache2ctl configtest

$ systemctl reload apache2

