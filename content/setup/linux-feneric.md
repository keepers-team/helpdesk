---
title: "Linux"
weight: 10
---

## Поставим и настроим nginx+php-fpm:

Debian-based (Ubuntu, Alt, OpenMediaVault):
```sh
apt-get install nginx-light php-fpm php-curl php-mbstring php-sqlite3 php-xml php-xmlrpc

rm /etc/nginx/sites-enabled/default

cat<'EOF' > /etc/nginx/sites-enabled/tlo.cfg
server {
    listen 8088 default_server;
    server_name _;
    root /var/www/webtlo.local;
    index index.html index.php;
    location / {
        try_files $uri $uri/ =404;
    }
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
        fastcgi_read_timeout 600s;
    }
    location ~ /\.ht {
        deny all;
    }
    location /data {
        deny all;
    }
}
EOF

service nginx reload
```
Замечания:
- Пакет `nginx-light` тянет меньше зависимостей в отличие от `nginx`, но может отсутствовать в некоторых дистрибутивах.
- порт веб-сервера (8088) устанавливается в строке `listen 8088 default_server;`
- `fastcgi_read_timeout 600s` нужен для выполнения тяжелых операций по обновлению списков из нескольких разделов

Установим/обновим релизную версию WebTLO:
```sh
wget https://github.com/berkut-174/webtlo/releases/latest/download/webtlo.zip
unzip -o webtlo.zip -d /var/www
rm webtlo.zip
install -m 775 -o www-data -g www-data -d /var/www/webtlo.local/data
```

Для использования последней версии из репозитория можно использовать `https://github.com/berkut-174/webtlo/archive/refs/heads/master.zip` (внимание, там отличается имя папки)

Готово, можно заходить на http://localhost:8088 или http://ip:8088
