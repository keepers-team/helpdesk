---
title: "ALT Linux"
weight: 2
---

На примере дистрибутива Альт Линукс [Кентавр 7.0.5](https://www.altlinux.org/Альт_Линукс_7.0_Кентавр)

Получаем права root:

`su -`

Устанавливаем необходимые пакеты:
```
apt-get install apache2 apache2-mod_php5 php5 php5-mbstring \
php5-curl php5-pdo_sqlite php5-dom php5-xmlrpc unzip
```
Скачиваем [архив](https://github.com/berkut-174/webtlo/releases/latest/download/webtlo.zip) с web-TLO и распаковываем его в каталог `/var/www/vhosts/`:

`unzip webtlo.zip -d /var/www/vhosts/`

Создаём файл виртуального хоста:

```bash
cat > /etc/httpd2/conf/sites-available/webtlo.local.conf <<EOF
<VirtualHost *:80>
	ServerName webtlo.local
	DocumentRoot /var/www/vhosts/webtlo.local
	<Directory /var/www/vhosts/webtlo.local>
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

`a2enmod mod_php5`

Добавляем в файл `/etc/hosts` новой строкой:
```
echo '127.0.0.1 webtlo.local' >> /etc/hosts
```
Добавляем в `/etc/php/5.5/apache2-mod_php/php.ini` в раздел `[PHP]`:
```
suhosin.post.max_vars = 100000
suhosin.request.max_vars = 100000
```
В `/etc/php/5.5/apache2-mod_php/php.ini` и `/etc/php/5.5/cli/php.ini`
добавляем в раздел `[Date]`:

`date.timezone = "Asia/Yekaterinburg"`

> **ПРИМЕЧАНИЕ!** Заменить "Asia/Yekaterinburg" на свой [часовой пояс](https://www.php.net/manual/ru/timezones.php).

Изменяем права на каталог с дистрибутивом web-TLO:

`chown root: -R /var/www/vhosts/webtlo.local`

Создаём каталог для пользовательских данных и задаём ему необходимые
права доступа:

`mkdir -p /var/www/vhosts/webtlo.local/data`

`chown apache2: -R /var/www/vhosts/webtlo.local/data`

Выполняем перезапуск сервиса apache2:

`service httpd2 restart`

и добавляем его в автозапуск:

`chkconfig httpd2 on`

Открываем в браузере страницу http://webtlo.local

![1](https://user-images.githubusercontent.com/1784545/81086181-d6847b80-8f00-11ea-8318-2849458b4078.png)
