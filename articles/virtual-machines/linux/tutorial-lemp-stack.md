---
title: Руководство. Развертывание LEMP на виртуальной машине Linux в Azure | Документация Майкрософт
description: В этом руководстве описано, как установить стек LEMP на виртуальной машине Linux в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 1f4c74ab96fc9494d1b3c7dd626265516533330b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188469"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>Руководство. Установка веб-сервера LEMP на виртуальной машине Linux в Azure

Эта статья содержит указания по развертыванию веб-сервера NGINX, MySQL и PHP (стека LEMP) на виртуальной машине Ubuntu в Azure. Стек LEMP является альтернативой известному [стеку LAMP](tutorial-lamp-stack.md), который также можно установить в Azure. Чтобы оценить работу сервера LEMP в действии, вы можете установить и настроить сайт WordPress. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Ubuntu ("L" в названии стека LEMP).
> * Открытие порта 80 для веб-трафика
> * Установка NGINX, MySQL и PHP
> * Проверка установки и настройки.
> * Установка WordPress на сервере LEMP.

Эта настройка выполняется для быстрых тестов или подтверждения концепции.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>Установка NGINX, MySQL и PHP

Чтобы обновить источники пакетов Ubuntu и установить NGINX, PHP и MySQL, выполните команду ниже. 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

Отобразится запрос на установку пакетов и других зависимостей. При появлении запроса укажите пароль привилегированного пользователя для MySQL, а затем нажмите клавишу ВВОД для продолжения. Выполните оставшиеся инструкции на экране. Это позволит установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. 

![Страница с паролем привилегированного пользователя MySQL][1]

## <a name="verify-installation-and-configuration"></a>Проверка установки и настройки


### <a name="nginx"></a>NGINX

Узнайте версию NGINX с помощью следующей команды:
```bash
nginx -v
```

Установив NGINX и открыв порт 80 для виртуальной машины, вы можете получить доступ к веб-серверу через Интернет. Откройте веб-браузер и введите общедоступный IP-адрес виртуальной машины, чтобы перейти на страницу приветствия NGINX. Укажите общедоступный IP-адрес, используемый для подключения по протоколу SSH к виртуальной машине.

![Страница NGINX по умолчанию][3]


### <a name="mysql"></a>MySQL

Узнайте версию MySQL, выполнив указанную ниже команду. Обратите внимание, что параметр `V` указан с заглавной буквы.

```bash
mysql -V
```

Чтобы защитить установку MySQL, выполните сценарий `mysql_secure_installation`. Если вы настраиваете только временный сервер, этот шаг можно пропустить. 

```bash
mysql_secure_installation
```

Введите пароль привилегированного пользователя MySQL и настройте параметры безопасности для своей среды.

Чтобы опробовать функции MySQL (создать базу данных MySQL, добавить пользователей или изменить параметры конфигурации), войдите в MySQL. Этот шаг не требуется для изучения данного руководства. 


```bash
mysql -u root -p
```

По окончании выйдите из командной строки MySQL, введя `\q`.

### <a name="php"></a>PHP

Узнайте версию PHP, выполнив следующую команду:

```bash
php -v
```

Настройте NGINX, чтобы использовать менеджер процессов FastCGI PHP (PHP-FPM). Чтобы создать резервную копию исходного файла конфигурации для блока сервера NGINX, а затем изменить этот файл в текстовом редакторе, выполните следующие команды:

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

В редакторе замените содержимое `/etc/nginx/sites-available/default` кодом ниже. Описания параметров см. в комментариях. Замените общедоступный IP-адрес виртуальной машины на *свой_общедоступный_IP-адрес*, а остальные параметры оставьте без изменений. Затем сохраните файл.

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

Проверьте наличие синтаксических ошибок в конфигурации NGINX.

```bash
sudo nginx -t
```

Если синтаксис правильный, перезапустите NGINX с помощью следующей команды:

```bash
sudo service nginx restart
```

Для дальнейшего тестирования создайте страницу кратких сведений о PHP для просмотра в браузере. Следующая команда создает страницу сведений о PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



Теперь можно просмотреть созданную страницу сведений о PHP. Откройте браузер и перейдите на страницу `http://yourPublicIPAddress/info.php`. Замените общедоступный IP-адрес своей виртуальной машины. Она должна выглядеть, как показано ниже.

![Страница сведений о PHP][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как развернуть сервер LEMP в Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Ubuntu.
> * Открытие порта 80 для веб-трафика
> * Установка NGINX, MySQL и PHP
> * Проверка установки и настройки
> * Установка WordPress в стеке LEMP.

Перейдите к следующему руководству, чтобы узнать, как защитить веб-серверы с помощью SSL-сертификатов.

> [!div class="nextstepaction"]
> [Secure a web server with SSL certificates on a Linux virtual machine in Azure](tutorial-secure-web-server.md) (Защита веб-сервера на виртуальной машине Linux в облаке Azure с помощью SSL-сертификата)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
