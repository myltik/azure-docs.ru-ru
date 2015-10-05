<properties
	pageTitle="Использование расширения CustomScript на виртуальной машине Linux | Microsoft Azure"
	description="Узнайте, как использовать расширение CustomScript для развертывания приложений на виртуальных машинах под управлением Linux, созданных с помощью классической модели развертывания."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services="virtual-machines"
	authors="gbowerman"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2015"
	ms.author="guybo"/>

#Развертывание приложения LAMP с помощью расширения Azure CustomScript для Linux##

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье рассматривается устранение неполадок доступа к приложениям на виртуальных машинах, созданных с помощью классической модели развертывания.

Расширение Microsoft Azure CustomScript для Linux позволяет использовать для настройки виртуальных машин произвольный код, написанный на одном из языков сценариев, которые поддерживаются виртуальной машиной (например, Python и Bash). Это обеспечивает гибкую автоматизацию развертывания приложения на нескольких виртуальных машинах.

Расширение CustomScript можно развернуть с помощью портала Azure, Windows PowerShell или интерфейса командной строки Azure (Azure CLI).

В этой статье мы будем использовать интерфейс командной строки Azure для развертывания простого приложения LAMP на виртуальной машине под управлением Ubuntu, созданной с помощью классической модели развертывания.

## Предварительные требования

Для этого примера создайте две виртуальные машины Azure под управлением Ubuntu 14.04 или более поздней версии. Присвойте им имена *script-vm* и *lamp-vm*. При создании виртуальных машин используйте уникальные имена. Одна из этих машин будет использоваться для выполнения команд интерфейса командной строки, а другая — для развертывания приложения LAMP.

Вам также потребуется учетная запись службы хранилища Azure и ключ доступа к ней (все это можно получить на портале Azure).

Дополнительную информацию о создании виртуальных машин Linux в Azure см. в статье [Создание виртуальной машины с ОС Linux](virtual-machines-linux-tutorial.md).

Команды установки рассчитаны на Ubuntu, но могут быть адаптированы для установки любого поддерживаемого дистрибутива Linux.

На виртуальной машине script-vm должен быть установлен интерфейс CLI Azure. Кроме того, ее необходимо подключить к Azure. Дополнительную информацию см. в статье [Установка и настройка интерфейса командной строки Azure](../xplat-cli.md).

## Загрузка сценария

Мы используем расширение CustomScript, чтобы выполнить сценарий на удаленной виртуальной машине для установки стека LAMP и создания PHP-страницы. Чтобы сценарий был доступен из любого расположения, мы передадим его как большой двоичный объект Azure.

### Общие сведения о сценариях

Сценарий в примере устанавливает стек LAMP в Ubuntu (включая настройку автоматической установки сервера MySQL), создает простой PHP-файл и запускает сервер Apache:

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo <center><h1>My Demo App</h1><br/></center> > /var/www/html/phpinfo.php
	echo <\?php phpinfo()\; \?> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

### Отправка скрипта

Сохраните сценарий как текстовый файл, например *lamp\_install.sh*, и передайте его в хранилище Azure. Это легко сделать с помощью интерфейса CLI Azure. Приведенный ниже пример передает файл в контейнер хранилища с именем scripts. Если контейнер не существует, необходимо сначала его создать.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Также создайте JSON-файл, в котором будет указан способ скачивания сценария из службы хранилища Azure. Сохраните его как *public\_config.json* (вместо mystorage укажите имя своей учетной записи хранения):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Развертывание расширения

Теперь расширение CustomScript для Linux можно развернуть на удаленной виртуальной машине с помощью интерфейса командной строки Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

Предыдущая команда загружает и выполняет сценарий *lamp\_install.sh* на виртуальной машине с именем *lamp-vm*.

Так как приложение включает в себя веб-сервер, не забудьте открыть порт прослушивания HTTP на удаленной виртуальной машине с помощью следующей команды:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Мониторинг и устранение неполадок

Можно проверить правильность выполнения пользовательского скрипта, просмотрите файл журнала на удаленной виртуальной машине. Добавьте SSH в *lamp-vm* и добавьте в файл журнала заключительный фрагмент с помощью следующей команды:

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

После запуска расширения CustomScript вы сможете перейти к созданной PHP-странице и проверить данные. PHP-страница для примера в этой статье — **http://lamp-vm.cloudapp.net/phpinfo.php*.

## Дополнительные ресурсы

С помощью описанных выше действий можно выполнять развертывание и более сложных приложений. В приведенном примере сценарий установки был сохранен в службе хранилища Azure как общедоступный большой двоичный объект. Чтобы обеспечить больший уровень защиты, сценарий установки можно сохранить как защищенный большой двоичный объект, для доступа к которому будет использоваться [подписанный URL-адрес](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Дополнительную информацию об интерфейсе командной строки Azure, Linux и расширении CustomScript см. в следующих статьях.

[Автоматизация задач настройки виртуальных машин под управлением Linux с помощью расширения CustomScript](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Расширения Azure для Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux и вычисления с открытым кодом в Azure](virtual-machines-linux-opensource.md)

<!---HONumber=Sept15_HO4-->