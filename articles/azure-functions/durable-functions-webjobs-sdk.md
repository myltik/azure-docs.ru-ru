---
title: Запуск устойчивых функций в качестве веб-заданий в Azure
description: Узнайте, как программировать и настраивать запуск устойчивых функций в веб-заданиях с помощью пакета SDK для веб-заданий.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 3fc84d1492d2855ffa3bb5538226da049a928339
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766696"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Запуск устойчивых функций в качестве веб-заданий

Расширения [Функций Azure](functions-overview.md) и [устойчивых функций](durable-functions-overview.md) созданы на основе [пакета SDK для веб-заданий](../app-service/web-sites-create-web-jobs.md). `JobHost` в пакете SDK для веб-заданий — это среда выполнения в Функциях Azure. Если необходимо отслеживать поведение `JobHost` способами, недоступными в Функциях Azure, можно самостоятельно разрабатывать и запускать устойчивые функции с помощью пакета SDK для веб-заданий. Устойчивые функции можно запускать в веб-задании Azure или в любом другом месте, где работает консольное приложение.

Пример цепочки устойчивых функций доступен в версии пакета SDK для веб-заданий. Загрузите или клонируйте [репозиторий устойчивых функций](https://github.com/azure/azure-functions-durable-extension/) и перейдите к папке *samples\\webjobssdk\\chaining*.

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается, что вы знакомы с основами пакета SDK для веб-заданий, разработки библиотеки классов C# для Функций Azure и устойчивых функций. Ознакомиться с этими темами вам помогут следующие ресурсы:

* [Начало работы с пакетом SDK WebJobs](../app-service/webjobs-sdk-get-started.md)
* [Создание первой функции с помощью Visual Studio](functions-create-your-first-function-visual-studio.md)
* [Цепочки функций в устойчивых функциях — пример последовательности Hello](durable-functions-sequence.md)

Чтобы выполнить действия, описанные в этой статье, сделайте следующее:

* [Установите Visual Studio 2017 версии 15.6 или выше](https://docs.microsoft.com/visualstudio/install/) с рабочей нагрузкой **разработки Azure**.

  Если у вас уже установлена среда Visual Studio, но нет рабочей нагрузки, добавьте ее, выбрав **Инструменты > Get Tools and Features** (Получить средства и компоненты). 

  (Вместо этого можно использовать [Visual Studio Code](https://code.visualstudio.com/), но некоторые инструкции относятся к Visual Studio.)

* Установите и запустите [эмулятор хранилища Azure](../storage/common/storage-use-emulator.md) версии 5.2 или выше. Альтернативой является добавление в файл *App.config* строки подключения к службе хранилища Azure.

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

В этой статье объясняется, как разрабатывать проект пакета SDK для веб-заданий версии 2.x (эквивалентен Функциям Azure версии 1.x). Дополнительные сведения о версии 3.x см. в разделе [Пакет SDK для веб-заданий версии 3.x](#webjobs-sdk-3x) далее в этой статье. 

## <a name="create-console-app"></a>Создание консольного приложения

Проект пакета SDK для веб-заданий — это просто проект консольного приложения с установленными соответствующими пакетами NuGet.

В диалоговом окне **Создать проект** Visual Studio выберите **Классический рабочий стол Windows > Консольное приложение (.NET Framework)**. В файле проекта `TargetFrameworkVersion` должна иметь значение `v4.6.1`.

В Visual Studio также есть шаблон проекта веб-задания, который можно использовать, выбрав **Облако > Веб-задание Azure (.NET Framework)**. Этот шаблон устанавливает множество пакетов, некоторые из которых могут не потребоваться.

## <a name="install-nuget-packages"></a>Установка пакетов Nuget

Вам потребуются пакеты NuGet для пакета SDK для веб-заданий, основные привязки, платформа ведения журналов и расширение устойчивых задач. Ниже приведены команды **консоли диспетчера пакетов** для этих пакетов с номерами последней стабильной версии на момент написания этой статьи:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Вам также понадобятся регистраторы. Следующие команды устанавливают регистратор (поставщик) Application Insights и `ConfigurationManager`. `ConfigurationManager` позволяет получить ключ инструментирования Application Insights из параметров приложения.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Следующая команда устанавливает поставщик консоли:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Код JobHost

Чтобы использовать расширение устойчивых функций, вызовите `UseDurableTask` для объекта `JobHostConfiguration` в вашем методе `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Список свойств, которые можно задать в объекте `DurableTaskExtension`, см. в файле [host.json](functions-host-json.md#durabletask).

Метод `Main` служит также местом для настройки регистраторов. В следующем примере настраивается консоль и поставщики Application Insights.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Функции Azure

Между кодом для функций пакета SDK для веб-заданий и кодом для службы Функций Azure есть несколько различий.

Пакет SDK для веб-заданий не поддерживает следующие возможности Функций Azure:

* [Атрибут FunctionName](#functionname-attribute)
* [Триггер HTTP](#http-trigger)
* [API управления HTTP устойчивых функций](#http-management-api)

### <a name="functionname-attribute"></a>Атрибут FunctionName

В проекте пакета SDK для веб-заданий имя метода функции является именем функции. Атрибут `FunctionName` используется только в Функциях Azure.

### <a name="http-trigger"></a>Триггер HTTP

В пакете SDK для веб-заданий нет триггера HTTP. Клиент оркестрации в примере проекта использует триггер таймера:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API управления HTTP

Так как пакет SDK для веб-заданий не содержит ни одного триггера HTTP, в нем нет [API управления HTTP](durable-functions-http-api.md).

В проекте пакета SDK для веб-заданий вместо того, чтобы отправлять HTTP-запросы, вы можете вызывать методы в объекте клиента оркестрации. Следующие методы соответствуют трем задачам, которые можно выполнить с помощью API управления HTTP.

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Функция клиента оркестрации в примере проекта запускает функцию оркестратора, а затем переходит в цикл, который вызывает `GetStatusAsync` каждые 2 секунды:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Запуск примера

Этот раздел содержит общие сведения о способах запуска [примера проекта](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Подробные инструкции о том, как запустить проект пакета SDK для веб-заданий локально и развернуть его в веб-задание Azure, см. в разделе [Развертывание в качестве WebJob (веб-задания)](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Локальный запуск

1. Запустите эмулятор хранилища (см. раздел [Предварительные требования](#prerequisites)).

1. Если при локальном запуске необходимо просмотреть журналы в Application Insights:

  a. Создайте ресурс Application Insights с типом приложения **Общее**.

  Б. Сохраните ключ инструментирования в файл *App.config*.

1. Запустите проект.

### <a name="run-in-azure"></a>Запуск в Azure

1. Создайте веб-приложение и учетную запись хранения.

1. В веб-приложении сохраните строку подключения к хранилищу в параметре приложения AzureWebJobsStorage.

1. Создайте ресурс Application Insights с типом приложения **Общее**.

1. Сохраните ключ инструментирования в параметре приложения APPINSIGHTS_INSTRUMENTATIONKEY.

1. Разверните приложение в качестве веб-задания.

## <a name="webjobs-sdk-3x"></a>Пакет SDK для веб-заданий версии 3.x

Основное отличие версии 3.x заключается в использовании .NET Core вместо .NET Framework. Инструкции по созданию проекта версии 3.x те же самые, но со следующими исключениями:

1. Создайте консольное приложение .NET Core. В диалоговом окне **Создать проект** Visual Studio выберите **.NET Core > Console App (.NET Core)** (Консольное приложение (.NET Core)). Файл проекта указывает, что `TargetFramework` является `netcoreapp2.0`.

1. Выберите предварительную версию 3.x следующих пакетов:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Измените код метода `Main` для получения строки подключения к хранилищу и ключа инструментирования Application Insights из файла *appsettings.json* с помощью платформы конфигурации .NET Core.  Ниже приведен пример:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString = 
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

           config.LoggerFactory = loggerFactory
               .AddApplicationInsights(instrumentationKey, null)
               .AddConsole();

           config.UseTimers();
           config.UseDurableTask(new DurableTaskExtension
           {
               HubName = "MyTaskHub",
           });
           var host = new JobHost(config);
           host.RunAndBlock();
       }
   }
   ```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о пакете SDK для веб-заданий см. в статье [Использование пакета SDK WebJobs для фоновой обработки управления событиями](../app-service/webjobs-sdk-how-to.md).

