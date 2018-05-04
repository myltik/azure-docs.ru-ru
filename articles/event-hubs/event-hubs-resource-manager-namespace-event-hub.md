---
title: Создание пространства имен и группы потребителей концентраторов событий Azure с помощью шаблона | Документация Майкрософт
description: Создание пространства имен концентраторов событий c концентратором событий и группой потребителей с помощью шаблонов Azure Resource Manager.
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 4b4dc5be9697bb96aec658fccbdf13b299e79e9e
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Создание пространства имен концентраторов событий с концентратором событий и группой потребителей с помощью шаблона Azure Resource Manager

Из этой статьи вы узнаете, как с помощью шаблона Azure Resource Manager создать пространство имен типа [концентраторы событий](event-hubs-what-is-event-hubs.md) с одним концентратором событий и одной группой потребителей. Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье о [создании шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates].

Полный шаблон приведен в разделе [Event Hub and consumer group template][Event Hub and consumer group template] (Шаблон концентратора событий и группы потребителей) на сайте GitHub.

> [!NOTE]
> Чтобы узнать о новых шаблонах, изучите коллекцию [Шаблоны быстрого запуска Azure][Azure Quickstart Templates] и выполните в ней поиск по запросу "концентраторы событий".
> 
> 

## <a name="what-will-you-deploy"></a>Что вы развернете?

С помощью этого шаблона вы развернете пространство имен "Концентраторы событий" с концентратором событий и группой потребителей.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для изменяющихся значений нужно определить параметры с учетом развертываемого проекта либо окружения, где выполняется развертывание. Не определяйте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Имя создаваемого пространства имен концентраторов событий.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Имя концентратора событий, создаваемого в пространстве имен концентраторов событий.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Имя группы потребителей, создаваемой для концентратора событий.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>версия_API

Версия API шаблона.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Развертываемые ресурсы

Создает пространство имен типа **EventHubs** с концентратором событий и группой потребителей.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
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

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
