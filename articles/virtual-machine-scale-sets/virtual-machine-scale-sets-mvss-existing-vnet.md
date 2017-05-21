---
title: "Указание виртуальной сети в шаблоне масштабируемого набора Azure | Документация Майкрософт"
description: "Узнайте, как добавить виртуальную сеть в существующий шаблон масштабируемого набора виртуальных машин Azure."
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
ms.date: 3/06/2017
ms.author: negat
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 8e9caf7eebc17682b3204004e3a74331efbd04fb
ms.contentlocale: ru-ru
ms.lasthandoff: 05/11/2017

---

# <a name="add-reference-to-a-virtual-network-to-an-azure-scale-set-template"></a>Добавление ссылки на виртуальную сеть в шаблон масштабируемого набора Azure

В этой статье показано, как изменить [шаблон минимального приемлемого масштабируемого набора](./virtual-machine-scale-sets-mvss-start.md) для развертывания в существующей виртуальной сети вместо создания новой.

## <a name="change-the-template-definition"></a>Изменение определения шаблона

Шаблон минимального приемлемого масштабируемого набора доступен [здесь](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), а шаблон для развертывания масштабируемого набора в существующей виртуальной сети — [здесь](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Давайте рассмотрим DIFF-файл, с помощью которого можно постепенно создать этот шаблон (`git diff minimum-viable-scale-set existing-vnet`).

Сначала мы добавим параметр `subnetId`. Эта строка будет передана в конфигурацию масштабируемого набора, благодаря чему он сможет идентифицировать предварительно созданную подсеть для развертывания виртуальных машин. Эта строка должна иметь следующий формат: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Например, чтобы развернуть масштабируемый набор в существующей виртуальной сети с именем `myvnet`, подсети `mysubnet`, группе ресурсов `myrg` и подписке `00000000-0000-0000-0000-000000000000`, идентификатор подсети должен быть таким: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Затем можно удалить ресурс виртуальной сети из массива `resources`, потому что мы используем существующую виртуальную сеть и не требуется развертывать новую.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Виртуальная сеть уже существовала до развертывания шаблона, поэтому нет необходимости указывать предложение dependsOn из масштабируемого набора для виртуальной сети. Следовательно, мы удаляем такие строки:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Наконец, мы передаем параметр `subnetId`, заданный пользователем (вместо использования `resourceId` для получения идентификатора виртуальной сети в том же развертывании, что происходит, если используется шаблон минимального приемлемого масштабируемого набора).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

