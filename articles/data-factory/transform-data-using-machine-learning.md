---
title: Создание прогнозирующих конвейеров данных с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как создать прогнозирующий конвейер с помощью действия выполнения пакета службы "Машинное обучение Azure" в фабрике данных Azure.
services: data-factory
documentationcenter: ''
author: shengcmsft
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 8d87facdf1724ddbcb3bb6d7c189d8ff41d44973
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Версия 2 — предварительная](transform-data-using-machine-learning.md)

[Машинное обучение Azure](https://azure.microsoft.com/documentation/services/machine-learning/) позволяет создавать, тестировать и развертывать решения для прогнозной аналитики. Этот процесс можно обобщить до трех этапов.

1. **Создание обучающего эксперимента**. Это можно сделать с помощью Студии машинного обучения Microsoft Azure, которая представляет собой среду совместной визуальной разработки, используемую для обучения и проверки модели прогнозной аналитики с использованием данных для обучения.
2. **Преобразование обучающего эксперимента в прогнозный**. Когда модель будет обучена с помощью существующих данных и готова для оценки новых данных, следует подготовить и оптимизировать эксперимент для оценки.
3. **Развертывание эксперимента в виде веб-службы**. Оценивающий эксперимент можно опубликовать в виде веб-службы Azure. С помощью конечной точки этой веб-службы вы можете отправлять данные в свою модель и получать из нее результаты прогнозов.  

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Создание прогнозирующих конвейеров с помощью Машинного обучения Azure и фабрики данных Azure](v1/data-factory-azure-ml-batch-execution-activity.md).


### <a name="data-factory-and-machine-learning-together"></a>Фабрика данных и машинное обучение вместе
Фабрика данных Azure позволяет легко создавать конвейеры, в которых для прогнозной аналитики используется опубликованная веб-служба [Машинного обучения Azure] [azure-machine-learning]. С помощью **действия выполнения пакета** в конвейере фабрики данных Azure можно обращаться к веб-службе машинного обучения Azure, чтобы создавать прогнозы по данным в пакете. 

Со временем прогнозные модели из оценивающих экспериментов машинного обучения Azure потребуют повторного обучения с помощью новых наборов входных данных. Чтобы повторно обучить модель машинного обучения Azure из конвейера фабрики данных, выполните следующие действия:

1. Опубликуйте обучающий (не прогнозный) эксперимент в виде веб-службы. Как и при публикации прогнозного эксперимента, это можно сделать в Студии машинного обучения Microsoft Azure.
2. Чтобы обратиться к веб-службе для получения обучающего эксперимента, воспользуйтесь действием «Выполнение пакета» машинного обучения Azure. По сути, действие «Выполнение пакета» машинного обучения Azure можно использовать для обращения как к веб-службе обучения, так и веб-службе оценки.

Когда повторное обучение будет завершено, обновите веб-службу оценки (прогнозный эксперимент, опубликованный в виде веб-службы) на основании обученной заново модели, используя **действие обновления ресурса машинного обучения Azure**. Дополнительные сведения см. в статье [Updating Azure Machine Learning models using Update Resource Activity](update-machine-learning-models.md) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").

## <a name="azure-machine-learning-linked-service"></a>Связанная служба Машинного обучения Azure

Создайте связанную службу **Машинного обучения Azure**, чтобы связывать веб-службы Машинного обучения Azure с фабрикой данных Azure. Связанная служба используется действием выполнения пакета службы "Машинное обучения Azure" и [действием обновления ресурса](update-machine-learning-models.md). 


```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "apiVersion" : "2017-09-01-preview",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

В статье [Вычисление поддерживаемых сред с помощью фабрики данных Azure](compute-linked-services.md) описаны свойства в определении JSON. 

Служба "Машинного обучения Azure" поддерживает как классические, так и новые веб-службы для прогнозного эксперимента. В фабрике данных можно выбрать один из них для использования. Чтобы получить сведения, необходимые для создания связанной службы машинного обучения Azure, перейдите на сайт https://services.azureml.net, где перечислены все (новые и классические) веб-службы. Щелкните веб-службу, к которой требуется получить доступ, а затем щелкните страницу **Использование**. Скопируйте **первичный ключ** для свойства **apiKey** и **запросы пакетной службы** для свойства **mlEndpoint**. 

![Веб-службы Машинного обучения Azure](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Действие выполнения пакета Машинного обучения Azure

Следующий фрагмент кода JSON определяет действие выполнения пакета в службе "Машинное обучение Azure". Определение действия содержит ссылку на созданную ранее связанную службу Машинного обучения Azure. 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"path1"
            }, 
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"path2"
            }        
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path3"
            }, 
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"path4"
            }         
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```



