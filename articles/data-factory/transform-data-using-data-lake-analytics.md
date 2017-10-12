---
title: "Преобразование данных с помощью сценария U-SQL в Azure | Документация Майкрософт"
description: "Узнайте, как обрабатывать и преобразовывать данные с помощью сценариев U-SQL в службе вычислений Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 5e54464ceabfe1fea2af80d63e538bea6a0a50a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Преобразование данных с помощью сценариев U-SQL в Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-usql-activity.md)
> * [Версия 2 — предварительная](transform-data-using-data-lake-analytics.md)

Конвейер в фабрике данных Azure обрабатывает данные в связанной службе хранилища с помощью связанных вычислительных служб. В нем содержится последовательность действий, каждое из которых выполняет определенную операцию обработки. В этой статье описывается **действие U-SQL в Data Lake Analytics**, которое запускает сценарий **U-SQL** в связанной службе вычислений **Azure Data Lake Analytics**. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, см. статью [Преобразование данных с помощью сценариев U-SQL в Azure Data Lake Analytics](v1/data-factory-usql-activity.md).

Перед созданием конвейера с действием U-SQL в Data Lake Analytics следует создать учетную запись Data Lake Analytics. Дополнительные сведения об Azure Data Lake Analytics см. в статье [Начало работы с аналитикой озера данных Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Связанная служба Azure Data Lake Analytics
Можно создать связанную службу **Azure Data Lake Analytics** , чтобы связать службу вычислений Azure Data Lake Analytics с фабрикой данных Azure. Действие U-SQL Data Lake Analytics в конвейере ссылается на эту связанную службу. 

В следующей таблице приведены описания универсальных свойств из определения JSON. 

| Свойство                 | Описание                              | Обязательно                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Свойству type необходимо присвоить значение **AzureDataLakeAnalytics**. | Да                                      |
| **accountName**          | Имя учетной записи аналитики озера данных Azure.  | Да                                      |
| **dataLakeAnalyticsUri** | Универсальный код ресурса (URI) аналитики озера данных Azure.           | Нет                                       |
| **subscriptionId**       | Идентификатор подписки Azure.                    | Нет (если не указан, используется подписка фабрики данных). |
| **resourceGroupName**    | Имя группы ресурсов Azure                | Нет (если не указано, используется группа ресурсов фабрики данных). |

### <a name="service-principal-authentication"></a>Проверка подлинности субъекта-службы
Для связанной службы Azure Data Lake Analytics необходимо выполнить проверку подлинности субъекта-службы, чтобы подключиться к службе Azure Data Lake Analytics. При использовании проверки подлинности на основе субъекта-службы необходимо зарегистрировать сущность приложения в Azure Active Directory (Azure AD) и предоставить ей доступ к Data Lake Analytics и Data Lake Store, которые она использует. Подробные инструкции см. в статье [Аутентификация между службами в Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Запишите следующие значения, которые используются для определения связанной службы:
* Идентификатор приложения
* Ключ приложения 
* Tenant ID

Используйте проверку подлинности на основе субъекта-службы, указав следующие свойства:

| Свойство                | Описание                              | Обязательно |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Укажите идентификатора клиента приложения.     | Да      |
| **servicePrincipalKey** | Укажите ключ приложения.           | Да      |
| **tenant**              | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Да      |

**Пример. Проверка подлинности на основе субъекта-службы**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Дополнительные сведения о связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Действие U-SQL в аналитике озера данных
В следующем фрагменте кода JSON определяется конвейер с действием U-SQL в аналитике озера данных. Определение действия содержит ссылку на созданную ранее связанную службу аналитики озера данных Azure. Чтобы выполнить скрипт U-SQL Data Lake Analytics, фабрика данных отправляет указанный скрипт в Data Lake Analytics. Необходимые входные и выходные данные определяются в скрипте Data Lake Analytics для извлечения и вывода. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "AzureDataLakeAnalyticsLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "LinkedServiceofAzureBlobStorageforscriptPath",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

В следующей таблице описаны имена и описания свойств, относящихся к этому действию. 

| Свойство            | Описание                              | Обязательно |
| :------------------ | :--------------------------------------- | :------- |
| name                | Имя действия в конвейере.     | Да      |
| description         | Описание действия.  | Нет       |
| type                | Для действия U-SQL Data Lake Analytics в качестве типа действия используется **DataLakeAnalyticsU-SQL**. | Да      |
| linkedServiceName (имя связанной службы)   | Связанная служба Azure Data Lake Analytics. Дополнительные сведения об этой связанной службе см. в статье [Вычислительные среды, поддерживаемые фабрикой данных Azure](compute-linked-services.md).  |Да       |
| scriptPath          | Путь к папке, содержащей скрипт U-SQL В имени файла учитывается регистр. | Да      |
| scriptLinkedService | Связанная служба, которая связывает хранилище, содержащее скрипт, с фабрикой данных | Да      |
| degreeOfParallelism | Максимальное количество узлов, используемых одновременно для выполнения задания. | Нет       |
| priority            | Определяет, какие задания из всех в очереди должны запускаться в первую очередь. Чем меньше число, тем выше приоритет. | Нет       |
| parameters          | Параметры скрипта U-SQL          | Нет       |
| runtimeVersion      | Версия среды выполнения обработчика U-SQL, которую нужно использовать. | Нет       |
| compilationMode     | <p>Режим компиляции U-SQL. Может иметь одно из следующих значений: **Semantic**: выполнение только семантических проверок и необходимых проверок работоспособности. **Full**: выполнение полной компиляции, включая проверку синтаксиса, оптимизацию, создание кода и т. д. **SingleBox**: выполнение полной компиляции с параметром TargetType для SingleBox. Если не указать значение для этого свойства, сервер определит оптимальный режим компиляции. | Нет |

Определение скрипта, отправляемого фабрикой данных, см. в разделе [Пример скрипта U-SQL](#sample-u-sql-script). 

## <a name="sample-u-sql-script"></a>Пример скрипта U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

В приведенном выше примере входные и выходные данные определяются в параметрах **@in** и **@out**. Значения параметров **@in** и **@out** в скрипте U-SQL передаются динамически с помощью фабрики данных, используя раздел parameters. 

В определении конвейера для заданий, которые выполняются в службе Azure Data Lake Analytics, можно определить другие свойства, например degreeOfParallelism и priority.

## <a name="dynamic-parameters"></a>Динамические параметры
В примере определения конвейера параметрам in и out присвоено жестко заданные значения. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Вместо этого можно использовать динамические параметры. Например: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

В этом случае входные файлы по-прежнему берутся из папки /datalake/input, а выходные файлы создаются в папке /datalake/output. Имена файлов присваиваются динамически, на основе времени начала среза.  

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими ссылками, в которых описаны способы преобразования данных другими способами: 

* [Действие Hive](transform-data-using-hadoop-hive.md)
* [Действие Pig](transform-data-using-hadoop-pig.md)
* [Действие MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Действие потоковой передачи Hadoop](transform-data-using-hadoop-streaming.md)
* [Действие Spark](transform-data-using-spark.md)
* [Настраиваемое действие .NET](transform-data-using-dotnet-custom-activity.md)
* [Создание прогнозирующих конвейеров с помощью машинного обучения Azure и фабрики данных Azure](transform-data-using-machine-learning.md)
* [Действие хранимой процедуры](transform-data-using-stored-procedure.md)
