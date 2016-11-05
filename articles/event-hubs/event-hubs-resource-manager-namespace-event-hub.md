---
title: Создание пространства имен концентратора событий, самого концентратора событий и группы потребителей с помощью шаблона Azure Resource Manager | Microsoft Docs
description: Создание пространства имен концентраторов событий, концентратора событий и группы потребителей с помощью шаблона Azure Resource Manager.
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/31/2016
ms.author: sethm;shvija

---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Создание пространства имен концентраторов событий с концентратором событий и группой потребителей с помощью шаблона Azure Resource Manager
Из этой статьи вы узнаете, как с помощью шаблона Azure Resource Manager создать пространство имен концентраторов событий с концентратором событий и группой потребителей. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или изменить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][Создание шаблонов Azure Resource Manager].

Полный шаблон приведен в разделе [Event Hub and consumer group template][Event Hub and consumer group template] (Шаблон концентратора событий и группы потребителей) на сайте GitHub.

> [!NOTE]
> Чтобы узнать о новых шаблонах, посетите коллекцию [Шаблоны быстрого запуска Azure][Шаблоны быстрого запуска Azure] и выполните поиск по запросу "концентраторы событий".
> 
> 

## <a name="what-will-you-deploy?"></a>Что вы развернете?
С помощью этого шаблона вы развернете пространство имен концентраторов событий с концентратором событий и группой потребителей.

[Концентраторы событий](event-hubs-what-is-event-hubs.md) — это служба обработки событий, используемая для крупномасштабной передачи данных событий и телеметрии в Azure. Работа службы характеризуется низкой задержкой и высокой надежностью.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры
С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
Имя создаваемого пространства имен концентраторов событий.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
Имя концентратора событий, создаваемого в пространстве имен концентраторов событий.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
Имя группы потребителей, создаваемой для концентратора событий.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>версия_API
Версия API шаблона.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Развертываемые ресурсы
Создает пространство имен типа **EventHub**с концентратором событий и группой потребителей.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Создание шаблонов Azure Resource Manager]: ../resource-group-authoring-templates.md
[Шаблоны быстрого запуска Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Использование Azure PowerShell с Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Использование интерфейса командной строки Azure для Mac, Linux и Windows со службой управления ресурсами Azure]: ../xplat-cli-azure-resource-manager.md
[Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/



<!--HONumber=Oct16_HO2-->


