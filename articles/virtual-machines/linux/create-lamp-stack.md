---
title: "Развертывание LAMP на виртуальной машине Linux в Azure | Документация Майкрософт"
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
ms.openlocfilehash: 174490bec1aea20abf2c2bd465c7aa2c8590e3e2
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>Развертывание стека LAMP в Azure
Эта статья содержит указания по развертыванию веб-сервера Apache, MySQL и PHP (стека LAMP) в Azure. Требуются учетная запись Azure (можно [получить бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/)) и [CLI Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2). Эти действия можно также выполнить с помощью [Azure CLI 1.0](create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-command-summary"></a>Краткая сводка по командам

1. Сохраните на локальном компьютере [файл azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) и измените его, указав свои параметры.
2. Выполните следующие две команды, чтобы создать группу ресурсов, а затем развернуть шаблон.

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>Развертывание LAMP на существующей виртуальной машине
Следующие команды обновляют пакеты, а затем устанавливают Apache, MySQL и PHP.

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Пошаговое руководство по развертыванию LAMP на новой виртуальной машине

1. С помощью команды [az group create](/cli/azure/group#create) создайте группу ресурсов для новой виртуальной машины.

```azurecli
az group create -l westus -n myResourceGroup
```
Чтобы создать саму виртуальную машину, можно использовать готовый шаблон Azure Resource Manager [с сайта GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

2. Сохраните [файл azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) на локальном компьютере.
3. Измените **файл azuredeploy.parameters.json**, указав свои входные параметры.
4. Разверните шаблон с помощью команды [az group deployment create], указав скачанный JSON-файл.

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

Вы создали виртуальную машину Linux с установленным стеком LAMP. При желании можно проверить установку, перейдя к разделу [Проверка успешности установки LAMP](#verify-lamp-successfully-installed).

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Пошаговое руководство по развертыванию LAMP на существующей виртуальной машине
Если вам нужна помощь с созданием виртуальной машины Linux, то вы можете перейти [сюда](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli), чтобы узнать, как создать виртуальную машину Linux. Далее требуется подключится по протоколу SSH к виртуальной машине Linux. Если вам нужна помощь с созданием ключа SSH, то вы можете перейти [сюда](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), чтобы узнать, как создать ключ SSH в Linux или Mac.
Если у вас же имеется ключ SSH, продолжайте процедуру и, используя командную строку, подключитесь по протоколу SSH к виртуальной машине Linux с помощью команды `ssh azureuser@mypublicdns.westus.cloudapp.azure.com`.

Теперь, когда вы работаете на своей виртуальной машине Linux, мы можем рассмотреть установку стека LAMP на дистрибутивы на основе Debian. Конкретные команды могут отличаться для других дистрибутивов Linux.

#### <a name="installing-on-debianubuntu"></a>Установка на Debian или Ubuntu
Необходимо установить следующие пакеты: `apache2`, `mysql-server`, `php5` и `php5-mysql`. Их можно установить, перетащив вручную или воспользовавшись Tasksel.
Перед установкой требуется скачать и обновить списки пакетов.

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>Установка отдельных пакетов
Можно использовать apt-get.

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>Установка с помощью Tasksel
В качестве альтернативы можно скачать Tasksel. Это инструмент Debian и Ubuntu, который устанавливает в систему несколько связанных пакетов в рамках одной скоординированной задачи.

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

После выполнения одной из приведенных выше команд вам будет предложено установить эти пакеты и некоторые другие зависимости. Нажмите клавишу Y, а затем клавишу ВВОД, чтобы продолжить, и следуйте остальным указаниям по установке административного пароля для MySQL. Это позволит установить минимальный набор расширений PHP, необходимый для использования PHP с MySQL. 

![][1]

Выполните следующую команду, чтобы увидеть другие расширения PHP, доступные как пакеты:

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>Создание документа info.php
Теперь можно проверить версию установленных компонентов Apache, MySQL и PHP из командной строки, введя `apache2 -v`, `mysql -v` или `php -v`.

Если вы хотите расширить тестирование, то можете создать страницу кратких сведений о PHP для просмотра в браузере. Создайте файл в текстовом редакторе Nano с помощью следующей команды.

```bash
sudo nano /var/www/html/info.php
```

В текстовом редакторе Nano GNU добавьте следующие строки.

```php
<?php
phpinfo();
?>
```

Сохраните файл и закройте текстовый редактор.

Перезапустите Apache с помощью следующей команды, чтобы все новые установленные компоненты вступили в действие.

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>Проверка успешности установки LAMP
Теперь можно просмотреть созданную страницу сведений о PHP. Откройте браузер и перейдите по адресу http://youruniqueDNS/info.php. Она должна выглядеть, как показано ниже.

![][2]

Можно проверить установленный экземпляр Apache, просмотрев страницу по умолчанию Apache2 Ubuntu. Для этого перейдите по ссылке http://youruniqueDNS/. Вы должны увидеть результат, аналогичный приведенному ниже.

![][3]

Поздравляем! Вы установили стек LAMP на виртуальную машину Azure.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с документацией Ubuntu по стеку LAMP.

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/deploy-lamp-stack/apachesuccesspage.png

