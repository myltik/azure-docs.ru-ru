---
title: "Просмотр журналов диагностики Azure Data Lake Store | Документация Майкрософт"
description: "Узнайте, как настроить журналы диагностики для Azure Data Lake Store и просматривать их. "
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 73d0dabe5b8b179cbc0847c2819947febd6ef4d8
ms.contentlocale: ru-ru
ms.lasthandoff: 08/29/2017

---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Доступ к журналам диагностики Azure Data Lake Store
Узнайте, как включить ведение журнала диагностики для учетной записи Data Lake Store и просматривать журналы, собранные для этой учетной записи.

Организации могут включить ведение журнала диагностики для учетной записи Azure Data Lake Store, чтобы собирать журналы аудита доступа к данным, содержащие такие сведения, как список пользователей, обращавшихся к данным, частота доступа к данным, объем данных в учетной записи и т. д.

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранилища озера данных Azure**. Следуйте инструкциям в разделе [Приступая к работе с хранилищем озера данных Azure на портале Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Включение ведения журнала диагностики для учетной записи Data Lake Store
1. Войдите на новый [портал Azure](https://portal.azure.com).
2. Откройте свою учетную запись Data Lake Store. В колонке учетной записи Data Lake Store выберите **Параметры**, а затем щелкните **Журналы диагностики**.
3. В колонке **Журналы диагностики** щелкните **Включить диагностику**.

    ![Включение ведения журналов диагностики](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Включение журналов диагностики")

3. В колонке **Диагностика** внесите следующие изменения, чтобы настроить ведение журнала диагностики.
   
    ![Включение ведения журналов диагностики](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Включение журналов диагностики")
   
   * В поле **Имя** введите значение конфигурации журнала диагностики.
   * Хранить и обрабатывать данные можно разными способами.
     
        * Выберите параметр **Archive to a storage account** (Архивация в учетную запись хранения), чтобы сохранять журналы в учетную запись хранения Azure. Используйте этот параметр, если вы хотите архивировать данные для последующей пакетной обработки. Если выбран этот параметр, то необходимо указать учетную запись хранения Azure для сохранения журналов.
        
        * Выберите параметр **Stream to an event hub** (Потоковая передача в концентратор событий), чтобы передавать поток данных журнала в концентратор событий Azure. Чаще всего этот параметр используется, если имеется конвейер последующей обработки для анализа входящих журналов в режиме реального времени. При выборе этого параметра необходимо указать сведения о концентраторе событий Azure, который вы хотите использовать.

        * Выберите параметр **Send to Log Analytics** (Отправить в Log Analytics), чтобы проанализировать данные созданного журнала с помощью Log Analytics. При выборе этого параметра необходимо указать сведения для рабочей области Operations Management Suite, которая будет использоваться для анализа журнала.
     
   * Укажите, хотите ли вы получить журналы аудита или журналы запросов, либо и те, и другие.
   * Укажите число дней, в течение которых должны храниться данные. Период удержания применяется, только если учетная запись хранения Azure используется для архивации данных журнала.
   * Щелкните **Сохранить**.

Включив параметры диагностики, вы сможете просматривать журналы на вкладке **Журналы диагностики** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Просмотр журналов диагностики для учетной записи Data Lake Store
Существует два способа просмотра данных журнала для учетной записи Data Lake Store:

* из представления параметров учетной записи Data Lake Store;
* из учетной записи хранения Azure, в которой хранятся данные.

### <a name="using-the-data-lake-store-settings-view"></a>Использование представления параметров Data Lake Store
1. В колонке **Параметры** учетной записи Data Lake Store щелкните **Журналы диагностики**.
   
    ![Просмотр ведения журнала диагностики](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Просмотр журналов диагностики") 
2. В колонке **Журналы диагностики** должны отображаться журналы, упорядоченные по категориям **Журналы аудита** и **Журналы запросов**.
   
   * В журналы запросов записываются все запросы API к учетной записи Data Lake Store.
   * Журналы аудита похожи на журналы запросов, но содержат более подробную статистику операций с учетной записью Data Lake Store. Например, вызов API для одной передачи в журнале запросов может породить несколько операций добавления в журналах аудита.
3. Щелкните ссылку **Скачать** для каждой записи журнала, чтобы скачать журналы.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Использование учетной записи хранения Azure, в которой хранятся данные
1. Откройте колонку учетной записи хранения Azure, связанную с Data Lake Store для ведения журнала, и щелкните "BLOB-объекты". В колонке **Служба BLOB-объектов** отображается два контейнера.
   
    ![Просмотр ведения журнала диагностики](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Просмотр журналов диагностики")
   
   * В контейнере **insights-logs-audit** содержатся журналы аудита.
   * В контейнере **insights-logs-requests** содержатся журналы запросов.
2. Журналы в этих контейнерах хранятся с использованием следующей структуры:
   
    ![Просмотр ведения журнала диагностики](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Просмотр журналов диагностики")
   
    Например, полный путь к журналу аудита может выглядеть таким образом: `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Аналогично, полный путь к журналу запросов может выглядеть так `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Описание структуры данных журнала
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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
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
| operationName |Строковый |Имя операции, добавленной в журнал. Например, getfilestatus. |
| resultType |Строка |Состояние операции. Например, 200. |
| callerIpAddress |Строковый |IP-адрес клиента, отправившего запрос. |
| correlationId |Строковый |Идентификатор журнала, который можно использовать для формирования набора связанных записей журнала. |
| identity |Объект |Идентификатор, для которого был создан журнал. |
| properties |JSON |Дополнительные сведения см. ниже. |

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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
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
| operationName |Строковый |Имя операции, добавленной в журнал. Например, getfilestatus. |
| resultType |Строка |Состояние операции. Например, 200. |
| correlationId |Строковый |Идентификатор журнала, который можно использовать для формирования набора связанных записей журнала. |
| identity |Объект |Идентификатор, для которого был создан журнал. |
| properties |JSON |Дополнительные сведения см. ниже. |

#### <a name="audit-log-properties-schema"></a>Схема свойств журнала аудита
| Name (Имя) | Тип | Описание |
| --- | --- | --- |
| StreamName |Строковый |Путь выполнения операции. |

## <a name="samples-to-process-the-log-data"></a>Примеры обработки данных журнала
В Azure Data Lake Store есть пример обработки и анализа данных журнала. Этот пример можно найти по адресу [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Дополнительные материалы
* [Обзор хранилища озера данных Azure](data-lake-store-overview.md)
* [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)


