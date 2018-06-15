---
title: Привязки Microsoft Graph для службы "Функции Azure"
description: Узнайте, как использовать триггеры и привязки Microsoft Graph в решении "Функции Azure".
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: 3b9a7d998e7153318b21adcada7c143b428e591f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724780"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Привязки Microsoft Graph для службы "Функции Azure"

В этой статье объясняется, как настраивать и использовать триггеры и привязки Microsoft Graph в решении "Функции Azure". Они помогут вам использовать решение "Функции Azure" для работы с данными, аналитической информацией и событиями в [Microsoft Graph](https://graph.microsoft.io).

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
> Привязки Microsoft Graph сейчас доступны в предварительной версии для Функций Azure версии 2.x. Они не поддерживаются в Функциях версии 1.x.

## <a name="packages"></a>Пакеты

Входная привязка маркера проверки подлинности содержится в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/). Другие привязки Microsoft Graph содержатся в пакете [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/). Исходный код для пакетов находится в репозитории GitHub [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Настройка расширений

Доступ к привязкам Microsoft Graph можно получить, используя _расширения привязок_. Расширения привязок — это дополнительные компоненты для среды выполнения решения "Функции Azure". В этом разделе объясняется, как настроить расширения Microsoft Graph и маркера проверки подлинности.

### <a name="enabling-functions-20-preview"></a>Включение выпуска 2.0 решения "Функции Azure" (предварительная версия)

Расширения привязок доступны только для выпуска 2.0 решения "Функции Azure" (предварительная версия). 

Сведения о настройке приложения-функции для использования среды выполнения Функций версии 2.0 (предварительная версия) см. в статье [Выбор целевых версий среды выполнения Функций Azure](set-runtime-version.md).

### <a name="installing-the-extension"></a>Установка расширения

Чтобы установить расширение с портала Azure, перейдите к шаблону или привязке, которые ссылаются на это расширение. Создайте новую функцию и в окне выбора шаблона выберите сценарий Microsoft Graph. Выберите один из шаблонов в этом сценарии. Вы также можете перейти на вкладку "Интегрировать" имеющейся функции и выбрать одну из привязок, которые рассматриваются в этой статье.

В обоих случаях появится предупреждение о том, что необходимо установить расширение. Нажмите кнопку **Установить**, чтобы получить расширение. Для каждого приложения-функции достаточно установить расширение один раз. 

> [!Note] 
> В рамках плана потребления процесс установки на портале может занять до 10 минут.

Если используется Visual Studio, вы можете получить расширения, установив [пакеты NuGet, указанные выше в этой статье](#packages).

### <a name="configuring-authentication--authorization"></a>Настройка аутентификации или авторизации

Для привязок, приведенных в этой статье, необходимо использовать удостоверение. Это позволит Microsoft Graph принудительно применять разрешения и выполнять аудит взаимодействия. Под удостоверением может подразумеваться пользователь, который пытается получить доступ к приложению, или само приложение. Чтобы настроить это удостоверение, настройте [аутентификацию и авторизацию в службе приложений](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) с помощью Azure Active Directory. Вам также понадобится запросить все разрешения на ресурсы, требуемые для функций.

> [!Note] 
> Расширение Microsoft Graph поддерживает только аутентификацию с помощью Azure AD. Для входа пользователи должны указывать рабочую или учебную учетную запись.

Если используется портал Azure, под запросом на установку расширения появится предупреждение о том, что необходимо настроить аутентификацию и авторизацию в службе приложений и запросить все разрешения, требуемые для шаблонов или привязок. Щелкните **Configure Azure AD now** (Настроить Azure AD сейчас) или **Добавить разрешения сейчас** соответственно.



<a name="token-input"></a>
## <a name="auth-token"></a>Маркер проверки подлинности

Эта входная привязка маркера проверки подлинности позволяет получить маркер Azure AD для определенного ресурса и вставить его в код в виде строки. Может использоваться любой ресурс, на который у приложения есть разрешения. 

Этот раздел содержит следующие подразделы:

* [Пример](#auth-token---example)
* [Атрибуты](#auth-token---attributes)
* [Конфигурация](#auth-token---configuration)
* [Использование](#auth-token---usage)

### <a name="auth-token---example"></a>Пример с маркером проверки подлинности

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Пример с маркером проверки подлинности: скрипт C#

В следующем примере возвращаются сведения о профиле пользователя.

Файл *function.json* определяет триггер HTTP с входной привязкой маркера:

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

В коде скрипта C# маркер используется для выполнения HTTP-вызова к Microsoft Graph и вывода результатов.

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

#### <a name="auth-token---javascript-example"></a>Пример с маркером проверки подлинности: JavaScript

В следующем примере возвращаются сведения о профиле пользователя.

Файл *function.json* определяет триггер HTTP с входной привязкой маркера:

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

Код JavaScript использует маркер для выполнения HTTP-вызова к Microsoft Graph и вывода результатов.

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

### <a name="auth-token---attributes"></a>Атрибуты маркера проверки подлинности

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs).

### <a name="auth-token---configuration"></a>Настройка маркера проверки подлинности

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Token`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для токена аутентификации. Дополнительные сведения см. в разделе [Использование входной привязки токена аутентификации в коде](#token-input-code).|
|**type**||Обязательное. Необходимо задать значение `token`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**Ресурс**|**resource**|Обязательное. URL-адрес ресурса Azure AD, для которого запрашивается маркер.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Использование маркера проверки подлинности

Самой привязке не требуются разрешения Azure AD, но в зависимости от того, как используется маркер, может потребоваться запросить дополнительные разрешения. Просмотрите требования ресурса, к которому необходимо получить доступ с помощью токена.

В коде токен всегда представлен виде строки.




<a name="excel-input"></a>
## <a name="excel-input"></a>Входная привязка Excel

Эта входная привязка таблицы Excel позволяет считывать содержимое из таблицы, хранящейся в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#excel-input---example)
* [Атрибуты](#excel-input---attributes)
* [Конфигурация](#excel-input---configuration)
* [Использование](#excel-input---usage)

### <a name="excel-input---example"></a>Пример со входной привязкой Excel

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Пример со входной привязкой Excel: скрипт C#

Следующий файл *function.json* определяет триггер HTTP с входной привязкой Excel:

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

В следующем коде скрипта C# считывается и возвращается пользователю содержимое указанной таблицы.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Пример со входной привязкой Excel: JavaScript

Следующий файл *function.json* определяет триггер HTTP с входной привязкой Excel:

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

Следующий код скрипта JavaScript считывает и возвращает пользователю содержимое указанной таблицы.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Атрибуты входной привязки Excel

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-input---configuration"></a>Конфигурация входной привязки Excel

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Excel`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для таблицы Excel. Дополнительные сведения см. в разделе [Использование входной привязки таблицы Excel в коде](#excel-input-code).|
|**type**||Обязательное. Необходимо задать значение `excel`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к книге Excel в OneDrive.|
|**worksheetName**|**WorksheetName**|Лист, на котором находится таблица.|
|**tableName**|**TableName**|Это имя таблицы. Если это свойство не указано, будет использоваться содержимое листа.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Использование входной привязки Excel

Для этой привязки требуются следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Чтение файлов пользователей|

Привязка предоставляет следующие типы функций .NET:
- string[][];
- Microsoft.Graph.WorkbookTable;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).










<a name="excel-output"></a>
## <a name="excel-output"></a>Выходная привязка Excel

Эта выходная привязка Excel позволяет изменять содержимое таблицы Excel, хранящейся в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#excel-output---example)
* [Атрибуты](#excel-output---attributes)
* [Конфигурация](#excel-output---configuration)
* [Использование](#excel-output---usage)

### <a name="excel-output---example"></a>Пример с выходной привязкой Excel

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Пример с выходной привязкой Excel: скрипт C#

Показанный ниже пример добавляет строки в таблицу Excel.

Файл *function.json* определяет триггер HTTP с выходной привязкой Excel.

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

В следующем коде скрипта C# в таблицу (предполагается, что это таблица с одним столбцом) добавляется новая строка на основе входных данных из строки запроса.

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

#### <a name="excel-output---javascript-example"></a>Пример с выходной привязкой Excel: JavaScript

Показанный ниже пример добавляет строки в таблицу Excel.

Файл *function.json* определяет триггер HTTP с выходной привязкой Excel.

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

В следующем примере кода JavaScript в таблицу (предполагается, что это таблица с одним столбцом) добавляется новая строка на основе входных данных из строки запроса.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Атрибуты выходной привязки Excel

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-output---configuration"></a>Конфигурация выходной привязки Excel

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Excel`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для токена аутентификации. Дополнительные сведения см. в разделе [Использование выходной привязки таблицы Excel в коде](#excel-output-code).|
|**type**||Обязательное. Необходимо задать значение `excel`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**UserId** |**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к книге Excel в OneDrive.|
|**worksheetName**|**WorksheetName**|Лист, на котором находится таблица.|
|**tableName**|**TableName**|Это имя таблицы. Если это свойство не указано, будет использоваться содержимое листа.|
|**updateType**|**UpdateType**|Обязательное. Тип изменения, которое требуется внести в таблицу. Принимается одно из следующих значений:<ul><li><code>update</code> — замена содержимого таблицы в OneDrive.</li><li><code>append</code> — добавление полезных данных в конец таблицы в OneDrive путем создания новых строк.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Использование выходной привязки Excel

Для этой привязки требуются следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Полный доступ к файлам пользователя|

Привязка предоставляет следующие типы функций .NET:
- string[][];
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable;
- пользовательские типы объектов (указанные с помощью привязки структурной модели).





<a name="onedrive-input"></a>
## <a name="file-input"></a>Входная привязка файла

Эта входная привязка файлов OneDrive позволяет считывать содержимое файла, хранящегося в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#file-input---example)
* [Атрибуты](#file-input---attributes)
* [Конфигурация](#file-input---configuration)
* [Использование](#file-input---usage)

### <a name="file-input---example"></a>Пример со входной привязкой файла

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Пример со входной привязкой файла: скрипт C#

В следующем примере считывается файл, хранящийся в OneDrive.

Файл *function.json* определяет триггер HTTP с входной привязкой файла OneDrive:

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

В следующем коде скрипта C# считывается файл, указанный в строке запроса, и регистрируется размер файла.

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Пример со входной привязкой файла: JavaScript

В следующем примере считывается файл, хранящийся в OneDrive.

Файл *function.json* определяет триггер HTTP с входной привязкой файла OneDrive:

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

Следующий код JavaScript считывает файл, указанный в строке запроса, и возвращает размер файла.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Атрибуты входной привязки файла

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-input---configuration"></a>Конфигурация входной привязки файла

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `OneDrive`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для файла. Дополнительные сведения см. в разделе [Использование входной привязки файлов OneDrive в коде](#onedrive-input-code).|
|**type**||Обязательное. Необходимо задать значение `onedrive`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к файлу в OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Использование входной привязки файла

Для этой привязки требуются следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Чтение файлов пользователей|

Привязка предоставляет следующие типы функций .NET:
- byte[]
- Поток
- строка
- Microsoft.Graph.DriveItem.






<a name="onedrive-output"></a>
## <a name="file-output"></a>Выходная привязка файла

Эта выходная привязка файла OneDrive позволяет изменять содержимое файла, хранящегося в OneDrive.

Этот раздел содержит следующие подразделы:

* [Пример](#file-output---example)
* [Атрибуты](#file-output---attributes)
* [Конфигурация](#file-output---configuration)
* [Использование](#file-output---usage)

### <a name="file-output---example"></a>Пример с выходной привязкой файла

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Пример с выходной привязкой файла: скрипт C#

В следующем примере данные записываются в файл, хранящийся в OneDrive.

Файл *function.json* определяет триггер HTTP с выходной привязкой OneDrive:

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

В следующем коде скрипта C# текст извлекается из строки запроса и записывается в текстовый файл (FunctionsTest.txt, как определено в предыдущем примере) в корневой папке в OneDrive вызывающего пользователя.

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

#### <a name="file-output---javascript-example"></a>Пример с выходной привязкой файла: JavaScript

В следующем примере данные записываются в файл, хранящийся в OneDrive.

Файл *function.json* определяет триггер HTTP с выходной привязкой OneDrive:

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

В коде JavaScript текст извлекается из строки запроса и записывается в текстовый файл (FunctionsTest.txt, как определено в файле конфигурации выше) в корневой папке в OneDrive вызывающего пользователя.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Атрибуты выходной привязки файла

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-output---configuration"></a>Конфигурация выходной привязки файла

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `OneDrive`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для файла. Дополнительные сведения см. в разделе [Использование выходной привязки файла OneDrive в коде](#onedrive-output-code).|
|**type**||Обязательное. Необходимо задать значение `onedrive`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**UserId** |**userId** |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**path**|**Путь**|Обязательное. Путь к файлу в OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Использование выходной привязки файла

Для этой привязки требуются следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Полный доступ к файлам пользователя|

Привязка предоставляет следующие типы функций .NET:
- byte[]
- Поток
- строка
- Microsoft.Graph.DriveItem.





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Выходная привязка Outlook

Выходная привязка сообщений Outlook позволяет отправлять сообщения электронной почты через Outlook.

Этот раздел содержит следующие подразделы:

* [Пример](#outlook-output---example)
* [Атрибуты](#outlook-output---attributes)
* [Конфигурация](#outlook-output---configuration)
* [Использование](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Пример с выходной привязкой Outlook

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Пример с выходной привязкой Outlook: скрипт C#

Следующий пример позволяет отправлять сообщения электронной почты через Outlook.

Файл *function.json* определяет триггер HTTP с выходной привязкой сообщений Outlook:

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

В следующем коде скрипта C# отправляется электронное сообщение от вызывающего пользователя получателю, указанному в строке запроса.

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

#### <a name="outlook-output---javascript-example"></a>Пример с выходной привязкой Outlook: JavaScript

Следующий пример позволяет отправлять сообщения электронной почты через Outlook.

Файл *function.json* определяет триггер HTTP с выходной привязкой сообщений Outlook:

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

В коде JavaScript отправляется электронное сообщение от вызывающего пользователя получателю, указанному в строке запроса.

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

### <a name="outlook-output---attributes"></a>Атрибуты выходной привязки Outlook

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs).

### <a name="outlook-output---configuration"></a>Конфигурация выходной привязки Outlook

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `Outlook`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `outlook`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Использование выходной привязки Outlook

Для этой привязки требуются следующие разрешения Azure AD:
|Ресурс|Разрешение|
|--------|--------|
|Microsoft Graph|Отправка почты от имени пользователя|

Привязка предоставляет следующие типы функций .NET:
- Microsoft.Graph.Message;
- Newtonsoft.Json.Linq.JObject
- строка
- пользовательские типы объектов (указанные с помощью привязки структурной модели).






## <a name="webhooks"></a>Объекты Webhook

Веб-перехватчики позволяют реагировать на события с помощью Microsoft Graph. Для поддержки веб-перехватчиков функциям требуется возможность создавать и обновлять _подписки на веб-перехватчики_, а также реагировать на них. Для полноценного решения веб-перехватчика требуется сочетание следующих привязок:
- [Триггер веб-перехватчика Microsoft Graph](#webhook-trigger) — позволяет реагировать на действия входящего веб-перехватчика.
- [Входная привязка подписок на веб-перехватчики Microsoft Graph](#webhook-input) — позволяет вывести список существующих подписок и при необходимости обновить их.
- [Выходная привязка подписок на веб-перехватчики Microsoft Graph](#webhook-output) — позволяет создавать и удалять подписки на веб-перехватчики.

Самим привязкам не требуются разрешения Azure AD, но вам необходимо запросить разрешения в соответствии с типом ресурса, на действия которого необходимо реагировать. Список необходимых разрешений для каждого типа ресурса см. в разделе [о разрешениях для подписок](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions).

Дополнительные сведения о веб-перехватчиках см. в статье [Работа с веб-перехватчиками в Microsoft Graph].





## <a name="webhook-trigger"></a>Триггер веб-перехватчика

Этот триггер веб-перехватчика Microsoft Graph позволяет функции реагировать на действия входящего веб-перехватчика в Microsoft Graph. Каждый экземпляр этого триггера может реагировать на один тип ресурса Microsoft Graph.

Этот раздел содержит следующие подразделы:

* [Пример](#webhook-trigger---example)
* [Атрибуты](#webhook-trigger---attributes)
* [Конфигурация](#webhook-trigger---configuration)
* [Использование](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Пример с триггером веб-перехватчика

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Пример с триггером веб-перехватчика: скрипт C#

В следующем примере обрабатываются веб-перехватчики для входящих сообщений Outlook. Чтобы использовать триггер веб-перехватчика, необходимо [создать подписку](#webhook-output---example). Вы также можете [обновить подписку](#webhook-subscription-refresh), чтобы предотвратить истечение ее срока действия.

Файл *function.json* определяет триггер веб-перехватчика:

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

В следующем примере кода скрипта C# показана реакция на входящие электронные сообщения и регистрация текста сообщений с темой "Функции Azure", отправленных получателем.

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

#### <a name="webhook-trigger---javascript-example"></a>Пример с триггером веб-перехватчика: JavaScript

В следующем примере обрабатываются веб-перехватчики для входящих сообщений Outlook. Чтобы использовать триггер веб-перехватчика, необходимо [создать подписку](#webhook-output---example). Вы также можете [обновить подписку](#webhook-subscription-refresh), чтобы предотвратить истечение ее срока действия.

Файл *function.json* определяет триггер веб-перехватчика:

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

В коде JavaScript показана реакция на входящие электронные сообщения и регистрация текста сообщений с темой "Функции Azure", отправленных получателем.

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

### <a name="webhook-trigger---attributes"></a>Атрибуты триггера веб-перехватчика

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs).

### <a name="webhook-trigger---configuration"></a>Конфигурация триггера веб-перехватчика

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `GraphWebHookTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `graphWebhook`.|
|**direction**||Обязательное. Необходимо задать значение `trigger`.|
|**resourceType**|**ResourceType**|Обязательное. Ресурс Graph, для которого эта функция должна отвечать на вызовы веб-перехватчиков. Принимается одно из следующих значений:<ul><li><code>#Microsoft.Graph.Message</code> — изменения, которые вносятся в сообщения Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code> — изменения, которые вносятся в корневые элементы OneDrive.</li><li><code>#Microsoft.Graph.Contact</code> —изменения, которые вносятся в личные контакты в Outlook.</li><li><code>#Microsoft.Graph.Event</code> — изменения, которые вносятся в элементы календаря Outlook.</li></ul>|

> [!Note]
> У приложения-функции может быть только одна функция, зарегистрированная для заданного значения `resourceType`.

### <a name="webhook-trigger---usage"></a>Использование триггера веб-перехватчика

Привязка предоставляет следующие типы функций .NET:
- Типы пакетов SDK для Microsoft Graph, соответствующие типам ресурсов, например `Microsoft.Graph.Message` или `Microsoft.Graph.DriveItem`.
- пользовательские типы объектов (указанные с помощью привязки структурной модели).




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Входная привязка веб-перехватчика

Входная привязка веб-перехватчика в Microsoft Graph позволяет получить список подписок, управляемых этим приложением-функцией. Привязка позволяет считывать данные из хранилища приложений-функций и не выводит другие подписки, созданные вне приложения.

Этот раздел содержит следующие подразделы:

* [Пример](#webhook-input---example)
* [Атрибуты](#webhook-input---attributes)
* [Конфигурация](#webhook-input---configuration)
* [Использование](#webhook-input---usage)

### <a name="webhook-input---example"></a>Пример со входной привязкой веб-перехватчика

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Пример со входной привязкой веб-перехватчика: скрипт C#

В следующем примере выводятся и удаляются все подписки для вызывающего пользователя.

Файл *function.json* определяет триггер HTTP с входной и выходной привязкой подписки с использованием действия удаления:

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

В следующем коде скрипта C# возвращаются, а затем удаляются подписки.

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

#### <a name="webhook-input---javascript-example"></a>Пример со входной привязкой веб-перехватчика: JavaScript

В следующем примере выводятся и удаляются все подписки для вызывающего пользователя.

Файл *function.json* определяет триггер HTTP с входной и выходной привязкой подписки с использованием действия удаления:

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

В коде JavaScript возвращаются, а затем удаляются подписки.

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

### <a name="webhook-input---attributes"></a>Атрибуты входной привязки веб-перехватчика

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-input---configuration"></a>Конфигурация входной привязки веб-перехватчика

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `GraphWebHookSubscription`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `graphWebhookSubscription`.|
|**direction**||Обязательное. Необходимо задать значение `in`.|
|**filter**|**Filter**| Если задано значение `userFromRequest`, привязка будет получать только подписки, принадлежащие вызывающему пользователю (применяется только с [Триггер HTTP]).| 

### <a name="webhook-input---usage"></a>Использование входной привязки веб-перехватчика

Привязка предоставляет следующие типы функций .NET:
- string[]
- массивы пользовательских типов объектов;
- Newtonsoft.Json.Linq.JObject[];
- Microsoft.Graph.Subscription[].





## <a name="webhook-output"></a>Выходная привязка веб-перехватчика

Выходная привязка подписки веб-перехватчика позволяет создавать, удалять и обновлять подписки веб-перехватчиков в Microsoft Graph.

Этот раздел содержит следующие подразделы:

* [Пример](#webhook-output---example)
* [Атрибуты](#webhook-output---attributes)
* [Конфигурация](#webhook-output---configuration)
* [Использование](#webhook-output---usage)

### <a name="webhook-output---example"></a>Пример с выходной привязкой веб-перехватчика

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Пример с выходной привязкой веб-перехватчика: скрипт C#

В следующем примере создается подписка. Вы можете [обновить подписку](#webhook-subscription-refresh), чтобы предотвратить истечение ее срока действия.

Файл *function.json* определяет триггер HTTP с выходной привязкой подписки с использованием действия создания:

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
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
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

В следующем коде скрипта C# регистрируется веб-перехватчик, который отправит уведомление этому приложению-функции, когда вызывающий пользователь получит сообщение в Outlook.

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

#### <a name="webhook-output---javascript-example"></a>Пример с выходной привязкой веб-перехватчика: JavaScript

В следующем примере создается подписка. Вы можете [обновить подписку](#webhook-subscription-refresh), чтобы предотвратить истечение ее срока действия.

Файл *function.json* определяет триггер HTTP с выходной привязкой подписки с использованием действия создания:

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
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
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

В коде JavaScript показана регистрация веб-перехватчика, который отправит уведомление этому приложению-функции, когда вызывающий пользователь получит сообщение в Outlook.

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Атрибуты выходной привязки веб-перехватчика

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-output---configuration"></a>Конфигурация выходной привязки веб-перехватчика

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `GraphWebHookSubscription`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**name**||Обязательное. Имя переменной, используемое в коде функции для сообщения электронной почты. Дополнительные сведения см. в разделе [Использование выходной привязки сообщений Outlook в коде](#outlook-output-code).|
|**type**||Обязательное. Необходимо задать значение `graphWebhookSubscription`.|
|**direction**||Обязательное. Необходимо задать значение `out`.|
|**identity**|**Удостоверение**|Обязательное. Удостоверение, которое будет использоваться для выполнения действия. Принимается одно из следующих значений:<ul><li><code>userFromRequest</code> — применяется только с [Триггер HTTP]. Позволяет использовать удостоверение вызывающего пользователя.</li><li><code>userFromId</code> — позволяет использовать удостоверение пользователя, ранее вошедшего в систему, с указанным идентификатором. См. описание свойства <code>userId</code>.</li><li><code>userFromToken</code> — позволяет использовать удостоверение, представленное указанным токеном. См. описание свойства <code>userToken</code>.</li><li><code>clientCredentials</code> — позволяет использовать удостоверение приложения-функции.</li></ul>|
|**userId**|**UserId**  |Требуется, только если для свойства _identity_ задано значение `userFromId`. Идентификатор субъекта-пользователя, связанный с пользователем, ранее вошедшим в систему.|
|**userToken**|**UserToken**|Требуется, только если для свойства _identity_ задано значение `userFromToken`. Токен, предназначенный для приложения-функции. |
|**action**|**Действие**|Обязательное. Указывает действие, которое необходимо выполнить с помощью привязки. Принимается одно из следующих значений:<ul><li><code>create</code> — регистрация новой подписки.</li><li><code>delete</code> — удаление указанной подписки.</li><li><code>refresh</code> — обновление указанной подписки. Позволяет предотвратить истечение срока действия.</li></ul>|
|**subscriptionResource**|**subscriptionResource**|Требуется, только если для свойства _action_ задано значение `create`. Указывает ресурс Microsoft Graph, изменения которого будут отслеживаться. Дополнительные сведения см. в статье [Работа с веб-перехватчиками в Microsoft Graph]. |
|**changeType**|**ChangeType**|Требуется, только если для свойства _action_ задано значение `create`. Указывает тип изменения в подписанном ресурсе, при внесении которого будет создано уведомление. Поддерживаются такие значения: `created`, `updated`, `deleted`. Можно объединить несколько значений, указав их через запятую.|

### <a name="webhook-output---usage"></a>Использование выходной привязки веб-перехватчика

Привязка предоставляет следующие типы функций .NET:
- строка
- Microsoft.Graph.Subscription.




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Обновление подписки веб-перехватчика

Существует два способа обновления подписок:

- Использование удостоверения приложения для всех подписок. Для этого потребуется согласие администратора Azure Active Directory. Этот способ можно применять ко всем языкам, которые поддерживает решение "Функции Azure".
- Использование удостоверения, связанного с каждой подпиской, путем привязки каждого идентификатора пользователя вручную. В этом случае для выполнения привязки потребуется пользовательский код. Этот метод можно использовать только с функциями .NET.

В этом разделе содержатся примеры для каждого из этих подходов:

* [Пример с удостоверением приложения](#webhook-subscription-refresh---app-identity-example)
* [Пример с удостоверением пользователя](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Пример обновления подписки веб-перехватчика с использованием удостоверения приложения

Языковой пример см. в разделах:

* [Скрипт C# (CSX)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>Пример обновления с использованием удостоверения приложения: скрипт C#

В следующем примере для обновления подписки используется удостоверение приложения.

Файл *function.json* определяет триггер таймера со входной и выходной привязками подписки.

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

Обновление подписок с использованием кода скрипта C#:

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

### <a name="app-identity-refresh---c-script-example"></a>Пример обновления с использованием удостоверения приложения: скрипт C#

В следующем примере для обновления подписки используется удостоверение приложения.

Файл *function.json* определяет триггер таймера со входной и выходной привязками подписки.

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

Обновление подписок с использованием кода JavaScript:

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Пример обновления подписки веб-перехватчика с использованием удостоверения пользователя

В следующем примере для обновления подписки используется удостоверение пользователя.

Файл *function.json* определяет триггер таймера и передает входную привязку подписки в код функции:

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

В следующем примере кода скрипта C# обновляются подписки и создается выходная привязка с использованием удостоверения каждого пользователя.

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

[Триггер HTTP]: functions-bindings-http-webhook.md
[Работа с веб-перехватчиками в Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
