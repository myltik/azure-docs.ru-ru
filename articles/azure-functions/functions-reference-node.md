---
title: Справочник разработчика JavaScript для Функций Azure | Документация Майкрософт
description: Узнайте, как разрабатывать функции с помощью JavaScript.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: tdykstra
ms.openlocfilehash: 78f29cd4a20861e40bb7f7f398979b8d93387a7b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936632"
---
# <a name="azure-functions-javascript-developer-guide"></a>Руководство разработчика JavaScript для Функций Azure

Интерфейс JavaScript для Azure Functions позволяет легко экспортировать функцию, которая передается в качестве объекта `context`, для взаимодействия со средой выполнения, а также для получения и отправки данных с помощью привязок.

В этой статье предполагается, что вы уже прочли [справочник разработчика по Функциям Azure](functions-reference.md).

## <a name="exporting-a-function"></a>Экспорт функции
Все функции JavaScript должны экспортировать одну `function` с помощью `module.exports`, чтобы среда выполнения могла найти эту функцию и запустить ее. Эта функция должна всегда включать объект `context` .

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Привязки `direction === "in"` передаются как аргументы функции, то есть вы можете использовать [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) для динамической обработки новых входных данных (например, используя `arguments.length` для итерации всех входных данных). Эта возможность удобна, когда у вас есть только триггер без дополнительных входных данных, так как к данным триггера можно обращаться напрямую, без ссылки на объект `context`.

Аргументы всегда передаются в функцию в порядке их появления в файле *function.json*, даже если они не указаны в инструкциях экспорта. Например, если у вас есть функция `function(context, a, b)` и вы изменяете ее на `function(context, a)`, то значение `b` все равно можно получить в коде функции с помощью `arguments[2]`.

Все привязки, независимо от направления, также передаются в объекте `context` (см. указанный ниже скрипт). 

## <a name="context-object"></a>Объект context
Среда выполнения использует объект `context` для передачи данных в функцию и из нее, а также для взаимодействия со средой выполнения.

