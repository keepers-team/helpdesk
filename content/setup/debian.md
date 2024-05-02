---
title: "Debian-based (Ubuntu, Mint, ALT, OMV), Apache"
weight: 6
---

На примере дистрибутива **Debian 10.8 (Buster)**.

Получаем права root:

`sudo -i`

Устанавливаем необходимые пакеты:
```
apt install apache2 libapache2-mod-php php php-curl \
php-mbstring php-sqlite3 php-xml php-xmlrpc unzip wget
```

Скачиваем архив с web-TLO и распаковываем его в каталог `/var/www/`:

> **ПРИМЕЧАНИЕ.** Используйте эти команды, чтобы обновить web-TLO до актуальной версии

```bash
wget https://github.com/keepers-team/webtlo/releases/latest/download/webtlo.zip
unzip -o webtlo.zip -d /var/www/
rm -f webtlo.zip
```

Создаём файл виртуального хоста:
```bash
cat > /etc/apache2/sites-available/webtlo.local.conf <<EOF
<VirtualHost *:80>
	ServerName webtlo.local
	DocumentRoot /var/www/webtlo.local
	<Directory /var/www/webtlo.local>
		AllowOverride All
		Require local
		Options -Indexes
	</Directory>
</VirtualHost>
EOF
```
> **ВНИМАНИЕ!** Если необходимо, добавьте после _Require local_ новой
> строкой список IP-адресов, которым разрешён доступ к web-TLO, например,
> _Require ip 192.168.1.33 192.168.1.36_, либо предоставьте доступ всем
> _— Require all granted._

Выключаем хост по умолчанию:

`a2dissite 000-default`

Включаем хост webtlo.local:

`a2ensite webtlo.local`

Включаем поддержку PHP в apache2:

`a2enmod php7.3`

Добавляем в файл `/etc/hosts` новой строкой:

`echo '127.0.0.1 webtlo.local' >> /etc/hosts`

Изменяем права на каталог с дистрибутивом web-TLO:

`chown root: -R /var/www/webtlo.local`

Создаём каталог для пользовательских данных и задаём ему необходимые права доступа:

```bash
install -m 750 -o www-data -g www-data -d /var/www/webtlo.local/data
```

Выполняем перезапуск сервиса apache2:

`systemctl restart apache2.service`

Открываем в браузере страницу http://webtlo.local

![1000020100000431000001D2FD687FA064007E5A](https://user-images.githubusercontent.com/1784545/81086181-d6847b80-8f00-11ea-8318-2849458b4078.png)
