---
title: "Windows"
weight: 1
---

Для запуска web-TLO под управлением ОС Windows рекомендуется использовать WAMP платформу [Open Server Panel](https://ospanel.io/).

Скачиваем дистрибутив с сайта [Open Server Panel](https://ospanel.io/download/), либо берём [здесь](https://cloud.mail.ru/public/GeiW/nZxz2WxaC).

### Установка Open Server Panel

Запускаем скачанный файл установщика.

![01](https://user-images.githubusercontent.com/1829509/184808323-ed1a85d2-ade8-4133-a80e-a1a02eda1f21.png)

Принимаем условия лицензионного соглашения.

![02](https://user-images.githubusercontent.com/1829509/184808441-72c3d2b1-7c3d-4825-964d-09ab03c80817.png)

Читаем рекомендации по установке.

![03](https://user-images.githubusercontent.com/1829509/184808493-08048a8a-cc6a-4eda-8e9b-3084eb52271f.png)

Указываем каталог для установки дистрибутива.

![04](https://user-images.githubusercontent.com/1829509/184808550-59e20bed-d3eb-4537-a476-ee6f48c4dd50.png)

Выбираем необходимые компоненты для установки как на снимках ниже, все остальные флажки отключаем.

![05](https://user-images.githubusercontent.com/1829509/184808666-5111dc4d-2558-4437-bbcf-c4d1c214cb7e.png)

![06](https://user-images.githubusercontent.com/1829509/184808662-5e280304-803a-4639-9934-3b41816cf5db.png)

![07](https://user-images.githubusercontent.com/1829509/184808611-5a229fe8-8c1b-4efe-a2be-0222b92ddffa.png)

При необходимости создаём отдельный пункт в меню «Пуск».

![08](https://user-images.githubusercontent.com/1829509/184808710-31b2b36c-7c30-4a20-9377-2684120ae4ca.png)

Указываем дополнительные настройки.

![09](https://user-images.githubusercontent.com/1829509/184808770-4e54c188-e8a3-4c87-8358-3f1f8f5a220d.png)

Соглашаемся на установку.

![10](https://user-images.githubusercontent.com/1829509/184808834-141ab776-d50c-4db1-8f55-c3f4430546ab.png)

Ожидаем установку.

![11](https://user-images.githubusercontent.com/1829509/184808856-df8304ef-9ad0-4193-8f4c-9727593f2232.png)

Соглашаемся на перезагрузку.

![12](https://user-images.githubusercontent.com/1829509/184808922-3902c40f-32f5-408f-bb22-6af2e04d24b8.png)

### Распаковка web-TLO

Предварительно удаляем каталог `C:\OpenServer\domains\localhost\`.

Скачиваем [архив](https://github.com/berkut-174/webtlo/releases/latest/download/webtlo.zip) с web-TLO и распаковываем его в каталог
`C:\OpenServer\domains\`

> Для обновления web-TLO потребуется распаковать новую версию с заменой существующих файлов в этот же каталог.

![13](https://user-images.githubusercontent.com/1784545/81086166-d4222180-8f00-11ea-8b3d-65e6f939bef9.png)

### Настройка Open Server Panel

Запускаем Open Server Panel из меню «Пуск» или «Рабочего стола».

Выбираем предпочитаемый язык интерфейса программы.

![14](https://user-images.githubusercontent.com/1829509/184808958-ca8540fd-c79f-4a26-91bf-a724cc655c45.png)

Открываем окно настроек Open Server Panel.

![15](https://user-images.githubusercontent.com/1784545/81086170-d4bab800-8f00-11ea-9431-eddd234266d2.png)

На вкладке «Основные» устанавливаем флажки «Автозапуск сервера» и «Запускать вместе с Windows».

> Также рекомендуется снять флажок «Автоматически проверять наличие новых версий».

![16](https://user-images.githubusercontent.com/1829509/184809030-e0be8cd4-75b4-4241-b0c6-85a8c78c1a55.png)

Переходим на вкладку «Сервер» и указываем необходимый «IP-адрес сервера».

![17](https://user-images.githubusercontent.com/1829509/184809037-6c822b33-e1ae-44b3-bdf2-d4cd7e669976.png)

Если доступ к web-TLO будет осуществляться только с машины, на которой он установлен, то есть локально, тогда укажите в поле «IP-адрес сервера»
локальный интерфейс «127.0.0.1». В таком варианте никто кроме вас не сможет подключиться к веб-серверу из локальной сети. Если же вы планируете
разместить web-TLO на одной машине, а обращаться к web-TLO будете, в том числе, и с другого устройства в сети, то выберите вариант
«все доступные IP», либо выберите из списка IP-адрес вашего сетевого интерфейса.

> Посмотреть какой IP-адрес назначен вашему сетевому интерфейсу можно в настройках сетевого подключения.

На вкладке «Модули» выбираем из списка «Apache_2.4-PHP_7.2-7.4+Nginx_1.21» и «PHP_7.4».

![18](https://user-images.githubusercontent.com/1829509/184809033-1458cade-93a0-4a1a-aec0-f659695b3812.png)

Добавляем алиасы на вкладке «Алиасы».

![19](https://user-images.githubusercontent.com/1829509/184809035-ff01e24b-bf28-428f-9bfc-8873ee371559.png)

Здесь же можно добавить связь «исходный домен → конечный домен». Это может понадобиться в тех случаях, когда вы захотите обращаться к web-TLO
не по имени домена `webtlo.local`, а какому-то другому варианту. Например, не http://webtlo.local, а http://127.0.0.1, http://comp2.local
или http://192.168.1.20 и т.д. Тогда вам следует добавить эту связь согласно используемым именам:

| Исходный домен    | Конечный домен    |
| ----              | ---- |
| 127.0.0.1         | webtlo.local    |
| localhost         | webtlo.local    |
| comp2.local       | webtlo.local    |
| 192.168.1.20      | webtlo.local    |

Нажимаем «Сохранить» и запускаем сервер.

![20](https://user-images.githubusercontent.com/1784545/81086178-d6847b80-8f00-11ea-94c8-9cb2bc7819c1.png)

Для работы с web-TLO открываем в браузере страницу http://webtlo.local

![21](https://user-images.githubusercontent.com/1784545/81086181-d6847b80-8f00-11ea-8318-2849458b4078.png)

### Решение проблем

#### Невозможно занять порт поскольку он уже используется

Если какая-то программа занимает стандартные порты 80 или 443 и при запуске Open Server Panel появляются ошибки:

![22](https://user-images.githubusercontent.com/1784545/81086182-d71d1200-8f00-11ea-85bc-c0a3f8091aa9.png)

![23](https://user-images.githubusercontent.com/1784545/81086185-d71d1200-8f00-11ea-8c65-fc627e11bb42.png)

То в настройках Open Server Panel следует переопределить стандартные порты. Для этого открываем настройки Open Server Panel, переходим на вкладку «Сервер»
и выставляем параметры как на снимке.

![24](https://user-images.githubusercontent.com/1784545/81086187-d7b5a880-8f00-11ea-915f-aaffbd142185.png)

Запускаем Open Server Panel и открываем страницу с web-TLO, принудительно указывая номер порта http://webtlo.local:801, который ввели в настройках.

![25](https://user-images.githubusercontent.com/1784545/81086189-d7b5a880-8f00-11ea-80b9-33bc3b166706.png)

#### Время в журнале отображается некорректно

Если при работе web-TLO в журнале наблюдается «чехарда» со временем:

![26](https://user-images.githubusercontent.com/1784545/81086190-d7b5a880-8f00-11ea-922f-3800aa95066a.png)

Необходимо в настройках PHP указать корректный часовой пояс. Для этого откройте конфигурационный файл php.ini: Open Server Panel →
Дополнительно → Конфигурация → PHP-5.3 (вместо 5.3 может быть другая версия)

![27](https://user-images.githubusercontent.com/1784545/81086192-d84e3f00-8f00-11ea-83bd-7aadcbce534b.png)

Добавьте в раздел [Date] необходимый [часовой пояс](https://www.php.net/manual/ru/timezones.php), например:

![28](https://user-images.githubusercontent.com/1784545/81086194-d8e6d580-8f00-11ea-8d55-ec16883ed4e7.png)

Сохраните изменения в файле и перезапустите Open Server Panel.

![29](https://user-images.githubusercontent.com/1784545/81086197-d8e6d580-8f00-11ea-8c80-c9e1c3b7313e.png)

#### Файл `C:\Windows\system32\drivers\etc\hosts` недоступен для записи

![30](https://user-images.githubusercontent.com/1784545/81086198-d8e6d580-8f00-11ea-8d20-78d47d05face.png)

Если при запуске Open Server Panel появляется такое сообщение, необходимо в настройках Open Server Panel на вкладке «Сервер» установить флажок
«Не вносить изменения в HOSTS файл».

![31](https://user-images.githubusercontent.com/1784545/81086200-d97f6c00-8f00-11ea-8d63-9573026218a1.png)

Нажать кнопку «Сохранить» и внести необходимые изменения в файл HOSTS вручную. Для этого откройте командную строку от имени администратора
и выполните команду (введите текст команды и нажмите Enter):

`echo 127.0.0.1 webtlo.local >> C:\Windows\System32\drivers\etc\hosts`

После внесения всех изменений запустите Open Server Panel.

![32](https://user-images.githubusercontent.com/1784545/81086178-d6847b80-8f00-11ea-94c8-9cb2bc7819c1.png)
