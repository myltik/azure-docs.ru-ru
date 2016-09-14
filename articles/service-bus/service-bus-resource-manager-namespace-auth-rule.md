<properties
    pageTitle="Создание правила авторизации служебной шины с помощью шаблона диспетчера ресурсов Azure | Microsoft Azure"
    description="Создание правила авторизации служебной шины для пространства имен и очереди с помощью шаблона диспетчера ресурсов Azure"
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

# Создание правила авторизации служебной шины для пространства имен и очереди с помощью шаблона диспетчера ресурсов Azure

В этой статье показывается, как использовать шаблон Azure Resource Manager, который создает [правило авторизации](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) для пространства имен и очереди служебной шины. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][].

Полный шаблон доступен в разделе [Шаблон правила проверки подлинности для служебной шины][] в GitHub.

>[AZURE.NOTE] Для скачивания и развертывания можно использовать указанные ниже шаблоны диспетчера ресурсов Azure.
>
>-    [Создание пространства имен концентратора событий с концентратором событий и группой потребителей](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Создание пространства имен служебной шины с очередью](service-bus-resource-manager-namespace-queue.md)
>-    [Создание пространства имен служебной шины с разделом и подпиской](service-bus-resource-manager-namespace-topic.md)
>-    [Создайте пространство имен служебной шины](service-bus-resource-manager-namespace.md)
>
>Чтобы узнать о новых шаблонах, в коллекции [шаблонов быстрого запуска Azure][] выполните поиск по запросу "служебная шина".

## Что вы развернете?

С помощью этого шаблона вы развернете правило авторизации служебной шины для пространства имен и сущности обмена сообщениями (в нашем примере — очереди).

Для проверки подлинности этот шаблон использует [подписанный URL-адрес (SAS)](service-bus-sas-overview.md). Подписанный URL-адрес (SAS) позволяет проверять подлинность приложений в служебной шине с помощью ключа доступа, настроенного в пространстве имен или в сущности обмена сообщениями (очереди или разделе) с определенными правами. Этот ключ можно использовать для создания маркера SAS, который клиенты могут использовать при проверке подлинности в служебной шине.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters`, содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Шаблон определяет следующие параметры.

### serviceBusNamespaceName

Имя создаваемого пространства имен служебной шины.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### namespaceAuthorizationRuleName 

Имя правила авторизации для пространства имен.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### serviceBusQueueName

Имя очереди в пространстве имен служебной шины.

```
"serviceBusQueueName": {
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

Создает стандартное пространство имен служебной шины типа **Messaging** и правило авторизации служебной шины для пространства имен и сущности.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Инфраструктура CLI Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Дальнейшие действия

Теперь, когда вы создали и развернули ресурсы с помощью диспетчера ресурсов Azure, узнайте, как управлять этими ресурсами, изучив следующие статьи:

- [Управление служебной шиной Azure при помощи службы автоматизации Azure](service-bus-automation-manage.md)
- [Управление служебной шиной с помощью PowerShell](service-bus-powershell-how-to-provision.md)
- [Управление ресурсами служебной шины с помощью обозревателя служебной шины](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Аутентификация и авторизация в служебной шине](service-bus-authentication-and-authorization.md)

  [Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
  [шаблонов быстрого запуска Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Шаблон правила проверки подлинности для служебной шины]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/

<!---HONumber=AcomDC_0831_2016-->