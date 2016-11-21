# mnmp
nginx,php-fpm,mysql for mac osx 10.12.x

## version

- nginx version: nginx/1.10.2
- mysql  Ver 14.14 Distrib 5.7.16, for osx10.12 (x86_64)
- PHP 5.6.27

## list port number

```
lsof -i -n -P
netstat -an | grep LISTEN 
```

## Xcode

Install the Xcode Command Line Tools:

```
xcode-select --install
```

## Homebrew

Homebrew is the missing package manager for OSX

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Check for any problems

```
brew doctor
```

Update and Upgrade

```
brew update && brew upgrade
```

Cleanup(remove outdated version)

```
brew cleanup
```

Info/options/uninstall/background running/stop

```
brew info php56
brew options php56
brew uninstall php56
brew services start/stop/restart php56
```


## PHP(FPM)

~~brew tap homebrew/dupes~~

~~brew tap josegonzalez/homebrew-php~~

~~brew untap homebrew/dupes~~

~~brew untap josegonzalez/php~~


Install PHP(default without apache,with mysql)

```
brew tap homebrew/php
brew install  php56
```
or
```
brew install homebrew/php/php56 
```

check php module

```
php -m
```


Setup auto start

```
mkdir -p ~/Library/LaunchAgents
ln -sfv /usr/local/opt/php56/*.plist ~/Library/LaunchAgents/
```

Start

```
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
or
brew services start php56
```

Check(127.0.0.1:9000)

```
lsof -Pni4 | grep LISTEN | grep php
```

## MySQL

```
brew install mysql
```

Setup auto start

```
ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
```

Start

```
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
or 
brew services start mysql
```

Trouble(mysql_secure_installation) Solved

```
sudo chown -R `whoami` /usr/local/var/mysql
sudo mysqld_safe --skip-grant-table
sudo mysql --user=root mysql
mysql> update user set authentication_string=PASSWORD('new-password') where user='root';
mysql>flush privileges;
mysql>\q
mysql_secure_installation
```

Test Connection

```
mysql -uroot -p
```

## Nginx

```
brew install nginx
```

Setup auto start

```
ln -sfv /usr/local/opt/nginx/*.plist /Library/LaunchDaemons/
```

Start/Stop

```
brew services start nginx
brew services stop nginx
```

Check

- [http://localhost](http://localhost) → “Nginx works” page

_tip:_ check fix localhost : `cat /etc/hosts` include like this `127.0.0.1  localhost`


## Configurations

nginx.conf

```
mkdir -p /usr/local/etc/nginx/logs
mkdir -p /usr/local/etc/nginx/sites-available
mkdir -p /usr/local/etc/nginx/sites-enabled
mkdir -p /usr/local/etc/nginx/conf.d
mkdir -p /usr/local/etc/nginx/ssl
sudo mkdir -p /var/www

sudo chown :staff /var/www
sudo chmod 775 /var/www

rm /usr/local/etc/nginx/nginx.conf
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/nginx.conf -o /usr/local/etc/nginx/nginx.conf

```

nginx/php-fpm

```
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/php-fpm -o /usr/local/etc/nginx/conf.d/php-fpm

```

virtual hosts

```
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/sites-available_default -o /usr/local/etc/nginx/sites-available/default
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/sites-available_default-ssl -o /usr/local/etc/nginx/sites-available/default-ssl
```

example:

```
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/www/phpinfo.php -o /var/www/phpinfo.php
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/www/403.html -o /var/www/403.html
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/www/404.html -o /var/www/404.html
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/www/index.html -o /var/www/index.html
```

Set up SSL

```
mkdir -p /usr/local/etc/nginx/ssl
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -subj '/C=US/ST=State/L=Town/O=Office/CN=localhost' -keyout /usr/local/etc/nginx/ssl/localhost.key -out /usr/local/etc/nginx/ssl/localhost.crt
```

Enable Virtual Host

```
ln -sfv /usr/local/etc/nginx/sites-available/default /usr/local/etc/nginx/sites-enabled/default
ln -sfv /usr/local/etc/nginx/sites-available/default-ssl /usr/local/etc/nginx/sites-enabled/default-ssl
```

Final Tests

- [http://localhost](http://localhost) → “Nginx works” page
- [http://localhost/phpinfo](http://localhost/phpinfo) → phpinfo()
- [http://localhost/nope](http://localhost/nope) → ” Not Found” page
- [https://localhost](https://localhost) → “Nginx works” page (SSL)
- [https://localhost/phpinfo](https://localhost/phpinfo) → phpinfo() (SSL)
- [https://localhost/nope](https://localhost/nope) → “Not Found” page (SSL)

## Alias

```
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/aliases -o /tmp/aliases
```

bash

```
cat /tmp/aliases >> ~/.profile
source ~/.profile
```

zsh

```
cat /tmp/.aliases >> ~/.zshrc 
source ~/.zshrc
```

#### Nginx
```
nginx.start
nginx.stop
nginx.restart
```

#### PHP-FPM
```
php56.start
php56.stop
php56.restart
```

#### MySQL
```
mysql.start
mysql.stop
mysql.restart
```

#### Nginx Logs
```
nginx.logs.error
nginx.logs.access
nginx.logs.default.access
nginx.logs.default-ssl.access
```

# phpMyAdmin

```
brew install phpmyadmin

curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/phpmyadmin/sites-available_phpmyadmin -o /usr/local/etc/nginx/sites-available/phpmyadmin

openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -subj '/C=US/ST=State/L=Town/O=Office/CN=phpmyadmin' -keyout /usr/local/etc/nginx/ssl/phpmyadmin.key -out /usr/local/etc/nginx/ssl/phpmyadmin.crt

ln -sfv /usr/local/etc/nginx/sites-available/phpmyadmin /usr/local/etc/nginx/sites-enabled/phpmyadmin

```
#### Check

- [https://localhost:306](https://localhost:306) → phpMyAdmin (SSL)

###### reference

- [https://gist.github.com/johnantoni/07df65898456ace4307d5bb6cbdc7f51](https://gist.github.com/johnantoni/07df65898456ace4307d5bb6cbdc7f51)


