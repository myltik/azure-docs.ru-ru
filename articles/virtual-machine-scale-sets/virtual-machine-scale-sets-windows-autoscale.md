---
title: "Автомасштабирование масштабируемых наборов виртуальных машин Windows | Документация Майкрософт"
description: "Настройка автоматического масштабирования набора масштабирования виртуальных машин Windows с помощью Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4fa0cdc2ee71d4e499dfc45bac7b690d728b626a
ms.openlocfilehash: 2649edd4af67e25ce49bf0ced858b7c7e4633290
ms.lasthandoff: 02/09/2017


---
# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Автоматическое масштабирование ВМ в наборе масштабирования ВМ
Наборы масштабирования виртуальных машин позволяют легко развертывать идентичные виртуальные машины (ВМ) в виде набора и управлять ими. Масштабируемые наборы обеспечивают высокую степень масштабируемости и персонализацию уровня вычислений для гипермасштабируемых приложений. Кроме того, они поддерживают образы платформ Windows и Linux, а также пользовательские образы и расширения. Дополнительные сведения о наборах масштабирования см. в разделе [Наборы масштабирования виртуальных машин](virtual-machine-scale-sets-overview.md).

В этом руководстве объясняется, как создать набор масштабирования виртуальных машин Windows и настроить их автоматическое масштабирование. Чтобы создать набор и настроить масштабирование, нужно создать шаблон Azure Resource Manager и развернуть его с помощью Azure PowerShell. Дополнительную информацию о шаблонах см. в статье [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md). Дополнительные сведения об автоматическом масштабировании наборов масштабирования см. в статье [Автоматическое масштабирование и наборы масштабирования виртуальных машин](virtual-machine-scale-sets-autoscale-overview.md).

Следуя инструкциям из этой статьи, вы развернете следующие ресурсы и расширения:

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets;
* Microsoft.Insights.VMDiagnosticsSettings;
* Microsoft.Insights/autoscaleSettings.

Дополнительные сведения о ресурсах Resource Manager см. в статье [Azure Resource Manager vs. classic deployment](../azure-resource-manager/resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание).

## <a name="step-1-install-azure-powershell"></a>Шаг 1. Установка Azure PowerShell
Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в Azure см. в статье [Как установить и настроить Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Шаг 2. Создание группы ресурсов и учетной записи хранения
1. **Создайте группу ресурсов**. Все ресурсы должны быть развернуты в группе ресурсов. Чтобы создать группу ресурсов с именем **vmsstestrg1**, выполните командлет [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx).
2. **Создайте учетную запись хранения**. Это учетная запись, в которой будет храниться шаблон. Используйте командлет [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx), чтобы создать учетную запись хранения с именем **vmsstestsa**.

## <a name="step-3-create-the-template"></a>Шаг 3. Создание шаблона
С помощью шаблона диспетчера ресурсов Azure можно развертывать ресурсы Azure и управлять ими совокупно, используя JSON-описание ресурсов и связанные параметры развертывания.

1. В любом текстовом редакторе создайте файл C:\VMSSTemplate.json и добавьте начальную структуру JSON для поддержки шаблона.

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. Параметры требуются не всегда, но при развертывании шаблона с их помощью можно передавать значения. Добавьте указанные ниже параметры в родительский элемент параметров, добавленный в шаблон.

    ```json   
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
    * Имя отдельной виртуальной машины, которая используется для доступа к машинам в наборе масштабирования.
    * Имя учетной записи хранения, в которой хранится шаблон.
    * Количество виртуальных машин, которое требуется изначально создать в наборе масштабирования.
    * Имя и пароль учетной записи администратора на виртуальных машинах.
    * Префикс имени для ресурсов, создаваемых для поддержки набора масштабирования.

3. Переменные в шаблоне можно использовать для указания значений, которые могут часто изменяться, или значений, которые должны быть созданы из сочетания значений параметров. Добавьте указанные ниже переменные в родительский элемент переменных, добавленный в шаблон.

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
      "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```
   
    * DNS-имена, которые используются сетевыми интерфейсами.

        * Имена и префиксы IP-адресов для виртуальной сети и подсетей.
        * Имена и идентификаторы виртуальной сети, балансировщик нагрузки и сетевые интерфейсы.
        * Имена учетных записей хранения, связанных с машинами в масштабируемом наборе.
        * Параметры для расширения системы диагностики, которая установлена на виртуальных машинах. Дополнительную информацию о расширении системы диагностики см. в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с помощью шаблона Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Добавьте ресурс учетной записи хранения в родительский элемент ресурсов, добавленный в шаблон. В этом шаблоне используется цикл для создания пяти рекомендуемых учетных записей хранения, в которых будут храниться диски операционной системы и диагностические данные. Этот набор учетных записей может поддерживать до 100 виртуальных машин в масштабируемом наборе (это максимальное количество на данный момент). Каждой учетной записи хранения присваивается буквенный код, который определен в переменных в сочетании с префиксом, указанным в параметрах шаблона.

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
      "apiVersion": "2015-06-15",
      "copy": {
        "name": "storageLoop",
        "count": 5
      },
      "location": "[resourceGroup().location]",
      "properties": { "accountType": "Standard_LRS" }
    },
    ```

5. Добавьте ресурс виртуальной сети. Дополнительную информацию см. в статье [Поставщик сетевых ресурсов](../virtual-network/resource-groups-networking.md).

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. Добавьте ресурсы общедоступных IP-адресов, используемые балансировщиком нагрузки и сетевым интерфейсом.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. Добавьте ресурс балансировщика нагрузки, используемый масштабируемым набором. Дополнительные сведения см. в статье, посвященной [поддержке Azure Resource Manager для подсистемы балансировки нагрузки](../load-balancer/load-balancer-arm.md).

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 3389
            }
          }
        ]
      }
    },
    ```

