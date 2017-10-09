---
title: "Привязки Microsoft Graph в решении \"Функции Azure\" | Документация Майкрософт"
description: "Узнайте, как использовать триггеры и привязки Microsoft Graph в решении \"Функции Azure\"."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0eb0ac63c7dbb9d6cbba093937231e93670529e
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="azure-functions-microsoft-graph-bindings"></a>Привязки Microsoft Graph в решении "Функции Azure"
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

В этой статье объясняется, как настраивать и использовать триггеры и привязки Microsoft Graph в решении "Функции Azure".
Они помогут вам использовать решение "Функции Azure" для работы с данными, аналитической информацией и событиями в [Microsoft Graph](https://graph.microsoft.io).

Расширение Microsoft Graph предоставляет следующие привязки:
- [Входная привязка токена аутентификации](#token-input) позволяет взаимодействовать с любым API Microsoft Graph.
- [Входная привязка таблицы Excel](#excel-input) позволяет считывать данные из Excel.
- [Выходная привязка таблицы Excel](#excel-output) позволяет изменять данные в Excel.
- [Входная привязка файлов OneDrive](#onedrive-input) позволяет считывать файлы из OneDrive.
- [Выходная привязка файлов OneDrive](#onedrive-output) позволяет записывать данные в файлы OneDrive.
- [Выходная привязка сообщений Outlook](#outlook-output) позволяет отправлять сообщения с помощью Outlook.
- Коллекция [привязок и триггеров веб-перехватчиков Microsoft Graph](#webhooks) позволяет реагировать на события из Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Привязки Microsoft Graph сейчас доступны в предварительной версии.

## <a name="setting-up-the-extensions"></a>Настройка расширений

Доступ к привязкам Microsoft Graph можно получить, используя _расширения привязок_. Расширения привязок — это дополнительные компоненты для среды выполнения решения "Функции Azure". В этом разделе объясняется, как настроить расширения Microsoft Graph и токена аутентификации.

### <a name="enabling-functions-20-preview"></a>Включение выпуска 2.0 решения "Функции Azure" (предварительная версия)

Расширения привязок доступны только для выпуска 2.0 решения "Функции Azure" (предварительная версия). Чтобы включить выпуск 2.0 решения "Функции Azure", задайте для параметра приложения `FUNCTIONS_EXTENSION_VERSION` значение beta.  Сведения о том, как настроить параметры приложения, см. в разделе о [параметрах приложения в решении "Функции Azure"](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings).

### <a name="installing-the-extension"></a>Установка расширения

Чтобы установить расширение с портала Azure, необходимо перейти к шаблону или привязке, которые ссылаются на это расширение. Создайте новую функцию и в окне выбора шаблона выберите сценарий Microsoft Graph. Выберите один из шаблонов в этом сценарии. Вы также можете перейти на вкладку "Интегрировать" существующей функции и выбрать одну из привязок, которые рассматриваются в этой статье.

В обоих случаях появится предупреждение о том, что необходимо установить расширение. Нажмите кнопку **Установить**, чтобы получить расширение.

> [!Note] 
> Для каждого приложения-функции достаточно установить расширение один раз. В рамках плана потребления процесс установки на портале может занять до 10 минут.

Если используется Visual Studio, вы можете получить расширения, установив следующие пакеты NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/);
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="configuring-app-service-authentication--authorization"></a>Настройка аутентификации и авторизации в службе приложений

Для привязок, приведенных в этой статье, необходимо использовать удостоверение. Это позволит Microsoft Graph принудительно применять разрешения и выполнять аудит взаимодействия. Под удостоверением может подразумеваться пользователь, который пытается получить доступ к приложению, или само приложение. Чтобы настроить это удостоверение, необходимо настроить [аутентификацию и авторизацию в службе приложений](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) с помощью Azure Active Directory. Вам также понадобится запросить все разрешения на ресурсы, требуемые для функций.

> [!Note] 
> Расширение Microsoft Graph поддерживает только аутентификацию с помощью AAD. Для входа пользователи должны указывать рабочую или учебную учетную запись.

Если используется портал Azure, под запросом на установку расширения появится предупреждение о том, что необходимо настроить аутентификацию и авторизацию в службе приложений и запросить все разрешения, требуемые для шаблонов или привязок. Щелкните **Настроить AAD сейчас** или **Добавить разрешения сейчас** соответственно.






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>Входная привязка токена аутентификации

Эта привязка позволяет получить токен AAD для определенного ресурса и вставить его в код в виде строки. Может использоваться любой ресурс, на который у приложения есть разрешения. 

### <a name="configuring-an-auth-token-input-binding"></a>Настройка входной привязки токена аутентификации

Самой привязке не требуются разрешения AAD, но в зависимости от того, как используется токен, может потребоваться запросить дополнительные разрешения. Просмотрите требования ресурса, к которому необходимо получить доступ с помощью токена.

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для токена аутентификации. Дополнительные сведения см. в разделе [Использование входной привязки токена аутентификации в коде](#token-input-code).|
|**type**|Обязательное. Необходимо задать значение `token`.|
|**direction**|Обязательное. Необходимо задать значение `in`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**resource**|Обязательное. URL-адрес ресурса AAD, для которого запрашивается токен.|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>Использование входной привязки токена аутентификации в коде

В коде токен всегда представлен виде строки.

#### <a name="sample-getting-user-profile-information"></a>Пример получения сведений из профиля пользователя

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с входной привязкой токена:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# токен используется для выполнения HTTP-вызова к Microsoft Graph и вывода результатов.

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

В следующем примере кода JS токен используется для выполнения HTTP-вызова к Microsoft Graph и вывода результатов. В файле `function.json` выше нужно сначала заменить `$return` на `res`.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Входная привязка таблицы Excel

Эта привязка позволяет считывать содержимое из таблицы Excel, хранящейся в OneDrive.

### <a name="configuring-an-excel-table-input-binding"></a>Настройка входной привязки таблицы Excel

Для этой привязки требуются следующие разрешения AAD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Чтение файлов пользователей|

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для таблицы Excel. Дополнительные сведения см. в разделе [Использование входной привязки таблицы Excel в коде](#excel-input-code).|
|**type**|Обязательное. Необходимо задать значение `excel`.|
|**direction**|Обязательное. Необходимо задать значение `in`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|Обязательное. Путь к книге Excel в OneDrive.|
|**worksheetName**|Лист, на котором находится таблица.|
|**tableName**|Это имя таблицы. Если это свойство не указано, будет использоваться содержимое листа.|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>Использование входной привязки таблицы Excel в коде

Привязка предоставляет следующие типы функций .NET:
- string[][];
- Microsoft.Graph.WorkbookTable;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).

#### <a name="sample-reading-an-excel-table"></a>Пример считывания содержимого таблицы Excel

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с входной привязкой Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# считывается и возвращается пользователю содержимое указанной таблицы.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

В следующем примере кода JS считывается и возвращается пользователю содержимое указанной таблицы. В файле `function.json` выше нужно сначала заменить `$return` на `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Выходная привязка таблицы Excel

Эта привязка позволяет изменять содержимое таблицы Excel, хранящейся в OneDrive.

### <a name="configuring-an-excel-table-output-binding"></a>Настройка выходной привязки таблицы Excel

Для этой привязки требуются следующие разрешения AAD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Полный доступ к файлам пользователя|

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для токена аутентификации. Дополнительные сведения см. в разделе [Использование выходной привязки таблицы Excel в коде](#excel-output-code).|
|**type**|Обязательное. Необходимо задать значение `excel`.|
|**direction**|Обязательное. Необходимо задать значение `out`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|Обязательное. Путь к книге Excel в OneDrive.|
|**worksheetName**|Лист, на котором находится таблица.|
|**tableName**|Это имя таблицы. Если это свойство не указано, будет использоваться содержимое листа.|
|**updateType**|Обязательное. Тип изменения, которое требуется внести в таблицу. Принимается одно из следующих значений:<ul><li><code>update</code> — замена содержимого таблицы в OneDrive.</li><li><code>append</code> — добавление полезных данных в конец таблицы в OneDrive путем создания новых строк.</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>Использование выходной привязки таблицы Excel в коде

Привязка предоставляет следующие типы функций .NET:
- string[][];
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).

#### <a name="sample-adding-rows-to-an-excel-table"></a>Пример добавления строк в таблицу Excel

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с выходной привязкой Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


В следующем примере кода C# в таблицу (предполагается, что это таблица с одним столбцом) добавляется новая строка на основе входных данных из строки запроса.

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

В следующем примере кода JS в таблицу (предполагается, что это таблица с одним столбцом) добавляется новая строка на основе входных данных из строки запроса. В файле `function.json` выше нужно сначала заменить `$return` на `res`.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>Входная привязка файлов OneDrive

Эта привязка позволяет считывать содержимое файла, хранящегося в OneDrive.

### <a name="configuring-a-onedrive-file-input-binding"></a>Настройка входной привязки файлов OneDrive

Для этой привязки требуются следующие разрешения AAD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Чтение файлов пользователей|

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для файла. Дополнительные сведения см. в разделе [Использование входной привязки файлов OneDrive в коде](#onedrive-input-code).|
|**type**|Обязательное. Необходимо задать значение `onedrive`.|
|**direction**|Обязательное. Необходимо задать значение `in`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|Обязательное. Путь к файлу в OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>Использование входной привязки файлов OneDrive в коде

Привязка предоставляет следующие типы функций .NET:
- byte[]
- Поток
- string
- Microsoft.Graph.DriveItem.

#### <a name="sample-reading-a-file-from-onedrive"></a>Пример считывания файла из OneDrive

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с входной привязкой OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# считывается файл, указанный в строке запроса, и регистрируется размер файла.

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

В следующем примере кода JS считывается файл, указанный в строке запроса, и возвращается размер файла. В файле `function.json` выше нужно сначала заменить `$return` на `res`.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>Выходная привязка файла OneDrive

Эта привязка позволяет изменять содержимое файла, хранящегося в OneDrive.

### <a name="configuring-a-onedrive-file-output-binding"></a>Настройка выходной привязки файла OneDrive

Для этой привязки требуются следующие разрешения AAD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Полный доступ к файлам пользователя|

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для файла. Дополнительные сведения см. в разделе [Использование выходной привязки файла OneDrive в коде](#onedrive-output-code).|
|**type**|Обязательное. Необходимо задать значение `onedrive`.|
|**direction**|Обязательное. Необходимо задать значение `out`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|Обязательное. Путь к файлу в OneDrive.|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>Использование выходной привязки файла OneDrive в коде

Привязка предоставляет следующие типы функций .NET:
- byte[]
- Поток
- string
- Microsoft.Graph.DriveItem.

#### <a name="sample-writing-to-a-file-in-onedrive"></a>Пример записи в файл в OneDrive

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с выходной привязкой OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# текст извлекается из строки запроса и записывается в текстовый файл (FunctionsTest.txt, как определено в файле конфигурации выше) в корневой папке в OneDrive вызывающего пользователя.

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
В следующем примере кода JS текст извлекается из строки запроса и записывается в текстовый файл (FunctionsTest.txt, как определено в файле конфигурации выше) в корневой папке в OneDrive вызывающего пользователя. В файле `function.json` выше нужно сначала заменить `$return` на `res`.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Выходная привязка сообщений Outlook

Позволяет отправлять сообщения с помощью Outlook.

### <a name="configuring-an-outlook-message-output-binding"></a>Настройка выходной привязки сообщений Outlook

Для этой привязки требуются следующие разрешения AAD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Отправка почты от имени пользователя|

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**|Обязательное. Необходимо задать значение `outlook`.|
|**direction**|Обязательное. Необходимо задать значение `out`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>Использование выходной привязки сообщений Outlook в коде

Привязка предоставляет следующие типы функций .NET:
- Microsoft.Graph.Message;
- Newtonsoft.Json.Linq.JObject
- string
- пользовательские типы объектов (указанные с помощью привязки структурной модели).

#### <a name="sample-sending-an-email-through-outlook"></a>Пример отправки сообщения с помощью Outlook

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с выходной привязкой сообщений Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# от вызывающего пользователя отправляется сообщение получателю, указанному в строке запроса.

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

В следующем примере кода JS от вызывающего пользователя отправляется сообщение получателю, указанному в строке запроса.

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Привязки веб-перехватчиков Microsoft Graph

Веб-перехватчики позволяют реагировать на события с помощью Microsoft Graph. Для поддержки веб-перехватчиков функциям требуется возможность создавать и обновлять _подписки на веб-перехватчики_, а также реагировать на них. Для полноценного решения веб-перехватчика требуется сочетание следующих привязок:
- [Триггер веб-перехватчика Microsoft Graph](#webhook-trigger) — позволяет реагировать на действия входящего веб-перехватчика.
- [Входная привязка подписок на веб-перехватчики Microsoft Graph](#webhook-input) — позволяет вывести список существующих подписок и при необходимости обновить их.
- [Выходная привязка подписок на веб-перехватчики Microsoft Graph](#webhook-output) — позволяет создавать и удалять подписки на веб-перехватчики.

Самим привязкам не требуются разрешения AAD, но вам потребуется запросить разрешения в соответствии с типом ресурса, на действия которого необходимо реагировать. Список необходимых разрешений для каждого типа ресурса см. в разделе [о разрешениях для подписок](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Дополнительные сведения о веб-перехватчиках см. в статье [Работа с веб-перехватчиками в Microsoft Graph].





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Триггер веб-перехватчика Microsoft Graph

Этот триггер позволяет функции реагировать на действия входящего веб-перехватчика в Microsoft Graph. Каждый экземпляр этого триггера может реагировать на один тип ресурса Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>Настройка триггера веб-перехватчика Microsoft Graph

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**|Обязательное. Необходимо задать значение `graphWebhook`.|
|**direction**|Обязательное. Необходимо задать значение `trigger`.|
|**resourceType**|Обязательное. Ресурс Graph, для которого эта функция должна отвечать на вызовы веб-перехватчиков. Принимается одно из следующих значений:<ul><li><code>#Microsoft.Graph.Message</code> — изменения, которые вносятся в сообщения Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> — изменения, которые вносятся в корневые элементы OneDrive.</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> У приложения-функции может быть только одна функция, зарегистрированная для заданного значения `resourceType`.

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>Использование триггера веб-перехватчика Microsoft Graph в коде

Привязка предоставляет следующие типы функций .NET:
- типы пакетов SDK для Microsoft Graph, соответствующие типам ресурсов, например Microsoft.Graph.Message, Microsoft.Graph.DriveItem;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).

Примеры использования этой привязки в коде см. в разделе [Примеры веб-перехватчиков Microsoft Graph](#webhook-samples).



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Входная привязка подписки на веб-перехватчик Microsoft Graph

Эта привязка позволяет получить список подписок, управляемых этим приложением-функцией. Привязка позволяет считывать данные из хранилища приложений-функций и не выводит другие подписки, созданные вне приложения.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>Настройка входной привязки подписки на веб-перехватчик Microsoft Graph

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**|Обязательное. Необходимо задать значение `graphWebhookSubscription`.|
|**direction**|Обязательное. Необходимо задать значение `in`.|
|**filter**| Если задано значение `userFromRequest`, привязка будет получать только подписки, принадлежащие вызывающему пользователю. Применяется только с [триггером HTTP].| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>Использование входной привязки подписки на веб-перехватчик Microsoft Graph в коде

Привязка предоставляет следующие типы функций .NET:
- string[]
- массивы пользовательских типов объектов;
- Newtonsoft.Json.Linq.JObject[];
- Microsoft.Graph.Subscription[].

Примеры использования этой привязки в коде см. в разделе [Примеры веб-перехватчиков Microsoft Graph](#webhook-samples).


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Выходная привязка подписки веб-перехватчика Microsoft Graph

Эта привязка позволяет создавать, удалять и обновлять подписки веб-перехватчиков в Microsoft Graph.

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>Настройка выходной привязки подписки веб-перехватчика Microsoft Graph

Привязка поддерживает следующие свойства.

|Свойство|Описание|
|--------|--------|
|**name**|Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**|Обязательное. Необходимо задать значение `graphWebhookSubscription`.|
|**direction**|Обязательное. Необходимо задать значение `out`.|
|**identity**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**action**|Обязательное. Указывает действие, которое необходимо выполнить с помощью привязки. Принимается одно из следующих значений:<ul><li><code>create</code> — регистрация новой подписки.</li><li><code>delete</code> — удаление указанной подписки.</li><li><code>refresh</code> — обновление указанной подписки. Позволяет предотвратить истечение срока действия.</li></ul>|
|**subscriptionResource**|Требуется, только если для свойства _action_ задано значение `create`. Указывает ресурс Microsoft Graph, изменения которого будут отслеживаться. Дополнительные сведения см. в статье [Работа с веб-перехватчиками в Microsoft Graph]. |
|**changeType**|Требуется, только если для свойства _action_ задано значение `create`. Указывает тип изменения в подписанном ресурсе, при внесении которого будет создано уведомление. Поддерживаются такие значения: `created`, `updated`, `deleted`. Можно объединить несколько значений, указав их через запятую.|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>Использование выходной привязки подписки на веб-перехватчик Microsoft Graph в коде

Привязка предоставляет следующие типы функций .NET:
- string
- Microsoft.Graph.Subscription.

Примеры использования этой привязки в коде см. в разделе [Примеры веб-перехватчиков Microsoft Graph](#webhook-samples).
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Примеры веб-перехватчиков Microsoft Graph

#### <a name="sample-creating-a-subscription"></a>Пример создания подписки

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с выходной привязкой подписки с использованием действия создания:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# регистрируется веб-перехватчик, который отправит уведомление этому приложению-функции, когда вызывающий пользователь получит сообщение в Outlook.

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

В следующем примере кода JS регистрируется веб-перехватчик, который отправит уведомление этому приложению-функции, когда вызывающий пользователь получит сообщение в Outlook.

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>Пример обработки уведомлений

Предположим, у вас есть следующий файл function.json, определяющий триггер веб-перехватчика Graph для обработки сообщений Outlook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# показана реакция на входящие сообщения и регистрация текста сообщений с темой "Функции Azure", отправленных получателем.

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

В следующем примере кода JS показана реакция на входящие сообщения и регистрация текста сообщений с темой "Функции Azure", отправленных получателем.

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>Пример удаления подписок

Предположим, у вас есть следующий файл function.json, определяющий триггер HTTP с входной и выходной привязкой подписки с использованием действия удаления:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

В следующем примере C# выводятся и удаляются все подписки для вызывающего пользователя.

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

В следующем примере JS выводятся и удаляются все подписки для вызывающего пользователя.

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>Пример обновления подписок

Существует два способа обновления подписок:
- Использование удостоверения приложения для всех подписок. Для этого потребуется согласие администратора Azure Active Directory. Этот способ можно применять ко всем языкам, которые поддерживает решение "Функции Azure".
- Использование удостоверения, связанного с каждой подпиской, путем привязки каждого идентификатора пользователя вручную. В этом случае для выполнения привязки потребуется пользовательский код. Этот метод можно использовать только с функциями .NET.

Ниже описаны оба этих способа.

**Использование удостоверения приложения**

Предположим, у вас есть следующий файл function.json, определяющий триггер таймера с выходной и входной привязкой подписки с использованием удостоверения приложения:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# выполняется обновление подписок на основе таймера с использованием удостоверения приложения.

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

В следующем примере кода JS выполняется обновление подписок на основе таймера с использованием удостоверения приложения.

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**Использование удостоверений динамических пользователей**

В качестве альтернативного варианта предположим, что у вас есть следующий файл function.json, определяющий триггер таймера, который позволяет делегировать коду функции выполнение привязки для входной привязки подписки:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

В следующем примере кода C# выполняется обновление подписок на основе таймера с использованием удостоверения каждого пользователя путем создания выходной привязки в коде.
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[триггером HTTP]: functions-bindings-http-webhook.md
[Работа с веб-перехватчиками в Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

