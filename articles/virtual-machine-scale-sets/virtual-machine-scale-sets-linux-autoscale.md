---
title: "Автомасштабирование масштабируемых наборов виртуальных машин Linux | Документация Майкрософт"
description: "Настройка масштабирования для набора масштабирования виртуальных машин Linux с помощью интерфейса командной строки Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 3c00742d9686b986841d676872cc93b58827a8e8
ms.openlocfilehash: b87b507118244f0f275f54cd65cea13a92cdaec6


---
# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Автоматическое масштабирование машин Linux в наборе масштабирования виртуальных машин
Наборы масштабирования виртуальных машин позволяют легко развертывать идентичные виртуальные машины (ВМ) в виде набора и управлять ими. Масштабируемые наборы обеспечивают высокую степень масштабируемости и персонализацию уровня вычислений для гипермасштабируемых приложений. Кроме того, они поддерживают образы платформ Windows и Linux, а также пользовательские образы и расширения. Дополнительные сведения см. в статье [Обзор наборов масштабирования виртуальных машин](virtual-machine-scale-sets-overview.md).

В этом руководстве объясняется, как создать набор масштабирования виртуальных машин Linux с помощью последней версии Ubuntu Linux. Также в руководстве показано, как автоматически масштабировать виртуальные машины в наборе. Чтобы создать набор и настроить масштабирование, нужно создать шаблон Azure Resource Manager и развернуть его с помощью интерфейса командной строки Azure. Дополнительную информацию о шаблонах см. в статье [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md). Дополнительные сведения об автоматическом масштабировании наборов масштабирования см. в статье [Автоматическое масштабирование и наборы масштабирования виртуальных машин](virtual-machine-scale-sets-autoscale-overview.md).

Следуя инструкциям этого руководства, вы развернете следующие ресурсы и расширения:

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

