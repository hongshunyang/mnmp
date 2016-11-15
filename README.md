# mnmp
nginx,php-fpm,mysql for mac

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

## PHP(FPM)

```
brew tap homebrew/dupes
brew tap josegonzalez/homebrew-php
```

Install PHP

```
brew install --without-apache --with-fpm --with-mysql php56
```

Setup auto start

```
mkdir -p ~/Library/LaunchAgents
cp /usr/local/opt/php56/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/
```

Start

```
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist
```

Check

```
lsof -Pni4 | grep LISTEN | grep php
```

## Mysql

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
```

Secure Installation

```
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
sudo cp /usr/local/opt/nginx/*.plist /Library/LaunchDaemons/
sudo chown root:wheel /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

Test

```
sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

Stop

```
sudo launchctl unload /Library/LaunchDaemons/homebrew.mxcl.nginx.plist
```

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

nginx php-fpm

```
curl -L https://raw.githubusercontent.com/hongshunyang/mnmp/master/php-fpm -o /usr/local/etc/nginx/conf.d/php-fpm

```













