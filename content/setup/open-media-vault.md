---
title: "OpenMediaVault"
weight: 5
---

Проверка работоспособности выполнялась на [OpenMediaVault 2.1.](https://cloud.mail.ru/public/P5zD/nHoigPLk3) После
установки необходимо включить доступ по SSH. Подключаемся по SSH и
устанавливаем необходимые пакеты, предварительно выполнив `apt-get update`:
```
apt-get install apache2 libapache2-mod-php5 php5 php5-curl \
php5-sqlite unzip
```
Скачиваем [архив](https://github.com/berkut-174/webtlo/releases/latest/download/webtlo.zip) с web-TLO и распаковываем его в каталог `/var/www/`:

`unzip webtlo.zip -d /var/www/`

Приводим к требуемому виду содержимое файла `/etc/apache2/sites-available/webtlo.local`:
```bash
cat > /etc/apache2/sites-available/webtlo.local.conf <<EOF
<VirtualHost *:801>
	ServerName webtlo.local
	DocumentRoot /var/www/webtlo.local
	<Directory /var/www/webtlo.local>
		AllowOverride All
		Order allow,deny
    Deny from all
    Allow from 127.0.0.1
		Options -Indexes
	</Directory>
</VirtualHost>
EOF
```
> **ВНИМАНИЕ!** В конец строки _Allow from 127.0.0.1_ допишите через
> пробел список IP-адресов, которым разрешён доступ к web-TLO, например,
> _Allow from 127.0.0.1 192.168.1.33 192.168.1.36_.


Выключаем хост по умолчанию:

`a2dissite 000-default`

Включаем хост webtlo.local:

`a2ensite webtlo.local`

Включаем поддержку PHP5 в apache2:

`a2enmod php5`

В файле `/etc/apache2/ports.conf` изменяем строки:
```
NameVirtualHost *:80
Listen 80
```
на:
```
NameVirtualHost *:801
Listen 801
```
Изменяем права на каталог с дистрибутивом web-TLO:

`chown root: -R /var/www/webtlo.local`

Создаём каталог для пользовательских данных и задаём ему необходимые
права доступа:

`mkdir -p /var/www/webtlo.local/data`

`chown www-data: -R /var/www/webtlo.local/data`

Выполняем перезапуск сервиса apache2:

`service apache2 restart`

Открываем в браузере страницу с web-TLO http://192.168.1.2:801, где
192.168.1.2 — IP-адрес машины с установленным OpenMediaVault, 801 — порт.

Для доступа к стандартному веб-интерфейсу OpenMediaVault используйте
адрес http://192.168.1.2.

![1000020100000431000001D2E7E68E6D37244978](https://user-images.githubusercontent.com/32463123/78280988-a5d8ad00-7522-11ea-823e-09b292df25bf.png)


