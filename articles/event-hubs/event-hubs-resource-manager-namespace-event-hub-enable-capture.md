---
title: "Создание пространства имен концентраторов событий Azure и включение записи с помощью шаблона | Документация Майкрософт"
description: "Создание пространства имен концентраторов событий Azure с одним концентратором событий и включение записи с помощью шаблона Azure Resource Manager."
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 19bbb51868e767aa1d15f4574628b7fd36607207
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---

# <a name="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template"></a>Создание пространства имен концентраторов событий с концентратором событий и включение записи с помощью шаблона Azure Resource Manager

Из этой статьи вы узнаете, как с помощью шаблона Azure Resource Manager создать пространство имен концентраторов событий с одним экземпляром концентратора событий и включить для него [функцию записи](event-hubs-capture-overview.md). Здесь вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

В этой статье также показано, как настроить запись событий в хранилище BLOB-объектов Azure или Azure Data Lake Store в зависимости от выбранного назначения.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates].

Дополнительные сведения о шаблонах и рекомендациях по соглашениям об именовании ресурсов Azure см. в статье [Naming conventions][Azure Resources naming conventions] (Соглашения об именовании).

Полные шаблоны доступны на сайте GitHub по следующим ссылкам:

- [шаблон для развертывания концентратора событий и включения записи в службу хранилища][Event Hub and enable Capture to Storage template]; 
- [шаблон для развертывания концентратора событий и включения записи в Azure Data Lake Store][Event Hub and enable Capture to Azure Data Lake Store template].

> [!NOTE]
> Чтобы узнать о новых шаблонах, изучите коллекцию [Шаблоны быстрого запуска Azure][Azure Quickstart Templates] и выполните в ней поиск по запросу "концентраторы событий".
> 
> 

## <a name="what-will-you-deploy"></a>Что вы развернете?

С помощью этого шаблона вы развернете пространство имен концентраторов событий с концентратором событий и включите [запись концентраторов событий](event-hubs-capture-overview.md).

[Концентраторы событий](event-hubs-what-is-event-hubs.md) — это служба обработки событий, используемая для крупномасштабной передачи данных событий и телеметрии в Azure. Работа службы характеризуется низкой задержкой и высокой надежностью. Функция записи концентраторов событий позволяет автоматически доставлять потоковые данные из концентраторов событий в хранилище BLOB-объектов Azure или Azure Data Lake Store с указанным интервалом времени или размера.

Чтобы включить запись данных из концентраторов событий в службу хранилища Azure, нажмите эту кнопку:

[![Развертывание в Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Чтобы включить запись данных из концентраторов событий в Azure Data Lake Store, нажмите эту кнопку:

[![Развертывание в Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не определяйте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Имя создаваемого [пространства имен концентраторов событий](event-hubs-create.md).

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Имя концентратора событий, создаваемого в [пространстве имен концентраторов событий](event-hubs-create.md).

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

Число дней для хранения сообщений в концентраторе событий. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

Число секций, создаваемых в концентраторе событий.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

Позволяет включить запись для концентратора событий.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

Формат кодировки, указываемый для сериализации данных событий.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Интервал времени, согласно которому функция записи концентраторов событий начинает запись данных.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
Интервал размера, согласно которому функция записи начинает запись данных.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

###<a name="capturenameformat"></a>captureNameFormat

Формат имени, используемый функцией записи концентраторов событий, для записи файлов Avro. Обратите внимание, что в формате имени для функции записи должны быть поля `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}`, и `{Second}`. Они могут быть размещены в любом порядке, с разделителями или без них.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  }
```

### <a name="apiversion"></a>версия_API

Версия API шаблона.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Если в качестве места назначения вы выбрали службу хранилища Azure, используйте следующие параметры.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

Функции записи требуется идентификатор ресурса учетной записи хранения Azure для включения записи в нужной учетной записи хранения.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

Контейнер больших двоичных объектов, в который необходимо записывать данные события.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Если в качестве места назначения вы выбрали Azure Data Lake Store, используйте указанные ниже параметры. Необходимо назначить разрешения для пути к хранилищу Data Lake Store, в которое необходимо записывать события. Сведения о назначении разрешений см. в [этой статье](event-hubs-capture-enable-through-portal.md#capture-data-to-an-azure-data-lake-store-account).

###<a name="subscriptionid"></a>subscriptionId

Идентификатор подписки для пространства имен концентраторов событий и Azure Data Lake Store. Оба ресурса должны использовать один и тот же идентификатор подписки.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription Id of both Azure Data Lake Store and Event Hub namespace"
     }
 }
```

###<a name="datalakeaccountname"></a>dataLakeAccountName

Имя Azure Data Lake Store для записываемых событий.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

###<a name="datalakefolderpath"></a>dataLakeFolderPath

Путь к целевой папке для записываемых событий.

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination archive folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>Развертываемые ресурсы для службы хранилища Azure как места назначения для записи событий

Здесь создается пространство имен типа **EventHubs** с одним концентратором событий и включается запись в хранилище BLOB-объектов Azure.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
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
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Развертываемые ресурсы для Azure Data Lake Store как места назначения

Здесь создается пространство имен типа **EventHubs** с одним концентратором событий и включается запись в Azure Data Lake Store.

```json
 "resources": [
        {
            "apiVersion": "2015-08-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2015-08-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "ArchiveDescription": {
                            "enabled": "true",
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('archiveTime')]",
                            "sizeLimitInBytes": "[parameters('archiveSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('archiveNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

Разверните шаблон, чтобы включить запись данных из концентраторов событий в службу хранилища Azure.
 
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

Разверните шаблон, чтобы включить запись данных из концентраторов событий в Azure Data Lake Store.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Выбор хранилища BLOB-объектов Azure в качестве места назначения:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Выбор Azure Data Lake Store в качестве места назначения:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>Дальнейшие действия

Функцию записи концентраторов событий можно включить с помощью [портала Azure](https://portal.azure.com). Дополнительные сведения см. в статье [Enable Event Hubs Capture using the Azure portal](event-hubs-capture-enable-through-portal.md) (Включение функции записи концентраторов событий с помощью портала Azure).

Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
