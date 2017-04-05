---
title: "Изучение шаблонов масштабируемых наборов виртуальных машин Azure | Документация Майкрософт"
description: "Научитесь создавать шаблон минимально приемлемого масштабируемого набора виртуальных машин Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 2a8b770521e67ff9a621ad9fb2fa97e3f9462e31
ms.lasthandoff: 03/24/2017

---

# <a name="learn-about-virtual-machine-scale-set-templates"></a>Подробнее о шаблонах масштабируемых наборов виртуальных машин

[Шаблоны Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) прекрасно подходят для развертывания группы связанных ресурсов. В этой серии руководств показано, как создать шаблон минимального приемлемого масштабируемого набора и изменить его в соответствии с различными сценариями. Все примеры взяты из этого [репозитория GitHub](https://github.com/gatneil/mvss). Этот шаблон должен быть простым. Полные примеры шаблонов масштабируемых наборов см. в [репозитории шаблонов быстрого запуска Azure на сайте GitHub](https://github.com/Azure/azure-quickstart-templates) в папках, содержащих строку `vmss`.

## <a name="a-minimum-viable-scale-set"></a>Минимальный приемлемый масштабируемый набор

Наш шаблон минимального приемлемого масштабируемого набора находится [здесь](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json). Если вы уже знакомы с шаблонами, можно без колебаний перейти к разделу [Дальнейшие действия](#next-steps), в котором рассматривается изменение этого шаблона для других сценариев. Тем не менее, если вы мало знакомы с шаблонами, это пошаговое описание пригодится вам. Для начала давайте рассмотрим простой шаблон по частям.

Сначала нужно определить `$schema` и `contentVersion` для шаблона. `$schema` определяет версию языка шаблона и используется для выделения синтаксиса Visual Studio и других аналогичных возможностей проверки. `contentVersion` вообще не используется в Azure. Этот параметр позволяет отслеживать версию шаблона.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

Далее мы определим два параметра: `adminUsername` и `adminPassword`. Параметры — это значения, которые пользователь задает во время развертывания. Параметр `adminUsername` принадлежит к типу `string`, но так как параметр `adminPassword` является секретом, ему нужно назначить тип `securestring`. Далее эти параметры передаются в конфигурацию масштабируемого набора.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```

Шаблоны Resource Manager позволяют определять переменные, которые будут использоваться в шаблоне позже. В этом примере мы не будем использовать переменные, поэтому объект JSON будет пустым.

```json
  "variables": {},
```

Далее в ресурсах шаблона мы определим то, что на самом деле нужно развернуть. В отличие от `parameters` и `variables` (объекты JSON), `resources` — это список JSON объектов JSON.

```json
   "resources": [
```

Все ресурсы требуют `type`, `name`, `apiVersion` и `location`. Наш первый ресурс имеет тип `Microsft.Network/virtualNetwork`, имя `myVnet` и версию API `2016-03-30`. Чтобы определить последнюю версию API для типа ресурса, см. [документацию по Azure REST API](https://docs.microsoft.com/rest/api/).

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

Чтобы указать расположение для виртуальной сети, мы используем [функцию шаблона Resource Manager](../azure-resource-manager/resource-group-template-functions.md), которую нужно заключить в кавычки и квадратные скобки следующим образом: `"[<template-function>]"`. В этом случае мы используем функцию resourceGroup, которая не принимает аргументы и возвращает объект JSON с метаданными о группе ресурсов, где находится это развертывание. Группу ресурсов устанавливает пользователь во время развертывания. Затем мы выполняем индексирование этого объекта JSON, используя `.location`, чтобы получить сведения о расположении из объекта JSON.

```json
       "location": "[resourceGroup().location]",
```


Каждый ресурс Resource Manager имеет собственный раздел `properties` для связанных с ним конфигураций. В этом случае мы указываем, что у виртуальной сети должна быть одна подсеть с диапазоном частных IP-адресов `10.0.0.0/16`. Масштабируемый набор всегда содержится в одной подсети. Он не может охватывать подсети.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

Помимо обязательных свойств `type`, `name`, `apiVersion` и `location` каждый ресурс может иметь список строк `dependsOn`, в котором указаны другие ресурсы из этого развертывания, которые следует выполнить перед развертыванием этого ресурса. В этом случае в списке есть всего один элемент — виртуальная сеть, упомянутая выше. Эту зависимость нужно указать, так как перед созданием виртуальных машин для масштабируемого набора требуется задать сеть. Таким образом, масштабируемый набор сможет назначить этим виртуальным машинам частные IP-адреса из диапазона IP-адресов, указанного в свойствах сети ранее. Каждая строка в списке dependsOn указывается в формате `<type>/<name>` (те же параметры `type` и `name`, использованные ранее в определении ресурса виртуальной сети).

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```

Для масштабируемого набора нужно указать размер виртуальной машины, которую следует создать (наименование SKU), и число создаваемых виртуальных машин (емкость SKU). Сведения о доступных размерах виртуальных машин см. в [документации по размерам виртуальных машин](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

Кроме того, нужно указать способ обновления масштабируемого набора. Существуют два варианта: `Manual` и `Automatic`. Дополнительные сведения о различиях между этими двумя способами см. в документации по [обновлению масштабируемого набора](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

Для масштабируемого набора также нужно указать операционную систему, которую следует установить на виртуальных машинах. В этой статье мы создадим виртуальную машину на базе образа Ubuntu 16.04 со всеми исправлениями.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

Так как масштабируемый набор развертывает нескольких виртуальных машин, вместо имени каждой виртуальной машины мы укажем `computerNamePrefix`. Масштабируемый набор добавляет индекс к этому префиксу для каждой виртуальной машины, поэтому имена виртуальных машин выглядят следующим образом: `<computerNamePrefix>_<auto-generated-index>`. В этом фрагменте кода используются параметры, упомянутые ранее, чтобы задать имя пользователя и пароль администратора для всех виртуальных машин в масштабируемом наборе. Для этого используется функция шаблона `parameters`, принимающая строку, которая указывает параметры для использования и выдает значение для этого параметра.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

Наконец, требуется указать конфигурацию сети для виртуальных машин в масштабируемом наборе. В этом случае необходимо указать только идентификатор подсети, созданной ранее, чтобы масштабируемый набор разместил в ней сетевые интерфейсы. Чтобы получить идентификатор виртуальной сети, содержащей подсеть, выполните функцию шаблона `resourceId`. Эта функция принимает тип и имя ресурса и возвращает полный идентификатор ресурса, который выглядит следующим образом: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`. Однако идентификатора виртуальной сети недостаточно. Следует указать конкретную подсеть, в которой должны находиться виртуальные машины из масштабируемого набора, поэтому объедините `/subnets/mySubnet` с идентификатором виртуальной сети. В результате мы получаем полный идентификатор подсети. Чтобы выполнить объединение, используйте функцию `concat`, которая принимает ряд строк и возвращает объединенные строки.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
