---
title: "Развертывание вычислительных ресурсов с помощью шаблонов Azure Resource Manager | Документация Майкрософт"
description: "Руководство по .NET Core для виртуальных машин Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b026fe81-1bc1-4899-ac32-886091671498
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b746e4218ea505ea5e0a1579608e0ecfa10e87e7


---
# <a name="application-architecture-with-azure-resource-manager-templates"></a>Архитектура приложений с использованием шаблонов Azure Resource Manager
При разработке развертывания Azure Resource Manager необходимую вычислительную мощность нужно сопоставлять с ресурсами и службами Azure. Если приложение содержит несколько конечных точек http, базы данных и службы кэша, следует рационально распределить ресурсы Azure между всеми этими компонентами. Например, приложение "Магазин музыки" содержит веб-приложение, размещенное на виртуальной машине, и базу данных SQL на основе базы данных Azure SQL. 

В этом документе описана конфигурация вычислительных ресурсов для такого приложения, представленная в шаблоне Azure Resource Manager. Здесь будут описаны все зависимости и уникальные настройки. Чтобы оптимизировать процесс, заранее разверните экземпляр решения в подписке Azure, а затем установите шаблон Azure Resource Manager. Полный шаблон можно найти [здесь](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="virtual-machine"></a>Виртуальная машина
Приложение "Магазин музыки" — это веб-приложение, где пользователи могут искать и приобретать доступные композиции. Для размещения веб-приложений можно использовать несколько служб Azure. В нашем примере используется виртуальная машина. Используя предложенный шаблон магазина музыки, мы развернем виртуальную машину, установим веб-сервер, а затем установим и настроим веб-сайт самого магазина. В этой статье подробно описывается только развертывание виртуальной машины. Настройка веб-сервера и приложения подробно описана в следующей статье.

Чтобы добавить в шаблон виртуальную машину, можно использовать мастер добавления нового ресурса в Visual Studio или вставить в шаблон развертывания допустимый объект JSON. Для развертывания виртуальной машины также потребуются несколько связанных ресурсов. Если для создания шаблона вы используете Visual Studio, эти ресурсы создаются автоматически. При создании шаблона вручную вам нужно самостоятельно добавить и настроить эти ресурсы.

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager — [JSON для виртуальной машины](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L285).

```json
{
  {
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat(variables('vmName'),copyindex())]",
  "location": "[resourceGroup().location]",
  "copy": {
    "name": "virtualMachineLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "tags": {
    "displayName": "virtual-machine"
  },
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('vhdStorageName'))]",
    "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]",
    "nicLoop"
  ],
  "properties": {
    "availabilitySet": {
      "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('availabilitySetName'))]"
    },
  ........<truncated>  
}
```

После развертывания свойства виртуальной машины можно увидеть на портале Azure.

![Виртуальная машина](./media/virtual-machines-windows-dotnet-core/vm-win.png)

## <a name="storage-account"></a>Учетная запись хранения
Учетные записи хранения имеют множество возможностей и параметров хранения. В контексте виртуальных машин Azure учетная запись хранения содержит виртуальные жесткие диски виртуальной машины и любые дополнительные диски данных. В нашем примере для магазина музыки используется по одной учетной записи для хранения виртуального жесткого диска каждой виртуальной машины в развертывании. 

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager для настройки [учетной записи хранения](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L98).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('vhdStorageName')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "storage-account"
  },
  "properties": {
    "accountType": "[variables('vhdStorageType')]"
  }
}
```

Учетная запись хранения сопоставляется с виртуальной машиной в объявлении шаблона Resource Manager для виртуальной машины. 

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager для настройки [сопоставления виртуальной машины и учетной записи хранения](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L321).

```json
"osDisk": {
  "name": "osdisk",
  "vhd": {
    "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/',variables('vhdStorageName')), '2015-06-15').primaryEndpoints.blob,'vhds/osdisk', copyindex(), '.vhd')]"
  },
  "caching": "ReadWrite",
  "createOption": "FromImage"
}
```

После развертывания учетная запись хранения появится на портале Azure.

![Учетная запись хранения](./media/virtual-machines-windows-dotnet-core/storacct-win.png)

Щелкните контейнер BLOB-объектов учетной записи хранения, чтобы просмотреть файл виртуального жесткого диска для каждой виртуальной машины, развернутой с использованием этого шаблона.

![Виртуальные жесткие диски](./media/virtual-machines-windows-dotnet-core/vhd-win.png)

Дополнительные сведения о службе хранилища Azure см. в [документации по службе хранилища](https://azure.microsoft.com/documentation/services/storage/).

## <a name="virtual-network"></a>Виртуальная сеть
Если для виртуальной машины требуется обмен данными по внутренней сети (например, с другими виртуальными машинами и ресурсами Azure), нужно использовать виртуальную сеть Azure.  Виртуальная сеть не предоставляет доступ к виртуальной машине из Интернета. Для открытого доступа нужен общедоступный IP-адрес, который описывается в следующих статьях этой серии.

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager с настройками [виртуальной сети и подсети](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L126).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroup'))]"
  ],
  "tags": {
    "displayName": "virtual-network"
  },
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/24"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "10.0.0.0/24",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
          }
        }
      }
    ]
  }
}
```