Перед началом выполнения действий в этом учебнике [установите Azure CLI](../xplat-cli-install.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Шаг 1. Создание группы ресурсов и учетной записи хранения

1. **Войдите в Microsoft Azure**.  
В интерфейсе командной строки (оболочка Bash, терминал, командная строка) переключитесь в режим Resource Manager и [войдите с использованием рабочего или учебного идентификатора](../xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login). Следуйте указаниям интерактивного интерфейса входа в учетную запись Azure.

    ```cli   
    azure config mode arm

    azure login
    ```
   
    > [!NOTE]
    > Если у вас есть рабочий или учебный идентификатор, для которого не включена двухфакторная проверка подлинности, используйте его в сочетании с `azure login -u` для входа без интерактивного интерфейса. Если у вас нет рабочего или учебного идентификатора, его можно [создать в личной учетной записи Майкрософт](../virtual-machines/virtual-machines-linux-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    
2. **Создайте группу ресурсов**.  
Все ресурсы должны развертываться в группе ресурсов. В нашем примере группа ресурсов будет называться **vmsstest1**.
   
    ```cli
    azure group create vmsstestrg1 centralus
    ```

3. **Разверните учетную запись хранения в новой группе ресурсов**.  
В этой учетной записи хранения хранится шаблон. Создайте учетную запись хранения с именем **vmsstestsa**.
   
    ```cli
    azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa
    ```

## <a name="step-2-create-the-template"></a>Шаг 2. Создание шаблона
С помощью шаблона диспетчера ресурсов Azure можно развертывать ресурсы Azure и управлять ими совокупно, используя JSON-описание ресурсов и связанные параметры развертывания.

1. В любом текстовом редакторе создайте файл VMSSTemplate.json и добавьте начальную структуру JSON для поддержки шаблона.

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
   * Количество экземпляров виртуальных машин, которое требуется создать сначала в масштабируемом наборе.
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
    "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```

   * DNS-имена, которые используются сетевыми интерфейсами.
   * Имена и префиксы IP-адресов для виртуальной сети и подсетей.
   * Имена и идентификаторы виртуальной сети, балансировщик нагрузки и сетевые интерфейсы.
   * Имена учетных записей хранения, связанных с машинами в масштабируемом наборе.
   * Параметры для расширения системы диагностики, которая установлена на виртуальных машинах. Дополнительную информацию о расширении системы диагностики см. в статье [Создание виртуальной машины Windows с мониторингом и диагностикой с помощью шаблона Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Добавьте ресурс учетной записи хранения в родительский элемент ресурсов, добавленный в шаблон. В этом шаблоне используется цикл для создания пяти рекомендуемых учетных записей хранения, в которых будут храниться диски операционной системы и диагностические данные. Этот набор учетных записей может поддерживать до 100 виртуальных машин в масштабируемом наборе (это максимальное количество на данный момент). Каждой учетной записи хранения присваивается буквенный код, который определен в переменных в сочетании с суффиксом, указанным в параметрах шаблона.
   
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
                "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
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
              "backendPort": 22
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
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "14.04.4-LTS",
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
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "Canonical",
              "offer": "UbuntuServer",
              "sku": "14.04.4-LTS",
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
                "name":"LinuxDiagnostic",
                "properties": {
                  "publisher":"Microsoft.OSTCExtensions",
                  "type":"LinuxDiagnostic",
                  "typeHandlerVersion":"2.1",
                  "autoUpgradeMinorVersion":false,
                  "settings": {
                    "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint":"https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Добавьте ресурс autoscaleSettings, который определяет, как масштабируемый набор изменяется в зависимости от использования процессоров на машинах в наборе.

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
                  "metricName": "\\Processor\\PercentProcessorTime",
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
    Значение этого параметра соответствует счетчику производительности, который мы определили в переменной wadperfcounter. Используя эту переменную, расширение диагностики собирает данные счетчика **Processor\PercentProcessorTime**.
    
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
    Это значение активирует действие масштабирования. В этом шаблоне машины добавляются в масштабируемый набор, когда среднее использование ЦП на машинах в наборе превышает 50 %.
    
    * **direction**  
    Этот параметр определяет действие, которое запускается при достижении порогового значения. Допустимые значения: Increase (Увеличить) или Decrease (Уменьшить). В этом шаблоне количество виртуальных машин в масштабируемом наборе увеличивается, если пороговое значение превышает 50 % за определенный период.

    * **type**  
    В этом параметре указывается тип действия, которое должно выполняться. Для этого параметра должно быть установлено значение ChangeCount.
    
    * **значение**  
    В этом параметре указывается количество виртуальных машин, которые добавляются в масштабируемый набор или удаляются из него. Для этого параметра должно быть указано значение не меньше 1. Значение по умолчанию — 1. В этом шаблоне количество машин в масштабируемом наборе увеличивается на 1 при достижении порогового значения.

    * **cooldown**  
    В этом параметре указывается время ожидания с момента выполнения последнего действия масштабирования до начала следующего. Допустимые значения: от одной минуты до одной недели.

12. Сохраните файл шаблона.    

## <a name="step-3-upload-the-template-to-storage"></a>Шаг 3. Отправка шаблона в хранилище
Шаблон можно отправить, если вы знаете имя и первичный ключ учетной записи хранения, созданной при выполнении шага 1.

1. В интерфейсе командной строки (Bash, терминал, командная строка) выполните следующие команды, чтобы задать переменные среды, необходимые для доступа к учетной записи хранения:

    ```cli   
    export AZURE_STORAGE_ACCOUNT={account_name}
    export AZURE_STORAGE_ACCESS_KEY={key}
    ```
    
    Чтобы получить этот ключ, щелкните значок ключа в представлении ресурсов учетной записи хранения на портале Azure. Если вы используете командную строку Windows, введите **set** вместо export.

2. Создайте контейнер для хранения шаблона.
   
    ```cli
    azure storage container create -p Blob templates
    ```

3. Отправьте файл шаблона в новый контейнер.
   
    ```cli
    azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json
    ```

## <a name="step-4-deploy-the-template"></a>Шаг 4. Развертывание шаблона
После создания шаблона можно начать развертывать ресурсы. Чтобы запустить процесс, используйте следующую команду:

```cli
azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1
```

Когда вы нажмете клавишу ВВОД, появится запрос на ввод переменных, которые вы назначили. Укажите следующие значения:

```cli
vmName: vmsstestvm1
vmSSName: vmsstest1
instanceCount: 5
adminUserName: vmadmin1
adminPassword: VMpass1
resourcePrefix: vmsstest
```

Для успешного развертывания всех ресурсов может потребоваться приблизительно 15 минут.

> [!NOTE]
> Развернуть ресурсы можно также с помощью портала. Используйте эту ссылку: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>.


## <a name="step-5-monitor-resources"></a>Шаг 5. Мониторинг ресурсов
Сведения о масштабируемых наборах виртуальных машин можно получать, используя следующие методы:

* Портал Azure — с помощью портала в настоящее время можно получить ограниченный объем сведений.

* [Обозреватель ресурсов Azure](https://resources.azure.com/). Это лучший инструмент для просмотра текущего состояния набора масштабирования. Используйте этот путь, чтобы увидеть представление экземпляра созданного масштабируемого набора:
  
    ```cli
    subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines
    ```

* Azure CLI — используйте эту команду, чтобы получить сведения:

    ```cli  
    azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1
    ```

* Подключитесь к основной виртуальной машине так же, как к любому другому компьютеру. Теперь вы сможете удаленно подключаться к виртуальным машинам в масштабируемом наборе, чтобы отслеживать отдельные процессы.

> [!NOTE]
> Полный REST API для получения информации о масштабируемых наборах можно найти на странице [Масштабируемые наборы виртуальных машин](https://msdn.microsoft.com/library/mt589023.aspx).

## <a name="step-6-remove-the-resources"></a>Шаг 6. Удаление ресурсов
Так как за использование ресурсов Azure взимается плата, рекомендуется всегда удалять ресурсы, которые больше не нужны. Не нужно отдельно удалять каждый ресурс из группы ресурсов. Вы можете удалить группу ресурсов, и все ее ресурсы будут автоматически удалены.

```cli
azure group delete vmsstestrg1
```

## <a name="next-steps"></a>Дальнейшие действия
* Просмотрите примеры функций мониторинга Azure Monitor в статье [Шаблоны для быстрого начала работы с межплатформенным интерфейсом командной строки Azure Monitor](../monitoring-and-diagnostics/insights-cli-samples.md).
* Узнайте о возможностях уведомлений в статье [Использование действий автомасштабирования для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
* Узнайте, как [использовать журналы аудита для отправки электронной почты и уведомлений об оповещениях веб-перехватчика в Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
* Ознакомьтесь с шаблоном [автоматического масштабирования набора масштабирования виртуальных машин, на которых работают приложения Ubuntu, Apache и PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) , который настраивает стек LAMP для автоматического масштабирования наборов масштабирования виртуальных машин.




<!--HONumber=Feb17_HO2-->


