---
title: "Просмотр журналов диагностики Azure Data Lake Analytics | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как настроить журналы диагностики для Azure Data Lake Analytics и просмотреть их. "
services: data-lake-analytics
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 71a839d9c076d33c404a56bf0544666dc86eaa4d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Доступ к журналам диагностики для Azure Data Lake Analytics

Узнайте, как включить ведение журнала диагностики для учетной записи Data Lake Analytics и просматривать журналы, собранные для этой учетной записи.

Организации могут включать ведение журналов диагностики, чтобы в учетных записях Azure Data Lake Analytics собирать журналы аудита доступа к данным. Эти журналы содержат такие сведения:

* список пользователей, которые получали доступ к данным;
* частота доступа к данным;
* объем данных, хранящихся в учетной записи.

## <a name="enable-logging"></a>Включение ведения журналов

1. Войдите на новый [портал Azure](https://portal.azure.com).

2. Откройте свою учетную запись Data Lake Analytics и в разделе __Мониторинг__ выберите **Журналы диагностики**. Затем выберите __Turn on diagnostics__ (Включить диагностику).

    ![Включение диагностики для сбора журналов аудита и запросов](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. В разделе __Параметры диагностики__ установите для состояния значение __Вкл.__ и выберите параметры ведения журнала.

    ![Включение диагностики для сбора журналов аудита и запросов](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Включение журналов диагностики")

   * Чтобы включить ведение журналов диагностики, для параметра **Состояние** установите значение **Включено**.

   * Можно выбрать хранение и обработку данных одним из двух способов.

     * Выберите __Archive to a storage account__ (Архивация в учетную запись хранения), чтобы сохранять журналы в учетную запись хранения Azure. Этот параметр следует использовать, если вы хотите архивировать данные. Если выбран этот параметр, то необходимо указать учетную запись хранения Azure для сохранения журналов.

     * Выберите **Stream to an event hub** (Потоковая передача в концентратор событий), чтобы передавать поток данных журнала в концентратор событий Azure. Этот параметр следует использовать, если есть конвейер последующей обработки для анализа входящих журналов в режиме реального времени. При выборе этого параметра необходимо указать сведения о концентраторе событий Azure, который вы хотите использовать.

     * Выберите __Send to Log Analytics__ (Отправить в Log Analytics), чтобы отправить данные в службу Log Analytics. Этот параметр следует использовать, если вы хотите использовать службу Log Analytics для сбора и анализа журналов.
   * Укажите, хотите ли вы получить журналы аудита или журналы запросов, либо и те, и другие.

   * Укажите число дней, в течение которых должны храниться данные.

   * Щелкните **Сохранить**.

        > [!NOTE]
        > Перед тем, как нажать кнопку __Сохранить__, необходимо выбрать один из следующих параметров: __Archive to a storage account__ (Архивация в учетную запись хранения), __Stream to an event hub__ (Потоковая передача в концентратор событий) или __Send to Log Analytics__ (Отправить в Log Analytics).

Включив параметры диагностики, вы сможете вернуться на вкладку __Журналы диагностики__, чтобы просмотреть журналы.

## <a name="view-logs"></a>Просмотр журналов

Существует два способа просмотра данных журнала для учетной записи Data Lake Analytics:

* из раздела параметров учетной записи Data Lake Analytics;
* из учетной записи хранения Azure, в которой хранятся данные.

### <a name="using-the-data-lake-analytics-settings-view"></a>Использование представления параметров Data Lake Analytics

1. В колонке учетной записи Data Lake Analytics выберите **Журналы диагностики**, а затем выберите запись, которая будет отображаться в журналах.

    ![Просмотр ведения журнала диагностики](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Просмотр журналов диагностики")

2. Журналы подразделяются на следующие категории: **журналы аудита** и **журналы запросов**.

    ![записи журнала](./media/data-lake-analytics-diagnostic-logs/diagnostic-log-entries.png)

   * В журналы запросов записываются все запросы API к учетной записи Data Lake Analytics.
   * Журналы аудита похожи на журналы запросов, но содержат более подробную статистику операций с учетной записью Data Lake Analytics. Например, вызов API для одной передачи в журнале запросов может породить несколько операций добавления в журналах аудита.

3. Щелкните ссылку **Скачать** для каждой записи журнала, чтобы скачать журналы.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Использование учетной записи хранения Azure, в которой хранятся данные

1. Откройте колонку учетной записи хранения Azure, связанную с Data Lake Analytics для ведения журнала, и щелкните "BLOB-объекты". В колонке **Служба BLOB-объектов** отображается два контейнера.

    ![Просмотр ведения журнала диагностики](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Просмотр журналов диагностики")

   * В контейнере **insights-logs-audit** содержатся журналы аудита.
   * В контейнере **insights-logs-requests** содержатся журналы запросов.
2. Журналы в этих контейнерах хранятся с использованием следующей структуры:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > В колонке `##` в пути обозначают год, месяц, день и час создания журнала. Служба Data Lake Analytics создает один файл каждый час, поэтому параметр `m=` всегда содержит значение `00`.

    Например, полный путь к журналу аудита может выглядеть таким образом:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Аналогично, полный путь к журналу запросов может выглядеть так:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Структура журнала

В журналах аудита и запросов используется формат JSON. В этом разделе мы рассмотрим структуру JSON для журналов запросов и аудита.

### <a name="request-logs"></a>Журналы запросов

Ниже приведен пример записи журнала запросов в формате JSON. Каждый большой двоичный объект имеет один корневой объект под названием **records** , который содержит массив объектов журнала.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Схема журнала запросов

| Name (Имя) | Тип | Описание |
| --- | --- | --- |
| Twitter в режиме реального |Строковый |Метка времени журнала (в формате UTC). |
| resourceId |Строковый |Идентификатор ресурса, с которым была выполнена операция. |
| category |Строковый |Категория журнала. Например, **Requests**. |
| operationName |Строковый |Имя операции, добавленной в журнал. Например, GetAggregatedJobHistory. |
| resultType |Строка |Состояние операции. Например, 200. |
| callerIpAddress |Строковый |IP-адрес клиента, отправившего запрос. |
| correlationId |Строковый |Идентификатор журнала. С помощью этого значения можно группировать связанные записи журнала. |
| identity |Объект |Идентификатор, для которого был создан журнал. |
| properties |JSON |Дополнительные сведения см. в следующем разделе ("Схема свойств журнала запросов"). |

#### <a name="request-log-properties-schema"></a>Схема свойств журнала запросов

| Name (Имя) | Тип | Описание |
| --- | --- | --- |
| HttpMethod |Строковый |Метод HTTP, использованный для операции. Например, GET. |
| Путь |Строковый |Путь выполнения операции. |
| RequestContentLength |int |Длина содержимого HTTP-запроса. |
| ClientRequestId |Строковый |Идентификатор, однозначно определяющий данный запрос. |
| StartTime |Строковый |Время получения запроса сервером. |
| EndTime |Строковый |Время отправки ответа сервером. |

### <a name="audit-logs"></a>Журналы аудита

Ниже приведен пример записи журнала аудита в формате JSON. Каждый большой двоичный объект имеет один корневой объект под названием **records** , который содержит массив объектов журнала.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Схема журнала аудита

| Name (Имя) | Тип | Описание |
| --- | --- | --- |
| Twitter в режиме реального |Строковый |Метка времени журнала (в формате UTC). |
| resourceId |Строковый |Идентификатор ресурса, с которым была выполнена операция. |
| category |Строковый |Категория журнала. Например, **Audit**. |
| operationName |Строковый |Имя операции, добавленной в журнал. Например, JobSubmitted. |
| resultType |Строковый |Подсостояние состояния задания (operationName). |
| resultSignature |Строковый |Дополнительные сведения о состоянии задания (operationName). |
| identity |Строковый |Пользователь, который запросил операцию. Пример: susan@contoso.com. |
| properties |JSON |Дополнительные сведения см. в следующем разделе ("Схема свойств журнала аудита"). |

> [!NOTE]
> Свойства **resultType** и **resultSignature** содержат сведения о результате операции. У них есть значение, только если операция завершена. К примеру, у них есть значение, если у свойства **operationName** есть значение **JobStarted** или **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Схема свойств журнала аудита

| Name (Имя) | Тип | Описание |
| --- | --- | --- |
| JobId |Строковый |Идентификатор, присвоенный заданию. |
| JobName |Строковый |Имя, указанное для задания. |
| JobRunTime |Строковый |Среда выполнения, используемая для обработки задания. |
| SubmitTime |Строковый |Время отправки задания (в формате UTC). |
| StartTime |Строковый |Время начала выполнения задания после отправки (в формате UTC). |
| EndTime |Строковый |Время завершения задания. |
| Параллелизм |Строковый |Количество единиц Data Lake Analytics, запрошенных для этого задания во время отправки. |

> [!NOTE]
> Свойства **SubmitTime**, **StartTime**, **EndTime** и **Parallelism** содержат сведения об операции. У них есть значение, только если операция началась или уже завершена. К примеру, у свойства **SubmitTime** появляется значение после того, как свойство **operationName** получает значение **JobSubmitted**.

## <a name="process-the-log-data"></a>Обработка данных журнала

В Azure Data Lake Analytics есть пример обработки и анализа данных журнала. Этот пример можно найти по адресу [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)