На портале Azure виртуальная сеть будет выглядеть примерно так, как на следующем изображении. Обратите внимание, что все виртуальные машины, развернутые с использованием этого шаблона, присоединяются к виртуальной сети.

![Виртуальная сеть](./media/virtual-machines-windows-dotnet-core/vnet-win.png)

## <a name="network-interface"></a>Сетевой интерфейс
 Сетевой интерфейс соединяет виртуальную машину с виртуальной сетью, а именно с той подсетью, которая определена в виртуальной сети. 

 Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager с настройками [сетевого интерфейса](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L156).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/networkInterfaces",
  "name": "[concat(variables('networkInterfaceName'), copyindex())]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-interface"
  },
  "copy": {
    "name": "nicLoop",
    "count": "[parameters('numberOfInstances')]"
  },
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
    "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIpAddressName'))]",
    "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'), '/inboundNatRules/', 'RDP-VM', copyIndex())]"
  ],
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[variables('subnetRef')]"
          },
          "loadBalancerBackendAddressPools": [
            {
              "id": "[variables('lbPoolID')]"
            }
          ],
          "loadBalancerInboundNatRules": [
            {
              "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyIndex())]"
            }
          ]
        }
      }
    ]
  }
}
```

Каждый ресурс виртуальной машины включает в себя профиль сети. В этом профиле сетевой интерфейс сопоставляется с виртуальной машиной.  

Щелкните эту ссылку, чтобы увидеть пример JSON в шаблоне Resource Manager с настройками [профиля виртуальной машины](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L330).

```json
"networkProfile": {
  "networkInterfaces": [
    {
      "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('networkInterfaceName'), copyindex()))]"
    }
  ]
}
```

На портале Azure сетевой интерфейс будет выглядеть примерно так, как на следующем изображении. Внутренний IP-адрес и сопоставления виртуальной машины можно просмотреть с помощью ресурса сетевого интерфейса.

![Сетевой интерфейс](./media/virtual-machines-windows-dotnet-core/nic-win.png)

Дополнительные сведения см. в [документации по виртуальным сетям Azure](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="azure-sql-database"></a>База данных SQL Azure
Помимо виртуальной машины для размещения веб-сайта "Магазин музыки" вам также нужно развернуть базу данных SQL Azure для размещения базы данных этого магазина. Использование базы данных SQL Azure позволяет нам обойтись без дополнительного набора виртуальных машин, а также обеспечивает встроенную поддержку масштабируемости и доступности.

Чтобы добавить в шаблон базу данных SQL Azure, можно использовать мастер добавления нового ресурса в Visual Studio или вставить в шаблон развертывания допустимый объект JSON. Ресурс SQL Server включает имя пользователя и пароль, которые предоставляют права администратора на экземпляре SQL Server. Также добавляется ресурс брандмауэра SQL. По умолчанию к экземпляру SQL Server могут подключаться только приложения, размещенные в Azure. Чтобы разрешить подключение к экземпляру SQL Server для внешних приложений, например SQL Server Management Studio, следует настроить брандмауэр. В нашем примере с магазином можно использовать конфигурацию по умолчанию. 

Щелкните эту ссылку, чтобы просмотреть пример JSON в шаблоне Resource Manager для [базы данных Azure SQL](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L379).

```json
{
  "apiVersion": "2014-04-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "[variables('musicstoresqlName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "sql-music-store"
  },
  "properties": {
    "administratorLogin": "[parameters('adminUsername')]",
    "administratorLoginPassword": "[parameters('adminPassword')]"
  },
  "resources": [
    {
      "apiVersion": "2014-04-01-preview",
      "type": "firewallrules",
      "name": "firewall-allow-azure",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('musicstoresqlName'))]"
      ],
      "properties": {
        "startIpAddress": "0.0.0.0",
        "endIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Представление SQL Server и базы данных MusicStore в соответствии с отображением на портале Azure.

![SQL Server](./media/virtual-machines-windows-dotnet-core/sql-win.png)

Дополнительные сведения о развертывании базы данных SQL см. в [документации по базе данных SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/).

## <a name="next-step"></a>Дальнейшие действия
<hr>

[Шаг 2. Доступ и безопасность в шаблонах Azure Resource Manager](virtual-machines-windows-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


