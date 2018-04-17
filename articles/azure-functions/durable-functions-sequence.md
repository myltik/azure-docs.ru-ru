---
title: Цепочки функций в устойчивых функциях (Azure)
description: Ознакомьтесь с примером устойчивой функции, которая выполняет последовательность функций.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0020f19e00f3365c4a0d80ebb67aeeedd7fe76df
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Цепочки функций в устойчивых функциях — пример последовательности Hello

Цепочкой функций называют схему выполнения последовательности функций в определенном порядке. Часто требуется передать выходные данные одной функции во входные данные другой. В этой статье приведен пример использования [устойчивых функций](durable-functions-overview.md) для реализации цепочки функций.

## <a name="prerequisites"></a>предварительным требованиям

* [Установите устойчивые функции](durable-functions-install.md).

## <a name="the-functions"></a>Функции

В этой статье описаны следующие функции в примере приложения:

* Функция оркестратора `E1_HelloSequence`, которая вызывает `E1_SayHello` несколько раз подряд. При этом сохраняются выходные данные каждого вызова `E1_SayHello` и записываются результаты.
* Функция действия `E1_SayHello`, которая добавляет Hello в начало строки.

В следующих разделах рассматривается конфигурация и код, которые используются для написания скриптов на языке C#. Код для разработки с помощью Visual Studio представлен в конце этой статьи.
 
## <a name="functionjson-file"></a>Файл function.json

Если вы используете для разработки Visual Studio Code или портал Azure, файл *function.json* с указанным здесь содержимым позволит создать функцию оркестратора. Для большинства функций оркестратора файл *function.json* будет выглядеть почти так же.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Самое важное здесь — это тип привязки `orchestrationTrigger`. Во всех функциях оркестратора должен использоваться такой тип триггера.

> [!WARNING]
> Чтобы не нарушать требование "без ввода и вывода", применяемое к функциям оркестратора, не используйте никакие привязки входных или выходных данных совместно с триггером привязки `orchestrationTrigger`.  Если вам нужны входные или выходные привязки, используйте их в контексте функций `activityTrigger`, которые вызываются оркестратором.

## <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Скрипт C# (пример кода Visual Studio Code и портала Azure) 

Ниже приведен исходный код:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Для всех функций оркестратора на C# должен использоваться параметр типа `DurableOrchestrationContext`, который присутствует в сборке `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Если вы работаете со скриптом C#, для ссылки на сборку можно использовать нотацию `#r`. Этот объект контекста позволяет вызывать другие функции *действия* и передавать им входные параметры с помощью метода [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

Этот код трижды последовательно вызывает `E1_SayHello` с разными значениями параметров. Значение, возвращаемое при каждом вызове, добавляется в список `outputs`, который возвращается в качестве результата функции.

Файл *Function.json* для функции действия `E1_SayHello` будет таким же, как и для `E1_HelloSequence`, с одним исключением: в нем используется тип привязки `activityTrigger` вместо `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Для любой функции, которую вызывает функция оркестратора, должна использоваться привязка `activityTrigger`.

Функция `E1_SayHello` реализует достаточно простую операцию форматирования строки.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Эта функция принимает параметр типа [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), при помощи которого она получает входные данные, переданные ранее при вызове функции оркестратора [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

## <a name="run-the-sample"></a>Запуск примера

Чтобы выполнить функцию оркестратора `E1_HelloSequence`, отправьте указанный ниже запрос HTTP POST.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

Скажем, если пример выполняется в приложении-функции с именем myfunctionapp, замените {host} значением myfunctionapp.azurewebsites.net.

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

## <a name="next-steps"></a>Дополнительная информация

В этом примере показана простая оркестрация с цепочкой функций. В приведенном ниже примере кода показана реализация шаблона развертывания и объединения. 

> [!div class="nextstepaction"]
> [Выполните пример с размножением и разветвлением](durable-functions-cloud-backup.md)
