---
title: "Цепочки функций в устойчивых функциях (Azure)"
description: "Ознакомьтесь с примером устойчивой функции, которая выполняет последовательность функций."
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
ms.openlocfilehash: 0eb031893d53f09097b696e18f6c129be061136a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Цепочки функций в устойчивых функциях — пример последовательности Hello

Цепочкой функций называют схему выполнения последовательности функций в определенном порядке. Часто требуется передать выходные данные одной функции во входные данные другой. В этой статье приведен пример использования [устойчивых функций](durable-functions-overview.md) для реализации цепочки функций.

## <a name="prerequisites"></a>Предварительные требования

* Выполните инструкции в статье об [установке устойчивых функций](durable-functions-install.md), чтобы настроить пример.

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции в примере приложения:

* Функция оркестратора `E1_HelloSequence`, которая вызывает `E1_SayHello` несколько раз подряд. При этом сохраняются выходные данные каждого вызова `E1_SayHello` и записываются результаты.
* Функция действия `E1_SayHello`, которая добавляет Hello в начало строки.

В следующих разделах рассматривается конфигурация и код, которые используются при разработке с помощью портала Azure. Код для разработки с помощью Visual Studio представлен в конце этой статьи.
 
## <a name="functionjson-file"></a>Файл function.json

Если вы используете для разработки портал Azure, файл *function.json* с указанным здесь содержимым позволит создать функцию оркестратора. Для большинства функций оркестратора файл *function.json* будет выглядеть почти так же.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Самое важное здесь — это тип привязки `orchestrationTrigger`. Во всех функциях оркестратора должен использоваться такой тип триггера.

> [!WARNING]
> Чтобы не нарушать требование "без ввода и вывода", применяемое к функциям оркестратора, не используйте никакие привязки входных или выходных данных совместно с триггером привязки `orchestrationTrigger`.  Если вам нужны привязки входных или выходных данных, используйте их в контексте функций `activityTrigger`.

## <a name="c-script"></a>Сценарии C#

Ниже приведен исходный код:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Для всех функций оркестратора на C# должен использоваться параметр `DurableOrchestrationContext`, который присутствует в сборке `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Если вы работаете со скриптом C#, для ссылки на сборку можно использовать нотацию `#r`. Этот объект контекста позволяет вызывать другие функции *действия* и передавать им входные параметры с помощью метода [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

Этот код трижды последовательно вызывает `E1_SayHello` с разными значениями параметров. Значение, возвращаемое при каждом вызове, добавляется в список `outputs`, который возвращается в качестве результата функции.

Файл *Function.json* для функции действия `E1_SayHello` будет таким же, как и для `E1_HelloSequence`, с одним исключением: в нем используется тип привязки `activityTrigger` вместо `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Для любой функции, которую вызывает функция оркестратора, должна использоваться привязка `activityTrigger`.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Эта функция принимает параметр [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), при помощи которого она получает входные данные, переданные ранее при вызове функции оркестратора [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_)>.

## <a name="running-the-orchestration"></a>Выполнение оркестрации

Чтобы выполнить функцию оркестратора `E1_HelloSequence`, используйте указанный ниже вызов HTTP.

```
POST http://{app-name}.azurewebsites.net/orchestrators/E1_HelloSequence
```
Вы получите примерно такой ответ HTTP с кодом 202 (фрагмент ответа):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Функция оркестратора помещается в очередь и немедленно передается на выполнение. URL-адрес из заголовка `Location` позволяет проверить состояние выполнения функции.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

В ответе на такой запрос возвращаются сведения о состоянии оркестрации. Функция выполняется и завершается достаточно быстро, поэтому, вероятнее всего, отобразится состояние *Завершено* в ответе примерно такого вида (фрагмент ответа):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Как видите, параметр `runtimeStatus` для экземпляра имеет значение *Завершено*, а `output` содержит результат выполнения функции оркестратора, сериализованный в формате JSON.

> [!NOTE]
> Конечная точка HTTP POST, которая запустила функцию оркестратора, реализована в этом примере как функция с именем HttpStart и активацией по HTTP-запросу. Вы можете использовать аналогичную логику запуска и для других типов триггеров, например `queueTrigger`, `eventHubTrigger` или `timerTrigger`.

Просмотрите журналы выполнения функции. Функция `E1_HelloSequence` была запущена и завершена несколько раз в соответствии с логикой повторов, описанной в [этом обзоре](durable-functions-overview.md). С другой стороны, функция `E1_SayHello` выполнялась только три раза, поскольку для таких процессов логика повторов не применяется.

## <a name="visual-studio-sample-code"></a>Пример кода для Visual Studio

Пример описанной оркестрации, реализованной в одном файле C# в проекте Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Дальнейшие действия

Теперь у вас есть базовое представление об основных механизмах, которые применяются для устойчивых функций. В этом простом примере показана лишь небольшая часть доступных возможностей. Следующие примеры будут ближе к реальным условиям, и вы найдете в них больше разных возможностей.

> [!div class="nextstepaction"]
> [Выполните пример с размножением и разветвлением](durable-functions-cloud-backup.md)
