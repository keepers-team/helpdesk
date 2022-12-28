---
title: "NAS4Free"
weight: 4
---

Все процедуры выполнялись на дистрибутиве [NAS4Free 11.0.0.4](https://cloud.mail.ru/public/DLVg/RLQ1PHD97)

Установка проводилась в режиме `«Install 'Full' OS/MBR on HDD/SSD + DATA/SWAP (For Expert)».` Размеры корневого раздела и файла подкачки выбраны по умолчанию. В настройках веб-интерфейса необходимо включить доступ по SSH.

Подключаемся по SSH и устанавливаем необходимые пакеты, предварительно выполнив `pkg update`:

```bash
pkg install php70-{curl,extensions,gettext,mbstring} mod_php70 apache24 unzip wget
```
Приводим к требуемому виду содержимое файла `/usr/local/etc/apache24/extra/httpd-vhosts.conf`:

```bash
cat > /usr/local/etc/apache24/extra/httpd-vhosts.conf <<EOF
<VirtualHost *:803>
    ServerName webtlo.local
    DocumentRoot /usr/local/www/apache24/webtlo.local
    <Directory /usr/local/www/apache24/webtlo.local>
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

Добавляем в конец файла `/usr/local/etc/apache24/httpd.conf`:

```bash
cat >> /usr/local/etc/apache24/httpd.conf <<EOF
<IfModule dir_module>
    DirectoryIndex index.php
</IfModule>
Include etc/apache24/extra/httpd-vhosts.conf
<FilesMatch "\.php\$">
    SetHandler application/x-httpd-php
</FilesMatch>
<FilesMatch "\.phps\$">
    SetHandler application/x-httpd-php-source
</FilesMatch>
Listen 803
EOF
```

Добавляем apache24 в автозапуск:

`echo 'apache24_enable="YES"' >> /etc/rc.conf`

Перезагружаемся:

`reboot`

Снова подключаемся по SSH, скачиваем архив с web-TLO и распаковываем его в каталог `/usr/local/www/apache24/`:

> **ПРИМЕЧАНИЕ.** Также используйте эти команды, чтобы обновить web-TLO до актуальной версии

```bash
wget https://github.com/keepers-team/webtlo/releases/latest/download/webtlo.zip
unzip -o webtlo.zip -d /usr/local/www/apache24/
rm -f webtlo.zip
```

Изменяем права на каталог с дистрибутивом web-TLO:

`chown -R root:root /usr/local/www/apache24/webtlo.local`

Создаём каталог для пользовательских данных и задаём ему необходимые права доступа:

```bash
mkdir -p /usr/local/www/apache24/webtlo.local/data
chown -R www:www /usr/local/www/apache24/webtlo.local/data
```

Выполняем перезапуск сервиса apache2:

`service apache24 restart`

Открываем в браузере страницу с web-TLO http://192.168.1.2:803, где 192.168.1.2 — IP-адрес машины с установленным NAS4Free, 803 — порт.

Для доступа к стандартному веб-интерфейсу NAS4Free используйте адрес http://192.168.1.2.

![1000020100000431000001D256E7354F4AA3CF81](https://user-images.githubusercontent.com/32463123/78280884-79bd2c00-7522-11ea-95ea-f1c4ffde5f8e.png)
