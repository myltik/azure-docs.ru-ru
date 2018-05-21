---
title: Ошибки с поиском ресурсов Azure | Документы Microsoft
description: Описываются способы устранения ошибок с поиском ресурсов.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: f5da2a74b3a399c60c518f386ccf2e60a617aeda
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Устранение ошибок с поиском ресурсов Azure

В этой статье описываются ошибки, которые могут возникнуть, когда во время развертывания не удается найти ресурс.

## <a name="symptom"></a>Симптом

Если шаблон содержит имя ресурса, которое не удается разрешить, появится сообщение об ошибке, аналогичное приведенному ниже.

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

Resource Manager нужно получить свойства ресурса, но он не может определить ресурс в вашей подписке.

## <a name="solution-1---set-dependencies"></a>Решение 1 — задание зависимостей

При попытке развернуть отсутствующий ресурс в шаблоне проверьте, нужно ли добавить зависимость. Resource Manager оптимизирует развертывание, создавая ресурсы параллельно, когда это возможно. Если один ресурс необходимо развернуть после другого, требуется использовать в шаблоне элемент **dependsOn**. Например, при развертывании веб-приложения вам нужно создать план службы приложений. Если вы не указали, что веб-приложение зависит от плана службы приложений, Resource Manager создаст оба ресурса одновременно. При попытке указать свойство веб-приложения появится сообщение о том, что невозможно найти ресурс плана службы приложений, так как он еще не существует. Чтобы предотвратить эту ошибку, в веб-приложении следует настроить зависимость.

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

Но следует избегать задания ненужных зависимостей. Ненужные зависимости могут замедлить развертывание, мешая параллельному развертыванию независимых между собой ресурсов. Кроме того, возможно образование циклических зависимостей, которые блокируют развертывание. Функция [reference](resource-group-template-functions-resource.md#reference) создает неявную зависимость от ресурса, на который указывает ссылка, когда этот ресурс развертывается в том же шаблоне. Таким образом можно использовать больше зависимостей, чем задано в свойстве **dependsOn**. Функция [ResourceId](resource-group-template-functions-resource.md#resourceid) не создает неявную зависимость и не проверяет, существует ли ресурс.

При возникновении проблем с зависимостями необходимо узнать, в каком порядке развертываются ресурсы. Вот как можно просмотреть порядок операций развертывания.

1. Выберите журнал развертывания для группы ресурсов.

   ![Выбор журнала развертывания](./media/resource-manager-not-found-errors/select-deployment.png)

2. Выберите развертывание в журнале, затем выберите **События**.

   ![Выбор событий развертывания](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Изучите последовательность событий для каждого ресурса. Обратите внимание на состояние каждой операции. Например, на следующем рисунке показаны три учетные записи хранения, которые были развернуты параллельно. Обратите внимание, что эти три учетные записи хранения были запущены одновременно.

   ![Параллельное развертывание](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   На следующем рисунке показаны три учетные записи хранения, которые не были развернуты параллельно. Вторая учетная запись хранения зависит от первой учетной записи хранения, а третья учетная запись хранения — от второй. Первая учетная запись хранения должна быть запущена, принята и завершена, прежде чем будет запущена следующая.

   ![Последовательное развертывание](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Решение 2 — получение ресурса из другой группы ресурсов

Если ресурс существует в группе ресурсов, отличной от той, где выполняется развертывание, используйте [функцию resourceId](resource-group-template-functions-resource.md#resourceid), чтобы получить полное имя ресурса.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Решение 3 — проверка функции reference

Найдите выражение с функцией [reference](resource-group-template-functions-resource.md#reference). Заданные значения различаются в зависимости от того, входит ли ресурс в тот же шаблон, группу ресурсов и подписку. Убедитесь, что вы предоставляете значения обязательного параметра для вашего сценария. Если ресурс находится в другой группе ресурсов, укажите идентификатор ресурса. Например, для создания ссылки на учетную запись хранения в другой группе ресурсов, используйте следующую команду:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```