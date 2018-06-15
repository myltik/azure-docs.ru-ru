---
title: Веб-действие в фабрике данных Azure | Документация Майкрософт
description: Сведения об использовании веб-действия, одного из действий потока управления, поддерживаемых в фабрике данных, для вызова конечной точки REST из конвейера.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: shlo
ms.openlocfilehash: 6d14d84610d97e487e3bf4286267623681c4eb75
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619241"
---
# <a name="web-activity-in-azure-data-factory"></a>Веб-действие в фабрике данных Azure
Веб-действие можно использовать для вызова из конвейера фабрики данных пользовательской конечной точки REST. Вы можете передать наборы данных и связанные службы, которые будет использовать это действие и к которым оно будет обращаться. 

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, ознакомьтесь с [документацией по фабрике данных версии 1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Синтаксис

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Свойства типа

Свойство | ОПИСАНИЕ | Допустимые значения | Обязательно
-------- | ----------- | -------------- | --------
name | Имя веб-действия | Строка | Yes
Тип | Необходимо задать значение **WebActivity**. | Строка | Yes
метод | Метод REST API для целевой конечной точки. | Строка. <br/><br/>Поддерживаемые типы: GET, POST и PUT | Yes
URL-адрес | Целевая конечная точка и путь | Строка (или выражение с типом результата "строка"). Действие завершится ошибкой времени ожидания через 1 минуту, если не получит ответ от конечной точки. | Yes
Заголовки | Заголовки, которые отправляются в запрос. Например, чтобы задать язык и тип запроса: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Строка (или выражение с типом результата "строка") | Да, требуется заголовок Content-type. `"headers":{ "Content-Type":"application/json"}`
текст | Представляет полезные данные, отправляемые конечной точке. Требуется для методов POST или PUT.  | Строка (или выражение с типом результата "строка"). <br/><br/>Просмотрите схему полезных данных запроса в разделе [Схема полезных данных запроса](#request-payload-schema). | Нет 
authentication | Метод проверки подлинности, используемый для вызова конечной точки. Поддерживаемые типы данных: Basic или ClientCertificate. Дополнительные сведения см. в разделе [Проверка подлинности](#authentication). Если проверка подлинности не требуется, исключите это свойство. | Строка (или выражение с типом результата "строка") | Нет 
datasets | Список наборов данных, передаваемых в конечную точку. | Массив ссылок на наборы данных. Может быть пустым массивом. | Yes
linkedServices | Список связанных служб, переданных в конечную точку. | Массив ссылок на связанные службы. Может быть пустым массивом. | Yes

> [!NOTE]
> Конечные точки REST, которые вызывает веб-действие, должны возвращать ответ типа JSON. Действие завершится ошибкой времени ожидания через 1 минуту, если не получит ответ от конечной точки.

Ниже приведены требования к содержимому JSON:

| Тип значения | Тело запроса | Тело ответа |
|---|---|---|
|Объект JSON | Поддерживаются | Поддерживаются |
|Массив JSON | Поддерживаются <br/>(В настоящее время массивы JSON не работают из-за ошибки. Исправление уже разрабатывается.) | Не поддерживается |
| Значение JSON | Поддерживаются | Не поддерживается |
| Тип, отличный от JSON | Не поддерживается | Не поддерживается |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>None
Если проверка подлинности не требуется, не включайте свойство authentication.

### <a name="basic"></a>базовая;
Укажите имя пользователя и пароль для использования с обычной проверкой подлинности. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Сертификат клиента
Укажите содержимое в кодировке base64 PFX-файла и пароль. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Схема полезных данных запроса
При использовании метода POST или PUT свойство body представляет полезные данные, отправляемые конечной точке. Связанные службы и наборы данных можно передать в качестве части полезных данных. Ниже приведена схема полезных данных. 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Пример
В этом примере веб-действие в конвейере вызывает конечную точку REST. Оно передает связанную службу SQL Azure и набор данных SQL Azure в конечную точку. Конечная точка REST использует строку подключения SQL Azure для подключения к серверу SQL Azure и возвращает имя экземпляра SQL Server. 

### <a name="pipeline-definition"></a>Определение конвейера

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Значения параметров конвейера

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Код конечной точки веб-службы

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие выполнения конвейера](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных](control-flow-get-metadata-activity.md)
- [Действие поиска](control-flow-lookup-activity.md)
