---
title: Обзор API-интерфейсов Node для ретранслятора Azure | Документация Майкрософт
description: Общие сведения об API Node для ретранслятора
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: b7d6e822-7c32-4cb5-a4b8-df7d009bdc85
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 696e3f77a283cc31d3c8f6007a839480ae8eb984
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019468"
---
# <a name="relay-hybrid-connections-node-api-overview"></a>Общие сведения об API Node для гибридных подключений ретранслятора

## <a name="overview"></a>Обзор

Пакет Node [`hyco-ws`](https://www.npmjs.com/package/hyco-ws) для гибридных подключений ретранслятора Azure собран на базе пакета NPM [ws](https://www.npmjs.com/package/ws) и расширяет его. Этот пакет повторно экспортирует все, что отправляется из этого базового пакета, и добавляет новый экспорт, который обеспечивает интеграцию с функцией гибридных подключений службы ретранслятора Azure. 

Вместо этого существующие приложения, которые требуют ws (`require('ws')`), могут использовать данный пакет с `require('hyco-ws')`, что также обеспечивает поддержку гибридных сценариев, в которых приложение может прослушивать подключения WebSocket локально (внутри брандмауэра), а также одновременно с помощью гибридных подключений.
  
## <a name="documentation"></a>Документация

Интерфейсы API [документированы в основном пакете ws](https://github.com/websockets/ws/blob/master/doc/ws.md). В этой статье описаны отличия этого пакета от базового. 

Основные различия между базовым пакетом и пакетом hyco-ws заключаются в том, что он добавляет новый класс сервера, который экспортируется с помощью `require('hyco-ws').RelayedServer`, и несколько вспомогательных методов.

### <a name="package-helper-methods"></a>Вспомогательные методы пакета

В экспорте пакета доступны несколько служебных методов, на которые можно ссылаться следующим образом:

```JavaScript
const WebSocket = require('hyco-ws');

var listenUri = WebSocket.createRelayListenUri('namespace.servicebus.windows.net', 'path');
listenUri = WebSocket.appendRelayToken(listenUri, 'ruleName', '...key...')
...

```

Вспомогательные методы предназначены для использования с этим пакетом, но также могут использоваться сервером Node для создания отправителей или прослушивателей с помощью веб-клиента или клиента устройства. Сервер использует эти методы, передавая их в коды URI, в которые внедрены кратковременные токены. Эти коды URI также можно использовать с общими стеками WebSocket, не поддерживающими задание HTTP-заголовков для подтверждения WebSocket. Внедрение маркеров проверки подлинности в URI поддерживается в основном для сценариев внешнего использования библиотек. 

#### <a name="createrelaylistenuri"></a>createRelayListenUri

```JavaScript
var uri = createRelayListenUri([namespaceName], [path], [[token]], [[id]])
```

Создает допустимый URI прослушивателя гибридного подключения ретранслятора Azure для указанного пространства имен и пути. Затем этот URI можно использовать с версией ретранслятора класса WebSocketServer.

- `namespaceName` (обязательно) — полное имя домена пространства имен ретранслятора Azure.
- `path` (обязательно) — имя существующего гибридного подключения ретранслятора Azure в этом пространстве имен.
- `token` (необязательно) — ранее выданный токен доступа к ретранслятору, который внедряется в URI прослушивателя (см. следующий пример).
- `id` (необязательно) — идентификатор отслеживания, который позволяет отслеживать комплексную диагностику запросов.

Значение `token` является обязательным и должно использоваться, только когда невозможно отправить заголовки HTTP вместе с подтверждением WebSocket, как в случае со стеком W3C WebSocket.                  


#### <a name="createrelaysenduri"></a>createRelaySendUri
 
```JavaScript
var uri = createRelaySendUri([namespaceName], [path], [[token]], [[id]])
```

Создает допустимый URI отправки гибридного подключения Azure ретранслятора для указанного пространства имен и пути. Этот URI можно использовать с любым клиентом WebSocket.

- `namespaceName` (обязательно) — полное имя домена пространства имен ретранслятора Azure.
- `path` (обязательно) — имя существующего гибридного подключения ретранслятора Azure в этом пространстве имен.
- `token` (необязательно) — ранее выданный токен доступа к ретранслятору, который внедряется в URI отправки (см. следующий пример).
- `id` (необязательно) — идентификатор отслеживания, который позволяет отслеживать комплексную диагностику запросов.

Значение `token` является обязательным и должно использоваться, только когда невозможно отправить заголовки HTTP вместе с подтверждением WebSocket, как в случае со стеком W3C WebSocket.                   


#### <a name="createrelaytoken"></a>createRelayToken 

```JavaScript
var token = createRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Создает токен подписи общего доступа (SAS) ретранслятора Azure для заданного целевого объекта URI, правило SAS и ключ правила SAS, который действителен заданное количество секунд или часов с текущего момента, если пропущен аргумент окончания срока действия.

- `uri` (обязательно) — URI, для которого будет выдаваться токен. URI нормализуется для использования схемы HTTP, а данные строки запроса будут удалены.
- `ruleName` (обязательно) — имя правила SAS для любой сущности, представленной заданным URI, или для пространства имен, представленного частью узла URI.
- `key` (обязательно) — допустимый ключ для правила SAS. 
- `expirationSeconds` (необязательно) — количество секунд до истечения срока действия созданного токена. Значение по умолчанию составляет 1 час (3600), если не указано другое.

Выданный токен предоставляет права, связанные с указанным правилом SAS, в течение заданного времени.

#### <a name="appendrelaytoken"></a>appendRelayToken

```JavaScript
var uri = appendRelayToken([uri], [ruleName], [key], [[expirationSeconds]])
```

Этот метод является функциональным эквивалентом описанного выше метода `createRelayToken`, но возвращаемый токен правильно добавлен в URI входа.

### <a name="class-wsrelayedserver"></a>Класс ws.RelayedServer

Класс `hycows.RelayedServer` является альтернативой классу `ws.Server`, который не прослушивает локальную сеть, но делегирует прослушивание службе ретранслятора Azure.

Эти два класса являются контрактно совместимыми. Это означает, что существующие приложения, использующие класс `ws.Server`, можно легко изменить для использования версии с ретрансляцией. Основное различие заключается в конструкторе и в параметрах.

#### <a name="constructor"></a>Конструктор  

```JavaScript 
var ws = require('hyco-ws');
var server = ws.RelayedServer;

var wss = new server(
    {
        server: ws.createRelayListenUri(ns, path),
        token: function() { return ws.createRelayToken('http://' + ns, keyrule, key); }
    });
```

Конструктор `RelayedServer` поддерживает набор аргументов, отличный от поддерживаемых `Server`, так как он не является автономным прослушивателем и не встраивается в имеющуюся структуру прослушивателя HTTP. В нем также доступно меньшее количество параметров, так как управление WebSocket во многом делегируется службе ретранслятора.

Аргументы конструктора:

- `server` (обязательно) — полный URI гибридного подключения для прослушивания, который обычно создается с помощью вспомогательного метода WebSocket.createRelayListenUri().
- `token` (обязательно) — этот аргумент содержит строку ранее выданного токена или функцию обратного вызова, которую можно вызвать для получения строки токена. Предпочтителен параметр обратного вызова, так как он позволяет возобновлять действие токена.

#### <a name="events"></a>События

Экземпляры `RelayedServer` генерируют три события, которые позволяют обрабатывать входящие запросы, устанавливать подключения и обнаруживать ошибочные условия. Чтобы обрабатывать сообщения, необходимо подписаться на событие `connect`. 

##### <a name="headers"></a>headers

```JavaScript 
function(headers)
```

Событие `headers` возникает непосредственно перед тем, как принимается входящее подключение, позволяя изменять заголовки для отправки клиенту. 

##### <a name="connection"></a>connection;

```JavaScript
function(socket)
```

Генерируется, когда принимается новое подключение WebSocket. Объект принадлежит к типу `ws.WebSocket`, как и базовый пакет.


##### <a name="error"></a>error

```JavaScript
function(error)
```

Если основной сервер генерирует ошибку, он перенаправляется сюда.  

#### <a name="helpers"></a>Вспомогательные методы

Чтобы упростить запуск сервера с ретрансляцией и немедленно подписаться на входящие подключения, пакет предоставляет простую вспомогательную функцию, которая также используется в примерах ниже.

##### <a name="createrelayedlistener"></a>createRelayedListener

```JavaScript
var WebSocket = require('hyco-ws');

var wss = WebSocket.createRelayedServer(
    {
        server: WebSocket.createRelayListenUri(ns, path),
        token: function() { return WebSocket.createRelayToken('http://' + ns, keyrule, key); }
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(JSON.parse(event.data));
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
});
``` 

##### <a name="createrelayedserver"></a>createRelayedServer

```javascript
var server = createRelayedServer([options], [connectCallback] )
```

Этот метод вызывает конструктор для создания нового экземпляра RelayedServer, а затем подписывает предоставленный обратный вызов на событие connection (подключение).
 
##### <a name="relayedconnect"></a>relayedConnect

Просто зеркально отображая вспомогательный метод `createRelayedServer` в функции, `relayedConnect` создает клиентское подключение и подписывается на событие open (открыть) для итогового сокета.

```JavaScript
var uri = WebSocket.createRelaySendUri(ns, path);
WebSocket.relayedConnect(
    uri,
    WebSocket.createRelayToken(uri, keyrule, key),
    function (socket) {
        ...
    }
);
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о ретрансляторе Azure доступны по следующим ссылкам:
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Available Relay APIs](relay-api-overview.md) (Доступные API-интерфейсы ретранслятора)
