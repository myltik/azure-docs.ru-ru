---
title: "Ошибки с поиском ресурсов Azure | Документы Microsoft"
description: "Описываются способы устранения ошибок с поиском ресурсов."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Устранение ошибок с поиском ресурсов Azure

В этой статье описываются ошибки, которые могут возникнуть, когда во время развертывания не удается найти ресурс. 

## <a name="symptom"></a>Симптом

Если шаблон содержит имя ресурса, которое не удается разрешить, появится сообщение об ошибке, аналогичное следующему:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

При попытке использовать функции [reference](resource-group-template-functions-resource.md#reference) или [listKeys](resource-group-template-functions-resource.md#listkeys) с ресурсом, который не удается разрешить, появится следующая ошибка:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Причина:

Диспетчеру ресурсов нужно получить свойства для ресурса, но он не может определить ресурс в вашей подписке.

## <a name="solution"></a>Решение

### <a name="solution-1"></a>Решение 1

При попытке развернуть отсутствующий ресурс в шаблоне проверьте, нужно ли добавить зависимость. Resource Manager оптимизирует развертывание, создавая ресурсы параллельно, когда это возможно. Если один ресурс необходимо развернуть после другого, создайте зависимость от другого ресурса, используя в шаблоне элемент **dependsOn** . Например, при развертывании веб-приложения вам нужно создать план службы приложений. Если вы не указали, что веб-приложение зависит от плана службы приложений, Resource Manager создаст оба ресурса одновременно. При попытке указать свойство веб-приложения вы получите сообщение о том, что невозможно найти ресурс плана службы приложений, так как он еще не существует. Чтобы предотвратить эту ошибку, в веб-приложении следует настроить зависимость.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Предложения по устранению ошибок зависимостей доступны в разделе [Проверка последовательности развертывания](resource-manager-troubleshoot-tips.md#check-deployment-sequence).

### <a name="solution-2"></a>Решение 2

Если ресурс существует в группе ресурсов, отличной от той, где выполняется развертывание, используйте [функцию resourceId](resource-group-template-functions-resource.md#resourceid), чтобы получить полное имя ресурса.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>Решение 3

Найдите выражение с функцией [reference](resource-group-template-functions-resource.md#reference). Заданные значения различаются в зависимости от того, входит ли ресурс в тот же шаблон, группу ресурсов и подписку. Убедитесь, что вы предоставляете значения обязательного параметра для вашего сценария. Если ресурс находится в другой группе ресурсов, укажите идентификатор ресурса. Например, для создания ссылки на учетную запись хранения в другой группе ресурсов, используйте следующую команду:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```