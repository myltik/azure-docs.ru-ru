---
title: Создание пространства имен служебной шины с разделом и подпиской с помощью шаблона Azure Resource Manager | Microsoft Docs
description: Создание пространства имен служебной шины с разделом и подпиской с помощью шаблона Azure Resource Manager.
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/11/2016
ms.author: sethm;shvija

---
# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Создание пространства имен служебной шины с разделом и подпиской с помощью шаблона Azure Resource Manager
В этой статье показывается, как использовать шаблон Azure Resource Manager, создающий пространство имен служебной шины с разделом и подпиской. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или изменить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][Создание шаблонов Azure Resource Manager].

Полный шаблон приведен в разделе [Шаблон пространства имен служебной шины с разделом и подпиской][Шаблон пространства имен служебной шины с разделом и подпиской] .

> [!NOTE]
> Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure.
> 
> * [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
> * [Создание пространства имен служебной шины с очередью](service-bus-resource-manager-namespace-queue.md)
> * [Создайте пространство имен служебной шины](service-bus-resource-manager-namespace.md)
> * [Создание пространства имен концентратора событий с концентратором событий и группой потребителей](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
> 
> Чтобы узнать о новых шаблонах, в коллекции [шаблонов быстрого запуска Azure][шаблонов быстрого запуска Azure] выполните поиск по запросу "служебная шина".
> 
> 

## <a name="what-will-you-deploy?"></a>Что вы развернете?
С помощью этого шаблона вы развернете пространство имен служебной шины с разделом и подпиской.

[Разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) предоставляют разновидность взаимодействия "один ко многим" в рамках схемы *публикации или подписки*.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры
С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Шаблон определяет следующие параметры.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Имя создаваемого пространства имен служебной шины.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Имя раздела, создаваемого в пространстве имен служебной шины.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Имя подписки, создаваемой в пространстве имен служебной шины.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Версия API служебной шины для шаблона.

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>Развертываемые ресурсы
Создает стандартное пространство имен служебной шины типа **Messaging**с разделом и подпиской.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы создали и развернули ресурсы с помощью диспетчера ресурсов Azure, узнайте, как управлять этими ресурсами, изучив следующие статьи:

* [Управление служебной шиной с помощью PowerShell](../service-bus/service-bus-powershell-how-to-provision.md)
* [Управление ресурсами служебной шины с помощью обозревателя служебной шины](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
[шаблонов быстрого запуска Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Ознакомьтесь с дополнительными сведениями о разделах и подписках служебной шины]: service-bus-queues-topics-subscriptions.md
[Использование Azure PowerShell с Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Использование интерфейса командной строки Azure для Mac, Linux и Windows со службой управления ресурсами Azure]: ../xplat-cli-azure-resource-manager.md
[Шаблон пространства имен служебной шины с разделом и подпиской]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/



<!--HONumber=Oct16_HO2-->


