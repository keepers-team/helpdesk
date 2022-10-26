---
title: "rTorrent"
weight: 5
---

## Что вам нужно?

* Apache 2.4+
* XML-RPC 1.00+
* rTorrent 0.9.7+ собранный с флагом `--with-xmlrpc-c`
* веб-интерфейс [ruTorrent](https://github.com/Novik/ruTorrent) (необязательно)

## Порядок настройки

На примере дистрибутива **Ubuntu 20.04 LTS (Focal Fossa)**

Получаем права root:
```bash
sudo -i
```

Устанавливаем пакеты:
```bash
apt install rtorrent apache2 curl libapache2-mod-php
```
> libapache2-mod-php нужен для работы ruTorrent

Создаём файл виртуального хоста:
```bash
cat > /etc/apache2/sites-available/rtorrent.loc.conf << EOF
<VirtualHost *:80>
        ServerName rtorrent.loc
        DocumentRoot /var/www/rtorrent
        DirectoryIndex index.html index.php
        CustomLog /var/log/apache2/rtorrent-access.log common
        ErrorLog /var/log/apache2/rtorrent-error.log
        ProxyPass /RPC2 scgi://127.0.0.1:5000
        TimeOut 600
        <Directory /var/log/apache2/torrents-access.log>
            AllowOverride all
            Require all granted
            Options +Indexes +FollowSymlinks
        </Directory>
</VirtualHost>
EOF
```

Устанавливаем веб-интерфейс ruTorrent:
```bash
wget https://github.com/Novik/ruTorrent/archive/v3.10-beta.zip
unzip -d /var/www/ v3.10-beta.zip
mv /var/www/ruTorrent-3.10-beta /var/www/rtorrent
chown www-data: -R /var/www/rtorrent/share/{torrents,settings}
rm -f v3.10-beta.zip
```

Отключаем все плагины ruTorrent (не разбирался почему они корректно не работают)
```bash
sed -i '/^\w/s|[^= ]*$|no|g' /var/www/rtorrent/conf/plugins.ini
```

Включаем виртуальный хост и нужные модули:
```bash
a2dissite 000-default
a2ensite rtorrent.loc
a2enmod proxy_scgi
```

Перезапускаем apache2:
```bash
systemctl restart apache2
```

Создадим файл службы rTorrent:
```bash
cat > /etc/systemd/system/rtorrent.service << EOF
[Unit]
Description=rTorrent Daemon Service
Documentation=man:rtorrent(1)
Wants=network-online.target
After=network-online.target nss-lookup.target

[Service]
# if you have systemd >= 240, you probably want to use Type=exec instead
Type=simple
User=rtorrent
ExecStart=/usr/bin/rtorrent
TimeoutStopSec=infinity

[Install]
WantedBy=multi-user.target
EOF
```

Заставим systemd прочитать нашу новую службу:
```bash
systemctl daemon-reload
```

Добавим отдельного пользователя для работы rTorrent:
```bash
adduser rtorrent
```

Войдём в его окружение:
```bash
su - rtorrent
```

Заберём шаблон для конфигурационного файла rTorrent:
```bash
curl -Ls "https://raw.githubusercontent.com/wiki/rakshasa/rtorrent/CONFIG-Template.md" \
    | sed -ne "/^######/,/^### END/p" \
    | sed -re "s:/home/USERNAME:$HOME:" >~/.rtorrent.rc
mkdir -p ~/rtorrent
```

Задействуем в настройках rTorrent режим демона:
```bash
cat >> ~/.rtorrent.rc << EOF
system.daemon.set = true
network.scgi.open_port = 127.0.0.1:5000
EOF
```

Вернёмся в окружение root:
```bash
exit
```

Отключим пользователю rtorrent доступ к /bin/bash:
```bash
usermod -s /usr/sbin/nologin rtorrent
```

Добавим службу rtorrent в автозапуск и одновременно запустим:
```bash
systemctl enable --now rtorrent.service
```

На этом первоначальная настройка окончена. Дополнительную информацию по тонкой настройке rTorrent можно найти на сайте разработчика https://github.com/rakshasa/rtorrent/wiki#users-manual
