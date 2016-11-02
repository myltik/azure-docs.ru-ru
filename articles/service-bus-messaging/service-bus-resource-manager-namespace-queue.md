<properties
    pageTitle="Создание пространства имен и очереди служебной шины с помощью шаблона диспетчера ресурсов Azure | Microsoft Azure"
    description="Создание пространства имен и очереди служебной шины с помощью шаблона диспетчера ресурсов Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>


# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Создание пространства имен и очереди служебной шины с помощью шаблона диспетчера ресурсов Azure

В этой статье показывается, как использовать шаблон диспетчера ресурсов Azure, создающий пространство имен служебной шины и очередь. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][].

Полный шаблон приведен в разделе [Шаблон пространства имен служебной шины и очереди][] в GitHub.

>[AZURE.NOTE] Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure.
>
>-    [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
>-    [Создайте пространство имен служебной шины](service-bus-resource-manager-namespace.md)
>-    [Создание пространства имен концентратора событий с концентратором событий и группой потребителей](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Чтобы узнать о новых шаблонах, в коллекции [шаблонов быстрого запуска Azure][] выполните поиск по запросу "служебная шина".

## <a name="what-will-you-deploy?"></a>Что вы развернете?

С помощью этого шаблона вы развернете пространство имен служебной шины с очередью.

[Очереди служебной шины](service-bus-queues-topics-subscriptions.md#queues) доставляют сообщения конкурирующим получателям по типу FIFO (в порядке очереди).

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Шаблон определяет следующие параметры.

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

### <a name="servicebusqueuename"></a>serviceBusQueueName

Имя очереди, создаваемой в пространстве имен служебной шины.

```
"serviceBusQueueName": {
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

Создает стандартное пространство имен служебной шины типа **Messaging**с очередью.

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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы создали и развернули ресурсы с помощью диспетчера ресурсов Azure, узнайте, как управлять этими ресурсами, изучив следующие статьи:

- [Управление служебной шиной с помощью PowerShell](service-bus-powershell-how-to-provision.md)
- [Управление ресурсами служебной шины с помощью обозревателя служебной шины](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Шаблон пространства имен служебной шины и очереди]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [шаблонов быстрого запуска Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Дополнительные сведения об очередях служебной шины]: service-bus-queues-topics-subscriptions.md
  [Использование Azure PowerShell с Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Использование интерфейса командной строки Azure для Mac, Linux и Windows со службой управления ресурсами Azure]: ../xplat-cli-azure-resource-manager.md



<!--HONumber=Oct16_HO2-->


