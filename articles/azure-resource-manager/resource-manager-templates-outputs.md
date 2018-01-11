---
title: "Выводит шаблона Azure Resource Manager | Документы Microsoft"
description: "Описание способов определения выходов для шаблонов диспетчера ресурсов Azure, с помощью декларативного синтаксиса JSON."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 485a3eb5c5d04d1540482245d088c48645704465
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Выходные данные раздела шаблоны Azure Resource Manager
В разделе выходных данных следует указать значения, которые возвращаются после развертывания. Например, можно возвращать URI для доступа к развернутому ресурсу.

## <a name="define-and-use-output-values"></a>Определение и использование выходных значений

Приведенный ниже показано, как получить идентификатор ресурса для общедоступного IP-адреса:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

После развертывания можно извлечь значение со скриптом. Для PowerShell используйте команду:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Для интерфейса командной строки Azure:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Выходное значение можно получить из связанного шаблона с помощью [ссылки](resource-group-template-functions-resource.md#reference) функции. Чтобы получить выходные значения из связанного шаблона, извлеките значение свойства с синтаксисом, например: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Например можно задать IP-адреса в подсистеме балансировки нагрузки, получая значение из связанного шаблона.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

## <a name="available-properties"></a>Доступные свойства

В следующем примере показана структура определения выходных данных:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Имя элемента | Требуется | ОПИСАНИЕ |
|:--- |:--- |:--- |
| outputName |Yes |Имя выходного значения. Должно быть допустимым идентификатором JavaScript. |
| Тип |Yes |Тип выходного значения. Выходные значения поддерживает те же типы, что и входные параметры шаблона. |
| value |Yes |Выражение на языке шаблона, которое вычисляется и возвращается в качестве выходного значения. |

## <a name="recommendations"></a>Рекомендации

При использовании шаблона для создания общих IP-адресов включите раздел выходные данные, для извлечения сведений о IP-адрес и полное доменное имя (FQDN). С помощью этих выходных данных можно легко получить сведения об общедоступных IP-адресах и полных доменных именах после развертывания.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Образцы шаблонов


|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[Переменные Копировать](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Создает комплексное переменные и выводит эти значения. Развертывает все ресурсы. |
|[Общедоступный IP-адрес](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Создает общий IP-адрес и выводит его идентификатором ресурса. |
|[Подсистема балансировки нагрузки](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Ссылки на шаблон выше. При создании подсистемы балансировки нагрузки по идентификатору ресурса в выходных данных. |


## <a name="next-steps"></a>Дальнейшие действия
* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Инструкции по объединению нескольких шаблонов при развертывании см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Может потребоваться использовать ресурсы, которые существуют в другой группе ресурсов. Это распространенная ситуация при работе с учетными записями хранения или виртуальными сетями, которые совместно используются в нескольких группах ресурсов. Дополнительные сведения см. в описании [функции resourceId](resource-group-template-functions-resource.md#resourceid).