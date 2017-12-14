---
title: "Привязки для устойчивых функций — Azure"
description: "Инструкции по использованию триггеров и привязок в расширении устойчивых функций для Функций Azure."
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
ms.openlocfilehash: 02c3e0e919b556bc6d4bb41d9c66b4a6d29bdd68
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2017
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Привязки для устойчивых функций (Функции Azure)

Расширение [устойчивых функций](durable-functions-overview.md) содержит две новые привязки триггера, которые управляют выполнением функций действий и оркестратора. Он также содержит привязку к выходным данным, которая действует как клиент для среды выполнения устойчивых функций.

## <a name="orchestration-triggers"></a>Триггеры оркестрации

Триггер оркестрации позволяет создавать устойчивые функции оркестратора. Этот триггер поддерживает запуск новых экземпляров функции оркестратора и возобновление существующих экземпляров функции оркестратора, которые "ожидают" задачу.

При использовании средств Visual Studio для Функций Azure триггер оркестрации настраивается с помощью атрибута .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

При написании функции оркестратора на языках сценариев (например, на портале Azure) триггер оркестрации определяется следующим объектом JSON в массиве `bindings` файла *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "version": "<Optional - version label of this orchestrator function>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` является именем оркестрации. Это значение, которое клиенты должны использовать, когда требуется запускать новые экземпляры этой функции оркестрации. Это необязательное свойство. Если не указан, используется имя функции.
* `version` является меткой версии оркестрации. Клиенты, которые запускают новый экземпляр оркестрации, должны включать соответствующую метку версии. Это необязательное свойство. Если не указано, используется пустая строка. Дополнительные сведения об управлении версиями см. в статье [Управление версиями в устойчивых функциях (Функции Azure)](durable-functions-versioning.md).

> [!NOTE]
> В настоящее время устанавливать значения для свойств `orchestration` или `version` не рекомендуется.

Внутри эта привязка триггера опрашивает серии очередей в учетной записи хранения по умолчанию приложения-функции. Эти очереди представляют собой сведения о внутренней реализации расширения, поэтому они не настраиваются явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Ниже приведены сведения о триггере оркестрации:

* **Однопоточная обработка**. Один поток диспетчера используется для выполнения всех функций оркестратора на одном экземпляре узла. По этой причине убедитесь, что код функции оркестратора эффективен и не выполняет никаких операций ввода-вывода. Также важно убедиться, что этот поток не выполняет никакой асинхронной работы, кроме тех случаев, когда ожидаются типы задач, специфичные для устойчивых функций.
* **Обработка сообщений о сбое.** В триггерах оркестрации не поддерживаются сообщения о сбое.
* **Видимость сообщений**. Сообщения триггеров оркестрации удаляются из очереди и сохраняются невидимыми в течение настраиваемого промежутка времени. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Возвращаемые значения**. Возвращаемые значения сериализуются в JSON и сохраняются в таблице с журналом оркестрации в хранилище таблиц Azure. Эти возвращаемые значения может запрашивать привязка клиента оркестрации, описанная ниже.

> [!WARNING]
> Функции оркестратора никогда не должны использовать никаких входных и выходных привязок, отличных от привязки триггера оркестрации. Это может вызвать проблемы с расширением устойчивых функций, так как эти привязки могут не подчиняться правилам однопоточности и ввода-вывода.

### <a name="trigger-usage"></a>Использование триггера

Привязка триггера оркестрации поддерживает входные и выходные данные. Ниже приведены некоторые сведения об обработке входных и выходных данных.

* **Входные данные**. Функции оркестрации поддерживают только [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) в качестве типа параметра. Входные данные десериализации непосредственно в сигнатуре функции не поддерживаются. В коде необходимо использовать метод [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) для извлечения входных данных функции оркестратора. Эти входные данные должны быть типа, который сериализуется в JSON.
* **Выходные данные**. Триггеры оркестрации поддерживают выходные значения, а также входные данные. Возвращаемое значение функции используется для назначения выходного значения и должно быть типа, который сериализуется в JSON. Если функция возвращает `Task` или `void`, значение `null` будет сохранено как выходное.

> [!NOTE]
> Сейчас триггеры оркестрации поддерживаются только в C#.

### <a name="trigger-sample"></a>Пример триггера

Ниже приведен пример, как может выглядеть простая функция оркестратора "Hello World" на C#:

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

Большинство функций оркестратора вызывают другие функции. Пример "Hello World" демонстрирует, как вызвать функцию:

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = await context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

## <a name="activity-triggers"></a>Триггеры действий

Триггер действия позволяет создавать функции, которые вызываются функциями оркестратора.

Если вы используете Visual Studio, триггер действия настраивается с помощью атрибута .NET [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html). 

Если вы используете портал Azure для разработки приложений, триггер действия определяется следующим объектом JSON в массиве `bindings` файла *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "version": "<Optional - version label of this activity function>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` является именем действия. Это значение, которое функции оркестратора используют для вызова этой функции действия. Это необязательное свойство. Если не указан, используется имя функции.
* `version` является меткой версии действия. Функции оркестратора, которые вызывают действия, должны содержать соответствующую метку версии. Это необязательное свойство. Если не указано, используется пустая строка. Дополнительные сведения см. в статье [Управление версиями в устойчивых функциях (Функции Azure)](durable-functions-versioning.md).

> [!NOTE]
> В настоящее время устанавливать значения для свойств `activity` или `version` не рекомендуется.

