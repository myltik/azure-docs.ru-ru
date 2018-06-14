---
title: Сведения о шаблонах масштабируемых наборов виртуальных машин | Документация Майкрософт
description: Узнайте, как создать шаблон минимально приемлемого масштабируемого набора виртуальных машин.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 5cd495d1332c71d7eae775f933b73d98826f10e4
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781261"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Подробнее о шаблонах масштабируемых наборов виртуальных машин
[Шаблоны Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) прекрасно подходят для развертывания группы связанных ресурсов. В этой серии руководств показано, как создать шаблон минимального приемлемого масштабируемого набора и изменить его в соответствии с различными сценариями. Все примеры взяты из этого [репозитория GitHub](https://github.com/gatneil/mvss). 

Этот шаблон должен быть простым. Полные примеры шаблонов масштабируемых наборов можно найти в [репозитории шаблонов быстрого запуска Azure на сайте GitHub](https://github.com/Azure/azure-quickstart-templates). Папки с этими шаблонами содержат строку `vmss`.

Если вы уже умеете создавать шаблоны, можете смело переходите к разделу "Дальнейшие действия", где мы расскажем, как нужно изменить этот шаблон.

## <a name="review-the-template"></a>Изучение шаблона

Изучите представленный на GitHub шаблон минимально приемлемого масштабируемого набора [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

В этом руководстве будут рассмотрены различия между файлами (`git diff master minimum-viable-scale-set`) для поэтапного выстраивания шаблона минимально приемлемого масштабируемого набора.

## <a name="define-schema-and-contentversion"></a>Определение элементов $schema и contentVersion
Сначала в шаблоне необходимо определить `$schema` и `contentVersion`. Элемент `$schema` определяет версию языка шаблона. Он используется для разметки синтаксиса в редакторе Visual Studio и других функций проверки кода. Элемент `contentVersion` не используется в Azure, но позволяет отслеживать версию шаблона.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Определение параметров
Далее определите два параметра: `adminUsername` и `adminPassword`. Параметры — это значения, которые пользователь задает во время развертывания. Параметр `adminUsername` будет иметь простейший тип `string`, но для параметра `adminPassword` необходимо назначить тип `securestring`, так как он содержит секретную информацию. Позднее эти параметры будут переданы в конфигурацию масштабируемого набора.

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
## <a name="define-variables"></a>Определение переменных
Шаблоны Resource Manager позволяют определять переменные и затем использовать их в шаблоне. В текущем примере не используется никаких переменных, поэтому объект JSON пустой.

```json
  "variables": {},
```

## <a name="define-resources"></a>Определение ресурсов
Теперь мы переходим к разделу шаблона resources, где вы можете определить ресурсы, которые следует развернуть. В отличие от разделов `parameters` и `variables`, которые сами являются объектами JSON, раздел `resources` представляет собой JSON-список объектов JSON.

```json
   "resources": [
```

Каждый ресурс должен иметь свойства `type`, `name`, `apiVersion` и `location`. В этом примере для первого ресурса указан тип `Microsft.Network/virtualNetwork`, имя `myVnet` и версия API `2016-03-30`. (Последнюю версию API для типа ресурса см. в [документации по REST API Azure](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Определение расположения
Чтобы указать расположение для виртуальной сети, примените [функции шаблона Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Каждая функция заключается в кавычки и квадратные скобки, примерно так: `"[<template-function>]"`. В этом случае используйте функцию `resourceGroup`. Она не принимает никаких аргументов и возвращает объект JSON с метаданными о группе ресурсов, в которой выполняется развертывание. Группу ресурсов устанавливает пользователь во время развертывания. Затем, используя функцию `.location`, значение этого объекта JSON индексируется, чтобы получить из него сведения о расположении.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Определение свойств виртуальной сети
Каждый ресурс Resource Manager имеет собственный раздел `properties` для связанных с ним конфигураций. В этом примере укажите, что у виртуальной сети должна быть одна подсеть с диапазоном частных IP-адресов `10.0.0.0/16`. Масштабируемый набор всегда содержится в одной подсети. Он не может охватывать подсети.

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

## <a name="add-dependson-list"></a>Добавление списка зависимостей (dependsOn)
Помимо обязательных свойств `type`, `name`, `apiVersion` и `location`, в каждом ресурсе можно использовать дополнительный список строк `dependsOn`. Этот список определяет, какие еще ресурсы из текущего развертывания необходимо создать перед развертыванием этого ресурса.

В нашем примере этот список содержит только один элемент — это упомянутая выше виртуальная сеть. Эту зависимость нужно указать, так как перед созданием виртуальных машин для масштабируемого набора требуется задать сеть. Тогда масштабируемый набор сможет назначить этим виртуальным машинам частные IP-адреса из диапазона IP-адресов, указанного ранее в свойствах сети. Каждая строка в списке dependsOn имеет формат `<type>/<name>`. Используйте такие же значения `type` и `name`, как и в определении ресурса виртуальной сети.

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
## <a name="specify-scale-set-properties"></a>Определение свойств масштабируемого набора
Масштабируемый набор имеет множество свойств, позволяющих настроить входящие в этот набор виртуальные машины. Полный список этих свойств см. в [документации по REST API для масштабируемого набора](https://docs.microsoft.com/rest/api/virtualmachinescalesets/create-or-update-a-set). Лишь несколько наиболее распространенных свойств определены в этом руководстве.
### <a name="supply-vm-size-and-capacity"></a>Определение размера и емкости виртуальных машин
Для масштабируемого набора следует указать размер виртуальной машины, которую следует создать (имя SKU), и число создаваемых виртуальных машин (емкость SKU). Сведения о доступных размерах (именах SKU) для виртуальных машин см. в [документации по размерам виртуальных машин](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Выбор типа обновления
Кроме того, нужно указать способ обновления масштабируемого набора. Сейчас поддерживаются два варианта: `Manual` и `Automatic`. Дополнительные сведения о различиях между этими двумя способами см. в документации по [обновлению масштабируемого набора](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Выбор операционной системы для виртуальных машин
Для масштабируемого набора нужно указать операционную систему, которую следует установить на виртуальных машинах. Создайте виртуальную машину на базе образа Ubuntu 16.04 со всеми исправлениями.

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

### <a name="specify-computernameprefix"></a>Определение префикса имени компьютера (computerNamePrefix)
В масштабируемом наборе развертываются несколько виртуальных машин. Не указывайте имя отдельно для каждой из них, а просто укажите префикс `computerNamePrefix`. Масштабируемый набор добавляет индекс к этому префиксу для каждой виртуальной машины, поэтому имена виртуальных машин выглядят следующим образом: `<computerNamePrefix>_<auto-generated-index>`.

В следующем фрагменте кода используйте описанные выше параметры, с помощью которых задается имя пользователя и пароль администратора для всех виртуальных машин в масштабируемом наборе. В этот процессе используется функция шаблона `parameters`. Эта функция принимает строку, обозначающую параметр для ссылки, и возвращает значение для этого параметра.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Определение конфигурации сети для виртуальных машин
Наконец, укажите конфигурацию сети для виртуальных машин в масштабируемом наборе. В данном случае достаточно просто указать идентификатор созданной ранее подсети. Это означает, что масштабируемый набор будет размещать в этой подсети сетевые интерфейсы.

Чтобы получить идентификатор виртуальной сети, содержащей подсеть, выполните функцию шаблона `resourceId`. Эта функция принимает тип и имя ресурса и возвращает полный идентификатор ресурса. Этот идентификатор имеет следующий вид: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`.

Однако идентификатора виртуальной сети недостаточно. Укажите конкретную подсеть, в которой должны размещаться виртуальные машины масштабируемого набора. Для этого нужно объединить строку `/subnets/mySubnet` с идентификатором виртуальной сети. Это и будет полный идентификатор вашей подсети. Чтобы выполнить объединение строк, используйте функцию `concat`. Она принимает несколько строк и возвращает одну объединенную строку.

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

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
