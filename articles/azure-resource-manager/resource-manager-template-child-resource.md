---
title: "Определение дочернего ресурса в шаблоне Azure | Документация Майкрософт"
description: "В этой статье показано, как задать тип и имя дочернего ресурса в шаблоне Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d7560b689d7cea56d40ffa2db9542f74a649f9c1
ms.lasthandoff: 03/03/2017


---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Указание имени и типа дочернего ресурса в шаблоне Resource Manager
При создании шаблона нередко требуется добавить в него дочерний ресурс, связанный с родительским ресурсом. Например, шаблон может содержать сервер SQL Server и базу данных. Сервер SQL Server является родительским ресурсом, а база данных — дочерним. 

Формат типа дочернего ресурса: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Формат имени дочернего ресурса: `{parent-resource-name}/{child-resource-name}`

Тип и имя указываются в шаблоне по-разному в зависимости от того, является ли дочерний ресурс вложенным в родительский ресурс или самостоятельным ресурсом на верхнем уровне. В этой статье описываются оба варианта.

## <a name="nested-child-resource"></a>Вложенный дочерний ресурс
Самый простой способ определить дочерний ресурс — вложить его в родительский ресурс. В следующем примере показана база данных SQL, вложенная в сервер SQL Server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Для дочернего ресурса задается тип `databases`, но его полным типом ресурса является `Microsoft.Sql/servers/databases`. `Microsoft.Sql/servers/` не указывается, так как предполагается из типа родительского ресурса. Дочернему ресурсу присваивается имя `exampledatabase`, но его полное имя включает в себя имя родительского ресурса. `exampleserver` не указывается, так как предполагается из родительского ресурса.

## <a name="top-level-child-resource"></a>Дочерний ресурс верхнего уровня
Дочерний ресурс можно определить на верхнем уровне. Этот подход можно использовать, если родительский ресурс не развертывается в том же шаблоне или требуется использовать `copy` для создания нескольких дочерних ресурсов. В этом случае нужно указать полный тип ресурса и добавить в имя дочернего ресурса имя родительского ресурса.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

База данных является дочерним ресурсом для сервера, даже если они определены на одном уровне в шаблоне.

## <a name="next-steps"></a>Дальнейшие действия
* См. дополнительные рекомендации по [созданию шаблонов Azure Resource Manager](resource-manager-template-best-practices.md).
* Пример создания нескольких дочерних ресурсов см. в разделе [Развертывание нескольких экземпляров ресурсов в шаблонах Azure Resource Manager](resource-group-create-multiple.md).

