<properties
	pageTitle="Развертывание приложения Linux с помощью расширения Azure CustomScript"
	description="Узнайте, как использовать расширение Azure CustomScript для развертывания приложений на виртуальных машинах под управлением Linux."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services=""
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="2/23/2015"
	ms.author="guybo"/>

#Развертывание приложения LAMP с помощью расширения Azure CustomScript для Linux#

Расширение Azure CustomScript для Linux позволяет использовать для настройки виртуальных машин произвольный код, написанный на одном из языков сценариев, которые поддерживаются виртуальной машиной (например Python, Bash и т. д.). Это обеспечивает гибкую автоматизацию развертывания приложения на нескольких виртуальных машинах.

Расширение CustomScript можно развернуть с помощью портала Azure, PowerShell или кроссплатформенного интерфейса командной строки Azure (xplat-cli).

В нашем примере мы развернем простое приложение LAMP на виртуальной машине под управлением Ubuntu с помощью интерфейса xplat-cli.

## Предварительные требования

В этом пошаговом руководстве мы создадим две виртуальные машины Azure под управлением Ubuntu 14.04. Назовем их *script-vm* и *lamp-vm*. Когда вы будете самостоятельно выполнять описанные действия, используйте уникальные имена. Одну из этих виртуальных машин мы будем использовать для выполнения команд CLI, а другую - для развертывания приложения LAMP.

Также нам потребуется учетная запись хранения Azure и ключ доступа к ней (все это можно получить на портале Azure).

Дополнительную информацию о создании виртуальных машин Linux в Azure см. в разделе [Создание виртуальной машины с ОС Linux](virtual-machines-linux-tutorial.md).

Несмотря на то, что для Ubuntu используется ряд специальных команд установки, общие действия могут быть адаптированы под любой поддерживаемый дистрибутив.

На виртуальной машине *script-vm* должен быть установлен интерфейс xplat-cli и она должна быть подключена к Azure. Дополнительную информацию см. в разделе [Установка и настройка кроссплатформенного интерфейса командной строки Azure](xplat-cli.md).

## Передача сценария

В этом примере расширение CustomScript запустит сценарий на удаленной виртуальной машине для установки стека LAMP и создания PHP-страницы. Чтобы сценарий был доступен из любого расположения, мы передадим его как большой двоичный объект Azure.

**Сценарий**

Этот сценарий устанавливает стек LAMP в Ubuntu (включая настройку автоматической установки сервера MySQL), создает простой PHP-файл и запускает сервер Apache:

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

**Передача**

Сохраните сценарий как текстовый файл, например *lamp_install.sh*, и передайте его в службу хранилища Azure. Это легко сделать с помощью интерфейса xplat-cli. Приведенный ниже пример передает файл в контейнер хранилища с именем scripts. Примечание. Если контейнер не существует, необходимо сначала его создать.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Также создайте JSON-файл, в котором будет указан способ скачивания сценария из службы хранилища Azure. Сохраните его как *public_config.json* (вместо mystorage укажите свою учетную запись хранения):

    {fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Развертывание расширения

Теперь расширение Custom Script для Linux можно развернуть на удаленной виртуальной машине с помощью интерфейса xplat-cli:

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

При этом будет скачан и запущен сценарий *lamp_install.sh* на виртуальной машине с именем *lamp-vm*.

Так как приложение включает в себя веб-сервер, не забудьте открыть порт прослушивания HTTP на удаленной виртуальной машине:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Мониторинг и устранение неполадок

Информация о ходе выполнения расширения Custom Script отображается в файле журнала на удаленной виртуальной машине. Установите SSH-подключение к виртуальной машине *lamp-vm* и создайте заключительный фрагмент файла журнала:

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Когда выполнение расширения CustomScript будет завершено, вы сможете перейти на созданную PHP-страницу. В нашем примере она будет находиться по адресу: *http://lamp-vm.cloudapp.net/phpinfo.php*.

## Дополнительные ресурсы

С помощью описанных выше действий можно выполнять развертывание и более сложных приложений. В приведенном примере сценарий установки был сохранен в службе хранилища Azure как общедоступный большой двоичный объект. Чтобы обеспечить больший уровень защиты, сценарий установки можно сохранить как защищенный большой двоичный объект, для доступа к которому будет использоваться [подписанный URL-адрес](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Дополнительную информацию об интерфейсе xplat-cli, Linux и расширении CustomScript см. в следующих разделах:

[Автоматизация задач настройки виртуальных машин под управлением Linux с помощью расширения CustomScript](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Расширения Azure для Linux (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux и вычисления с открытым кодом в Azure](virtual-machines-linux-opensource.md)

<!--HONumber=47-->
