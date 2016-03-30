<properties
   pageTitle="Пошаговое руководство по созданию шаблона Resource Manager | Microsoft Azure"
   description="Пошаговые инструкции по созданию шаблона Resource Manager, который подготавливает базовую архитектуру Azure IaaS."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/14/2016"
   ms.author="navale;tomfitz"/>
   
# Пошаговое руководство по созданию шаблона Resource Manager

В этой статье рассматриваются инструкции по созданию шаблона Resource Manager. Предполагается, что вы уже знакомы со службами Azure, которые хотите развернуть, но вы не знаете, как представить эту инфраструктуру в шаблоне. Вы создадите шаблон, основанный на [двух виртуальных машинах с балансировщиком нагрузки и шаблоне правил балансировщика нагрузки](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) из [коллекции быстрого запуска](https://github.com/Azure/azure-quickstart-templates). Эти же методы можно применять к любому создаваемому шаблону.

Рассмотрим общую архитектуру.

* Две виртуальные машины, использующие одну и ту же учетную запись хранения, которые находятся в одной группе доступности и одной подсети виртуальной сети.
* Одна сетевая карта и IP-адрес для каждой виртуальной машины.
* Балансировщик нагрузки с правилом балансировки нагрузки на порте 80.

![архитектура](./media/resource-group-overview/arm_arch.png)

Вы решили развернуть эту архитектуру в Azure и хотите использовать шаблоны Resource Manager, чтобы можно было легко повторно развернуть архитектуру в другое время. Но вы не знаете, как создать этот шаблон. Из этой статьи вы узнаете, что следует добавить в шаблон.

Для создания шаблона можно использовать любой редактор. В Visual Studio есть средства, которые упрощают разработку шаблонов, но для работы с этим руководством вам не требуется Visual Studio. Инструкции по созданию веб-приложений и развертыванию баз данных SQL с помощью Visual Studio см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Создание шаблона Resource Manager

Шаблон — это JSON-файл, в котором определены все развертываемые ресурсы. В нем также можно определять параметры, задаваемые во время развертывания, переменные, создаваемые из других значений и выражений, а также выходные данные развертывания.

Начнем с простейшего шаблона.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }

Сохраните этот код как файл **azuredeploy.json**.

Сначала мы сосредоточимся на разделе **resources**. О разделах **parameters** и **variables** мы поговорим позже в этой статье.

