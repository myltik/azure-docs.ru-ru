---
title: "Одноэлементные экземпляры с отслеживанием состояния в устойчивых функциях (Azure)"
description: "Сведения о том, как реализовать одноэлементные экземпляры с отслеживанием состояния в расширении устойчивых функций для Функций Azure."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 05099e868e62f612be0a3354eb8b339507ac7e4a
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Одноэлементные экземпляры с отслеживанием состояния в устойчивых функциях — пример функции счетчика

Одноэлементные экземпляры с отслеживанием состояния представляют собой функции оркестратора, которые выполняются долго (в том числе, неограниченно долго), способны сохранять свое состояние, могут вызываться и опрашиваться другими функциями. Одноэлементные экземпляры с отслеживанием состояния аналогичны [модели субъекта](https://en.wikipedia.org/wiki/Actor_model) в распределенных вычислениях.

Такие функции оркестратора, хоть и не являются строгой реализацией субъекта, имеют во многом схожие характеристики выполнения. Они выполняются с отслеживанием состояния, не зависят от расположения, являются надежными, однопоточными и глобально адресуемыми. Эти характеристики предоставляют полезные возможности при реализации фактических субъектов и не требуют создания отдельной платформы.

В этой статье показано, как выполнить пример функции *счетчика*. В примере создается одноэлементный экземпляр, который поддерживает операции *приращения* и *уменьшения* и сохраняет данные о внутреннем состоянии.

## <a name="prerequisites"></a>предварительным требованиям

* Выполните инструкции в статье об [установке устойчивых функций](durable-functions-install.md), чтобы настроить пример.
* В этой статье предполагается, что вы уже ознакомлены с пошаговым руководством по примеру [последовательности Hello](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Обзор сценария

Реализовать счетчик при помощи обычных функций без отслеживания состояния очень сложно. Одной из самых трудоемких задач является контроль **параллелизма**. Отдельные операции, такие как *приращение* и *уменьшение*, должны быть атомарными, чтобы не возникало состояния гонки, при котором результаты одновременных операций отменяют друг друга.

Конечно, вы можете разместить данные счетчика на отдельной виртуальной машине. Но это достаточно дорого и не всегда **надежно**, так как одна виртуальная машина будет недоступна при периодических перезагрузках. Также для решения проблемы параллелизма можно применить распределенную платформу со средствами синхронизации, например с арендой BLOB-объектов. Но такой вариант сопряжен со значительным **повышением сложности**.

Устойчивые функции позволяют без труда реализовать этот сценарий. Экземпляры оркестрации привязываются к одной виртуальной машине, и функция оркестратора всегда выполняется в одном потоке. Кроме того, они могут выполняться длительное время, отслеживать свое состояние и реагировать на внешние события. Следующий пример кода демонстрирует реализацию счетчика в виде длительно выполняемой функции оркестратора.

## <a name="the-sample-function"></a>Пример функции

В этой статье рассматривается функция **E3_Counter** из примера приложения.



## <a name="the-counter-orchestration"></a>Оркестрация счетчика

В следующих разделах мы опишем код, который используется при разработке в Visual Studio Code и на портале Azure.

### <a name="c-script"></a>Скрипт C#

Файл function.json:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)]

Файл run.csx:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)]

### <a name="precompiled-c"></a>Предкомпилированный код C# 

В следующих разделах мы опишем код, который используется при разработке в Visual Studio.

Ниже приведен код, реализующий функцию оркестратора.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

### <a name="explanation-of-the-code"></a>Объяснение кода

Эта функция оркестратора выполняет следующие задачи:

1. Прослушивает внешнее событие с именем *operation*, используя метод [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Увеличивает или уменьшает значение локальной переменной `counterState` в зависимости от запрошенной операции.
3. Перезапускает оркестратор с помощью метода [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_), передавая в качестве входных данных последнее значение `counterState`.
4. Выполняется неограниченно долго, пока не будет получено сообщение *end*.

Это пример *внешней оркестрации*, которая потенциально не завершается никогда. Она реагирует на сообщения, которые с помощью метода [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) могут создавать даже функции, не поддерживающие оркестрацию.

Одной из уникальных характеристик этой функции оркестратора является фактическое отсутствие журнала выполнения. Метод `ContinueAsNew` сбрасывает данные журнала после каждого обработанного события. Это правильный способ реализации оркестратора, срок существования которого ничем не ограничен. Цикл `while` в такой ситуации может привести к недопустимому росту журнала оркестратора и бесполезному потреблению памяти.

> [!NOTE]
> Метод `ContinueAsNew` полезен и в других случаях, помимо внешних оркестраций. Дополнительные сведения см. в статье [о внешних оркестрациях](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Запуск примера

Можно начать оркестрацию, отправив приведенный ниже запрос HTTP POST. Чтобы начать работу с нулевым значением `counterState` (это значение по умолчанию для `int`), запрос не должен иметь содержимого.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Экземпляр **E3_Counter** запускается и переходит в режим ожидания событий, которые в него можно отправить с помощью `RaiseEventAsync` или веб-перехватчика HTTP POST **sendEventUrl**, ссылка на который передается в HTTP-ответе 202. Для `eventName` поддерживаются значения *incr*, *decr* и *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Результат операции incr можно просмотреть в журналах функций на портале Функций Azure.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

Также при проверке состояния оркестратора отобразится поле `input` с обновленным значением (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Вы можете отправить еще несколько операций в этот экземпляр и пронаблюдать соответствующие изменения его состояния. Чтобы завершить работу этого экземпляра, отправьте в него операцию *end*.

> [!WARNING]
> На момент написания этой статьи существовала известная проблема с состоянием гонки, которое возникало при вызове метода `ContinueAsNew` одновременно с обработкой сообщений, например, внешних событий или запросов на прекращение. Последние сведения об этом состоянии гонки вы найдете в [описании проблемы на GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Дополнительная информация

В этом примере показано, как обрабатывать [внешние события](durable-functions-external-events.md) и реализовать [нескончаемую оркестрацию](durable-functions-eternal-orchestrations.md) в [одноэлементных экземплярах с отслеживанием состояния](durable-functions-singletons.md). В приведенном ниже примере показано, как использовать внешние события и [устойчивые таймеры](durable-functions-timers.md) для обработки действий человека.

> [!div class="nextstepaction"]
> Выполните [пример взаимодействия с пользователем](durable-functions-phone-verification.md)
