---
title: "Создание пространства имен служебной шины с помощью шаблона Resource Manager | Документация Майкрософт"
description: "Используйте шаблон Azure Resource Manager для создания пространства имен служебной шины"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/04/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 39907193d006d6060152c9ac737a082d655c2256


---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Создание пространства имен служебной шины с помощью шаблона диспетчера ресурсов Azure
В этой статье показывается, как использовать шаблон Azure Resource Manager, создающий пространство имен служебной шины типа **Messaging** с SKU уровня "Стандартный" или "Базовый". В этой статье также определяются параметры, которые задаются во время развертывания. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][Создание шаблонов Azure Resource Manager].

Полный шаблон пространства имен служебной шины приведен [здесь][Шаблон пространства имен служебной шины] в GitHub.

> [!NOTE]
> Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure. 
> 
> * [Создание пространства имен служебной шины с очередью](service-bus-resource-manager-namespace-queue.md)
> * [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
> * [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
> * [Создание пространства имен служебной шины с разделом, подпиской и правилом с помощью шаблона Azure Resource Manager](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Чтобы узнать о новых шаблонах, в коллекции [шаблонов быстрого запуска Azure][шаблонов быстрого запуска Azure] выполните поиск по запросу "служебная шина".
> 
> 

## <a name="what-will-you-deploy"></a>Что вы развернете?
С помощью этого шаблона вы развернете пространство имен служебной шины с SKU уровня ["Базовый", "Стандартный" или "Премиум"](https://azure.microsoft.com/pricing/details/service-bus/).

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры
С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Имя создаваемого пространства имен служебной шины.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebussku"></a>serviceBusSKU
Имя создаваемого [SKU](https://azure.microsoft.com/pricing/details/service-bus/) служебной шины.

```
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Basic", 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

В шаблоне определены допустимые значения этого параметра (Basic, Standard и Premium). Если значение не указано, параметру назначается значение по умолчанию (Standard).

Дополнительные сведения о ценах на использование служебной шины см. в статье [Сведения о расценках и выставлении счетов служебной шины][Сведения о расценках и выставлении счетов служебной шины].

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Версия API служебной шины для шаблона.

```
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2015-08-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       } 
```

## <a name="resources-to-deploy"></a>Развертываемые ресурсы
### <a name="service-bus-namespace"></a>Пространство имен служебной шины
Создает стандартное пространство имен служебной шины типа **Messaging**.

```
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure
```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали и развернули ресурсы с помощью Azure Resource Manager, узнайте, как управлять этими ресурсами, изучив следующие статьи.

* [Управление служебной шиной с помощью PowerShell](service-bus-powershell-how-to-provision.md)
* [Управление ресурсами служебной шины с помощью обозревателя служебной шины](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
[Шаблон пространства имен служебной шины]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[шаблонов быстрого запуска Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Сведения о расценках и выставлении счетов служебной шины]: https://azure.microsoft.com/documentation/articles/service-bus-pricing-billing/
[Использование Azure PowerShell с Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Использование интерфейса командной строки Azure для Mac, Linux и Windows со службой управления ресурсами Azure]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Nov16_HO3-->


