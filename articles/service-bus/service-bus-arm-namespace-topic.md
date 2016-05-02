<properties
    pageTitle="Создание пространства имен служебной шины с разделом и подпиской | Microsoft Azure"
    description="Создайте пространство имен служебной шины с разделом и подпиской с помощью шаблона ARM"
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
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Создание пространства имен служебной шины с разделом и подпиской с помощью шаблона ARM

В этой статье показывается, как использовать шаблон Azure Resource Manager (ARM), создающий пространство имен служебной шины с разделом и подпиской. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или изменить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][].

Полный шаблон приведен в разделе [Шаблон пространства имен служебной шины с разделом и подпиской][].

>[AZURE.NOTE] Для загрузки и развертывания доступны следующие шаблоны ARM.
>
>-    [Создание пространства имен служебной шины с очередью и правилом авторизации](service-bus-arm-namespace-auth-rule.md)
>-    [Создание пространства имен служебной шины с концентратором событий и группой потребителей](service-bus-arm-namespace-event-hub.md)
>-    [Создание пространства имен служебной шины с очередью](service-bus-arm-namespace-queue.md)
>-    [Создайте пространство имен служебной шины](service-bus-arm-namespace.md)
>
>Для получения последних шаблонов обратитесь к статье [Шаблоны быстрого запуска Azure][] и введите в строке поиска "Служебная шина".

## Что вы развернете?

С помощью этого шаблона вы развернете пространство имен служебной шины с разделом и подпиской.

Разделы и подписки предоставляют вид взаимодействия "один ко многим" в рамках шаблона *публикации или подписки*.

[Ознакомьтесь с дополнительными сведениями о разделах и подписках служебной шины][].

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/service-bus-arm-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters`, содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Далее следует описание каждого параметра в шаблоне.

### serviceBusNamespaceName

Имя создаваемого пространства имен служебной шины.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusTopicName

Имя раздела, создаваемого в пространстве имен служебной шины.

```
"serviceBusTopicName": {
"type": "string"
}
```

### serviceBusSubscriptionName

Имя подписки, создаваемой в пространстве имен служебной шины.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```

### serviceBusApiVersion

Версия API служебной шины для шаблона.

```
" serviceBusApiVersion": {
"type": "string"
}
```
## Развертываемые ресурсы

Создает стандартное пространство имен служебной шины типа **Messaging** с разделом и подпиской.

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

## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## Инфраструктура CLI Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## Дальнейшие действия

Теперь, когда вы создали и развернули ресурсы с помощью ARM, узнайте, как управлять этими ресурсами, изучив следующие статьи:

- [Управление служебной шиной Azure при помощи службы автоматизации Azure](service-bus-automation-manage.md)
- [Управление служебной шиной с помощью PowerShell](service-bus-powershell-how-to-provision.md)
- [Управление ресурсами служебной шины с помощью обозревателя служебной шины](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Шаблоны быстрого запуска Azure]: https://azure.microsoft.com/documentation/templates/
  [Ознакомьтесь с дополнительными сведениями о разделах и подписках служебной шины]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Шаблон пространства имен служебной шины с разделом и подпиской]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/

<!---HONumber=AcomDC_0420_2016-->