8. Добавьте ресурс сетевого интерфейса, используемый отдельной виртуальной машиной. К виртуальным машинам из набора масштабирования нельзя получить доступ через общедоступный IP-адрес, поэтому для удаленного доступа к ним в той же виртуальной сети создается отдельная виртуальная машина.

    ```json  
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. Добавьте отдельную виртуальную машину в ту же сеть, в которой находится набор масштабирования.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"        
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    ```

10. Добавьте ресурс набора масштабирования ВМ и укажите расширение диагностики, устанавливаемое на все виртуальные машины из набора масштабирования. Многие параметры данного ресурса похожи на ресурс виртуальной машины. Основные отличия заключаются в элементе capacity, который указывает количество виртуальных машин в наборе масштабирования, и элементе upgradePolicy, который указывает способ выполнения обновлений на виртуальных машинах. Набор масштабирования не будет создан, пока не будут созданы все учетные записи хранения, указанные в элементе dependsOn.

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Insights.VMDiagnosticsSettings",
                "properties": {
                  "publisher": "Microsoft.Azure.Diagnostics",
                  "type": "IaaSDiagnostics",
                  "typeHandlerVersion": "1.5",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint": "https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Добавьте ресурс autoscaleSettings, который определяет, как набор масштабирования изменяется в зависимости от использования процессоров на машинах из набора.

    ```json
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Processor(_Total)\\% Processor Time",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```

    Для целей этого руководства важны значения следующих параметров:
    
    * **metricName**  
    Значение этого параметра соответствует счетчику производительности, который мы определили в переменной wadperfcounter. С помощью этой переменной расширение диагностики собирает данные счетчика **Processor(_Total)\%% Processor Time**.
    
    * **metricResourceUri**  
    В этом параметре указывается идентификатор ресурса масштабируемого набора виртуальных машин.
    
    * **timegrain**  
    В этом параметре указывается степень детализации собираемых метрик. В нашем шаблоне значение этого параметра равно&1; минуте.
    
    * **statistic**  
    Этот параметр определяет, как объединяются метрики для автоматического масштабирования. Возможные значения: Average (Среднее), Min (Минимальное), Max (Максимальное). В этом шаблоне выполняется сбор данных о среднем общем использовании ЦП на виртуальных машинах.
    
    * **timeWindow**  
    В этом параметре указывается интервал, в пределах которого собираются данные об экземплярах. Значение должно составлять от 5 минут до 12 часов.
    
    * **timeAggregation**  
    Этот параметр определяет способ объединения собранных данных с течением времени. Значение по умолчанию — Average (Среднее). Возможные значения: Average (Среднее), Minimum (Минимальное), Maximum (Максимальное), Last (Последнее), Total (Всего), Count (Количество).
    
    * **operator**  
    Оператор, который используется для сравнения данных метрики с пороговым значением. Возможные значения: Equals (Равно), NotEquals (Не равно), GreaterThan (Больше), GreaterThanOrEqual (Больше или равно), LessThan (Меньше), LessThanOrEqual (Меньше или равно).
    
    * **threshold**  
    Значение этого параметра активирует действие масштабирования. В этом шаблоне машины добавляются в масштабируемый набор, когда среднее использование ЦП на машинах в наборе превышает 50 %.
    
    * **direction**  
    Этот параметр определяет действие, которое запускается при достижении порогового значения. Допустимые значения: Increase (Увеличить) или Decrease (Уменьшить). В этом шаблоне количество виртуальных машин в масштабируемом наборе увеличивается, если пороговое значение превышает 50 % за определенный период.
    
    * **type**  
    В этом параметре указывается тип действия, которое должно выполняться. Для этого параметра должно быть установлено значение ChangeCount.
    
    * **значение**  
    В этом параметре указывается количество виртуальных машин, которые добавляются в масштабируемый набор или удаляются из него. Для этого параметра должно быть указано значение не меньше 1. Значение по умолчанию — 1. В этом шаблоне количество машин в масштабируемом наборе увеличивается на 1 при достижении порогового значения.
    
    * **cooldown**  
    В этом параметре указывается время ожидания с момента выполнения последнего действия масштабирования до начала следующего. Допустимые значения: от одной минуты до одной недели.

12. Сохраните файл шаблона.    

## <a name="step-4-upload-the-template-to-storage"></a>Шаг 4. Отправка шаблона в хранилище
Шаблон можно отправить, если вы знаете имя и первичный ключ учетной записи хранения, созданной при выполнении шага 1.

1. В окне Microsoft Azure PowerShell укажите переменную, которая означает имя учетной записи хранения, созданной при выполнении шага 1.
   
    ```powershell
    $storageAccountName = "vmstestsa"
    ```

2. Укажите переменную, которая означает первичный ключ учетной записи хранения.
   
    ```powershell
    $storageAccountKey = "<primary-account-key>"
    ```
   
   Чтобы получить этот ключ, щелкните значок ключа в представлении ресурсов учетной записи хранения на портале Azure.
3. Создайте объект контекста учетной записи хранения, который используется для проверки операций с учетной записью хранения.
   
    ```powershell
    $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    ```

4. Создайте контейнер для хранения шаблона.

    ```powershell
    $containerName = "templates"
    New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob
    ```

5. Отправьте файл шаблона в новый контейнер.

    ```powershell   
    $blobName = "VMSSTemplate.json"
    $fileName = "C:\" + $BlobName
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx
    ```

## <a name="step-5-deploy-the-template"></a>Шаг 5. Развертывание шаблона
После создания шаблона можно начать развертывать ресурсы. Чтобы запустить процесс, используйте следующую команду:

```powershell
New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"
```

Когда вы нажмете клавишу ВВОД, появится запрос на ввод переменных, которые вы назначили. Укажите следующие значения:

```powershell
vmName: vmsstestvm1
  vmSSName: vmsstest1
  instanceCount: 5
  adminUserName: vmadmin1
  adminPassword: VMpass1
  resourcePrefix: vmsstest
```

Для успешного развертывания всех ресурсов может потребоваться приблизительно 15 минут.

> [!NOTE]
> Развернуть ресурсы можно также с помощью портала. Используйте эту ссылку: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>
> 
> 

## <a name="step-6-monitor-resources"></a>Шаг 6. Мониторинг ресурсов
Сведения о масштабируемых наборах виртуальных машин можно получать, используя следующие методы:

* Портал Azure — с помощью портала в настоящее время можно получить ограниченный объем сведений.
* [Обозреватель ресурсов Azure](https://resources.azure.com/). Это лучший инструмент для просмотра текущего состояния набора масштабирования. Используйте этот путь, чтобы увидеть представление экземпляра созданного масштабируемого набора:
  
    subscriptions > {ваша подписка} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

* Azure PowerShell — используйте эту команду, чтобы получить сведения:

  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  ```
  
  Или
  
  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView
  ```

* Подключитесь к отдельной виртуальной машине так же, как к любому другому компьютеру. Теперь вы сможете удаленно подключаться к виртуальным машинам в наборе масштабирования, чтобы отслеживать отдельные процессы.

> [!NOTE]
> Полный REST API для получения информации о масштабируемых наборах можно найти на странице [Масштабируемые наборы виртуальных машин](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Шаг 7. Удаление ресурсов
Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Вы можете удалить группу ресурсов, и все ее ресурсы будут автоматически удалены.

  ```powershell
  Remove-AzureRmResourceGroup -Name vmsstestrg1
  ```

Если вы хотите сохранить группу ресурсов, вы можете удалить только масштабируемый набор.

  ```powershell
  Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"
  ```

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об управлении созданным набором масштабирования можно найти в статье [Управление виртуальными машинами в наборе масштабирования виртуальных машин](virtual-machine-scale-sets-windows-manage.md).
* Дополнительные сведения о вертикальном масштабировании см. в статье [Вертикальное автомасштабирование наборов масштабирования виртуальных машин](virtual-machine-scale-sets-vertical-scale-reprovision.md).
* Просмотрите примеры функций мониторинга Azure Monitor в статье [Примеры для быстрого запуска Azure Insights с помощью PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md).
* Узнайте о возможностях уведомлений в статье [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
* Узнайте, как [использовать журналы аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).


