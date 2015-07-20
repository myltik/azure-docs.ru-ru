<properties
   pageTitle="Развертывание шаблона с помощью интерфейса командной строки Azure для Mac, Linux и Windows | Microsoft Azure"
   description="Описывает основные шаги для развертывания или обновления любого шаблона."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# Развертывание шаблона с помощью Azure CLI для Mac, Linux и Windows

## Установка curl, wget или другого средства скачивания
В этом разделе используется **curl**. Используйте диспетчер пакетов операционной системы или скачайте его [здесь](http://curl.haxx.se/download.html).

## Скачивание файла параметров шаблона (или шаблона, при необходимости)

Следующие шаги позволят вам развернуть один шаблон Azure, даже если он сложный. В этом разделе используется шаблон, который создает основной сервер Ubuntu с расширением customscript для виртуальной машины, которое установлено в качестве примера. Эти файлы включены в конец раздела для справки.

### Скачивание файла azuredeploy parameters.json

Скачайте файл azuredeploy parameters.json, если он существует для шаблона, который требуется развернуть.

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json

## Ввод данных для развертывания группы ресурсов

Откройте этот файл в предпочитаемом редакторе. Вы увидите, что необходимо указать значение для нескольких ключей, в частности для **adminUsername** и **adminPassword** (помните о правилах сложности!), а также имя учетной записи хранения и нужные DNS-имена.

    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-ru-ru-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }

Добавьте новые значения — Azure создаст новое хранилище и ресурсы DNS автоматически, если сможет — или используйте ресурсы, которые уже созданы. В следующем файле azuredeploy parameters.json приведен пример:




приведенный ниже URL-адрес извлекает файл параметров из «пустого» azuredeploy-parameters.json, что сработает, если используется интерактивный метод. Если используются скаченный файл с настраиваемыми параметрами, вместо этого потребуется использовать параметр --template-file<template-file>. Я также создал сценарии, которые извлекают отдельные разделы из любой части этих файлов, в зависимости от того, что требуется сделать. Следует упомянуть, что чтобы выполнить синтаксический анализ json, может потребоваться ввести jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq.


### Развертывание файлов шаблона и параметров


[AZURE.NOTE]Возможно, у некоторых шаблонов не окажется соответствующего файла azuredeploy-parameters.json,

параметров для установки, или они уже могут быть частью шаблона, в зависимости от того, какие шаблоны вы используете. В этих случаях можно сделать следующее.

Шаблон может непосредственно содержать параметры или вам может понадобиться извлечь эти параметры самостоятельно. Дополнительную информацию о структуре шаблонов см. в разделе [Язык шаблонов диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json (или просто файл azuredeploy parameters.json). Вы также можете извлечь раздел параметров шаблона — в этом случае необходимо сохранить его внутри самого шаблона ИЛИ в виде отдельного файла azuredeploy-parameters.json. Вам необходимо получить значения для параметров.

## Изменение файла azuredeploy templates.json

Получите значения, которые вам понадобятся.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Primis ipsum ante Vestibul в faucibus orci luctus et cubilia posuere ultrices Curae; Ultricies Nullam, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis: [Ссылка 3 на другие разделы документации на сайте azure.microsoft.com](../storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png
 

<!---HONumber=July15_HO2-->