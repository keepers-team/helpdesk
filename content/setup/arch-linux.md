---
title: "Arch Linux, nginx"
weight: 8
---

**Устанавливаем пакеты:**

```bash
sudo pacman -S nginx php-fpm php-sqlite
```

***

**Включаем автозагрузку служб и запускаем их:**

```bash
sudo systemctl enable --now nginx.service php-fpm.service
```

***

**Скачиваем архив с web-TLO и распаковываем его** в каталог `/var/www/`:

> **ПРИМЕЧАНИЕ.** Используйте эти команды, чтобы обновить web-TLO до актуальной версии

```bash
wget https://github.com/keepers-team/webtlo/releases/latest/download/webtlo.zip
sudo unzip -o webtlo.zip -d /var/www/
rm -f webtlo.zip
```

Поменять права и группу на директорию WebTLO, необходимо выдать юзеру/группе, под которым работает сервис php-fpm, чтобы он смог писать логи:
```bash
sudo chown http: -R /var/www/webtlo.local
```

*в случае если вашим юзером для php-fpm указан http

***


**Включаем чтение сторонних конфигов** в `/etc/nginx/nginx.conf` 

```bash
sudo nano /etc/nginx/nginx.conf
```

Добавляем строку `include conf.d/*.conf;` после последнего конфига, но перед последней скобой, чтобы конец конфига выглядел так:

![изображение](https://user-images.githubusercontent.com/54148165/176678865-b7fbfd4b-3eac-4af1-a0b2-7e8c7f6fa3e1.png)


> **ПРИМЕЧАНИЕ.** сменить юзера и группу по умолчанию можно во второй строке

`user пользователь группа;` 

например `www`, по умолчанию может быть `http`

Сохраняем и выходим _Ctrl+X_


***


**Создаём новый отдельный конфиг для WebTLO:**

```bash
sudo nano /etc/nginx/conf.d/webtlo.conf
```
> **ПРИМЕЧАНИЕ.** в директиве root должен быть прописан путь до index.php. Например, если WebTLO качался не по ссылке выше и файл index.php находится в подпапке src.

Вписываем в конфиг:
```bash
server {
        listen 8099;
        server_name _;
        # Путь к папке с кодом
        root /var/www/webtlo.local/;
        index index.php;
        # Обработка php файлов с помощью fpm
        location ~ \.php$ {
                try_files $uri =404;
                include /etc/nginx/fastcgi.conf;
                fastcgi_pass unix:/run/php-fpm/php-fpm.sock;
                }
    }
```
> **ПРИМЕЧАНИЕ.** новой строкой после `fastcgi_pass unix:/run/php-fpm/php-fpm.sock;` можно указать `fastcgi_read_timeout 600s`, он нужен для выполнения тяжелых операций по обновлению списков из нескольких разделов

Сохраняем и выходим _Ctrl+X_

***

**Редактируем конфиг PHP:**

```bash
sudo nano /etc/php/php.ini
```

Ищем и расскоментируем (удаляем `;` в начале строки) `;extension=sqlite3` и `;extension=pdo_sqlite`

Сохраняем и выходим _Ctrl+X_

Проверяем конфиг nginx и перезапускаем его, если всё ОК:
```bash
sudo nginx -t
sudo systemctl restart nginx
```

***


**Добавляем хост в файл** `/etc/hosts`

```bash
sudo nano /etc/hosts
```

новой строкой:

`127.0.0.1 webtlo.local`


***



Открываем в браузере страницу http://webtlo.local:8099/

![изображение](https://user-images.githubusercontent.com/54148165/176675025-41db14f3-e8f5-4f75-8199-0f5592e61fbb.png)

