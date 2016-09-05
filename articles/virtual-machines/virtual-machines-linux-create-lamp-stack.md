<properties
	pageTitle="Развертывание LAMP на виртуальной машине Linux | Microsoft Azure"
	description="Узнайте, как установить стек LAMP на виртуальную машину Linux."
	services="virtual-machines-linux"
	documentationCenter="virtual-machines"
	authors="jluk"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="jluk"/>

# Развертывание стека LAMP в Azure
Эта статья содержит указания по развертыванию веб-сервера Apache, MySQL и PHP (стека LAMP) в Azure. Вам потребуется учетная запись Azure (можно [получить бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [интерфейс командной строки Azure (Azure CLI)](../xplat-cli-install.md), который [подключен к вашей учетной записи Azure](../xplat-cli-connect.md).

В этой статье описано два способа установки LAMP.

## Краткая сводка по командам

1) Развертывание LAMP на новой виртуальной машине.

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Развертывание LAMP на существующей виртуальной машине.

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## Пошаговое руководство по развертыванию LAMP на новой виртуальной машине

Можно начать с создания новой [группы ресурсов](../resource-group-overview.md), содержащей виртуальную машину.

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

Вы создали виртуальную машину Linux с установленным стеком LAMP. При желании можно проверить установку, перейдя к разделу [Проверка успешности установки LAMP].

## Пошаговое руководство по развертыванию LAMP на существующей виртуальной машине

Если вам нужна помощь с созданием виртуальной машины Linux, то вы можете перейти [сюда](./virtual-machines-linux-quick-create-cli.md), чтобы узнать, как создать виртуальную машину Linux. Далее потребуется подключение SSH к виртуальной машине Linux. Если вам нужна помощь с созданием ключа SSH, то вы можете перейти [сюда](./virtual-machines-linux-mac-create-ssh-keys.md), чтобы узнать, как создать ключ SSH в Linux или Mac. Если у вас же имеется ключ SSH, продолжайте настройку подключения SSH к виртуальной машине Linux с использованием `ssh username@uniqueDNS`.

Теперь, когда вы работаете в своей виртуальной машине Linux, мы рассмотрим установку стека LAMP на примере дистрибутивов Debian. Конкретные команды могут отличаться для других дистрибутивов Linux.

#### Установка на Debian или Ubuntu

Необходимо установить следующие пакеты: `apache2`, `mysql-server`, `php5` и `php5-mysql`. Их можно установить вручную или с помощью Tasksel. Ниже приведены указания для обоих способов. Перед установкой потребуется скачать и обновить списки пакетов.

    user@ubuntu$ sudo apt-get update
    
##### Установка отдельных пакетов
Можно использовать apt-get.

	user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### Установка с помощью Tasksel
В качестве альтернативы можно скачать Tasksel. Это инструмент Debian и Ubuntu, который устанавливает в систему несколько связанных пакетов в рамках одной скоординированной задачи.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

После выполнения одной из приведенных выше команд вам будет предложено установить эти пакеты и некоторые другие зависимости. Нажмите «y», а затем клавишу ВВОД, чтобы продолжить, и следуйте остальным указаниям по установке административного пароля для MySQL. Это позволит вам установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL.

![][1]

Выполните следующую команду, чтобы увидеть другие расширения PHP, доступные как пакеты:

	user@ubuntu$ apt-cache search php5


#### Создание документа info.php

Теперь можно проверить версию установленных компонентов Apache, MySQL и PHP из командной строки, введя `apache2 -v`, `mysql -v` или `php -v`.

Если вы хотите расширить тестирование, то можете создать страницу кратких сведений о PHP для просмотра в браузере. Создайте новый файл в текстовом редакторе Nano с помощью следующей команды.

    user@ubuntu$ sudo nano /var/www/html/info.php

В текстовом редакторе Nano GNU добавьте следующие строки.

    <?php
    phpinfo();
    ?>

Сохраните файл и закройте текстовый редактор.

Перезапустите Apache с помощью следующей команды, чтобы все новые установленные компоненты вступили в действие.

    user@ubuntu$ sudo service apache2 restart

## Проверка успешности установки LAMP

Теперь можно просмотреть только что созданную страницу сведений о PHP, перейдя по адресу http://youruniqueDNS/info.php в браузере. Она должна иметь следующий вид.

![][2]

Можно проверить установленный экземпляр Apache, просмотрев страницу по умолчанию Apache2 Ubuntu. Для этого перейдите по адресу http://youruniqueDNS/. Вы увидите нечто вроде этого:

![][3]

Поздравляем! Вы установили стек LAMP на виртуальную машину Azure.

## Дальнейшие действия

Ознакомьтесь с документацией Ubuntu по стеку LAMP.

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

<!---HONumber=AcomDC_0824_2016-->