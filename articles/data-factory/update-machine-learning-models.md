---
title: Обновление моделей машинного обучения с помощью фабрики данных Azure | Документация Майкрософт
description: Здесь описывается, как создавать прогнозирующие конвейеры с помощью фабрики данных Azure и машинного обучения.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 931c6b2cc0230e4605089dfc1eb0764aa61ec7b8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620468"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Обновление моделей машинного обучения Azure с помощью действия обновления ресурса
Эта статья дополняет основную статью, посвященную интеграции фабрики данных Azure и машинного обучения Azure: [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](transform-data-using-machine-learning.md). Перед прочтением этой статьи ознакомьтесь с основной статьей, если вы еще этого не сделали. 

## <a name="overview"></a>Обзор
В рамках процесса ввода в эксплуатацию моделей Машинного обучения Azure ваша модель будет обучена и сохранена. После этого она используется для создания прогнозной веб-службы. Созданная веб-служба может впоследствии использоваться на веб-сайтах, панелях мониторинга и в мобильных приложениях.

Модели, создаваемые с помощью машинного обучения, обычно не статические. Если появляются новые данные или у пользователя API есть свои данные, модель нужно переобучить. Дополнительные сведения о переобучении модели Машинного обучения Azure см. в статье [Переобучение модели машинного обучения](../machine-learning/machine-learning-retrain-machine-learning-model.md). 

Переобучение может требоваться достаточно часто. С помощью действия выполнения пакета и действия обновления ресурса можно переобучить модель Машинного обучения Azure и обновить прогнозную веб-службу с помощью фабрики данных. 

На следующем рисунке показа связь между обучением и прогнозными веб-службами. 

![ВЕБ-СЛУЖБЫ](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Действие обновления ресурса в службе машинного обучения Azure 

Следующий фрагмент кода JSON определяет действие выполнения пакета в службе "Машинное обучение Azure".

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```




| Свойство                      | ОПИСАНИЕ                              | Обязательно |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | Имя действия в конвейере.     | Yes      |
| description                   | Описание действия.  | Нет        |
| Тип                          | Для действия обновления ресурса в службе машинного обучения Azure тип действия — **AzureMLUpdateResource**. | Yes      |
| linkedServiceName             | Связанная служба машинного обучения Azure, которая содержит свойство updateResourceEndpoint. | Yes      |
| trainedModelName              | Имя модуля модели обучения для обновления в эксперименте веб-службы. | Yes      |
| trainedModelLinkedServiceName | Имя связанной службы хранилища Azure, содержащей файл iLearner, который был отправлен действием обновления. | Yes      |
| trainedModelFilePath          | Относительный путь к файлу в trainedModelLinkedService для представления файла iLearner, который отправлен действием обновления. | Yes      |


## <a name="end-to-end-workflow"></a>Комплексный рабочий процесс

Процесс переобучения модели и обновления прогнозных веб-служб включает в себя такие шаги: 

- Вызов **веб-службы обучения** с помощью **действия выполнения пакета**. Вызов веб-службы обучения выполняется так же, как вызов прогнозной веб-службы, описанный в статье [Create predictive pipelines using Azure Machine Learning and Azure Data Factory](transform-data-using-machine-learning.md) (Создание прогнозных конвейеров с помощью Машинного обучения Azure и фабрики данных Azure). Выходные данные веб-службы обучения — это файл iLearner, который можно использовать для обновления прогнозной веб-службы. 
- Вызов **обновления конечной точки ресурса** **прогнозной веб-службы** с помощью **действия обновления ресурса**, чтобы обновить веб-службу и добавить новую обученную модель. 

## <a name="azure-machine-learning-linked-service"></a>Связанная служба Машинного обучения Azure

Чтобы вышеупомянутые комплексные рабочие процессы выполнялись, необходимо создать две связанные службы машинного обучения Azure: 

1. Связанная служба машинного обучения Azure для веб-службы обучения, которая используется действием выполнения пакета так же, как описывается в статье [Create predictive pipelines using Azure Machine Learning and Azure Data Factory](transform-data-using-machine-learning.md) (Создание прогнозных конвейеров с помощью Машинного обучения Azure и фабрики данных Azure). Разница заключается в том, что выходные данные веб-службы обучения являются файлом iLearner, который используется действием обновления ресурса для обновления прогнозной веб-службы. 
2. Связанная служба машинного обучения Azure для обновления конечной точки ресурса прогнозной веб-службы. Эта связанная служба используется действием обновления ресурса для обновления прогнозной веб-службы с помощью файла iLearner, возвращенного на предыдущем шаге. 

Конфигурация второй связанной службы машинного обучения Azure отличается, если веб-служба машинного обучения Azure является классической веб-службой или новой веб-службой. Различия отдельно рассматриваются в следующих разделах. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Веб-служба — новая веб-служба Azure Resource Manager 

Если веб-служба является новым типом веб-службы и предоставляет конечную точку Azure Resource Manager, то необходимо добавить вторую конечную точку **не по умолчанию**. Конечная точка **updateResourceEndpoint** в связанной службе имеет такой формат: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Значения для заполнителей в URL-адресе при выполнении запросов к веб-службе можно найти на [портале веб-служб машинного обучения Azure](https://services.azureml.net/). 

Новому типу конечной точки ресурса для обновления необходима проверка подлинности субъекта-службы. Чтобы использовать проверку подлинности субъекта-службы, зарегистрируйте сущность приложения в Azure Active Directory (Azure AD) и предоставьте ей роль **участника** или **владельца** подписки или группы ресурсов, которой принадлежит веб-служба. Ознакомьтесь со статьей [Создание приложения Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md). Запишите следующие значения, которые используются для определения связанной службы:

- Идентификатор приложения
- Ключ приложения 
- Tenant ID

Ниже приведен пример определения связанной службы: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
            "type": "SecureString",
            "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
            "type": "SecureString",
            "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Ниже приведен сценарий с более подробными сведениями. Он представляет пример повторного обучения и обновления моделей машинного обучения Azure из конвейера фабрики данных Azure.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Пример. Переобучение и обновление модели Машинного обучения Azure

В этом разделе показан пример конвейера, который использует **действие выполнения пакета машинного обучения Azure** для повторного обучения модели, а также **действие ресурса обновления машинного обучения Azure** для обновления модели в веб-службе оценки. Здесь также приведены фрагменты JSON для всех связанных служб, наборов данных и конвейера, которые используются в примере.

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

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Связанная служба для обновляемой конечной точки оценки Машинного обучения Azure:
Следующий фрагмент JSON определяет связанную службу машинного обучения Azure, которая указывает на конечную точку веб-службы оценки.  

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

### <a name="pipeline"></a>Конвейер
Конвейер содержит два действия: **AzureMLBatchExecution** и **AzureMLUpdateResource**. Действие выполнения пакета принимает входные данные для обучения и создает выходной файл iLearner. Действие ресурса обновления принимает этот файл iLearner и использует его для обновления прогнозной веб-службы. 

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            }, 
                            "FilePath":"azuremltesting/input"
                        }, 
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference" 
                            }, 
                            "FilePath":"azuremltesting/input"
                        }        
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"   
                            }, 
                            "FilePath":"azuremltesting/output"
                        }        
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                                "type": "LinkedServiceReference",
                                "referenceName": "StorageLinkedService"
                            },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [ 
                    { 
                        "activity": "amlbeGetilearner", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ]

            }
        ]
    }
}
```
## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами: 

* [Действие U-SQL](transform-data-using-data-lake-analytics.md)
* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)
