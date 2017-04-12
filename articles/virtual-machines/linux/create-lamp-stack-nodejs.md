---
title: "Развертывание LAMP на виртуальной машине Linux с помощью Azure CLI 1.0 | Документация Майкрософт"
description: "Узнайте, как установить стек LAMP на виртуальную машину Linux в Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: feba2fb20d1831e92358ff5d1b4c9589d63d28dc
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-with-the-azure-cli-10"></a>Развертывание стека LAMP с помощью Azure CLI 1.0
Эта статья содержит указания по развертыванию веб-сервера Apache, MySQL и PHP (стека LAMP) в Azure. Вам потребуется учетная запись Azure (можно [получить бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [Azure CLI](../../cli-install-nodejs.md), который [подключен к вашей учетной записи Azure](../../xplat-cli-connect.md).

## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- [Azure CLI 1.0] — интерфейс командной строки для классической модели развертывания и модели развертывания с помощью Resource Manager (в этой статье).
- [Azure CLI 2.0](create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

* Развертывание LAMP на существующей виртуальной машине

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Пошаговое руководство по развертыванию LAMP на новой виртуальной машине
Можно начать с создания [группы ресурсов](../../azure-resource-manager/resource-group-overview.md) для новой виртуальной машины.

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Чтобы создать саму виртуальную машину, можно использовать готовый шаблон Azure Resource Manager [с сайта GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Вы увидите запрос на ввод дополнительных данных.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Вы создали виртуальную машину Linux с установленным стеком LAMP. При желании можно проверить установку, перейдя к разделу [Проверка успешности установки LAMP](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Пошаговое руководство по развертыванию LAMP на существующей виртуальной машине
Если вам нужна помощь с созданием виртуальной машины Linux, то вы можете перейти [сюда](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), чтобы узнать, как создать виртуальную машину Linux. Далее требуется подключится по протоколу SSH к виртуальной машине Linux. Если вам нужна помощь с созданием ключа SSH, то вы можете перейти [сюда](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), чтобы узнать, как создать ключ SSH в Linux или Mac.
Если у вас же имеется ключ SSH, продолжайте процедуру и, используя командную строку, подключитесь по протоколу SSH к виртуальной машине Linux с помощью команды `ssh exampleUsername@exampleDNS`.

Теперь, когда вы работаете на своей виртуальной машине Linux, мы можем рассмотреть установку стека LAMP на дистрибутивы на основе Debian. Конкретные команды могут отличаться для других дистрибутивов Linux.

#### <a name="installing-on-debianubuntu"></a>Установка на Debian или Ubuntu
Необходимо установить следующие пакеты: `apache2`, `mysql-server`, `php5` и `php5-mysql`. Их можно установить, перетащив вручную или воспользовавшись Tasksel. Ниже приведены указания для обоих способов.
Перед установкой требуется скачать и обновить списки пакетов.

    user@ubuntu$ sudo apt-get update

##### <a name="individual-packages"></a>Установка отдельных пакетов
Можно использовать apt-get.

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Установка с помощью Tasksel
В качестве альтернативы можно скачать Tasksel. Это инструмент Debian и Ubuntu, который устанавливает в систему несколько связанных пакетов в рамках одной скоординированной задачи.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

После выполнения одной из приведенных выше команд вам будет предложено установить эти пакеты и некоторые другие зависимости. Нажмите «y», а затем клавишу ВВОД, чтобы продолжить, и следуйте остальным указаниям по установке административного пароля для MySQL. Это позволит установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. 

![][1]

Выполните следующую команду, чтобы увидеть другие расширения PHP, доступные как пакеты:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Создание документа info.php
Теперь можно проверить версию установленных компонентов Apache, MySQL и PHP из командной строки, введя `apache2 -v`, `mysql -v` или `php -v`.

Если вы хотите расширить тестирование, то можете создать страницу кратких сведений о PHP для просмотра в браузере. Создайте файл в текстовом редакторе Nano с помощью следующей команды.

    user@ubuntu$ sudo nano /var/www/html/info.php

В текстовом редакторе Nano GNU добавьте следующие строки.

    <?php
    phpinfo();
    ?>

Сохраните файл и закройте текстовый редактор.

Перезапустите Apache с помощью следующей команды, чтобы все новые установленные компоненты вступили в действие.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Проверка успешности установки LAMP
Теперь можно просмотреть созданную страницу сведений о PHP. Откройте браузер и перейдите по адресу http://youruniqueDNS/info.php. Она должна выглядеть, как показано ниже.

![][2]

Можно проверить установленный экземпляр Apache, просмотрев страницу по умолчанию Apache2 Ubuntu. Для этого перейдите по ссылке http://youruniqueDNS/. Должна отобразиться страница следующего вида.

![][3]

Поздравляем! Вы установили стек LAMP на виртуальную машину Azure.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с документацией Ubuntu по стеку LAMP.

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png
