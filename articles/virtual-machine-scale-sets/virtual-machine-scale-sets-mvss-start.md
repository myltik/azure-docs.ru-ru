---
title: "Сведения о шаблонах масштабируемых наборов виртуальных машин | Документация Майкрософт"
description: "Узнайте, как создать шаблон минимально приемлемого масштабируемого набора виртуальных машин."
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
ms.date: 06/01/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 3b978f5448c2cfbba4d02e3efd730dea7c7813c3
ms.contentlocale: ru-ru
ms.lasthandoff: 03/31/2017

---

# <a name="learn-about-virtual-machine-scale-set-templates"></a>Подробнее о шаблонах масштабируемых наборов виртуальных машин
[Шаблоны Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) прекрасно подходят для развертывания группы связанных ресурсов. В этой серии руководств показано, как создать шаблон минимального приемлемого масштабируемого набора и изменить его в соответствии с различными сценариями. Все примеры взяты из этого [репозитория GitHub](https://github.com/gatneil/mvss). 

Этот шаблон должен быть простым. Полные примеры шаблонов масштабируемых наборов можно найти в [репозитории шаблонов быстрого запуска Azure на сайте GitHub](https://github.com/Azure/azure-quickstart-templates). Папки с этими шаблонами содержат строку `vmss`.

Если вы уже умеете создавать шаблоны, можете смело переходите к разделу "Дальнейшие действия", где мы расскажем, как нужно изменить этот шаблон.

## <a name="review-the-template"></a>Изучение шаблона

Изучите представленный на GitHub шаблон минимально приемлемого масштабируемого набора [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

В этом руководстве мы рассмотрим различия между файлами (`git diff master minimum-viable-scale-set`) и поэтапно выстроим шаблон минимально приемлемого масштабируемого набора.

## <a name="define-schema-and-contentversion"></a>Определение элементов $schema и contentVersion
Сначала в нашем шаблоне нужно определить `$schema` и `contentVersion`. Элемент `$schema` определяет версию языка шаблона. Он используется для разметки синтаксиса в редакторе Visual Studio и других функций проверки кода. Элемент `contentVersion` не используется в Azure, но позволяет отслеживать версию шаблона.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Определение параметров
Далее мы определим два параметра: `adminUsername` и `adminPassword`. Параметры — это значения, которые пользователь задает во время развертывания. Параметр `adminUsername` будет иметь простейший тип `string`, но для параметра `adminPassword` нужно назначить тип `securestring`, так как он содержит секретную информацию. Позднее эти параметры будут переданы в конфигурацию масштабируемого набора.

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
Шаблоны Resource Manager позволяют определять переменные и затем использовать их в шаблоне. В нашем примере переменные не нужны, поэтому объект JSON будет пустым.

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
Чтобы указать расположение для виртуальной сети, мы применим [функцию шаблона Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Каждая функция заключается в кавычки и квадратные скобки, примерно так: `"[<template-function>]"`. Для нашего примера мы используем функцию `resourceGroup`. Она не принимает никаких аргументов и возвращает объект JSON с метаданными о группе ресурсов, в которой выполняется развертывание. Группу ресурсов устанавливает пользователь во время развертывания. Затем мы выполняем индексирование этого объекта JSON, используя `.location`, чтобы получить сведения о расположении из объекта JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Определение свойств виртуальной сети
Каждый ресурс Resource Manager имеет собственный раздел `properties` для связанных с ним конфигураций. В этом примере мы указываем, что у виртуальной сети должна быть одна подсеть с диапазоном частных IP-адресов `10.0.0.0/16`. Масштабируемый набор всегда содержится в одной подсети. Он не может охватывать подсети.

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
Масштабируемый набор имеет множество свойств, позволяющих настроить входящие в этот набор виртуальные машины. Полный список этих свойств см. в [документации по REST API для масштабируемого набора](https://docs.microsoft.com/en-us/rest/api/virtualmachinescalesets/create-or-update-a-set). В этом руководстве мы определим лишь несколько наиболее распространенных свойств.
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
Для масштабируемого набора нужно указать операционную систему, которую следует установить на виртуальных машинах. В этой статье мы создадим виртуальную машину на базе образа Ubuntu 16.04 со всеми исправлениями.

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
В масштабируемом наборе развертываются несколько виртуальных машин. Мы не будем указывать имя отдельно для каждой из них, а просто укажем префикс `computerNamePrefix`. Масштабируемый набор добавляет индекс к этому префиксу для каждой виртуальной машины, поэтому имена виртуальных машин выглядят следующим образом: `<computerNamePrefix>_<auto-generated-index>`.

В следующем фрагменте кода используются описанные выше параметры, с помощью которых мы задаем имя пользователя и пароль администратора для всех виртуальных машин в масштабируемом наборе. Мы применяем для них функцию шаблона `parameters`. Эта функция принимает строку, обозначающую параметр для ссылки, и возвращает значение для этого параметра.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Определение конфигурации сети для виртуальных машин
И наконец мы должны указать конфигурацию сети для виртуальных машин в масштабируемом наборе. Для нашего примера достаточно просто указать идентификатор созданной ранее подсети. Это означает, что масштабируемый набор будет размещать в этой подсети сетевые интерфейсы.

Чтобы получить идентификатор виртуальной сети, содержащей подсеть, выполните функцию шаблона `resourceId`. Эта функция принимает тип и имя ресурса и возвращает полный идентификатор ресурса. Этот идентификатор имеет следующий вид: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`.

Однако идентификатора виртуальной сети недостаточно. Нужно указать конкретную подсеть, в которой должны размещаться виртуальные машины масштабируемого набора. Для этого нужно объединить строку `/subnets/mySubnet` с идентификатором виртуальной сети. Это и будет полный идентификатор вашей подсети. Чтобы выполнить объединение строк, используйте функцию `concat`. Она принимает несколько строк и возвращает одну объединенную строку.

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

