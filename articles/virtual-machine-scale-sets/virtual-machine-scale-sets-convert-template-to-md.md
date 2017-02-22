---
title: "Преобразование шаблона масштабируемого набора Azure Resource Manager для использования управляемого диска | Документация Майкрософт"
description: "Преобразование шаблона масштабируемого набора в соответствующий шаблон с управляемым диском."
keywords: "наборы масштабирования виртуальных машин"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/25/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 01983ac7b60f7fdb237fba27f22cb31fcb3c1b7b
ms.openlocfilehash: cd1e67ce89a856f325b66087f003b1a9a1ac6f6a


---


# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Преобразование шаблона масштабируемого набора в соответствующий шаблон с управляемым диском.

Клиенты, которые с помощью шаблона Resource Manager создают масштабируемые наборы без поддержки управляемого диска, могут легко добавить поддержку управляемого диска. В этой статье такое преобразование демонстрируется на примере создания запроса на включение внесенных изменений. (Опубликовано в [шаблонах быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates) — репозитории сообщества пользователей с примерами шаблонов Resource Manager.) Полную версию этого запроса можно увидеть здесь: [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998). Все важные фрагменты, изменения и пояснения к ним приводятся в этой статье.

## <a name="making-the-os-disks-managed"></a>Переход на управляемые диски ОС

В следующем списке с описанием различий видно, что мы удалили несколько переменных, имеющих отношение к учетной записи хранения и свойствам диска. Тип учетной записи хранения больше не нужно указывать (по умолчанию используется тип Standard_LRS), но при желании это можно сделать. Для управляемых дисков поддерживаются только типы Standard_LRS и Premium_LRS. В старой версии шаблона для создания имен учетных записей хранения использовались следующие компоненты: суффикс учетной записи хранения, уникальный строковый массив и счетчик учетных записей хранения. Эти переменные в новой версии шаблона не нужны, так как управляемый диск автоматически создает для клиента учетные записи хранения. Также теперь не нужно указывать контейнер виртуального жесткого диска и имя диска ОС, ведь управляемый диск автоматически присваивает имена базовым контейнерам больших двоичных объектов и дискам хранилища.

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


В следующем списке различий видно, что мы изменили версию вычислительного API до выпуска 2016-04-30-preview. Это самая ранняя версия, поддерживающая управляемые диски с масштабируемыми наборами. Обратите внимание, что в новой версии API можно использовать и неуправляемые диски, сохранив старый синтаксис обращений. Таким образом, если мы изменим только версию вычислительного API, а все остальное сохраним без изменений, то и работать все будет как прежде.

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

В следующем списке с описанием различий видно, что мы полностью удалили из массива ресурс учетной записи хранения. Он нам не нужен, так как управляемый диск автоматически создает учетные записи от нашего имени.

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

В следующем списке с описанием различий видно, что удалены зависимости от предложения со ссылкой от масштабируемого набора к циклу, создающему учетные записи хранения. В старом шаблоне эта часть кода отвечала за то, чтобы учетные записи хранения обязательно создавались перед созданием масштабируемого набора. С управляемым диском это условие выполнять не нужно. Мы также удалили свойство контейнера виртуальных жестких дисков и имени диска операционной системы, так как они автоматически и прозрачно обрабатываются управляемым диском. Если нам нужны диски ОС класса Premium, мы можем добавить `"managedDisk": { "storageAccountType": "Premium_LRS" }` в конфигурацию osDisk. Диски класса Premium могут использоваться только такими виртуальными машинами, в номере SKU которых есть заглавная или строчная буква s.

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

Если в этот массив вы включите диски `n`, каждая виртуальная машина в масштабируемом наборе получит диски данных `n`. Но при этом не забывайте, что эти диски данных — необработанные устройства. Они не отформатированы. Все операции по подключению, разбиению и форматированию клиент должен выполнить самостоятельно, прежде чем использовать эти диски. При необходимости можно также настроить `"managedDisk": { "storageAccountType": "Premium_LRS" }` в каждом объекте диска данных использование дисков класса Premium. Диски класса Premium могут использоваться только такими виртуальными машинами, в номере SKU которых есть заглавная или строчная буква s.

Дополнительные сведения об использовании дисков данных с масштабируемыми наборами можно получить [в этой статье](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Дальнейшие действия
Чтобы найти примеры шаблонов Resource Manager с использованием наборов масштабирования, выполните поиск строки "vmss" в [репозитории GitHub с шаблонами быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates).

Общие сведения см. на [главной целевой странице по наборам масштабирования](https://azure.microsoft.com/services/virtual-machine-scale-sets/).




<!--HONumber=Feb17_HO2-->


