<properties
	pageTitle="Справочник разработчика NodeJS по функциям Azure | Microsoft Azure"
	description="Узнайте, как пользоваться функциями Azure в NodeJS."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="функции azure, функции, обработка событий, веб-перехватчики, динамические вычисления, независимая архитектура"/>

<tags
	ms.service="functions"
	ms.devlang="nodejs"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/06/2016"
	ms.author="chrande"/>

# Справочник разработчика NodeJS по функциям Azure

Интерфейс Node/JavaScript для функций Azure позволяет легко экспортировать функцию, которой передается объект `context`, для взаимодействия со средой выполнения, а также для получения и отправки данных с помощью привязок.

В этой статье предполагается, что вы уже прочли статью [Справочник разработчика по функциям Azure](functions-reference.md).

## Экспорт функции

Все функции JavaScript должны экспортировать одну `function` с помощью `module.exports`, чтобы среда выполнения могла найти эту функцию и запустить ее. Эта функция должна всегда включать объект `context`.

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

Привязки `direction === "in"` передаются как аргументы функции, то есть вы можете использовать [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) для динамической обработки новых входных данных (например, используя `arguments.length` для итерации всех входных данных). Эта возможность очень удобна, если у вас есть только триггер без дополнительных входных данных, так как к данным триггера можно обращаться напрямую, без ссылки на объект `context`.

Аргументы всегда передаются в функцию в порядке их появления в файле *function.json*, даже если они не указаны в выражениях экспорта. Например, если у вас есть функция `function(context, a, b)` и вы изменяете ее на `function(context, a)`, то значение `b` все равно можно получить в коде функции с помощью выражения `arguments[3]`.

Все привязки, независимо от направления, также передаются в объекте `context` (см. ниже).

## Объект context

Среда выполнения использует объект `context` для передачи данных в функцию и из нее, а также для взаимодействия со средой выполнения.

Объект context всегда является первым параметром функции, и его необходимо указывать всегда, так как он содержит методы, необходимые для правильного использования среды выполнения, такие как `context.done` и `context.log`. Для этого объекта можно указать любое имя (например `ctx` или `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## Объект context.bindings

Объект `context.bindings` собирает все входные и выходные данные. Данные добавляются в объект `context.bindings` с помощью свойства `name` привязки. Например, если в файле *function.json* содержится следующее определение привязки, то доступ к содержимому очереди можно получить с помощью `context.bindings.myInput`.

```json
    {
        "type":"queue",
        "direction":"in",
        "name":"myInput"
        ...
    }
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`

Функция `context.done` сообщает среде выполнения, что выполнение завершено. Ее важно вызвать после завершения работы с функцией; если этого не сделать, среда выполнения не узнает, что функция завершилась.

Функция `context.done` позволяет передавать в среду выполнения пользовательское сообщение об ошибке, а также контейнер свойств, которые переопределят свойства объекта `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## context.log(message)

Метод `context.log` позволяет вывести инструкции журнала, которые связаны друг с другом. Он может применяться для ведения журнала. При использовании `console.log` будут показаны сообщения только для ведения журнала на уровне процесса, что не слишком удобно.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

Метод `context.log` поддерживает тот же формат параметров, что и [метод util.format](https://nodejs.org/api/util.html#util_util_format_format) Node. Поэтому следующий код

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

можно записать так:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## Триггеры HTTP: context.req и context.res

Мы решили упростить доступ к триггерам HTTP в объекте context, чтобы вам не приходилось использовать полный шаблон `context.bindings.name`, так как для объектов запроса и ответа HTTP обычно используются `req` и `res`.

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## Управление версиями и пакетами Node

Версия Node сейчас зафиксирована в значении `5.9.1`. Мы работаем над тем, чтобы добавить поддержку дополнительных версий и настройки для этих версий.

Вы можете включить пакеты в каталог вашей функции (с помощью `npm install`), а затем импортировать их в функцию обычным образом (с помощью `require('packagename')`).

```javascript
// Import the underescore.js library
var _ = require('underscore');
var version = process.version; // version === 'v5.9.1'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## Поддержка TypeScript и CoffeeScript

Сейчас прямой поддержки автоматической компиляции TypeScript и CoffeeScript в среде выполнения нет, поэтому соответствующую обработку необходимо осуществлять вне среды выполнения во время развертывания.

## Дальнейшие действия

Для получения дополнительных сведений см. следующие ресурсы:

* [Справочник разработчика по функциям Azure](functions-reference.md)
* [Справочник разработчика C# по функциям Azure](functions-reference-csharp.md)
* [Триггеры и привязки в функциях Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0420_2016-->