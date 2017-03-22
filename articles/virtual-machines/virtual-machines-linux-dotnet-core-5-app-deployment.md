---
title: "Автоматизация развертывания приложений с помощью расширений виртуальной машины | Документация Майкрософт"
description: "Руководство по .NET Core для виртуальных машин Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 9fc8b1ba-60f5-410b-8190-9f1ff885e50e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 031943c4d7b7736d249b507e2e59f0cc9896a54e
ms.lasthandoff: 03/03/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-linux-vms"></a>Развертывание приложений с использованием шаблонов Azure Resource Manager для виртуальных машин Linux

Как только все инфраструктурные требования Azure будут определены и на их основе будет создан шаблон развертывания, можно приступать фактическому развертыванию приложения. Развертывание приложения в данном случае сводится к установке фактических двоичных файлов приложения в ресурсы Azure. Чтобы развернуть пример приложения магазина музыки, на каждой виртуальной машине необходимо установить и настроить .NET Core, NGINX и Supervisor. Вам потребуется установить двоичные файлы магазина музыки на виртуальную машину и предварительно создать базу данных музыкального магазина.

В этом документе описана автоматизация развертывания и настройки приложений на виртуальных машинах Azure с помощью расширений виртуальных машин. Здесь будут описаны все зависимости и уникальные настройки. Чтобы оптимизировать процесс, заранее разверните экземпляр решения в подписке Azure, а затем установите шаблон Azure Resource Manager. Полный шаблон можно найти [здесь](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Скрипт настройки
Расширения виртуальных машин — это специальные программы, которые выполняются на виртуальных машинах для автоматизации настройки. Расширения используются в различных целях, например для автоматизации антивирусных программ, настройки ведения журнала и конфигурации Docker. Расширение пользовательских скриптов можно использовать для запуска любого скрипта на виртуальной машине. В примере магазина музыки расширение пользовательских скриптов используется для настройки виртуальных машинах Ubuntu и установки приложения магазина музыки. 

Прежде чем перейти к подробному описанию того, как расширения виртуальных машин объявляются в шаблоне Azure Resource Manager, изучите выполняющийся скрипт. Он настраивает виртуальную машину Ubuntu для размещения приложения магазина музыки. Во время выполнения скрипт устанавливает все необходимое программное обеспечение, устанавливает приложение магазина музыки из системы управления версиями и выполняет подготовку базы данных. 

Дополнительные сведения о размещении приложения .NET Core на платформе Linux см. в статье [Публикация в рабочей среде Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html).

> Этот пример предназначен только для демонстрации.
> 
> 

```bash
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Расширение скриптов виртуальной машины
Расширения ВМ можно запустить на виртуальной машине во время сборки, включив ресурс расширения в шаблон Azure Resource Manager. Чтобы добавить расширение, можно использовать мастер добавления ресурсов в Visual Studio или вставить в шаблон развертывания правильно определенный объект JSON. Ресурс расширения скрипта вложен в ресурс виртуальной машины. Это можно увидеть в следующем примере.

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager: [расширение скрипта виртуальной машины](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Обратите внимание, что в объекте JSON, приведенном ниже, скрипт хранится в GitHub. Этот скрипт также может храниться в хранилище BLOB-объектов Azure. Кроме того, шаблоны Azure Resource Manager позволяют составить строку выполнения скрипта таким образом, чтобы значения параметров шаблона можно было использовать в качестве параметров для выполнения скрипта. В этом случае данные указываются при развертывании шаблонов, и эти значения затем можно использовать при выполнении скрипта.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Дополнительные сведения об использовании пользовательского расширения сценария см. в статье [Использование расширения пользовательских сценариев для виртуальных машин Linux с шаблонами Azure Resource Manager](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-step"></a>Дальнейшее действие
<hr>

[Ознакомьтесь с другими шаблонами Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)


