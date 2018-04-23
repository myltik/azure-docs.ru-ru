---
title: Просмотр журналов диагностики Azure Data Lake Analytics | Документация Майкрософт
description: 'Из этой статьи вы узнаете, как настроить журналы диагностики для Azure Data Lake Analytics и просмотреть их. '
services: data-lake-analytics
documentationcenter: ''
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/12/2018
ms.openlocfilehash: efec1e00e9c2da519028f7e6ff094a0f0876df79
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Доступ к журналам диагностики для Azure Data Lake Analytics

Журнал ведения диагностики позволяет собирать аудиторские следы, связанные с доступом к данным. Эти журналы содержат такие сведения:

* список пользователей, которые получали доступ к данным;
* частота доступа к данным;
* объем данных, хранящихся в учетной записи.

## <a name="enable-logging"></a>Включение ведения журналов

1. Выполните вход на [портал Azure](https://portal.azure.com).

2. Откройте свою учетную запись Data Lake Analytics и в разделе __Мониторинг__ выберите **Журналы диагностики**. Затем выберите __Turn on diagnostics__ (Включить диагностику).

    ![Включение диагностики для сбора журналов аудита и запросов](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. В колонке __Параметры диагностики__ введите __имя__ для этой конфигурации ведения журнала и выберите параметры ведения журнала.

    ![Включение диагностики для сбора журналов аудита и запросов](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Включение журналов диагностики")

   * Можно выбрать один из трех способов хранения и обработки данных.

     * Выберите __Archive to a storage account__ (Архивация в учетную запись хранения), чтобы сохранять журналы в учетную запись хранения Azure. Этот параметр следует использовать, если вы хотите архивировать данные. Если выбран этот параметр, то необходимо указать учетную запись хранения Azure для сохранения журналов.

     * Выберите **Stream to an event hub** (Потоковая передача в концентратор событий), чтобы передавать поток данных журнала в концентратор событий Azure. Этот параметр следует использовать, если есть конвейер последующей обработки, анализирующий входящие журналы в режиме реального времени. При выборе этого параметра необходимо указать сведения о концентраторе событий Azure, который вы хотите использовать.

     * Выберите __Send to Log Analytics__ (Отправить в Log Analytics), чтобы отправить данные в службу Log Analytics. Этот параметр следует использовать, если вы хотите использовать службу Log Analytics для сбора и анализа журналов.
   * Укажите, что вы хотите получать: журналы аудита, журналы запросов либо и те, и другие журналы.  В журнал запросов записываются все запросы API, а в журнал аудита — все операции, активируемые запросами API.

   * Для параметра __Архивировать в учетной записи хранения__ укажите срок хранения данных в днях.

   * Выберите команду __Сохранить__.

        > [!NOTE]
        > Перед нажатием кнопки __Сохранить__ нужно выбрать один из следующих параметров: __Архивировать в учетной записи хранения__, __Передать в концентратор событий__ или __Отправить в Log Analytics__.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Использование учетной записи службы хранилища Azure, в которой хранятся данные

1. Чтоб вывести список контейнеров BLOB-объектов, которые содержат данные ведения журнала, откройте колонку учетной записи хранения Azure, используемой Data Lake Analytics для ведения журнала, и щелкните __BLOB-объекты__.

   * В контейнере **insights-logs-audit** содержатся журналы аудита.
   * В контейнере **insights-logs-requests** содержатся журналы запросов.

2. Журналы в контейнерах хранятся в такой структуре файлов:

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

В журналах аудита и запросов используется структурированный формат JSON.

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

| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| Twitter в режиме реального |Строка |Метка времени журнала (в формате UTC). |
| ResourceId |Строка |Идентификатор ресурса, с которым была выполнена операция |
| category |Строка |Категория журнала. Например, **Requests**. |
| operationName |Строка |Имя операции, добавленной в журнал. Например, GetAggregatedJobHistory. |
| resultType |Строка |Состояние операции. Например, 200. |
| callerIpAddress |Строка |IP-адрес клиента, отправившего запрос. |
| correlationId |Строка |Идентификатор журнала. С помощью этого значения можно группировать связанные записи журнала. |
| identity |Объект. |Идентификатор, для которого был создан журнал. |
| properties |JSON |Дополнительные сведения см. в следующем разделе ("Схема свойств журнала запросов"). |

#### <a name="request-log-properties-schema"></a>Схема свойств журнала запросов

| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| HttpMethod |Строка |Метод HTTP, использованный для операции. Например, GET. |
| Путь |Строка |Путь выполнения операции. |
| RequestContentLength |int |Длина содержимого HTTP-запроса. |
| ClientRequestId |Строка |Идентификатор, однозначно определяющий данный запрос. |
| StartTime |Строка |Время получения запроса сервером. |
| EndTime |Строка |Время отправки ответа сервером. |

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

| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| Twitter в режиме реального |Строка |Метка времени журнала (в формате UTC). |
| ResourceId |Строка |Идентификатор ресурса, с которым была выполнена операция |
| category |Строка |Категория журнала. Например, **Audit**. |
| operationName |Строка |Имя операции, добавленной в журнал. Например, JobSubmitted. |
| resultType |Строка |Подсостояние состояния задания (operationName). |
| resultSignature |Строка |Дополнительные сведения о состоянии задания (operationName). |
| identity |Строка |Пользователь, который запросил операцию. Например, susan@contoso.com. |
| properties |JSON |Дополнительные сведения см. в следующем разделе ("Схема свойств журнала аудита"). |

> [!NOTE]
> Свойства **resultType** и **resultSignature** содержат сведения о результате операции. У них есть значение, только если операция завершена. К примеру, у них есть значение, только если у свойства **operationName** есть значение **JobStarted** или **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Схема свойств журнала аудита

| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| JobId |Строка |Идентификатор, присвоенный заданию. |
| JobName |Строка |Имя, указанное для задания. |
| JobRunTime |Строка |Среда выполнения, используемая для обработки задания. |
| SubmitTime |Строка |Время отправки задания (в формате UTC). |
| StartTime |Строка |Время начала выполнения задания после отправки (в формате UTC). |
| EndTime |Строка |Время завершения задания. |
| Parallelism |Строка |Количество единиц Data Lake Analytics, запрошенных для этого задания во время отправки. |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** и **Parallelism** предоставляют сведения об операции. У этих записей есть значение только в том случае, если операция уже началась или завершилась. Например, у свойства **SubmitTime** значение появляется только после того, как свойство **operationName** получает значение **JobSubmitted**.

## <a name="process-the-log-data"></a>Обработка данных журнала

В Azure Data Lake Analytics есть пример обработки и анализа данных журнала. Этот пример можно найти по адресу [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Дополнительная информация
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
