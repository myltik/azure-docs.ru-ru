---
title: "Внешние привязки таблицы для функций Azure (экспериментальная функция)"
description: "Использование привязок внешних таблиц в Функциях Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Внешние привязки таблицы для функций Azure (экспериментальная функция)

В этой статье объясняется, как работать с табличными данными на поставщики SaaS, таких как Sharepoint и Dynamics в функциях Azure. Azure поддерживает функции ввода и вывода привязки для внешних таблиц.

> [!IMPORTANT]
> Привязка внешней таблицы является экспериментальной и никогда не может перейти в состояние общедоступными (GA). Она включена только в Azure 1.x, и не планируется добавление функций Azure 2.x. Для сценариев, которым требуется доступ к данным в поставщики SaaS, рассмотрите возможность использования [логику приложения, которые вызывают функции](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API подключения

Привязки таблицы использовать внешние подключения API для проверки подлинности с помощью сторонних поставщиков SaaS. 

При назначении привязки можно создать новое соединение API или использовать существующее соединение API в пределах той же группе ресурсов.

### <a name="available-api-connections-tables"></a>Доступные подключения API (таблицы)

|Соединитель|Триггер|Входные данные|Выходные данные|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Таблицы Google](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[База данных Oracle](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Внешние таблицы подключения также может использоваться в [приложения логики Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Пошаговые инструкции по созданию подключения API

1. На странице портала Azure для приложения функции, выберите знак «плюс» (**+**) для создания функции.

1. В **сценарий** выберите **экспериментальный**.

1. Выберите **внешней таблицы**.

1. Выберите язык.

2. В разделе **подключения внешней таблицы**выберите существующее соединение или **новый**.

1. Новое соединение, настройте параметры и выберите **авторизовать**.

1. Выберите **создать** для создания функции.

1. Выберите **интегрировать > внешней таблицы**.

1. Настройте подключение так, чтобы использовать в нем целевую таблицу. Эти параметры будут различаться для разных поставщиков SaaS. Примеры, описаны в следующем разделе.

## <a name="example"></a>Пример

В этом примере мы установим подключение к таблице Contact со столбцами Id, LastName и FirstName. В коде перечислены сущности таблицы Contact, а также записаны имена и фамилии.

Ниже показан файл *function.json*.

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

Ниже приведен код скрипта C#.

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>Источник данных SQL Server

Чтобы создать таблицы в SQL Server для использования с этим примером, Вот сценарий. `dataSetName`«Default».

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Источник данных Google листов

Чтобы создать таблицу, чтобы использовать в данном примере в Google документы, создайте таблицу с листом `Contact`. Соединитель не может использовать отображаемое имя листа. В качестве dataSetName необходимо использовать внутреннее имя (выделено полужирным шрифтом), например: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**. Добавьте имена столбцов `Id`, `LastName`, `FirstName` в первую строку, а затем заполните данные в последующих строках.

### <a name="salesforce"></a>Salesforce

Чтобы использовать этот пример с Salesforce, `dataSetName` «Default».

## <a name="configuration"></a>Параметр Configuration

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json*.

|свойство function.json | ОПИСАНИЕ|
|---------|----------------------|
|**type** | Нужно задать значение `apiHubTable`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Имя переменной, представляющей элемент события в коде функции. | 
|**подключение**| Определяет параметр приложения, в которой хранятся строки подключения API. Параметр приложения создается автоматически при добавлении подключения API в интерфейсе интеграции.|
|**dataSetName**|Имя набора данных, содержащую таблицу, для чтения.|
|**tableName**|Имя таблицы|
|**entityId**|Должен быть пустым для привязки таблицы.

Табличный соединитель предоставляет наборы данных, и каждый набор данных содержит таблицы. По умолчанию задано имя набора данных default. Ниже перечислены заголовки для набора данных и таблицы у различных поставщиков SaaS.

|Соединитель|Выборка|Таблица|
|:-----|:---|:---| 
|**SharePoint**|Сайт|Список SharePoint
|**SQL**|База данных|Таблица 
|**Таблица Google**|Электронная таблица|Лист 
|**Excel**|Файл Excel|Лист 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
