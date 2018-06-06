---
title: API HTTP в устойчивых функциях — Azure
description: Сведения о том, как внедрять API HTTP в расширении устойчивых функций для Функций Azure.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: aa5c46a4d0ca55339e8f26a3e577d03bf4b504b2
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "32309986"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API HTTP в устойчивых функциях (Функции Azure)

Расширение устойчивых задач предоставляет набор API HTTP, которые могут использоваться для выполнения следующих задач:

* получение состояния экземпляра оркестрации;
* отправка события в ожидающий экземпляр оркестрации;
* завершение работающего экземпляра оркестрации.


Каждый из этих API HTTP является операцией веб-перехватчика, обрабатываемой напрямую расширением устойчивых задач. Они не относятся к какой-либо функции в приложении-функции.

> [!NOTE]
> Эти операции также могут быть вызваны напрямую с помощью API управления экземплярами в классе [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Дополнительные сведения см. в [статье об управлении экземплярами](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Обнаружение URL-адреса API HTTP

Класс [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) предоставляет API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_), который можно использовать для создания полезных данных ответа HTTP, содержащих ссылки на все поддерживаемые операции. Ниже приведен пример функции HTTP-триггера, в котором показано, как использовать этот API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Этот пример функции выводит следующие данные ответа JSON. Тип данных всех полей — `string`.

| Поле             |ОПИСАНИЕ                           |
|-------------------|--------------------------------------|
| id                |Идентификатор экземпляра оркестрации. |
| statusQueryGetUri |URL-адрес состояния экземпляра оркестрации. |
| sendEventPostUri  |URL-адрес вызова события экземпляра оркестрации. |
| terminatePostUri  |URL-адрес завершения экземпляра оркестрации. |

Вот пример ответа на запрос:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Формат URL-адресов веб-перехватчиков может отличаться в зависимости от того, какая версия узла Функций Azure выполняется. Приведенный выше пример предназначен для узла Функций Azure 2.0.

## <a name="async-operation-tracking"></a>Отслеживание асинхронных операций

Упомянутый ранее HTTP-ответ предназначен для помощи в реализации долго выполняющихся асинхронных API HTTP с устойчивыми функциями. Это иногда называется *шаблоном опрашивающего объекта-получателя*. Поток клиента или сервера работает следующим образом:

1. Клиент отправляет запрос HTTP для запуска длительного процесса, например функции оркестратора.
2. Целевой триггер HTTP возвращает ответ HTTP 202 с заголовком `Location` со значением `statusQueryGetUri`.
3. Клиент отправляет URL-адрес в заголовке `Location`. Он продолжает просматривать ответы HTTP 202 с заголовком `Location`.
4. При завершении (или сбое) экземпляра конечная точка в заголовке `Location` возвращает ответ HTTP 200.

Этот протокол позволяет согласовать долго выполняющиеся процессы с помощью внешних клиентов или служб, которые поддерживают опрос конечной точки HTTP и используют заголовок `Location`. Ключевые компоненты уже встроены в API HTTP устойчивых функций.

> [!NOTE]
> По умолчанию все действия на основе HTTP, предоставляемые [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), поддерживают стандартную модель асинхронных операций. Эта возможность позволяет внедрять долго выполняющиеся устойчивые функции в рамках рабочего процесса Logic Apps. Дополнительные сведения о поддержке Logic Apps для асинхронных шаблонов HTTP см. в разделе [Модель асинхронных операций](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Справочник по API HTTP

Все API HTTP, реализованные с помощью расширения, принимают следующие параметры. Тип данных всех параметров — `string`.

| Параметр  | Тип параметра  | ОПИСАНИЕ |
|------------|-----------------|-------------|
| instanceId | URL-адрес             | Идентификатор экземпляра оркестрации. |
| taskHub    | Строка запроса    | Имя [центра задач](durable-functions-task-hubs.md). Если не указано, предполагается имя центра задач текущего приложения-функции. |
| connection; | Строка запроса    | **Имя** строки подключения для учетной записи хранения. Если не указано, предполагается строка подключения по умолчанию для приложения-функции. |
| systemKey  | Строка запроса    | Ключ авторизации, необходимый для вызова API. |
| showHistory| Строка запроса    | Необязательный параметр. Если задано значение `true`, журнал выполнения оркестрации будет включен в полезные данные ответа.| 
| showHistoryOutput| Строка запроса    | Необязательный параметр. Если задано значение `true`, выходные данные действия будут включены в журнал выполнения оркестрации.| 

`systemKey` — это ключ авторизации, автоматически создаваемый узлом Функций Azure. В частности, он предоставляет доступ к API расширения устойчивых задач и им можно управлять так же, как и [другими ключами авторизации](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Самый простой способ обнаружения значения `systemKey` — с помощью API `CreateCheckStatusResponse`, упомянутого ранее.

В следующих нескольких разделах рассматриваются определенные API HTTP, поддерживаемые расширением, и приведены примеры их использования.

### <a name="get-instance-status"></a>Получение состояния экземпляра

Возвращает состояние определенного экземпляра оркестрации.

#### <a name="request"></a>Запрос

Формат запроса для Функций 1.0 имеет такой вид:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Формат для Функций 2.0 имеет те же параметры, но имеет другой префикс URL-адреса:

```http
GET /runtime/webhooks/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Ответ

Может быть возвращено несколько кодов состояния.

* **HTTP 200 (ОК)** (HTTP 200 (ОК)). Указанный экземпляр находится в завершенном состоянии.
* **HTTP 202 (Accepted)** (HTTP 202 (принято)). Указанный экземпляр выполняется.
* **HTTP 400 (Bad Request)** (HTTP 400 (неверный запрос)). На определенном экземпляре произошел сбой, или его работа была прервана.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)). Указанный экземпляр не существует или не был запущен.

Полезные данные ответа для случаев **HTTP 200** и **HTTP 202** являются объектами JSON со следующими полями:

| Поле           | Тип данных | ОПИСАНИЕ |
|-----------------|-----------|-------------|
| runtimeStatus   | строка    | Состояние среды выполнения экземпляра. Возможные значения: *Running*, *Pending*, *Failed*, *Canceled*, *Terminated*, *Completed*. |
| input           | JSON      | Данные JSON, используемые для инициализации экземпляра. |
| customStatus    | JSON      | Данные JSON, используемые для состояния пользовательской оркестрации. Если поле не заполнено, для него устанавливается значение `null`. |
| output          | JSON      | Выходные данные JSON экземпляра. Это поле имеет значение `null`, если экземпляр не находится в завершенном состоянии. |
| createdTime     | строка    | Время, когда был создан экземпляр. Использует расширенную нотацию ISO 8601. |
| lastUpdatedTime | строка    | Время, когда экземпляр был в последний раз сохранен. Использует расширенную нотацию ISO 8601. |
| historyEvents   | JSON      | Массив JSON, содержащий журнал выполнения оркестрации. Это поле имеет значение `null`, если для параметра строки запроса `showHistory` не задано значение `true`.  | 

Ниже приведен пример полезных данных ответа, включающий журнал выполнения оркестрации и выходные данные действия (в удобном для чтения формате).

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Ответ **HTTP 202** также включает заголовок ответа **Location**, который ссылается на тот же URL-адрес, что и поле `statusQueryGetUri`, упомянутое ранее.

### <a name="raise-event"></a>Вызов события

Отправляет сообщение уведомления о событии в выполняющийся экземпляр оркестрации.

#### <a name="request"></a>Запрос

Формат запроса для Функций 1.0 имеет такой вид:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Формат для Функций 2.0 имеет те же параметры, но имеет другой префикс URL-адреса:

```http
POST /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующие уникальные параметры:

| Поле       | Тип параметра  | Тип данных | ОПИСАНИЕ |
|-------------|-----------------|-----------|-------------|
| eventName   | URL-адрес             | строка    | Имя события, которое ожидает целевой экземпляр оркестрации. |
| {content}   | Содержимое запроса | JSON      | Полезные данные события в формате JSON. |

#### <a name="response"></a>Ответ

Может быть возвращено несколько кодов состояния.

* **HTTP 202 (Accepted)** (HTTP 202 (принято)). Вызванное событие принято в обработку.
* **HTTP 400 (Bad request)** (HTTP 400 (неверный запрос)). Содержимое запроса не принадлежит к допустимому типу `application/json` или не является допустимым файлом JSON.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)). Указанный экземпляр не найден.
* **HTTP 410 (Gone)** (HTTP 410 (потеряно)). Указанный экземпляр выполнен или завершился с ошибкой и не может обрабатывать возникающие события.

Ниже приведен пример запроса, отправляющий строку JSON `"incr"` в экземпляр, который ожидает событие с именем **operation**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Ответы этого API не содержат какого-либо содержимого.

### <a name="terminate-instance"></a>Завершение экземпляра

Завершите выполнение экземпляра оркестрации.

#### <a name="request"></a>Запрос

Формат запроса для Функций 1.0 имеет такой вид:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Формат для Функций 2.0 имеет те же параметры, но имеет другой префикс URL-адреса:

```http
DELETE /runtime/webhooks/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Параметры запроса для этого API включают набор по умолчанию, упомянутый ранее, а также следующий уникальный параметр.

| Поле       | Тип параметра  | Тип данных | ОПИСАНИЕ |
|-------------|-----------------|-----------|-------------|
| reason      | Строка запроса    | строка    | Необязательный элемент. Причина завершения работы экземпляра оркестрации. |

#### <a name="response"></a>Ответ

Может быть возвращено несколько кодов состояния.

* **HTTP 202 (Accepted)** (HTTP 202 (принято)). Запрос на завершение принят для обработки.
* **HTTP 404 (Not Found)** (HTTP 404 (не найдено)). Указанный экземпляр не найден.
* **HTTP 410 (Gone)** (HTTP 410 (потеряно)). Определенный экземпляр выполнен успешно или завершился со сбоем.

Ниже приведен пример запроса, который завершает выполнение экземпляра и указывает причину **ошибки**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Ответы этого API не содержат какого-либо содержимого.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Обработка ошибок в устойчивых функциях (Функции Azure)](durable-functions-error-handling.md)
