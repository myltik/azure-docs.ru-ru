---
title: Ошибки в Azure, связанные с родительским ресурсом | Документация Майкрософт
description: В этой статье объясняется, как устранить ошибки при работе с родительским ресурсом.
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
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c996a644f206051cb58522065f87f95a4058cdee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357781"
---
# <a name="resolve-errors-for-parent-resources"></a>Устранение ошибок, связанных с родительскими ресурсами

В этой статье приведены ошибки, которые могут возникнуть при развертывании ресурсов, зависящих от родительского ресурса.

## <a name="symptom"></a>Симптом

При развертывании ресурса, являющегося дочерним для другого ресурса, может появиться следующая ошибка:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Причина:

Если один ресурс является дочерним для другого ресурса, перед созданием дочернего ресурса должен существовать его родительский ресурс. Имя дочернего ресурса включает в себя имя родительского ресурса. Например, база данных SQL может быть определена следующим образом.

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Но если не определять зависимость от сервера, база данных может быть развернута раньше сервера.

## <a name="solution"></a>Решение

Чтобы устранить эту ошибку, добавьте соответствующую зависимость.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

Дополнительные сведения см. в статье [Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager](resource-group-define-dependencies.md).