---
title: Обновление моделей машинного обучения с помощью фабрики данных Azure | Документация Майкрософт
description: Описывается, как создавать прогнозирующие конвейеры с помощью фабрики данных Azure и машинного обучения Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d1d4c847916b76785cbee40a12b59387498fb5c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621223"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс"

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Действие Hive](data-factory-hive-activity.md) 
> * [Действие Pig](data-factory-pig-activity.md)
> * [Действие MapReduce](data-factory-map-reduce.md)
> * [Потоковая активность Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Действие Spark](data-factory-spark.md)
> * [Действие выполнения пакета машинного обучения](data-factory-azure-ml-batch-execution-activity.md)
> * [Действие "Обновить ресурс" в службе машинного обучения](data-factory-azure-ml-update-resource-activity.md)
> * [Действие хранимой процедуры](data-factory-stored-proc-activity.md)
> * [Действие U-SQL в Data Lake Analytics](data-factory-usql-activity.md)
> * [Настраиваемое действие .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Статья относится к версии 1 фабрики данных, которая является общедоступной версией. Если вы используете версию 2 службы фабрики данных, которая находится на этапе предварительной версии, ознакомьтесь с [обновлением моделей машинного обучения в фабрике данных версии 2](../update-machine-learning-models.md).

Эта статья дополняет основную статью, посвященную интеграции фабрики данных Azure и машинного обучения Azure: [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](data-factory-azure-ml-batch-execution-activity.md). Перед прочтением этой статьи ознакомьтесь с основной статьей, если вы еще этого не сделали. 

## <a name="overview"></a>Обзор
Со временем прогнозные модели из оценивающих экспериментов машинного обучения Azure потребуют повторного обучения с помощью новых наборов входных данных. Когда повторное обучение будет завершено, вам потребуется обновить веб-службу оценки на основании обновленной модели машинного обучения. Для использования новых данных и обновления моделей машинного обучения Azure через веб-службы выполните следующие действия.

1. Создайте эксперимент в [Студии машинного обучения Azure](https://studio.azureml.net).
2. Если модель вас устраивает, то опубликуйте веб-службы для **обучающего эксперимента** и оценивающего или **прогнозного эксперимента** с помощью Студии машинного обучения Microsoft Azure.

В следующей таблице описаны веб-службы, используемые в данном примере.  Подробные сведения см. в статье [Программное переобучение моделей машинного обучения](../../machine-learning/machine-learning-retrain-models-programmatically.md).

- **Веб-служба обучения**: получает данные для обучения и создает обученные модели. Результат повторного обучения — файл формата ILEARNER в хранилище BLOB-объектов Azure. При публикации обучающего эксперимента в виде веб-службы автоматически создается **конечная точка по умолчанию** . Вы можете создать несколько конечных точек, но в этом примере используется только конечная точка по умолчанию.
- **Веб-служба оценки**: получает данные без метки и осуществляет прогнозирование. Результаты прогноза могут записываться в различных форматах, например в CSV-файлы или строки в базе данных SQL Azure (в зависимости от конфигурации эксперимента). При публикации прогнозного эксперимента в виде веб-службы автоматически создается конечная точка по умолчанию. 

На следующей схеме показана связь между конечными точками обучения и конечными точками оценки в машинном обучении Azure.

![ВЕБ-СЛУЖБЫ](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Чтобы вызвать **веб-службу обучения**, воспользуйтесь **действием выполнения пакета машинного обучения Azure**. Это действие аналогично обращению к веб-службе машинного обучения Azure (веб-службе оценки) для получения данных оценки. В предыдущих разделах подробно описано, как обращаться к веб-службе машинного обучения Azure из конвейера фабрики данных. 

Чтобы обновить веб-службу с помощью заново обученной модели, можно вызвать **веб-службу оценки**, воспользовавшись **действием ресурса обновления машинного обучения Azure**. Ниже приведены примеры определений связанной службы. 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Веб-служба оценки — классическая веб-служба
Если веб-служба оценки является **классической веб-службой**, то создайте вторую **обновляемую конечную точку не по умолчанию** с помощью портала Azure. Инструкции см. в статье [Создание конечных точек](../../machine-learning/machine-learning-create-endpoint.md). Создав обновляемую конечную точку не по умолчанию, выполните следующие действия:

* Щелкните **Выполнение пакета**, чтобы получить значение URI свойства JSON **mlEndpoint**.
* Затем щелкните **Обновить ресурс**, чтобы получить значение URI свойства JSON **updateResourceEndpoint**. Ключ API указывается на странице конечной точки (в правом нижнем углу).

![обновляемая конечная точка](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

В следующем примере представлен вариант определения JSON для связанной службы AzureML. Связанная служба использует для аутентификации ключ apiKey.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Веб-служба оценки — веб-служба Azure Resource Manager 
Если веб-служба является новым типом веб-службы и предоставляет конечную точку Azure Resource Manager, то необходимо добавить вторую конечную точку **не по умолчанию**. Конечная точка **updateResourceEndpoint** в связанной службе имеет такой формат: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Значения для заполнителей в URL-адресе при выполнении запросов к веб-службе можно найти на [портале веб-служб машинного обучения Azure](https://services.azureml.net/). Для нового типа конечной точки обновления ресурса требуется маркер AAD (Azure Active Directory). Укажите **servicePrincipalId** и **servicePrincipalKey** в связанной службе AzureML. Ознакомьтесь со статьей [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Ниже приведен пример определения связанной службы AzureML. 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Ниже приведен сценарий с более подробными сведениями. Он представляет пример повторного обучения и обновления моделей машинного обучения Azure из конвейера фабрики данных Azure.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Сценарий: повторное обучение и обновление модели машинного обучения Azure
В этом разделе показан пример конвейера, который использует **действие выполнения пакета машинного обучения Azure** для повторного обучения модели, а также **действие ресурса обновления машинного обучения Azure** для обновления модели в веб-службе оценки. Здесь также приведены фрагменты JSON для всех связанных служб, наборов данных и конвейера, которые используются в примере.

Пример конвейера представлен на схеме. Как видите, действие "Выполнение пакета" машинного обучения Azure принимает входные данные обучения и создает выходные данные обучения (файл iLearner). Действие «Обновить ресурс» машинного обучения Azure принимает эти результаты и обновляет модель через конечную точку веб-службы оценки. Действие «Обновить ресурс» не создает никаких выходных данных. Набор данных placeholderBlob является фиктивным набором данных, который необходим фабрике данных Azure для запуска конвейера.

![схема конвейера](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Связанная служба хранилища BLOB-объектов Azure
Служба хранилища Azure содержит следующие данные:

* Данные для обучения. Это входные данные для веб-службы машинного обучения Azure.  
* Файл iLearner. Это выходные данные из веб-службы машинного обучения Azure. Этот файл также служит в качестве входных данных для действия "Обновить ресурс".  

Вот пример определения JSON для связанной службы:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Входной набор данных для обучения:
Следующий набор данных представляет собой учебные данные для веб-службы машинного обучения Azure. Действие «Выполнение пакета» машинного обучения Azure использует этот набор в качестве входных данных.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Выходной набор данных для обучения:
Следующий набор данных представляет собой выходной файл iLearner из веб-службы машинного обучения Azure. Этот набор данных создается с помощью действия "Выполнение пакета" машинного обучения Azure. Этот набор данных также является входным для действия "Обновить ресурс" машинного обучения Azure.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Связанная служба для конечной точки машинного обучения Azure
Следующий фрагмент JSON определяет связанную службу машинного обучения Azure, которая указывает на конечную точку веб-службы обучения по умолчанию.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

Чтобы получить значения **mlEndpoint** и **apiKey**, выполните следующие действия в **Студии машинного обучения Azure**:

1. В меню слева щелкните **ВЕБ-СЛУЖБЫ** .
2. В списке веб-служб выберите **веб-службу обучения** .
3. Рядом с полем **Ключ API** нажмите кнопку копирования. Вставьте ключ из буфера обмена в редактор JSON фабрики данных.
4. В **Студии машинного обучения Azure** щелкните ссылку **Выполнение пакета**.
5. Скопируйте **URI запроса** из раздела **Запрос** и вставьте его в редактор JSON фабрики данных.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Связанная служба для обновляемой конечной точки оценки машинного обучения Azure
Следующий фрагмент JSON определяет связанную службу машинного обучения Azure, которая указывает на конечную точку веб-службы оценки не по умолчанию.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Заполнитель выходного набора данных
Действие "Обновить ресурс" машинного обучения Azure не создает никаких выходных данных. Однако фабрике данных Azure требуется выходной набор данных для планирования конвейера. Поэтому в этом примере используется фиктивный набор данных (заполнитель набора данных).  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Конвейер
Конвейер содержит два действия: **AzureMLBatchExecution** и **AzureMLUpdateResource**. Действие "Выполнение пакета" машинного обучения Azure принимает входные данные для обучения и создает выходной файл iLearner. Это действие обращается к веб-службе обучения (обучающему эксперименту, опубликованному в виде веб-службы), передает ей данные для обучения и получает файл ilearner. Набор данных placeholderBlob является фиктивным набором данных, который необходим фабрике данных Azure для запуска конвейера.

![схема конвейера](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
