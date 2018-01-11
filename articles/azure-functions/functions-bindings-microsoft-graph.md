---
title: "Microsoft Graph привязки для функций Azure"
description: "Узнайте, как использовать триггеры и привязки Microsoft Graph в решении \"Функции Azure\"."
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 63b94c0a9b77a3f3a6fd394a130bf8f132d51369
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph привязки для функций Azure

В этой статье объясняется, как настраивать и использовать триггеры и привязки Microsoft Graph в решении "Функции Azure". Они помогут вам использовать решение "Функции Azure" для работы с данными, аналитической информацией и событиями в [Microsoft Graph](https://graph.microsoft.io).

Расширение Microsoft Graph предоставляет следующие привязки:
- [Входная привязка токена аутентификации](#token-input) позволяет взаимодействовать с любым API Microsoft Graph.
- [Входная привязка таблицы Excel](#excel-input) позволяет считывать данные из Excel.
- [Выходная привязка таблицы Excel](#excel-output) позволяет изменять данные в Excel.
- Объект [файл OneDrive входной привязки](#onedrive-input) дает возможность чтения файлов из OneDrive.
- Объект [файл OneDrive привязка для вывода](#onedrive-output) позволяет записывать файлы в OneDrive.
- [Выходная привязка сообщений Outlook](#outlook-output) позволяет отправлять сообщения с помощью Outlook.
- Коллекция [привязок и триггеров веб-перехватчиков Microsoft Graph](#webhooks) позволяет реагировать на события из Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Привязки Microsoft Graph сейчас доступны в предварительной версии.

## <a name="setting-up-the-extensions"></a>Настройка расширений

Доступ к привязкам Microsoft Graph можно получить, используя _расширения привязок_. Расширения привязок — это дополнительные компоненты для среды выполнения решения "Функции Azure". В этом разделе показано, как настроить Microsoft Graph и расширения токена проверки подлинности.

### <a name="enabling-functions-20-preview"></a>Включение выпуска 2.0 решения "Функции Azure" (предварительная версия)

Расширения привязки доступны только для предварительной версии 2.0 функций Azure. 

Сведения о приложении функции для использования версии среды выполнения функции предварительной версии 2.0 см. в разделе [ориентированные на среду выполнения версии 2.0](functions-versions.md#target-the-version-20-runtime).

### <a name="installing-the-extension"></a>Установка расширения

Чтобы установить расширение на портале Azure, перейдите в шаблоне или привязка, которая ссылается на него. Создайте новую функцию и в окне выбора шаблона выберите сценарий Microsoft Graph. Выберите один из шаблонов в этом сценарии. Кроме того можно перейти на вкладку «Интеграция» существующую функцию и выберите одну из привязок, описанные в данной статье.

В обоих случаях появится предупреждение о том, что необходимо установить расширение. Нажмите кнопку **Установить**, чтобы получить расширение.

> [!Note] 
> Для каждого приложения-функции достаточно установить расширение один раз. В рамках плана потребления процесс установки на портале может занять до 10 минут.

Если используется Visual Studio, вы можете получить расширения, установив следующие пакеты NuGet:
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/);
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="configuring-authentication--authorization"></a>Настройка проверки подлинности и авторизации

Привязки, описанные в этой статье требуется идентификатор, который необходимо использовать. Это позволит Microsoft Graph принудительно применять разрешения и выполнять аудит взаимодействия. Под удостоверением может подразумеваться пользователь, который пытается получить доступ к приложению, или само приложение. Чтобы настроить это удостоверение, настройте [проверки подлинности службы для приложения / авторизации](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) с Azure Active Directory. Вам также понадобится запросить все разрешения на ресурсы, требуемые для функций.

> [!Note] 
> Расширения Microsoft Graph поддерживает только проверку подлинности Azure AD. Для входа пользователи должны указывать рабочую или учебную учетную запись.

Если вы используете портал Azure, вы увидите предупреждение ниже командной строке, чтобы установить расширение. Предупреждение предложит настроить проверку подлинности службы приложения и требует авторизации и запрос все разрешения, шаблон или привязки. Нажмите кнопку **Настройка Azure AD теперь** или **Теперь добавьте разрешения** соответствующим образом.



<a name="token-input"></a>
## <a name="auth-token"></a>Токен проверки подлинности

Привязка ввода токена проверки подлинности возвращает токен Azure AD для данного ресурса и предоставляет его в код в виде строки. Может использоваться любой ресурс, на который у приложения есть разрешения. 

Этот раздел содержит следующие подразделы:

* [Пример](#auth-token---example)
* [Атрибуты](#auth-token---attributes)
* [Конфигурация](#auth-token---configuration)
* [Использование](#auth-token---usage)

### <a name="auth-token---example"></a>Токен проверки подлинности — пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Токен проверки подлинности — пример скрипта C#

Следующий пример возвращает сведения о профиле пользователя.

*Function.json* файла определяет триггер HTTP с помощью токена входной привязки:

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

Код скрипта C# использует маркер, чтобы сделать вызов HTTP для Microsoft Graph и возвращает результат:

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

#### <a name="auth-token---javascript-example"></a>Токен проверки подлинности — пример JavaScript

Следующий пример возвращает сведения о профиле пользователя.

*Function.json* файла определяет триггер HTTP с помощью токена входной привязки:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Код JavaScript использует маркер, чтобы сделать вызов HTTP для Microsoft Graph и возвращает результат.

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

### <a name="auth-token---attributes"></a>Маркер проверки подлинности - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [маркера](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/).

### <a name="auth-token---configuration"></a>Маркер проверки подлинности - настройки

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Token`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для токена аутентификации. Дополнительные сведения см. в разделе [Использование входной привязки токена аутентификации в коде](#token-input-code).|
|**type**||Обязательное. Необходимо задать значение `token`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**Ресурс**|**resource**|Требуемая URL ресурсов Azure AD, для которого запрашивается токен.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Токен проверки подлинности — использование

Самой привязки не требуется ни разрешений Azure AD, но в зависимости от того, как используется токен, может потребоваться запросить дополнительные разрешения. Просмотрите требования ресурса, к которому необходимо получить доступ с помощью токена.

В коде токен всегда представлен виде строки.




<a name="excel-input"></a>
## <a name="excel-input"></a>Входные данные Excel

Входной привязки таблицы Excel считывает содержимое таблицы Excel в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#excel-input---example)
* [Атрибуты](#excel-input---attributes)
* [Конфигурация](#excel-input---configuration)
* [Использование](#excel-input---usage)

### <a name="excel-input---example"></a>Excel входные данные - пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel input — пример скрипта C#

Следующие *function.json* файла определяет триггер HTTP с помощью привязки ввода Excel:

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

Следующий код скрипта C# считывает содержимое указанной таблицы и возвращает их пользователю.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel input — пример JavaScript

Следующие *function.json* файла определяет триггер HTTP с помощью привязки ввода Excel:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Следующий код JavaScript, считывает содержимое указанной таблицы и возвращает их пользователю.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel входных - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="excel-input---configuration"></a>Входные данные — в формате Excel конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Excel`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для таблицы Excel. Дополнительные сведения см. в разделе [Использование входной привязки таблицы Excel в коде](#excel-input-code).|
|**type**||Обязательное. Необходимо задать значение `excel`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к книге Excel в OneDrive.|
|**worksheetName**|**WorksheetName**|Лист, на котором находится таблица.|
|**tableName**|**TableName**|Это имя таблицы. Если это свойство не указано, будет использоваться содержимое листа.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel input — использование

Эта привязка необходимы следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Чтение файлов пользователей|

Привязка предоставляет следующие типы функций .NET:
- string[][];
- Microsoft.Graph.WorkbookTable;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).










<a name="excel-output"></a>
## <a name="excel-output"></a>В формате Excel

Excel выходной привязки изменяет содержимое таблицы Excel в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#excel-output---example)
* [Атрибуты](#excel-output---attributes)
* [Конфигурация](#excel-output---configuration)
* [Использование](#excel-output---usage)

### <a name="excel-output---example"></a>Excel вывод — пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Output - Excel пример скрипта C#

Пример добавления строк в таблицу Excel.

*Function.json* файл определяет триггер HTTP с помощью Excel привязка для вывода:

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

Код скрипта C# добавляет новую строку в таблицу (предполагается один столбец) на основе данных из строки запроса:

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

#### <a name="excel-output---javascript-example"></a>Output - Excel пример JavaScript

Пример добавления строк в таблицу Excel.

*Function.json* файл определяет триггер HTTP с помощью Excel привязка для вывода:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Следующий код JavaScript добавляет новую строку в таблицу (предполагается один столбец) на основании входных данных из строки запроса.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel результаты - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="excel-output---configuration"></a>Excel вывода - конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Excel`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для токена аутентификации. Дополнительные сведения см. в разделе [Использование выходной привязки таблицы Excel в коде](#excel-output-code).|
|**type**||Обязательное. Необходимо задать значение `excel`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**UserId** |**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к книге Excel в OneDrive.|
|**worksheetName**|**WorksheetName**|Лист, на котором находится таблица.|
|**tableName**|**TableName**|Это имя таблицы. Если это свойство не указано, будет использоваться содержимое листа.|
|**updateType**|**Тип обновления**|Обязательное. Тип изменения, которое требуется внести в таблицу. Принимается одно из следующих значений:<ul><li><code>update</code> — замена содержимого таблицы в OneDrive.</li><li><code>append</code> — добавление полезных данных в конец таблицы в OneDrive путем создания новых строк.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Output - Excel использования

Эта привязка необходимы следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Полный доступ к файлам пользователя|

Привязка предоставляет следующие типы функций .NET:
- string[][];
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).





<a name="onedrive-input"></a>
## <a name="file-input"></a>Файл входных данных

Привязки входного файла OneDrive считывает содержимое файла, сохраненным в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#file-input---example)
* [Атрибуты](#file-input---attributes)
* [Конфигурация](#file-input---configuration)
* [Использование](#file-input---usage)

### <a name="file-input---example"></a>Файлового ввода - пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Файлового ввода - пример скрипта C#

В следующем примере считывается файл, который хранится в OneDrive.

*Function.json* файла определяет триггер HTTP с привязкой входной файл OneDrive:

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

Код скрипта C# считывает файл, указанный в строке запроса и регистрирует его длина:

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Файлового ввода - пример JavaScript

В следующем примере считывается файл, который хранится в OneDrive.

*Function.json* файла определяет триггер HTTP с привязкой входной файл OneDrive:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Следующий код JavaScript, считывает файл, указанный в строке запроса и возвращает его длину.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Файлового ввода - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="file-input---configuration"></a>Файлового ввода - конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `OneDrive`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для файла. Дополнительные сведения см. в разделе [Использование входной привязки файлов OneDrive в коде](#onedrive-input-code).|
|**type**||Обязательное. Необходимо задать значение `onedrive`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к файлу в OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Файлового ввода - использования

Эта привязка необходимы следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Чтение файлов пользователей|

Привязка предоставляет следующие типы функций .NET:
- byte[]
- Поток
- строка
- Microsoft.Graph.DriveItem.






<a name="onedrive-output"></a>
## <a name="file-output"></a>Выходной файл

Файл OneDrive выходной привязки изменяет содержимое файлов, хранящихся в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#file-output---example)
* [Атрибуты](#file-output---attributes)
* [Конфигурация](#file-output---configuration)
* [Использование](#file-output---usage)

### <a name="file-output---example"></a>Файл вывода - пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Файл вывода - пример скрипта C#

Следующий пример записывает в файл, который хранится в OneDrive.

*Function.json* файл определяет триггер HTTP с OneDrive привязка для вывода:

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

Код C# сценарий получает текст из строки запроса и записывает его в текстовый файл (FunctionsTest.txt, определенный в предыдущем примере) в корне вызывающего OneDrive:

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

#### <a name="file-output---javascript-example"></a>Файл вывода - пример JavaScript

Следующий пример записывает в файл, который хранится в OneDrive.

*Function.json* файл определяет триггер HTTP с OneDrive привязка для вывода:

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Код JavaScript получает текст из строки запроса и записывает его в текстовый файл (FunctionsTest.txt, как определено в файле config выше) в корне OneDrive вызывающего.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Файл вывода - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="file-output---configuration"></a>Выходные данные - файл конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `OneDrive`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для файла. Дополнительные сведения см. в разделе [Использование выходной привязки файла OneDrive в коде](#onedrive-output-code).|
|**type**||Обязательное. Необходимо задать значение `onedrive`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**UserId** |**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к файлу в OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Файл вывода - использования

Эта привязка необходимы следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Полный доступ к файлам пользователя|

Привязка предоставляет следующие типы функций .NET:
- byte[]
- Поток
- строка
- Microsoft.Graph.DriveItem.





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Выходные данные Outlook

Сообщения Outlook вывода привязки отправляет сообщение электронной почты через Outlook.

Этот раздел содержит следующие подразделы:

* [Пример](#outlook-output---example)
* [Атрибуты](#outlook-output---attributes)
* [Конфигурация](#outlook-output---configuration)
* [Использование](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Вывод Outlook — пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook результаты - пример скрипта C#

Следующий пример отправляет по электронной почте через Outlook.

*Function.json* файл определяет триггер HTTP с Outlook привязка для вывода сообщений:

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

Код скрипта C# отправляет почту из вызывающего получателя, указанного в строке запроса:

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

#### <a name="outlook-output---javascript-example"></a>Outlook результаты - пример JavaScript

Следующий пример отправляет по электронной почте через Outlook.

*Function.json* файл определяет триггер HTTP с Outlook привязка для вывода сообщений:

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

Код JavaScript отправляет почту из вызывающего получателя, указанного в строке запроса:

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

### <a name="outlook-output---attributes"></a>Outlook результаты - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="outlook-output---configuration"></a>Выходные данные Outlook - конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Outlook`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `outlook`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Output - Outlook использования

Эта привязка необходимы следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Отправка почты от имени пользователя|

Привязка предоставляет следующие типы функций .NET:
- Microsoft.Graph.Message;
- Newtonsoft.Json.Linq.JObject
- строка
- пользовательские типы объектов (указанные с помощью привязки структурной модели).






## <a name="webhooks"></a>Объекты Webhook

Веб-перехватчики позволяют реагировать на события с помощью Microsoft Graph. Для поддержки веб-перехватчиков функциям требуется возможность создавать и обновлять _подписки на веб-перехватчики_, а также реагировать на них. Сочетание следующие привязки, необходимых для завершения веб-перехватчика решения:
- [Триггер веб-перехватчика Microsoft Graph](#webhook-trigger) — позволяет реагировать на действия входящего веб-перехватчика.
- [Входная привязка подписок на веб-перехватчики Microsoft Graph](#webhook-input) — позволяет вывести список существующих подписок и при необходимости обновить их.
- [Выходная привязка подписок на веб-перехватчики Microsoft Graph](#webhook-output) — позволяет создавать и удалять подписки на веб-перехватчики.

Привязки не требуется ни разрешений Azure AD, но необходимо запрашивать разрешения, относящиеся к тип ресурса, который вы хотите реагировать на. Список необходимых разрешений для каждого типа ресурса см. в разделе [о разрешениях для подписок](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Дополнительные сведения о веб-привязок см. в разделе [работа с веб-привязок в Microsoft Graph].





## <a name="webhook-trigger"></a>Триггер веб-перехватчика

Триггер веб-перехватчика Microsoft Graph позволяет функцию реагировать на перехватчик входящих Microsoft Graph. Каждый экземпляр этого триггера может реагировать на один тип ресурса Microsoft Graph.

Этот раздел содержит следующие подразделы:

* [Пример](#webhook-trigger---example)
* [Атрибуты](#webhook-trigger---attributes)
* [Конфигурация](#webhook-trigger---configuration)
* [Использование](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Веб-перехватчика триггер - пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Веб-перехватчика триггер - пример скрипта C#

Следующий пример обрабатывает веб-перехватчиков для входящих сообщений Outlook. Для использования веб-перехватчика триггера вы [создать подписку](#webhook-output---example), и вы можете [обновление подписки](#webhook-subscription-refresh) чтобы предотвратить истечение срока действия.

*Function.json* файл определяет триггер веб-перехватчика:

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

Код скрипта C# реагирует на входящие почтовые сообщения и журналы текст из них отправленное получателем и имеющее «Функции Azure» в поле темы:

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

#### <a name="webhook-trigger---javascript-example"></a>Веб-перехватчика триггер - пример JavaScript

Следующий пример обрабатывает веб-перехватчиков для входящих сообщений Outlook. Для использования веб-перехватчика триггера вы [создать подписку](#webhook-output---example), и вы можете [обновление подписки](#webhook-subscription-refresh) чтобы предотвратить истечение срока действия.

*Function.json* файл определяет триггер веб-перехватчика:

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

Код JavaScript реагирует на входящие почтовые сообщения и журналы текст из них отправленное получателем и имеющее «Функции Azure» в поле темы:

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

### <a name="webhook-trigger---attributes"></a>Веб-перехватчика триггер - атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-trigger---configuration"></a>Веб-перехватчика триггер - конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `GraphWebHookTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `graphWebhook`.|
|**direction**||Обязательное. Необходимо задать значение `trigger`.|
|**resourceType**|**Типа ресурса**|Обязательное. Ресурс Graph, для которого эта функция должна отвечать на вызовы веб-перехватчиков. Принимается одно из следующих значений:<ul><li><code>#Microsoft.Graph.Message</code> — изменения, которые вносятся в сообщения Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> — изменения, которые вносятся в корневые элементы OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>-изменения, внесенные в личных контактов в Outlook.</li><li><code>#Microsoft.Graph.Event</code>-изменения, внесенные в элементы календаря Outlook.</li></ul>|

> [!Note]
> Приложение функция может иметь только одну функцию, зарегистрированного для данного `resourceType` значение.

### <a name="webhook-trigger---usage"></a>Веб-перехватчика триггер - использования

Привязка предоставляет следующие типы функций .NET:
- Пакет SDK для Microsoft Graph типы актуальны для типа ресурса, например `Microsoft.Graph.Message` или `Microsoft.Graph.DriveItem`.
- пользовательские типы объектов (указанные с помощью привязки структурной модели).




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Входные данные веб-перехватчика

Входной привязки веб-перехватчика Microsoft Graph позволяет получить список подписок, управляемых этим приложением функции. Привязки считывает из функции приложения хранилища, поэтому он не отражает другие подписки, созданные из за пределами приложения.

Этот раздел содержит следующие подразделы:

* [Пример](#webhook-input---example)
* [Атрибуты](#webhook-input---attributes)
* [Конфигурация](#webhook-input---configuration)
* [Использование](#webhook-input---usage)

### <a name="webhook-input---example"></a>Входные данные веб-перехватчика - пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Input — веб-перехватчика пример скрипта C#

Следующий пример возвращает все подписки для вызывающего пользователя и удаляет их.

*Function.json* файла определяет триггер HTTP с привязкой ввода подписки и подписку вывода, привязка, которая использует действие delete:

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

Код скрипта C# возвращает подписки и удаляет их.

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

#### <a name="webhook-input---javascript-example"></a>Input — веб-перехватчика пример JavaScript

Следующий пример возвращает все подписки для вызывающего пользователя и удаляет их.

*Function.json* файла определяет триггер HTTP с привязкой ввода подписки и подписку вывода, привязка, которая использует действие delete:

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

Код JavaScript возвращает подписки и удаляет их.

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

### <a name="webhook-input---attributes"></a>Input — веб-перехватчика атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-input---configuration"></a>Входные данные веб-перехватчика - конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `GraphWebHookSubscription`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `graphWebhookSubscription`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**filter**|**Filter**| Если значение `userFromRequest`, то привязка будет получать только подписок, принадлежащих вызывающему пользователю (доступен только при использовании [триггером HTTP]).| 

### <a name="webhook-input---usage"></a>Input — веб-перехватчика использования

Привязка предоставляет следующие типы функций .NET:
- string[]
- массивы пользовательских типов объектов;
- Newtonsoft.Json.Linq.JObject[];
- Microsoft.Graph.Subscription[].





## <a name="webhook-output"></a>Выходные данные веб-перехватчика

Веб-перехватчика подписку вывода привязка позволяет создавать, удалять и обновлять веб-перехватчика подписок в Microsoft Graph.

Этот раздел содержит следующие подразделы:

* [Пример](#webhook-output---example)
* [Атрибуты](#webhook-output---attributes)
* [Конфигурация](#webhook-output---configuration)
* [Использование](#webhook-output---usage)

### <a name="webhook-output---example"></a>Выходные данные веб-перехватчика - пример

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Output - веб-перехватчика пример скрипта C#

В следующем примере создается подписка. Вы можете [обновление подписки](#webhook-subscription-refresh) чтобы предотвратить истечение срока действия.

*Function.json* файла определяет триггер HTTP с помощью привязки с использованием действие создания вывода подписки:

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

Код скрипта C# регистрирует webhook, который будет уведомлять приложение функции вызывающий пользователь получает сообщение Outlook:

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

#### <a name="webhook-output---javascript-example"></a>Output - веб-перехватчика пример JavaScript

В следующем примере создается подписка. Вы можете [обновление подписки](#webhook-subscription-refresh) чтобы предотвратить истечение срока действия.

*Function.json* файла определяет триггер HTTP с помощью привязки с использованием действие создания вывода подписки:

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

Код JavaScript регистрирует webhook, который будет уведомлять приложение функции вызывающий пользователь получает сообщение Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Output - веб-перехватчика атрибуты

В [библиотеки классов C#](functions-dotnet-class-library.md), используйте [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) атрибут, который определен в пакет NuGet [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/).

### <a name="webhook-output---configuration"></a>Выходные данные веб-перехватчика - конфигурации

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `GraphWebHookSubscription`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `graphWebhookSubscription`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [триггером HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**action**|**Действие**|Обязательное. Указывает действие, которое необходимо выполнить с помощью привязки. Принимается одно из следующих значений:<ul><li><code>create</code> — регистрация новой подписки.</li><li><code>delete</code> — удаление указанной подписки.</li><li><code>refresh</code> — обновление указанной подписки. Позволяет предотвратить истечение срока действия.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Требуется, только если для свойства _action_ задано значение `create`. Указывает ресурс Microsoft Graph, изменения которого будут отслеживаться. Дополнительные сведения см. в статье [работа с веб-привязок в Microsoft Graph]. |
|**changeType**|**ChangeType**|Требуется, только если для свойства _action_ задано значение `create`. Указывает тип изменения в подписанном ресурсе, при внесении которого будет создано уведомление. Поддерживаются такие значения: `created`, `updated`, `deleted`. Можно объединить несколько значений, указав их через запятую.|

### <a name="webhook-output---usage"></a>Output - веб-перехватчика использования

Привязка предоставляет следующие типы функций .NET:
- строка
- Microsoft.Graph.Subscription.




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Обновление подписки веб-перехватчика

Существует два способа обновления подписок:

- Использование удостоверения приложения для всех подписок. Для этого потребуется согласие администратора Azure Active Directory. Этот способ можно применять ко всем языкам, которые поддерживает решение "Функции Azure".
- Использование удостоверения, связанного с каждой подпиской, путем привязки каждого идентификатора пользователя вручную. В этом случае для выполнения привязки потребуется пользовательский код. Этот метод можно использовать только с функциями .NET.

Этот раздел содержит пример для каждого из этих подходов:

* [Пример идентификаторов приложений](#webhook-subscription-refresh---app-identity-example)
* [Пример удостоверение пользователя](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Обновление подписки веб-перехватчика - пример идентификаторов приложений

Языковой пример см. в разделах:

* [Скрипт C# (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Обновление удостоверения приложение - пример скрипта C#

Следующий пример использует удостоверение приложения для обновления подписки.

*Function.json* определяет триггер таймера в подписку входной привязки и подписку вывода привязки:

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

Код скрипта C# обновляет подписок:

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

### <a name="app-identity-refresh---c-script-example"></a>Обновление удостоверения приложение - пример скрипта C#

Следующий пример использует удостоверение приложения для обновления подписки.

*Function.json* определяет триггер таймера в подписку входной привязки и подписку вывода привязки:

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

Код JavaScript обновляет подписок:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Обновление подписки веб-перехватчика - пример удостоверение пользователя

Следующий пример использует удостоверение пользователя для обновления подписки.

*Function.json* файла определяет триггер таймера и переложить привязка подписки ввода кода функции:

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

Код скрипта C# обновляет подписки и создает выходной привязки в коде, используя удостоверение каждого пользователя.

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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

[триггером HTTP]: functions-bindings-http-webhook.md
[работа с веб-привязок в Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