Внутри эта привязка триггера опрашивает очередь в учетной записи хранения по умолчанию приложения-функции. Эта очередь представляет собой сведения о внутренней реализации расширения, поэтому она не настраивается явно в свойствах привязки.

### <a name="trigger-behavior"></a>Поведение триггера

Ниже приведены сведения о триггере действия:

* **Работа с потоками**. В отличие от триггера оркестрации, триггеры действий не имеют каких-либо ограничений при работе с потоками или операциями ввода-вывода. Они могут рассматриваться как обычные функции.
* **Обработка сообщений о сбое**. В триггерах действий не поддерживаются сообщения о сбое.
* **Видимость сообщений**. Сообщения триггеров действий удаляются из очереди и сохраняются невидимыми в течение настраиваемого промежутка времени. Видимость этих сообщений обновляется автоматически, пока приложение-функция выполняется и работоспособно.
* **Возвращаемые значения**. Возвращаемые значения сериализуются в JSON и сохраняются в таблице с журналом оркестрации в хранилище таблиц Azure.

> [!WARNING]
> Внутренняя часть хранилища функций действий является частью реализации, и код пользователя не должен взаимодействовать с этими сущностями хранилища напрямую.

### <a name="trigger-usage"></a>Использование триггера

Привязка триггера действий поддерживает входные и выходные данные, как и триггер оркестрации. Ниже приведены некоторые сведения об обработке входных и выходных данных.

* **Входные данные**. Функции действий изначально используют [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) как тип параметра. Кроме того, функция действия может быть объявлена с любым типом параметра, который сериализуется в JSON. При использовании `DurableActivityContext` можно вызвать [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) для извлечения и десериализации входных данных функции действия.
* **Выходные данные**. Триггеры действия поддерживают выходные значения, а также входные данные. Возвращаемое значение функции используется для назначения выходного значения и должно быть типа, который сериализуется в JSON. Если функция возвращает `Task` или `void`, значение `null` будет сохранено как выходное.
* **Метаданные**. Функции действий можно привязать к параметру `string instanceId`, чтобы получить идентификатор экземпляра родительской оркестрации.

> [!NOTE]
> Триггеры действий в настоящее время не поддерживаются в функциях Node.js.

### <a name="trigger-sample"></a>Пример триггера

Ниже приведен пример, как может выглядеть простая функция действия "Hello World" на C#:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Типом параметра по умолчанию для привязки `ActivityTriggerAttribute` является `DurableActivityContext`. Однако триггеры действий также поддерживают привязку непосредственно к JSON-сериализуемым типам (включая примитивные типы), поэтому эту же функцию можно упростить следующим образом:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

## <a name="orchestration-client"></a>Клиент оркестрации

Привязка клиента оркестрации дает возможность создавать функции, которые взаимодействуют с функциями оркестратора. Например, с экземплярами оркестрации можно взаимодействовать следующим образом:
* Запустите их.
* Запросите их состояние.
* Завершите их.
* Отправляйте им события во время их работы.

Если вы используете Visual Studio, выполнить привязку к клиенту оркестрации можно с помощью атрибута .NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html).

Если для разработки вы используете языки скриптов (например, *CSX*-файлы), триггер оркестрации определяется следующим объектом JSON в массиве `bindings` файла function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` используется в ситуациях, когда несколько приложений-функций совместно используют одну и ту же учетную запись хранения, но должны быть изолированы друг от друга. Если значение не задано, по умолчанию используется значение из `host.json`. Оно должно соответствовать значению, которое используется целевыми функциями оркестратора.
* `connectionName` — имя параметра приложения, содержащего строку подключения к службе хранилища. Учетная запись хранения, представленная этой строкой подключения, должна быть той же, которая используется целевыми функциями оркестратора. Если не указано, используется строка подключения по умолчанию для приложения-функции.

> [!NOTE]
> В большинстве случаев рекомендуется исключить эти свойства и полагаться на поведение по умолчанию.

### <a name="client-usage"></a>Использование клиента

В функциях C # вы обычно выполняете привязку к `DurableOrchestrationClient`, что обеспечивает полный доступ ко всем клиентским API, поддерживаемым устойчивыми функциями. API-интерфейсы в клиентском объекте включают в себя:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Кроме того, можно выполнить привязку к `IAsyncCollector<T>`, где `T` — [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) или `JObject`.

Дополнительные сведения об этих операциях см. в документации по API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html).

### <a name="client-sample-visual-studio-development"></a>Пример клиента (разработка в Visual Studio)

Ниже приведен пример функции, активируемой с помощью очереди, которая запускает оркестрацию HelloWorld.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Пример клиента (не Visual Studio)

Если для разработки вы не используете Visual Studio, можно создать следующий файл function.json. В этом примере показано, как настроить функцию, активируемую с помощью очереди, которая использует привязку клиента устойчивой оркестрации:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

Ниже приведены примеры конкретно для языка, которые запускают новые экземпляры функции оркестратора.

#### <a name="c-sample"></a>Пример на языке C#

В следующем примере показано, как использовать привязку клиента устойчивой оркестрации для запуска нового экземпляра функции из функции скрипта C #:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="nodejs-sample"></a>Образец Node.js

В следующем примере показано, как использовать привязку клиента устойчивой оркестрации для запуска нового экземпляра функции из функции Node.js:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

Дополнительные сведения о запуске экземпляров см. в статье [Управление экземплярами в устойчивых функциях (Функции Azure)](durable-functions-instance-management.md).

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Контрольные точки и воспроизведение в устойчивых функциях (Функции Azure)](durable-functions-checkpointing-and-replay.md)

