---
title: Загрузка пакета Azure SDK для PHP
description: Узнайте, как скачать и установить пакет SDK для Azure для PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: cfcf908145e8a384782953e045f9e10fd3c0e8f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639475"
---
# <a name="download-the-azure-sdk-for-php"></a>Загрузка пакета Azure SDK для PHP

## <a name="overview"></a>Обзор

Azure SDK для PHP включает компоненты для разработки, развертывания приложений PHP в Azure и управления ими. Пакет Azure SDK для PHP включает следующие средства:

* **Клиентские библиотеки PHP для Azure**. Эти библиотеки классов предоставляют интерфейс для доступа к функциям Azure, таким как службы управления данными и облачные службы.
* **Интерфейс командной строки Azure для Mac, Linux и Windows (CLI Azure)**. Это набор команд для развертывания и управления службами Azure, например веб-сайтами Azure и виртуальными машинами Azure. CLI Azure работает на любой платформе, включая Windows, Mac и Linux.
* **Azure PowerShell (только для Windows)**. Это набор командлетов PowerShell для развертывания служб Azure, таких как облачные службы и виртуальные машины, и управления ими.
* **Эмуляторы Azure (только для Windows)**. Эмуляторы вычислений и хранения данных – это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эмуляторы Azure работают только в Windows.

В приведенных ниже разделах описано, как загрузить и установить указанные выше компоненты.

В инструкциях, приведенных в этой статье, предполагается, что установлена среда [PHP][install-php].

> [!NOTE]
> Для использования клиентских библиотек PHP для Azure требуется PHP версии 5.5 и выше.
>
>

## <a name="php-client-libraries-for-azure"></a>Клиентские библиотеки PHP для Azure

Клиентские библиотеки PHP для Azure предоставляют интерфейс для доступа к возможностям Azure, например службам управления данными и облачным службам, из любой операционной системы. Эти библиотеки можно установить с помощью компоновщика.

Сведения об использовании клиентских библиотек PHP для Azure см. в статьях [Использование хранилища больших двоичных объектов из PHP][blob-service], [Использование табличного хранилища из PHP][table-service] и [Использование хранилища очередей из PHP][queue-service].

### <a name="install-via-composer"></a>Установка через компоновщик

1. [Установите Git][install-git]. В системе Windows необходимо также добавить исполняемый файл Git в переменную среды PATH.

2. Создайте файл с именем **composer.json** в корневой папке проекта и добавьте в него следующий код:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Скачайте **[composer.phar][composer-phar]** в корневой каталог проекта.

4. Откройте командную строку и выполните эту команду в корневом каталоге проекта.

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell и эмуляторы Azure

Azure PowerShell — это набор командлетов PowerShell для развертывания служб Azure и управления ими, например облачных служб и виртуальных машин. Эмуляторы Azure — это локальные эмуляторы облачных служб и служб управления данными, которые позволяют тестировать приложение локально. Эти компоненты поддерживаются только в Windows.

Чтобы установить Azure PowerShell и эмуляторы Azure, мы рекомендуем использовать [установщик веб-платформы Майкрософт][download-wpi]. Обратите внимание, что также можно установить другие компоненты разработки, например PHP, SQL Server, драйверы Microsoft для SQL Server для PHP и WebMatrix.

Дополнительные сведения о том, как использовать Azure PowerShell, см. в [этой статье][powershell-tools].

## <a name="azure-cli"></a>Инфраструктура CLI Azure

CLI Azure — это набор команд для развертывания и управления службами Azure, например веб-сайтами Azure и виртуальными машинами Azure. Сведения об установке Azure CLI см. [здесь](cli-install-nodejs.md).

## <a name="next-steps"></a>Дополнительная информация

Дополнительную информацию можно найти в [Центре разработчика PHP](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
