.<properties
    pageTitle="Создание пространства имен концентратора событий, самого концентратора событий и группы потребителей с помощью шаблона Azure Resource Manager | Microsoft Azure"
    description="Создание пространства имен концентраторов событий, концентратора событий и группы потребителей с помощью шаблона Azure Resource Manager."
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>

# Создание пространства имен концентратора событий, самого концентратора событий и группы потребителей с помощью шаблона Azure Resource Manage

Из этой статьи вы узнаете, как использовать шаблон Azure Resource Manager, создающий пространство имен концентратора событий, сам концентратор событий и группу потребителей. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или изменить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][].

Полный шаблон приведен в разделе [Шаблон группы потребителей и концентратора событий служебной шины][] в GitHub.

>[AZURE.NOTE] Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure.
>
>-    [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Создание пространства имен служебной шины с очередью](service-bus-resource-manager-namespace-queue.md)
>-    [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
>-    [Создайте пространство имен служебной шины](service-bus-resource-manager-namespace.md)
>
>Чтобы узнать о новых шаблонах, в коллекции [шаблонов быстрого запуска Azure][] выполните поиск по запросу "служебная шина".

## Что вы развернете?

С помощью этого шаблона вы развернете пространство имен концентратора событий, сам концентратор событий и группу потребителей.

[Концентраторы событий](../event-hubs/event-hubs-what-is-event-hubs.md) — это служба обработки событий, используемая для крупномасштабной передачи входящих событий и данных телеметрии в Azure. Работа службы характеризуется низкой задержкой и высокой надежностью.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-eventhub-and-consumergroup%2Fazuredeploy.json)

## Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters`, содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

### eventHubNamespaceName

Имя создаваемого пространства имен концентратора событий.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### eventHubName

Имя концентратора событий, создаваемого в пространстве имен концентратора событий.

```
"eventHubName": {
"type": "string"
}
```

### eventHubConsumerGroupName

Имя группы потребителей, создаваемой для концентратора событий в пространстве имен служебной шины.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### serviceBusApiVersion

Версия API служебной шины для шаблона.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## Развертываемые ресурсы

Создает пространство имен служебной шины типа **EventHub** с концентратором событий и группой получателей.

```
"resources": [
        {
            "apiVersion": "[variables('ehVersion')]",
            "name": "[parameters('eventHubNamespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "kind": "EventHub",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('ehVersion')]",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('ehVersion')]",
                            "name": "[parameters('eventHubConsumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {
                            }
                        }
                    ]
                }
            ]
        }
    ]
```

## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json
```

## Инфраструктура CLI Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json][]
```

## Дальнейшие действия

Теперь, когда вы создали и развернули ресурсы с помощью диспетчера ресурсов Azure, узнайте, как управлять этими ресурсами, изучив следующие статьи:

- [Управление служебной шиной Azure при помощи службы автоматизации Azure](service-bus-automation-manage.md)
- [Управление концентраторами событий с помощью PowerShell](service-bus-powershell-how-to-provision.md)
- [Управление ресурсами концентратора событий с помощью обозревателя служебной шины](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
  [шаблонов быстрого запуска Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Шаблон группы потребителей и концентратора событий служебной шины]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-eventhub-and-consumergroup/

<!---HONumber=AcomDC_0817_2016-->