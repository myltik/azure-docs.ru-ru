---
title: "Указание настраиваемого образа в шаблоне масштабируемого набора Azure | Документация Майкрософт"
description: "Узнайте, как добавить настраиваемый образ в существующий шаблон масштабируемого набора виртуальных машин Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Добавление настраиваемого образа в шаблон масштабируемого набора Azure

В этой статье объясняется, как изменить [шаблон минимального приемлемого масштабируемого набора](./virtual-machine-scale-sets-mvss-start.md), чтобы выполнить развертывание с помощью настраиваемого образа.

## <a name="change-the-template-definition"></a>Изменение определения шаблона

Шаблон Минимальный масштаб допустимого набора может видеть [здесь](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), и шаблон для развертывания шкалы значение с помощью пользовательского образа можно будет увидеть [здесь](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Давайте рассмотрим DIFF-файл, с помощью которого можно постепенно создать этот шаблон (`git diff minimum-viable-scale-set custom-image`).

### <a name="creating-a-managed-disk-image"></a>Создание образа управляемого диска

Если вы уже создали настраиваемый образ управляемого диска (ресурс типа `Microsoft.Compute/images`), можете пропустить этот раздел.

Сначала добавьте `sourceImageVhdUri` параметра, который является обобщенной большого двоичного объекта в хранилище Azure, содержащий пользовательского образа для развертывания из URI.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Добавьте ресурс типа `Microsoft.Compute/images`, который является изображение управляемого диска, на основании обобщенный большой двоичный объект, расположенный в URI `sourceImageVhdUri`. Этот образ должен находиться в том же регионе, что и масштабируемый набор, который его использует. В свойствах образа, укажите тип операционной системы, расположение большого двоичного объекта (из `sourceImageVhdUri` параметра) и тип учетной записи хранения:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

На шкале задать ресурсов, добавить `dependsOn` предложение, ссылающиеся на пользовательского образа, чтобы убедиться, что изображение создается до шкалы пытается развернуть из этого образа:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Изменение свойств масштабируемого набора для использования управляемого образа

В `imageReference` шкалы задать `storageProfile`, вместо указания издателя, предложение, sku, и укажите версию образа платформы, `id` из `Microsoft.Compute/images` ресурсов:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

В этом примере используется `resourceId` функции, чтобы получить идентификатор ресурса изображения, созданные в тот же шаблон. Если заранее после создания образа управляемого диска, необходимо предоставить идентификатор этого образа вместо. Этот идентификатор должен иметь вид: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Следующие шаги

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
