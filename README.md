# Fundamental-PHP
- Go to this link: https://github.com/lucadegasperi/oauth2-server-laravel
- Go to app.php
- Way 1: composer require lucadegasperi/oauth2-server-laravel
- Way 2: Add by your hand

# How can I downgrade from PHP 7 to PHP 5.6 on Ubuntu 16.04? [duplicate]
**Observer**: composer.json
```
  sudo a2dismod proxy_fcgi proxy; sudo service apache2 restart
```

```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install php7.0 php5.6 php5.6-mysql php-gettext php5.6-mbstring php-mbstring php7.0-mbstring php-xdebug libapache2-mod-php5.6 libapache2-mod-php7.0

sudo a2dismod php7.0 ; sudo a2enmod php5.6 ; sudo service apache2 restart
service apache restart
sudo update-alternatives --set php /usr/bin/php5.6
php --version
```
# bootstrap/app.php
```json
"name": "laravel/laravel",
    "description": "The Laravel Framework.",
    "keywords": ["framework", "laravel"],
    "license": "MIT",
    "type": "project",
    "require": {
        "php": ">=7.0.0",
        "fideloper/proxy": "~3.3",
        "laravel/framework": "5.5.*",
        "laravel/tinker": "~1.0",
        "lucadegasperi/oauth2-server-laravel": "^5.2",
        "uxweb/sweet-alert": "^1.4",
        "yajra/laravel-datatables": "1.0"
    },
```
## Adjust information: 5.6.37 (require)

## https://github.com/sirinibin/laravel-5.5-lumen-5.5-with-OAuth2
