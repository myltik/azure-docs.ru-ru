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
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017


---
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>

# Создание пространства имен концентраторов событий с концентратором событий и включение записи с помощью шаблона Azure Resource Manager
Из этой статьи вы узнаете, как с помощью шаблона Azure Resource Manager создать пространство имен концентраторов событий с одним экземпляром концентратора событий и включить для него функцию записи. Здесь вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или изменить его в соответствии с вашими требованиями.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager][Authoring Azure Resource Manager templates].

Дополнительные сведения о практиках и шаблонах соглашений об именовании ресурсов Azure см. в разделе [Recommended naming conventions for Azure resources][Azure Resources Naming Conventions] (Рекомендуемые соглашения об именовании ресурсов Azure).

Полный шаблон для создания концентратора событий и включения записи доступен [на сайте GitHub][Event Hub and enable Capture template].

> [!NOTE]
> Чтобы узнать о новых шаблонах, изучите коллекцию [Шаблоны быстрого запуска Azure][Azure Quickstart Templates] и выполните в ней поиск по запросу "концентраторы событий".
> 
> 

<a id="what-will-you-deploy" class="xliff"></a>

## Что вы развернете?
С помощью этого шаблона вы развернете пространство имен концентраторов событий с концентратором событий и включите [запись концентраторов событий](event-hubs-capture-overview.md).

[Концентраторы событий](event-hubs-what-is-event-hubs.md) — это служба обработки событий, используемая для крупномасштабной передачи данных событий и телеметрии в Azure. Работа службы характеризуется низкой задержкой и высокой надежностью. Функция записи концентраторов событий позволяет автоматически доставлять потоковые данные из концентраторов событий в выбранное хранилище BLOB-объектов Azure с указанным интервалом времени или размера.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

<a id="parameters" class="xliff"></a>

## Параметры
С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел `Parameters` , содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не определяйте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Ниже описаны параметры, которые определяет шаблон.

<a id="eventhubnamespacename" class="xliff"></a>

### eventHubNamespaceName
Имя создаваемого пространства имен концентраторов событий.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

<a id="eventhubname" class="xliff"></a>

### eventHubName
Имя концентратора событий, создаваемого в пространстве имен концентраторов событий.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

<a id="messageretentionindays" class="xliff"></a>

### messageRetentionInDays
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

<a id="partitioncount" class="xliff"></a>

### partitionCount
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

<a id="captureenabled" class="xliff"></a>

### captureEnabled
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
<a id="captureencodingformat" class="xliff"></a>

### captureEncodingFormat
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

<a id="capturetime" class="xliff"></a>

### captureTime
Интервал времени, согласно которому функция записи концентратора событий начинает запись данных в хранилище BLOB-объектов Azure.

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

<a id="capturesize" class="xliff"></a>

### captureSize
Интервал размера, согласно которому функция записи начинает запись данных в хранилище BLOB-объектов Azure.

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

<a id="destinationstorageaccountresourceid" class="xliff"></a>

### destinationStorageAccountResourceId
Функции записи требуется идентификатор ресурса учетной записи хранения Azure для включения записи в нужной учетной записи хранения.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

<a id="blobcontainername" class="xliff"></a>

### blobContainerName
Контейнер больших двоичных объектов, в который необходимо записывать данные события.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


<a id="apiversion" class="xliff"></a>

### версия_API
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

<a id="resources-to-deploy" class="xliff"></a>

## Развертываемые ресурсы
Создает пространство имен типа **EventHubs** с одним концентратором событий и включает запись.

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

<a id="commands-to-run-deployment" class="xliff"></a>

## Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

<a id="powershell" class="xliff"></a>

## PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

<a id="azure-cli" class="xliff"></a>

## Инфраструктура CLI Azure
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
<a id="next-steps" class="xliff"></a>

## Дальнейшие действия

Функцию записи концентраторов событий можно включить с помощью [портала Azure](https://portal.azure.com). Дополнительные сведения см. в статье [Enable Event Hubs Capture using the Azure portal](event-hubs-capture-enable-through-portal.md) (Включение функции записи концентраторов событий с помощью портала Azure).

Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