## Учетная запись хранения
Определите учетную запись хранения, которую будут использовать виртуальные машины. В разделе **resources** добавьте объект, который определяет учетную запись хранения, как показано ниже.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Вы, возможно, спросите, откуда взялись эти свойства и значения. Свойства **type**, **name**, **apiVersion** и **location** — это стандартные элементы, доступные для всех типов ресурсов. Информацию об общих элементах см. в разделе [Ресурсы](../resource-group-authoring-templates/#resources). Для свойства **name** укажите значение, передаваемое во время развертывания. Для свойства **location** укажите расположение, используемое группой ресурсов. Способ определения свойств **type** и **apiVersion** будет описан в следующих разделах.

Раздел **properties** содержит все свойства, которые являются уникальными для определенного типа ресурсов. Значения, задаваемые в этом разделе, точно повторяют операцию PUT в REST API для создания данного типа ресурсов. Во время создания учетной записи хранения необходимо указать свойство **accountType**. В статье о [REST API для создания учетной записи хранения](https://msdn.microsoft.com/library/azure/mt163564.aspx) обратите внимание, что раздел свойств операции REST также содержит свойство **accountType**. Его допустимые значения подробно задокументированы. В этом примере тип учетной записи имеет значение **Standard\_LRS**. Вы можете указать другое значение или же разрешить пользователям передавать тип учетной записи в качестве параметра.

## Группа доступности
Определив учетную запись хранения, добавьте группу доступности для виртуальных машин. В этом случае дополнительные свойства не требуются, поэтому определить группу доступности очень просто. Если вы хотите определить значения счетчиков для доменов обновления и сбоя, изучите полный раздел свойств в статье о [REST API для создания группы доступности](https://msdn.microsoft.com/library/azure/mt163607.aspx).

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Обратите внимание, что свойству **name** присвоено значение переменной. Для этого шаблона имя группы доступности нужно указать в нескольких местах. Вам будет легче работать с шаблоном, если вы определите это значение один раз и будете использовать его в нескольких местах.

Значение, указываемое для свойства **type**, определяет поставщика ресурсов и их тип. Для групп доступности поставщик ресурсов — это **Microsoft.Compute**, а тип ресурсов — **availabilitySets**. Чтобы получить список доступных поставщиков ресурсов, выполните следующую команду PowerShell:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

Если вы используете Azure CLI, выполните следующую команду:

    azure provider list

Так как вы создаете учетные записи хранения, виртуальные машины и виртуальные сети, вы будете использовать следующие поставщики ресурсов:

- Microsoft.Storage;
- Microsoft.Compute;
- Microsoft.Network.

Чтобы просмотреть типы ресурсов для конкретного поставщика, выполните следующую команду PowerShell:

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes

Если вы используете Azure CLI, приведенная ниже команда возвращает доступные типы в формате JSON и сохраняет результаты в файл.

    azure provider show Microsoft.Compute --json > c:\temp.json

Свойство **availabilitySets** должно отображаться как один из типов в **Microsoft.Compute**. Полное имя типа — **Microsoft.Compute/availabilitySets**. Определить имя типа ресурсов можно для всех ресурсов в шаблоне.

## Общедоступный IP-адрес
Определите общедоступный IP-адрес. Настраиваемые свойства см. в статье о [REST API для общедоступных IP-адресов](https://msdn.microsoft.com/library/azure/mt163590.aspx).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

Для метода распределения установлено значение **Dynamic**, но вы можете задать любое другое значение или настроить это свойство так, чтобы оно принимало значение параметра. Вы разрешили пользователям шаблона передавать в него значение метки доменного имени.

Теперь рассмотрим, как определить свойство **apiVersion**. Указываемое значение должно совпадать с версией REST API, которую вы хотите использовать при создании ресурса. Найти нужный тип ресурса можно в документации по REST API. Кроме того, для определенного типа можно выполнить следующую команду PowerShell:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions

Она возвратит следующие значения:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Для просмотра версий API с помощью Azure CLI выполните приведенную ранее команду **azure provider show**.

При создании нового шаблона выбирайте последнюю версию API.

## Виртуальная сеть и подсеть
Создайте виртуальную сеть с одной подсетью. Настраиваемые свойства см. в статье о [REST API для виртуальных сетей](https://msdn.microsoft.com/library/azure/mt163661.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## Балансировщик нагрузки
Теперь необходимо создать внешний балансировщик нагрузки. Так как этот балансировщик использует общедоступный IP-адрес, в разделе **dependsOn** необходимо объявить зависимость от общедоступного IP-адреса. Это означает, что балансировщик нагрузки не будет развернут, пока не завершится развертывание общедоступного IP-адреса. Если вы не определите эту зависимость, возникнет ошибка, так как Resource Manager попытается развернуть ресурсы одновременно. Он будет пытаться назначить балансировщику нагрузки общедоступный IP-адрес, который еще не существует.

В этом определении ресурса вам также нужно создать пул адресов серверной части, несколько правил NAT для входящих подключений к виртуальным машинам с помощью протокола удаленного рабочего стола и правило балансировки нагрузки с TCP-зондом на порте 80. Все свойства см. в документации по [REST API для балансировщика нагрузки](https://msdn.microsoft.com/library/azure/mt163574.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## Сетевой интерфейс
Вам нужно создать два сетевых интерфейса — по одному для каждой виртуальной машины. Вместо того, чтобы включать повторяющиеся записи для сетевых интерфейсов, вы можете использовать [функцию copyIndex()](resource-group-create-multiple.md) для итерации цикла копирования (называется nicLoop) и создания количества сетевых интерфейсов, определенного в переменных `numberOfInstances`. Сетевой интерфейс зависит от создания виртуальной сети и балансировщика нагрузки. Он использует подсеть, определенную во время создания виртуальной сети, и идентификатор балансировщика нагрузки для настройки пула адресов балансировщика нагрузки и правил NAT для входящих подключений. Все свойства см. в документации по [REST API для сетевых интерфейсов](https://msdn.microsoft.com/library/azure/mt163668.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## Виртуальная машина
Вы создадите две виртуальные машины с помощью функции copyIndex(), как вы делали это в разделе [Сетевые интерфейсы](#network-interface). Создание виртуальной машины зависит от учетной записи хранения, сетевого интерфейса и группы доступности. Эта виртуальная машина будет создана из образа из магазина. В свойстве `storageProfile` определено, что `imageReference` используется для определения издателя образа, предложения, SKU и версии. В конце настраивается профиль диагностики для включения диагностирования виртуальной машины.

Соответствующие свойства для образа из магазина см. в статье [Просмотр и выбор образов виртуальных машин Azure с помощью оболочки Windows PowerShell и инфраструктуры Azure CLI](./virtual-machines/resource-groups-vm-searching.md). Для образов, опубликованных сторонними поставщиками, необходимо указать другое свойство с именем `plan`. Пример можно найти в [этом шаблоне](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) из коллекции быстрого запуска.


```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

Ресурсы шаблона теперь определены.

## Параметры

В разделе параметров определите значения, которые могут быть указаны во время развертывания шаблона. Определите только те значения, которые, по вашему мнению, должны изменяться во время развертывания. Вы можете указать для параметра значение по умолчанию. Оно будет использоваться в том случае, если во время развертывания значение не указано. Вы также можете определить допустимые значения, как показано для параметра **imageSKU**.

```
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## Переменные

В разделе переменных можно определить значения, которые используются в нескольких местах в шаблоне или создаются из других выражений или переменных. Переменные часто используются для упрощения синтаксиса в шаблоне.

```
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```



## Дальнейшие действия

Вы завершили создание шаблона, и он готов к развертыванию.

- Дополнительную информацию о структуре шаблона см. в статье о [создании шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
- Инструкции по развертыванию шаблонов см. в статье о [развертывании группы ресурсов с помощью Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->