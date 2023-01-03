---
title: "XigmaNAS"
weight: 7
---

Все процедуры выполнялись на дистрибутиве [XigmaNAS 12.2.0.4.8311](https://sourceforge.net/projects/xigmanas/files/XigmaNAS-12.2.0.4/12.2.0.4.8311/XigmaNAS-x64-LiveCD-12.2.0.4.8311.iso/download)

Установка проводилась в режиме `«Install 'Full' OS/MBR on HDD/SSD + DATA/SWAP (For Expert)».`
Размеры корневого раздела — 10240, файла подкачки — по умолчанию.
В настройках веб-интерфейса необходимо включить доступ по SSH.

Подключаемся по SSH и устанавливаем необходимые пакеты:

```bash
pkg install php74-{curl,extensions,gettext,mbstring,intl,bcmath} mod_php74 apache24 unzip wget
```

Приводим к требуемому виду содержимое файла `/usr/local/etc/apache24/extra/httpd-vhosts.conf`:

```bash
cat > /usr/local/etc/apache24/extra/httpd-vhosts.conf <<EOF
<VirtualHost *:803>
    ServerName webtlo.local
    DocumentRoot /usr/local/www/apache24/webtlo.local
    <Directory /usr/local/www/apache24/webtlo.local>
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

Проверяем секцию `<IfModule mime_module>` на наличие строки ```AddType application/x-httpd-php .php```.
Без этого php файлы будут выводится обычным текстом. А в конфигурации по умолчанию этой строки нет.

Добавляем apache24 в автозапуск:

```bash
echo 'apache24_enable="YES"' >> /etc/rc.conf
```

Перезагружаемся:

```bash
reboot
```

Снова подключаемся по SSH, скачиваем архив с web-TLO и распаковываем его в каталог `/usr/local/www/apache24/`:

> **ПРИМЕЧАНИЕ.** Также используйте эти команды, чтобы обновить web-TLO до актуальной версии

```bash
wget https://github.com/keepers-team/webtlo/releases/latest/download/webtlo.zip
unzip -o webtlo.zip -d /usr/local/www/apache24/
rm -f webtlo.zip
```

Изменяем права на каталог с дистрибутивом web-TLO:

```bash
chown -R root: /usr/local/www/apache24/webtlo.local
```

Создаём каталог для пользовательских данных и задаём ему необходимые
права доступа:

```bash
mkdir -p /usr/local/www/apache24/webtlo.local/data
chown -R www:www /usr/local/www/apache24/webtlo.local/data
```

Выполняем перезапуск сервиса apache2:

```bash
service apache24 restart
```

Открываем в браузере страницу с web-TLO http://192.168.1.2:803, где 192.168.1.2 — IP-адрес машины с установленным XigmaNAS, 803 — порт.

Для доступа к стандартному веб-интерфейсу XigmaNAS используйте адрес http://192.168.1.2.

![1000020100000431000001D256E7354F4AA3CF81](https://user-images.githubusercontent.com/32463123/78280884-79bd2c00-7522-11ea-95ea-f1c4ffde5f8e.png)

> ### Переадресация по DNS
Опционально также настроить переадресацию по DNS (если он работает в вашей сети).
Например, по-умолчанию, web-интерфейс сетевого хранилища доступен на [http://xigmanas.local](http://xigmanas.local) 
Можно сделать, чтобы webTLO был доступен по адресу http://webtlo.local (или любой ваш вариант).
Для этого нужно отредактировать конфигурацию демона lighttpd, который отвечает за web-интерфейс NAS. Однако, следует заметить, что файл конфигурации /var/etc/lighttpd.conf автоматически перезаписывается при перезапуске демона. Редактировать его нет смысла. Поэтому внесем изменения в /etc/rc.d/lighttpd

```bash
nano /etc/rc.d/lighttpd
```

Листаем вниз до секции 
```bash
server.modules = (
  "mod_access",
  "mod_auth",
  "mod_expire",
  "mod_cgi",
  "mod_fastcgi",
  "mod_openssl",
  "mod_setenv"
 )
 ```

Добавим туда, не забыв поставить запятую после "mod_setenv":
```bash
  "mod_rewrite",
  "mod_redirect",
  "mod_rrdtool"
 ```
Далее после строк:
```bash
\$HTTP["url"] =~ "\.(js|css|css.php|png|gif|jpg)$" {
    expire.url = ( "" => "access plus 1 hours" )
}
```
Добавляем:
```bash
\$HTTP["host"] == "webtlo.local" {
    url.redirect = ("" => "http://192.168.1.2:803")
}
```

Исправляем webtlo.local на нужный вам адрес, а также 192.168.1.2 на IP-адрес вашего сетевого хранилища.

Сохраняем изменения и перезапускаем демон:
```bash
service lighttpd restart
```
Привязываем домен webtlo.local к IP на вашем роутере или локальном DNS. Проверяем, радуемся.