| Свойство          | ОПИСАНИЕ                              | Обязательно |
| :---------------- | :--------------------------------------- | :------- |
| name              | Имя действия в конвейере.     | Yes      |
| description       | Описание действия.  | Нет        |
| Тип              | Для действия U-SQL Data Lake Analytics в качестве типа действия используется **AzureMLBatchExecution**. | Yes      |
| linkedServiceName | Связанные службы для связанной службы Машинного обучения Azure. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md). | Yes      |
| webServiceInputs  | Пары "ключ —значение", сопоставляющие имена входных данных веб-службы Машинного обучения Azure. Ключ должен соответствовать входным параметрам, определенным в опубликованной веб-службе Машинного обучения Azure. Значение — это пара свойств связанных служб хранилища Azure и FilePath, указывающих на расположения входных больших двоичных объектов. | Нет        |
| webServiceOutputs | Пары "ключ —значение", сопоставляющие имена выходных данных веб-службы Машинного обучения Azure. Ключ должен соответствовать выходным параметрам, определенным в опубликованной веб-службе Машинного обучения Azure. Значением является пара свойств связанных служб хранилища Azure и FilePath, указывающих на расположения выходных больших двоичных объектов. | Нет        |
| globalParameters  | Пары "ключ —значение", которые необходимо передать в конечную точку службы пакетного выполнения службы "Машинное обучение Azure". Ключ должен соответствовать именам параметров веб-службы, определенным в опубликованной веб-службе Машинного обучения Azure. Значения передаются в свойстве GlobalParameters запроса пакетного выполнения службы "Машинное обучение Azure" | Нет        |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Сценарий 1. Эксперименты с вводом-выводом веб-службы, ссылающейся на данные в хранилище BLOB-объектов

В этом сценарии веб-служба Машинного обучения Azure делает прогнозы с использованием данных из файла в хранилище больших двоичных объектов Azure и сохраняет результаты прогнозирования в хранилище больших двоичных объектов. Следующий код JSON определяет конвейер фабрики данных действием AzureMLBatchExecution. Входные и выходные данные в хранилище BLOB-объектов Azure указываются с помощью пары LinkedName и FilePath. В образце связанной службы входные и выходные данные не совпадают. Чтобы фабрика данных могла извлечь нужные файлы и отправить их в веб-службу Машинного обучения Azure, можно использовать разные связанные службы для входных и выходных данных для фабрики данных. 

> [!IMPORTANT]
> В эксперименте службы "Машинное обучение Azure" у входных и выходных портов и глобальных параметров есть имена по умолчанию (input1, input2), которые можно настроить. Имена, используемые для параметров webServiceInputs, webServiceOutputs и globalParameters, должны точно совпадать с именами в экспериментах. Пример полезных данных запроса можно просмотреть на странице справки по выполнению пакета для конечной точки Машинного обучения Azure, чтобы проверить ожидаемое сопоставление.
>
> 

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                }, 
                "FilePath":"amltest/input/in1.csv"
            }, 
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference" 
                }, 
                "FilePath":"amltest/input/in2.csv"
            }        
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out1.csv"
            }, 
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"   
                }, 
                "FilePath":"amltest2/output/out2.csv"
            }         
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Сценарий 2. Эксперименты с использованием модулей чтения и записи для обращения к данным в различных хранилищах
При создании экспериментов службы "Машинное обучение Azure" еще одним распространенным сценарием является использование модулей импорта и вывода данных. Модуль импорта используется для загрузки данных в эксперимент, а модуль вывода — для сохранения данных экспериментов. Дополнительные сведения о модулях импорта и вывода данных см. в [этом](https://msdn.microsoft.com/library/azure/dn905997.aspx) и в [этом](https://msdn.microsoft.com/library/azure/dn905984.aspx) разделах библиотеки MSDN.     

При использовании модулей импорта и вывода данных рекомендуется указывать параметр веб-службы для каждого их свойства. Эти параметры веб-службы позволяют настроить значения во время выполнения. Например, можно создать эксперимент с модулем импорта, который использует базу данных SQL Azure: XXX.database.windows.net. После развертывания веб-службы вам необходимо включить объекты-получатели веб-службы, чтобы указать другой сервер Azure SQL `YYY.database.windows.net`. Вы можете использовать параметр веб-службы, чтобы разрешить настройку этого значения.

> [!NOTE]
> Входные и выходные данные веб-службы отличаются от параметров веб-службы. В первом сценарии вы узнали, как можно указать ввод и вывод для веб-службы Машинного обучения Azure. В этом сценарии вы передаете параметры для веб-службы, соответствующие свойствам модулей импорта и вывода данных.
>
> 

Рассмотрим сценарий использования параметров веб-службы. Вы развернули веб-службу машинного обучения Azure, которая использует модуль чтения для чтения данных из одного из поддерживаемых источников данных машинного обучения Azure (например, базы данных SQL Azure). После пакетного выполнения результаты записываются с помощью модуля записи (база данных SQL Azure).  В экспериментах не определены входные и выходные данные веб-службы. В этом случае мы советуем настроить соответствующие параметры веб-службы для модулей чтения и записи. Это позволит настроить их при использовании действия AzureMLBatchExecution. Параметры веб-службы указываются в разделе **globalParameters** действия JSON, как показано ниже.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```


> [!NOTE]
> В параметрах веб-службы учитывается регистр, поэтому убедитесь, что имена, которые указываются в действии JSON, соответствуют именам, предоставляемым веб-службой.
>

Когда повторное обучение будет завершено, обновите веб-службу оценки (прогнозный эксперимент, опубликованный в виде веб-службы) на основании обученной заново модели, используя **действие обновления ресурса машинного обучения Azure**. Дополнительные сведения см. в статье [Updating Azure Machine Learning models using Update Resource Activity](update-machine-learning-models.md) (Обновление моделей машинного обучения Azure с помощью действия "Обновить ресурс").



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