Объект `context` всегда является первым параметром функции, и его надо указывать всегда, так как он содержит методы, необходимые для правильного использования среды выполнения, такие как `context.done` и `context.log`. Для этого объекта можно указать любое имя (например, `ctx` или `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>Свойство context.bindings

```
context.bindings
```
Возвращает именованный объект, который содержит все входные и выходные данные. Например, следующее определение привязки в *function.json* позволяет вам получить доступ к содержимому очереди с помощью объекта `context.bindings.myInput`. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### <a name="contextdone-method"></a>Метод context.done
```
context.done([err],[propertyBag])
```

Информирует среду выполнения о завершении выполнения кода. Необходимо вызвать метод `context.done`, или в противном случае среда выполнения не узнает, что функция завершена, и время ожидания выполнения истечет. 

Метод `context.done` позволяет передавать в среду выполнения пользовательское сообщение об ошибке, а также контейнер свойств, которые перезапишут свойства объекта `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>Метод context.log  

```
context.log(message)
```
Этот метод позволяет делать записи в потоковые журналы консоли на уровне трассировки по умолчанию. В `context.log` доступны дополнительные методы ведения журнала, позволяющие выполнять запись в журнал консоли на других уровнях трассировки:


| Метод                 | ОПИСАНИЕ                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Записывает сообщение в журнал на уровне ошибок или более низком.   |
| **warn(_message_)**    | Записывает сообщение в журнал на уровне предупреждений или более низком. |
| **info(_message_)**    | Записывает сообщение в журнал на уровне сведений или более низком.    |
| **verbose(_message_)** | Записывает сообщение в журнал на уровне детализации.           |

Следующий пример записывает в консоль на уровне трассировки "предупреждения":

```javascript
context.log.warn("Something has happened."); 
```
Вы можете задать пороговое значение уровня трассировки для ведения журнала в файле host.json или отключить эту функцию.  Дополнительные сведения о том, как делать записи в журнал, см. в следующем разделе.

## <a name="binding-data-type"></a>Тип данных привязки

Чтобы определить тип данных для входной привязки, используйте свойство `dataType` в определении привязки. Например, чтобы прочитать содержимое HTTP-запроса в двоичном формате, используйте тип `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Другие варианты для `dataType` — `stream` и `string`.

## <a name="writing-trace-output-to-the-console"></a>Вывод выходных данных трассировки на консоль 

В Azure Functions воспользуйтесь методами `context.log` для записи выходных данных трассировки в консоль. На этом этапе для записи в консоль нельзя использовать метод `console.log`.

При вызове `context.log()` ваше сообщение записывается в консоль на уровне трассировки по умолчанию — уровень _сведений_. Следующий код записывает на консоль на уровне трассировки "сведения":

```javascript
context.log({hello: 'world'});  
```

Предыдущий код аналогичен следующему:

```javascript
context.log.info({hello: 'world'});  
```

Следующий код записывает на консоль на уровне трассировки "ошибки":

```javascript
context.log.error("An error has occurred.");  
```

Так как _уровень ошибок_ является наивысшим уровнем трасировки, эта трассировка записывается в выходные данные на всех уровнях трассировки при условии, что ведение журнала включено.  


Все методы `context.log` поддерживают тот же формат параметров, что и метод Node.js [util.format](https://nodejs.org/api/util.html#util_util_format_format). Просмотрите следующий код, который выполняет запись в консоль, используя уровень трассировки по умолчанию:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Этот же код можно записать в таком формате:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Настройка уровня трассировки для ведения журнала консоли

В Функциях Azure можно определить пороговое значение уровня трассировки для записи в консоль, чтобы легко контролировать, как трассировки записываются в консоль из ваших функций. Чтобы задать пороговое значение для всех трассировок, которые записываются в консоль, используйте свойство `tracing.consoleLevel` в файле host.json. Этот параметр применяется ко всем функциям в приложении-функции. В следующем примере задается пороговое значение трассировки, чтобы включить подробное ведение журнала:

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

Значения **consoleLevel** соответствуют именам методов в `context.log`. Чтобы отключить ведение журнала трассировки в консоли, задайте для параметра **consoleLevel** значение _off_. Дополнительные сведения см. в [справочной статье о host.json](functions-host-json.md).

## <a name="http-triggers-and-bindings"></a>Триггеры и привязки HTTP

Триггеры HTTP и webhook, а также привязки вывода HTTP используют объекты запроса и ответа для обмена сообщениями HTTP.  

### <a name="request-object"></a>Объект запроса

Объект `request` имеет следующие свойства.

| Свойство      | ОПИСАНИЕ                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Объект, содержащий текст запроса.               |
| _headers_     | Объект, содержащий заголовок запроса.                   |
| _method_      | Метод HTTP, используемый для запроса.                                |
| _originalUrl_ | URL-адрес запроса.                                        |
| _params_      | Объект, содержащий параметры маршрутизации запроса. |
| _query_       | Объект, содержащий параметры запроса.                  |
| _rawBody_     | Текст сообщения в виде строки.                           |


### <a name="response-object"></a>Объект ответа

Объект `response` имеет следующие свойства.

| Свойство  | ОПИСАНИЕ                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Объект, содержащий текст ответа.         |
| _headers_ | Объект, содержащий заголовок ответа.             |
| _isRaw_   | Указывает, что форматирование пропускается для ответа.    |
| _состояние_  | Код состояния HTTP ответа.                     |

### <a name="accessing-the-request-and-response"></a>Доступ к запросу и ответу 

При работе с триггерами HTTP вы можете получить доступ к объектам запроса и ответа HTTP одним из 3 способов:

+ С помощью именованных входных и выходных привязок. В этом случае триггер и привязки HTTP работают как любая другая привязка. В следующем примере объект ответа задается с помощью именованной привязки `response`: 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ От свойств `req` и `res` объекта `context`. В этом случае можно использовать обычный шаблон доступа к данным HTTP из объекта context вместо полного шаблона `context.bindings.name`. Следующий пример демонстрирует доступ к объектам `req` и `res` в `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Путем вызова `context.done()`. Специальный тип привязки HTTP возвращает ответ, передавшийся методу `context.done()`. Следующая привязка вывода HTTP определяет параметр вывода `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    Эта привязка вывода ожидает предоставления ответа при вызове `done()` следующим образом:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>Управление версиями и пакетами Node

В следующей таблице показана версия Node.js, используемая каждой основной версией среды выполнения службы "Функции".

| Версия службы "Функции" | Версия Node.js | 
|---|---|
| 1.x | 6.11.2 (заблокировано средой выполнения) |
| 2.x  |не ниже версии 8.4.0 с рекомендуемой текущей версией LTS 8.9.4. Установите версию, используя [параметр приложения](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION.|

Введя `process.version` из любой функции, можно увидеть текущую версию, которую использует среда выполнения.

Следующие шаги позволяют включить пакеты в приложение-функцию: 

1. Перейдите на сайт `https://<function_app_name>.scm.azurewebsites.net`.

2. Щелкните **Debug Console** (Консоль отладки)  > **CMD**.

3. Перейдите к `D:\home\site\wwwroot`, а затем перетащите файл package.json в папку **wwwroot** в верхней части страницы.  
    Существуют другие способы передачи файлов в приложение-функцию. Дополнительные сведения см. в разделе [Как обновить файлы приложения-функции](functions-reference.md#fileupdate). 

4. После загрузки файла package.json запустите команду `npm install` в **консоли удаленного выполнения Kudu**.  
    В результате этого действия будут загружены пакеты, указанные в файле package.json, и перезапущено приложение-функция.

После установки необходимых пакетов их необходимо импортировать в функцию, вызвав `require('packagename')`, как это показано в следующем примере:

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Следует определить файл `package.json` в корне вашего приложения-функции. После этого все функции в приложении будут совместно использовать одни и те же кэшированные пакеты, что обеспечивает наилучшую производительность. При возникновении конфликтов версий их можно разрешить, добавив файл `package.json` в папку определенной функции.  

## <a name="environment-variables"></a>Переменные среды
Чтобы получить значение переменной среды или параметра приложения, используйте `process.env`, как показано в следующей функции `GetEnvironmentVariable`:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## <a name="considerations-for-javascript-functions"></a>Рекомендации для функций JavaScript

При работе с функциями JavaScript следует помнить о рекомендациях, приведенных в двух следующих разделах.

### <a name="choose-single-vcpu-app-service-plans"></a>Выбор планов службы приложений для конфигурации с одним виртуальным ЦП

При создании приложения-функции, которое использует план службы приложения, мы советуем выбирать план для конфигурации с одним виртуальным ЦП вместо плана для нескольких виртуальных ЦП. Сейчас служба "Функции Azure" намного эффективнее выполняет функции JavaScript на виртуальных машинах с одним ЦП. Использование более крупных виртуальных машин не приводит к ожидаемому улучшению производительности. При необходимости вы можете вручную добавить дополнительные экземпляры виртуальных машин с одним ЦП или включить автоматическое масштабирование. Дополнительные сведения см. в статье [Масштабирование числа экземпляров вручную или автоматически](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json).    

### <a name="typescript-and-coffeescript-support"></a>Поддержка TypeScript и CoffeeScript
Так как прямой поддержки автоматической компиляции TypeScript и CoffeeScript в среде выполнения сейчас нет, соответствующую обработку необходимо осуществлять вне среды выполнения во время развертывания. 

## <a name="next-steps"></a>Дополнительная информация
Для получения дополнительных сведений см. следующие ресурсы:

* [Рекомендации по функциям Azure](functions-best-practices.md)
* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Azure Functions triggers and bindings (Триггеры и привязки в Функциях Azure)](functions-triggers-bindings.md)

