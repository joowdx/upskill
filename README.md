# Welcome!

## Enviroment setup

### Using Windows

You can install [XAMPP](https://www.apachefriends.org/) to get [Apache](https://httpd.apache.org/), [PHP](https://www.php.net/), and [MariaDB](https://getcomposer.org/) setup pretty fast. This also comes with [PhpMyAdmin](https://www.phpmyadmin.net/) preconfigured out of the box.

You are free to install them independently. However, it may require that you have advanced knowledge as Apache (if you need webserver) and PHP have no windows executable installer, only zip installs [here](https://www.apachelounge.com/download/) and [here](https://windows.php.net/download/).

Alternative databases: [MySQL](https://www.mysql.com/) and [PostgreSQL](https://www.postgresql.org/).

Finally install [Git](https://git-scm.com/), [Node.js](https://nodejs.org/en/), and [Composer](https://getcomposer.org/).


### WSL

WSL allows you to run Linux command-line tools and apps alongside your Windows command-line. This enables you to use Windows apps and Linux command-line tools on the same set of files if you wish. You can read more [here](https://learn.microsoft.com/en-us/windows/wsl/faq).

You can skip all the windows installation above, and take full advantage of Linux terminal within windows.

Requires Windows 10 running version 2004 and higher (Build 19041 and higher) or Windows 11. To check your current build, run:
```
winver
```

WSL Installation
```
wsl --install
```
If the command above doesn't work, specify a distro of your choice. For this example we'll use Ubuntu...
```
wsl --install -d Ubuntu
```
For more distro options run:
```
wsl --list --online
```
If you are running on older Windows builds, please refer [here](https://learn.microsoft.com/en-us/windows/wsl/install-manual).

### Docker

[Docker](https://www.docker.com/) streamlines the development lifecycle by allowing developers to work in standardized environments using local containers which provide your applications and services. You can read more [here](https://docs.docker.com/get-started/overview/).

### Using Ubuntu 22.04
Make sure to install these packages as these are necessary to run laravel projects. Though some of these are optional, we'll still need them later on somehow.
```sh
sudo apt-get update \
    && sudo apt-get install -y cron ca-certificates curl zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python2 libc-ares-dev build-essential libcurl4-openssl-dev \
        php-cli php-dev php-pgsql php-sqlite3 php-gd php-curl php-imap php-mysql php-mbstring php-xml php-zip php-bcmath php-soap \
        php-intl php-readline php-ldap php-msgpack php-igbinary php-redis php-memcached php-pcov php-xdebug \
        composer nodejs npm \
    && npm install -g n \
    && n i 18
```
#### Database server setup
##### Pick one of your choice.
You are free to skip this step and proceed to use SQLite.
MySQL
```sh
sudo apt install mysql-server
```
PostgreSQL
```
sudo apt install postgresql
```

Optional: Install and enable swoole php extension. This extension is used by laravel octane to serve the application.
```sh
sudo pecl install -D 'enable-sockets="yes" enable-openssl="yes" enable-http2="yes" enable-mysqlnd="yes" enable-swoole-json="yes" enable-swoole-curl="yes" enable-cares="yes"' swoole \
    && echo "extension=swoole.so" >> /etc/php/8.1/cli/conf.d/20-swoole.ini
```

---
## Making new laravel projects
There are quite a few ways to get started with laravel projects. More in their [documentation](https://laravel.com/docs/9.x/installation).
### Using installer
One way is to install laravel installer.
```
composer global require laravel/installer
```
Add composer bin to path
```sh
echo "export PATH='~/.config/composer/vendor/bin:$PATH'" >> ~/.bashrc && source ~/.bashrc
```
Create new project
```
laravel new project
```
### With docker
If [Docker Desktop](https://www.docker.com/products/docker-desktop/) is already installed, run:
```
curl -s "https://laravel.build/new-project" | bash
```
Change new-project to whatever you want. Projects made with these command are already preconfigured to work with docker. Read more [here](https://laravel.com/docs/9.x/installation#getting-started-on-windows) and [here](https://laravel.com/docs/9.x/sail#introduction).

---
## Running existing laravel projects
For this example, we'll simply clone this newly created project.
```
git clone https://github.com/joowdx/upskill
```
Before anything else, make sure you are on the directory of the project you are working on. Now, we'll change directory `cd` to the newly cloned project.
```
cd upskill
```
### Install dependencies
Install all php dependencies and node modules to the project.
```
composer i && npm i
```
### Setup environment file
Copy pre-existing .env.example file as .env file to the current directory.
This file contains various configuration settings and sensitive info that may vary from device to device.
```sh
cp .env.example .env
```
Setup application key.
```
php artisan key:generate
```
Open and edit the env file then update the database credentials. Make sure these are correct.
```env
DB_CONNECTION={database: mysql / pgsql }
DB_HOST=127.0.0.1
DB_PORT={database-port: 3306(mysql) / 5432(pgsql)}
DB_DATABASE={database name}
DB_USERNAME={username}
DB_PASSWORD={password}
```
For example, if we want to connect to `PostgreSQL` with username `tester` and password `pass` to database `test`...
```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=test
DB_USERNAME=tester
DB_PASSWORD=pass
```

If you have opted not to use MySQL or PostgreSQL, you can use SQLite. Make sure to create sqlite file first in the database directory.
```sh
touch database/database.sqlite
```
Then you can proceed with these config with enabled foreign key constraints.
```env
DB_CONNECTION=sqlite
DB_FOREIGN_KEYS=true
```
### Migration
After you have completely setup the database in the env, you can now run the migration command. This setups database tables and columns set by the application.
```
php artisan migrate
```
If the database already have existing data, you can always force to run the migration command. However, any existing data will be lost. Proceed with caution, especially when in production.
```
php artisan migrate:fresh
```
### Serving the application
If you don't feel setting up webservers like [Apache](https://httpd.apache.org/) or [Nginx](https://www.nginx.com/) you can serve the app with command:
```
php artisan serve
```
Or if you have successfully installed and configured [Laravel Octane](https://laravel.com/docs/9.x/octane), serve the app using it by running:
```
php artisan octane:start
```
If you are currently developing, you may want to use the watch flag. This allows octane to listen for file changes and restart itself accordingly.
```
php artisan octane:start --watch
```

### Running with Docker
If you want, you can configure your laravel project to use docker technology with the help of [Laravel Sail](https://laravel.com/docs/9.x/sail).

Skip installation below if app is [installed with docker](https://github.com/joowdx/upskill/edit/master/README.md#with-docker).
Install Laravel Sail and choose services you want to install...
```sh
composer require laravel\sail --dev && php artisan sail:install
```

Add sail alias to your .bashrc to shorten `.vendor/bin/sail` command to just `sail`...
```sh
echo "alias sail='[ -f sail ] && sh sail || sh vendor/bin/sail'" >> ~/.bashrc && source ~/.bashrc
```
Now, build and run your laravel app...
```
sail up -d
```
If you want to stop this services, execute:
```
sail down
```
