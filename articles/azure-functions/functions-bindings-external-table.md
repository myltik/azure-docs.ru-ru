---
title: "Привязка внешних таблиц в Функциях Azure (предварительная версия) | Документация Майкрософт"
description: "Использование привязок внешних таблиц в Функциях Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 716438e5ea490f6716999813112305499dbe61a8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="azure-functions-external-table-binding-preview"></a>Привязка внешних таблиц в Функциях Azure (предварительная версия)
В этой статье показано, как управлять табличными данными для поставщиков SaaS (например, Sharepoint, Dynamics) внутри функции со встроенными привязками. Функции Azure поддерживают входные и выходные привязки для внешних таблиц.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Подключения API

Привязки таблиц используют внешние подключения API для проверки подлинности у сторонних поставщиков SaaS. 

При назначении привязки можно создать новое подключение API или использовать существующее подключение API в той же группе ресурсов.

### <a name="supported-api-connections-tables"></a>Поддерживаемые подключения API (таблица)

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
> В [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) могут также использоваться подключения к внешним таблицам.

### <a name="creating-an-api-connection-step-by-step"></a>Пошаговые инструкции по созданию подключения API

1. Create a function (Создать функцию) > Пользовательская функция ![Создание пользовательской функции](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. Сценарий `Experimental`  >  Шаблон `ExternalTable-CSharp` > Создать новое подключение `External Table connection` 
 ![Выбор шаблона входных данных для таблицы](./media/functions-bindings-storage-table/create-template-table.jpg)
1. Выберите поставщика SaaS > Выберите или создайте подключение ![Настройка подключения SaaS](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. Выберите подключение API > Создайте функцию ![Создание табличной функции](./media/functions-bindings-storage-table/table-template-options.jpg)
1. Выберите `Integrate` > `External Table`
    1. Настройте подключение так, чтобы использовать в нем целевую таблицу. Эти параметры варьируются у разных поставщиков SaaS. Они описаны ниже в разделе [Параметры источника данных](#datasourcesettings).
![Настройка таблицы](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>Использование

В этом примере мы установим подключение к таблице Contact со столбцами Id, LastName и FirstName. В коде перечислены сущности таблицы Contact, а также записаны имена и фамилии.

### <a name="bindings"></a>Привязки
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
Параметр `entityId` должен быть пустым для привязок таблиц.

`ConnectionAppSettingsKey` определяет параметр приложения, в котором хранится строка подключения API. Параметр приложения создается автоматически при добавлении подключения API в интерфейсе интеграции.

Табличный соединитель предоставляет наборы данных, и каждый набор данных содержит таблицы. По умолчанию задано имя набора данных default. Ниже перечислены заголовки для набора данных и таблицы у различных поставщиков SaaS.

|Соединитель|Выборка|Таблица|
|:-----|:---|:---| 
|**SharePoint**|Сайт|Список SharePoint
|**SQL**|База данных|Таблица 
|**Таблица Google**|Электронная таблица|Лист 
|**Excel**|Файл Excel|Лист 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>Использование в языке C# #

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
        //retreive table values
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

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## Параметры источника данных

### <a name="sql-server"></a>SQL Server

Скрипт для создания и заполнения таблицы Contact приведен ниже. Имя dataSetName — default.

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

### <a name="google-sheets"></a>Таблицы Google
В Документах Google создайте таблицу с листом `Contact`. Соединитель не может использовать отображаемое имя листа. В качестве dataSetName необходимо использовать внутреннее имя (выделено полужирным шрифтом), например: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**. Добавьте имена столбцов `Id`, `LastName`, `FirstName` в первую строку, а затем заполните данные в последующих строках.

### <a name="salesforce"></a>Salesforce
Имя dataSetName — default.

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

