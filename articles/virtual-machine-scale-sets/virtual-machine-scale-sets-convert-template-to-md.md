---
title: "Преобразование шаблона масштабируемого набора Azure Resource Manager для использования управляемого диска | Документация Майкрософт"
description: "Преобразование шаблона масштабируемого набора в соответствующий шаблон с управляемым диском."
keywords: "наборы масштабирования виртуальных машин"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Преобразование шаблона масштабируемого набора в соответствующий шаблон с управляемым диском.

Клиенты, которые с помощью шаблона Resource Manager создают масштабируемые наборы без поддержки управляемого диска, могут легко добавить поддержку управляемого диска. В этой статье показано, как использовать управляемые диски, используя в качестве примера запросу из [шаблоны быстрый запуск Azure](https://github.com/Azure/azure-quickstart-templates), сообщество ведет репозитория для образца шаблонов диспетчера ресурсов. Полную версию этого запроса можно увидеть здесь: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998). Все важные фрагменты, изменения и пояснения к ним приводятся в этой статье.

## <a name="making-the-os-disks-managed"></a>Переход на управляемые диски ОС

В следующем diff удаляются несколько переменных, связанных с свойств учетной записи и диск хранилища. Тип учетной записи хранения больше не требуется (Standard_LRS используется по умолчанию), но можно указать его при необходимости. Для управляемых дисков поддерживаются только типы Standard_LRS и Premium_LRS. В старой версии шаблона для создания имен учетных записей хранения использовались следующие компоненты: суффикс учетной записи хранения, уникальный строковый массив и счетчик учетных записей хранения. Эти переменные в новой версии шаблона не нужны, так как управляемый диск автоматически создает для клиента учетные записи хранения. Аналогичным образом имя контейнера vhd и имя диска ОС отпала поскольку управляемого диска автоматически имени базового контейнеров больших двоичных объектов хранилища и дисков.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


В следующем diff вы вычислений API обновлен до версии 2016-04-30-preview это самая ранняя требуемую версию для поддержки управляемого диска с наборами шкалы. При желании можно использовать неуправляемый дисков в новой версии API с помощью старого синтаксиса. Если только не обновите версию API вычислений, не вносите никаких других изменений шаблона должны продолжать работать как раньше.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

В следующем diff ресурс учетной записи хранения из массива ресурсы полностью удаляется. Ресурс больше не нужен, как управляемого диска создает их автоматически.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

В следующих diff, мы видим, что мы удаляем зависит от предложения ссылками из набора в цикл, создании учетных записей хранения шкалы. В старом шаблоне эта часть кода отвечала за то, чтобы учетные записи хранения обязательно создавались перед созданием масштабируемого набора. С управляемым диском это условие выполнять не нужно. Свойство контейнеры vhd также будут удалены, вместе со свойством имени диска операционной системы, эти свойства будут автоматически обрабатываются в механизме управляемого диска. Можно добавить `"managedDisk": { "storageAccountType": "Premium_LRS" }` в конфигурации «osDisk», если требуется, чтобы ОС диски premium. Диски класса Premium могут использоваться только такими виртуальными машинами, в номере SKU которых есть заглавная или строчная буква s.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Для использования управляемого или неуправляемого диска не существует явным образом указанного свойства в конфигурации масштабируемого набора. Масштабируемый набор выбирает, какой диск будет использоваться с учетом свойств, присутствующих в профиле хранилища. Поэтому при изменении шаблона важно обеспечить наличие правильных свойств в профиле хранения для масштабируемого набора.


## <a name="data-disks"></a>Диски данных

После внесения всех этих изменений масштабируемый набор будет использовать управляемые диски для операционной системы. Но что нам делать с дисками данных? Чтобы добавить диски данных, включите параметр dataDisks в раздел storageProfile на том же уровне, где расположен osDisk. Значение этого свойства содержит список объектов JSON, каждый из которых включает следующие свойства: lun (которое должно быть уникальным для каждого диска данных на виртуальной машине), createOption (сейчас поддерживается только значение empty) и diskSizeGB (размер диска в гигабайтах, допускаются значения в пределах от 0 до 1024). Ниже представлен пример такого списка: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Если в этот массив вы включите диски `n`, каждая виртуальная машина в масштабируемом наборе получит диски данных `n`. Но при этом не забывайте, что эти диски данных — необработанные устройства. Они не отформатированы. Именно клиенту подключить, разбить на разделы и отформатируйте диски перед их использованием. При необходимости можно также указать `"managedDisk": { "storageAccountType": "Premium_LRS" }` в каждом объекте диска данных для указания, следует использовать диск данных premium. Диски класса Premium могут использоваться только такими виртуальными машинами, в номере SKU которых есть заглавная или строчная буква s.

Дополнительные сведения об использовании дисков данных с масштабируемыми наборами можно получить [в этой статье](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Дальнейшие действия
Чтобы найти примеры шаблонов Resource Manager с использованием наборов масштабирования, выполните поиск строки "vmss" в [репозитории GitHub с шаблонами быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates).

Общие сведения см. на [главной целевой странице по наборам масштабирования](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

