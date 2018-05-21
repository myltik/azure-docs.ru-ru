---
title: Руководство. Развертывание LAMP на виртуальной машине Linux в Azure | Документация Майкрософт
description: В этом руководство описано, как установить стек LAMP на виртуальной машине Linux в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: bd102645e7546d9ad9d3ce6b064ae9fa0ce415bd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Руководство. Установка веб-сервера LAMP на виртуальной машине Linux в Azure

Эта статья содержит указания по развертыванию веб-сервера Apache, MySQL и PHP (стека LAMP) на виртуальной машине Ubuntu в Azure. Если вы хотите использовать веб-сервер NGINX, ознакомьтесь с руководством [Install a LEMP web server on an Azure VM](tutorial-lemp-stack.md) (Установка веб-сервера LEMP на виртуальной машине Azure). Если необходимо оценить работу сервера LAMP в действии, вы можете установить и настроить сайт WordPress. Из этого руководства вы узнали, как выполнять такие задачи:

> [!div class="checklist"]
> * создание виртуальной машины Ubuntu (L в названии стека LAMP);
> * Открытие порта 80 для веб-трафика
> * Установка Apache, PHP и MySQL
> * проверка установки и настройки;
> * установка WordPress на сервере LAMP.

Эта настройка выполняется для быстрых тестов или подтверждения концепции. Дополнительные сведения о стеке LAMP, включая рекомендации для рабочей среды, см. в [документации Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI версии 2.0.30 и выше. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Установка Apache, PHP и MySQL

Чтобы обновить источники пакетов Ubuntu и установить Apache, PHP и MySQL, выполните указанную ниже команду. Обратите внимание на знак курсора (^) в конце команды, который является частью имени пакета `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```


Отобразится запрос на установку пакетов и других зависимостей. При появлении запроса укажите пароль привилегированного пользователя для MySQL, а затем нажмите клавишу ВВОД для продолжения. Выполните оставшиеся инструкции на экране. Это позволит установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. 

![Страница с паролем привилегированного пользователя MySQL][1]

## <a name="verify-installation-and-configuration"></a>Проверка установки и настройки


### <a name="apache"></a>Apache

Проверьте версию Apache, выполнив следующую команду:
```bash
apache2 -v
```

Установив Apache и открыв порт 80 для виртуальной машины, вы можете получить доступ к веб-серверу через Интернет. Чтобы просмотреть страницу по умолчанию Apache2 Ubuntu, откройте веб-браузер и введите общедоступный IP-адрес виртуальной машины. Укажите общедоступный IP-адрес, используемый для подключения по протоколу SSH к виртуальной машине.

![Страница Apache по умолчанию][3]


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

Для дальнейшего тестирования создайте страницу кратких сведений о PHP для просмотра в браузере. Следующая команда создает страницу сведений о PHP:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Теперь можно просмотреть созданную страницу сведений о PHP. Откройте браузер и перейдите на страницу `http://yourPublicIPAddress/info.php`. Замените общедоступный IP-адрес своей виртуальной машины. Она должна выглядеть, как показано ниже.

![Страница сведений о PHP][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как развернуть сервер LAMP в Azure. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины Ubuntu.
> * Открытие порта 80 для веб-трафика
> * Установка Apache, PHP и MySQL
> * проверка установки и настройки;
> * установка WordPress на сервере LAMP.

Перейдите к следующему руководству, чтобы узнать, как защитить веб-серверы с помощью SSL-сертификатов.

> [!div class="nextstepaction"]
> [Secure a web server with SSL certificates on a Linux virtual machine in Azure](tutorial-secure-web-server.md) (Защита веб-сервера на виртуальной машине Linux в облаке Azure с помощью SSL-сертификата)

[1]: ./media/tutorial-lamp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png