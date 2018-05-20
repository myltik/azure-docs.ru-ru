---
title: Начало работы с пакетом SDK WebJobs в Azure
description: Знакомство с пакетом SDK WebJobs для фоновой обработки на основе событий. Получение доступа к данным в службах Azure и службах сторонних разработчиков.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: b1de898ec4f661c54b5227367ad416a5edec80ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-the-webjobs-sdk"></a>Начало работы с пакетом SDK WebJobs

В этой статье описано создание проекта с помощью пакета SDK WebJobs, его локальный запуск и развертывание в Azure.

Эти инструкции касаются [Visual Studio 2017](https://www.visualstudio.com/vs/), но аналогичные задачи можно решить и с помощью других средств, таких как [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-webjobs-sdk"></a>Сведения о пакете SDK WebJobs

Пакет SDK WebJobs Azure WebJobs — это платформа, упрощающая задачу написания кода для фоновой обработки данных из служб Azure. Пакет SDK поддерживает декларативный синтаксис для описания событий, активирующих функцию, например добавление нового сообщения в очередь. Аналогичный декларативный синтаксис управляет чтением и записью данных в результате активации функции. Эта система триггеров и привязок отвечает за большинство низкоуровневых задач кодирования, связанных с доступом к службам Azure и сторонних разработчиков.

### <a name="functions-triggers-and-bindings"></a>Функции, триггеры и привязки

Проект в пакете SDK WebJobs определяет одну или несколько *функций*. Функция — это метод с атрибутом триггера в сигнатуре метода. Триггеры задают условия вызова функции, а привязка указывает, что нужно прочесть и записать. Например, атрибут триггера следующей функции вызывает в среде выполнения функцию всякий раз, когда в очереди `items` появляется очередное сообщение. Атрибут `Blob` вынуждает среду выполнения использовать сообщение из очереди для чтения большого двоичного объекта из контейнера *workitems*. Содержимое сообщения в очереди &mdash;, переданное в параметр `queueTrigger`, &mdash; является именем большого двоичного объекта.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Версии 2.x и 3.x

Инструкции описывают создание проекта пакета SDK WebJobs версии 2.x с комментариями об отличиях от версии 3.x (в режиме предварительного просмотра). Основное отличие версии 3.x заключается в использовании .NET Core вместо .NET Framework.

### <a name="azure-functions"></a>Функции Azure

[Функции Azure](../azure-functions/functions-overview.md) основаны на пакете SDK WebJobs и применяются в тех случаях, когда непосредственное использование пакета SDK WebJobs не требуется. Функции Azure 1.x использует пакет SDK WebJobs 2.х. Дополнительные сведения см. в разделе [Сравнение функций Azure и WebJobs SDK (веб-заданий пакета SDK)](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>предварительным требованиям

В статье предполагается, что у вас есть [учетная запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) и опыт работы с [приложениями в службе приложений Azure](app-service-web-overview.md). Чтобы выполнить действия, описанные в этой статье, сделайте следующее:

* [Установите Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) с рабочей нагрузкой **разработки в Azure**. Если у вас уже установлена среда Visual Studio, но нет компонентов для разработки приложений, добавьте их, выбрав **Tools > Get Tools and Features** (Инструменты > Добавить инструменты и компоненты).
* [Создайте приложение службы приложений](app-service-web-get-started-dotnet-framework.md). Если оно у вас уже есть и на нем можно развернуть веб-задание, тогда вместо создания нового можно использовать его.

## <a name="create-a-project"></a>Создание проекта

1. В Visual Studio выберите **Файл > Создать проект**.

2. Выберите **Классический рабочий стол Windows > Консольное приложение (.NET Framework)**.

   Чтобы создать проект версии 3.x, выберите **.NET Core > Консольное приложение (.NET Core)**.

3. Назовите проект *WebJobsSDKSample*, а затем нажмите кнопку **OK**.

   ![Диалоговое окно "Новый проект"](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Добавьте пакет WebJobs NuGet

1. Установите последнюю стабильную версию 2.x пакета NuGet `Microsoft.Azure.WebJobs`. (Для пакета SDK WebJobs 3.x выберите, соответственно, последнюю версию 3.x.)
 
   Команда **Консоли диспетчера пакетов** для версии 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Создание JobHost

Объект `JobHost` представляет собой контейнер среды выполнения для функций, прослушивающий триггеры и вызовы функций. 

1. В *Program.cs* добавьте инструкцию `using`:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Замените метод `Main` следующим кодом:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Включение журналирования консоли

Есть несколько вариантов ведения журнала в проекте пакета SDK WebJobs. Рекомендованным является [платформа журналирования, разработанная для ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Эта платформа обеспечивает более высокую производительность и гибкость при хранении и фильтрации. 

В этом разделе описана настройка журнала консоли с использованием новой платформы.

1. Установите последнюю стабильную версию следующих пакетов NuGet:

   * `Microsoft.Extensions.Logging` — платформа ведения журналов;
   * `Microsoft.Extensions.Logging.Console` — *поставщик* консоли. Поставщик отправляет журналы в определенное место назначения, в данном случае — в консоль. 
 
   Ниже приведены команды **консоли диспетчера пакетов** для версии 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. В *Program.cs* добавьте инструкцию `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. В метод `Main` добавьте код для обновления `JobHostConfiguration` перед созданием `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Этот код вносит следующие изменения.

   * Отключает [ведение журнала панели мониторинга](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Панель мониторинга — это устаревшее средство мониторинга, поэтому ведение журнала панели мониторинга не рекомендуется в высокопроизводительных сценариях.
   * Добавляет поставщика консоли с [фильтрацией](webjobs-sdk-how-to.md#log-filtering) по умолчанию. 

   Метод `Main` теперь выглядит следующим образом:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Создание функции

1. Создайте сценарий *Functions.cs* в папке проекта и замените код шаблона таким кодом:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   Атрибут `QueueTrigger` вызывает в среде выполнения эту функцию при записи нового сообщения в очередь службы хранилища Azure с именем `queue`. Содержимое сообщения в очереди передается коду метода в параметре `message`. Тело метода реализуется при обработке данных триггера. В этом примере код просто записывает сообщение в журнал.

   Параметр `message` не должен быть строкой. Также можно выполнить привязку к объекту JSON, массиву байтов или [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage). [См. "Использование триггера очереди"](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Каждый тип привязки (например, очереди, BLOB-объекты или таблицы) обладает своим набором типов параметров привязки.

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Эмулятор службы хранилища Azure, работающий локально, не обладает всеми возможностями, необходимыми пакету SDK WebJobs. Поэтому здесь описывается создание учетной записи хранилища в Azure и настройка проекта для его использования.

1. Откройте **обозреватель сервера** и войдите в Azure. Щелкните правой кнопкой мыши узел **Azure** и выберите команду **Connect to Microsoft Azure Subscription** (Подключение к подписке Microsoft Azure).

   ![Вход в Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. В узле **Azure** **обозревателя сервера** щелкните правой кнопкой мыши элемент **Хранилище** и выберите **Создать учетную запись хранения**.

   ![Создание меню учетной записи хранения](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. В диалоговом окне **Создание учетной записи хранения** введите уникальное имя учетной записи хранения.

3. Выберите тот же **Регион**, в котором вы создали приложение службы приложений, или ближайший к вам регион.

1. Нажмите кнопку **Создать**.

   ![Создание учетной записи хранения](./media/webjobs-sdk-get-started/create-storage-account.png)

1. В узле **Хранилище** **обозревателя серверов** выберите новую учетную запись хранения. В окне **Свойства** выберите многоточие (**...**) справа от поля со значением **Строка подключения**.

   ![Многоточие в строке подключения](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. Скопируйте строку подключения и сохраните это значение где-либо, откуда впоследствии его можно будет легко скопировать еще раз.

   ![Копирование строки подключения](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Настройка хранилища для локальной работы

Пакет SDK WebJobs ищет строку подключения в коллекции параметров приложения. При локальном запуске он ищет строку подключения в файле *App.config* или среди переменных среды.

1. Добавьте следующий XML-код в файл *App.config* сразу после открывающего тега `<configuration>`.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. Замените значение *{storage connection string}* на строку подключения, скопированную ранее.

   Позже эта строка подключения будет использоваться еще раз при настройке приложения службы приложений в Azure.

## <a name="test-locally"></a>Локальное тестирование

В этом разделе проект создается и запускается локально, а кроме того, посредством создания очереди сообщений запускается триггер вызова функции.

1. Чтобы запустить проект, нажмите сочетание клавиш CTRL+F5.

   Консоль показывает, что среда выполнения обнаружила функцию и ожидает сообщений в очереди для ее активации.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   В это время может появиться предупреждение о настройке `ServicePointManager`. При тестировании этого проекта, исходя из целей тестирования, это предупреждение можно пропустить. Дополнительные сведения о предупреждении см. в разделе [Использование пакета SDK WebJobs](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

2. Закройте окно консоли.

1. В **обозревателе сервера** разверните узел новой учетной записи хранения и щелкните правой кнопкой мыши элемент **Очереди**. 

2. Выберите **Создать очередь**. 

3. Введите *queue* в качестве имени очереди, а затем нажмите кнопку **OK**.

   ![Создание очереди](./media/webjobs-sdk-get-started/create-queue.png)

4. Щелкните правой кнопкой мыши узел новой очереди, а затем выберите команду **Просмотреть очередь**.

5. Выберите значок **Добавить сообщение**.

   ![Создание очереди](./media/webjobs-sdk-get-started/create-queue-message.png)

6. В диалоговом окне **Добавить сообщение** введите *Здравствуй, мир!* в поле **Текст сообщения**, а затем нажмите кнопку **OK**.

   ![Создание очереди](./media/webjobs-sdk-get-started/hello-world-text.png)

7. Запустите проект снова.

   Поскольку в функции `ProcessQueueMessage` использовался атрибут `QueueTrigger`, среда выполнения пакета SDK WebJobs прослушивает запускаемые сообщения в очереди. Она находит новое сообщение в очереди с именем *queue* и вызывает функцию.

   Из-за [экспоненциально растущего интервала опроса очереди](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm) процесс поиска сообщения и вызова функции может занимать до 2 минут. Время отклика можно сократить путем запуска в [режиме разработки](webjobs-sdk-how-to.md#jobhost-development-settings).

  В консоли отобразятся следующие сообщения:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

8. Закройте окно консоли.

## <a name="add-application-insights-logging"></a>Добавление журнала Application Insights

При выполнении проекта в Azure вам не удастся отследить выполнение функции в консоли. В качестве решения для мониторинга рекомендуется использовать [Application Insights](../application-insights/app-insights-overview.md). Дополнительные сведения см. в разделе [Мониторинг функций Azure](../azure-functions/functions-monitoring.md).

В этом разделе рассматриваются следующие задания по настройке ведения журнала Application Insights перед развертыванием в Azure:

* Убедитесь, что у вас есть приложение службы приложений Azure и экземпляр Application Insights, с которым будет вестись работа.
* Настройте приложение службы приложений Azure для использования экземпляра Application Insights и учетной записи хранения, созданной ранее.
* Настройте проект ведения журнала Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Создание приложения службы приложений Azure и экземпляра Application Insights

1. Если у вас еще нет приложения службы приложений, которое можно использовать, [создайте его](app-service-web-get-started-dotnet-framework.md).

2. Если у вас еще нет доступного ресурса Application Insights, [создайте его](../application-insights/app-insights-create-new-resource.md). Установите для параметра **Тип приложения** значение **Общий** и пропустите разделы, следующие за разделом **Copy the instrumentation key** (Копировать ключ инструментирования).

3. При наличии ресурса Application Insights, который вы собираетесь использовать, [скопируйте ключ инструментирования](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Настройка параметров приложения 

1. В **обозревателе сервера** разверните узел **Служба приложений** в разделе **Azure**.

1. Разверните группу ресурсов с приложением службы приложений, а затем выберите это приложение службы приложений.

3. Выберите **Просмотр параметров**.

4. В поле **Строки подключения** добавьте следующую запись.

   |ИМЯ  |Строка подключения  |Тип базы данных|
   |---------|---------|------|
   |AzureWebJobsStorage | {строка подключения к службе хранилища, скопированная ранее}|Пользовательская|
   
6. Если поле **Параметры приложения** не содержит ключа инструментирования Application Insights, добавьте один, скопированный ранее. (Ключ инструментирования уже может быть там, в зависимости от способа создания приложения службы приложений).

   |ИМЯ  |Значение  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

2. Замените строку *{instrumentation key}* ключом инструментирования с используемого ресурса Application Insights.

2. Щелкните **Сохранить**.

1. Добавьте следующий XML-код в файл *App.config* сразу после коллекции строк подключения.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. Замените строку *{instrumentation key}* ключом инструментирования с используемого ресурса Application Insights.

   Добавление этих данных в файл *App.config* позволяет проверить соединение с Application Insights при локальном выполнении проекта. 

3. Сохраните изменения.

### <a name="add-application-insights-logging-provider"></a>Добавление поставщика журнала для Application Insights

1. Установите последнюю стабильную версию 2.x пакета NuGet для поставщика журнала Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`. (Для пакета SDK WebJobs 3.x выберите последнюю версию 3.x.)

   Команда **Консоли диспетчера пакетов** для версии 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Установите последнюю стабильную версию 4.x пакета NuGet для диспетчера конфигурации .NET: `System.Configuration.ConfigurationManager`.

   Команда **консоли диспетчера пакетов** для версии 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. Откройте сценарий *Program.cs* и добавьте инструкцию `using` для диспетчера конфигурации:

   ```csharp
   using System.Configuration;
   ```

2. Замените код в методе `Main` следующим кодом:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Этот код вносит следующие изменения.

   * Добавляет поставщика журнала Application Insights с [фильтрацией](webjobs-sdk-how-to.md#log-filtering) по умолчанию; при локальном запуске все сведения и журналы более высокого уровня теперь переместятся одновременно в Application Insights и в консоль. 
   * Помещает объект `LoggerFactory` в блок `using`, чтобы активировать освобождение вывода журнала при выходе узла. 

## <a name="test-application-insights-logging"></a>Тестирование ведения журнала Application Insights

В этом разделе выполняется повторный локальный запуск для проверки того, что данные журналов теперь передаются в Application Insights и в консоль.

1. Используйте **обозреватель сервера** для создания сообщения в очереди таким же образом, как это было сделано [ранее](#trigger-the-function), введя в качестве текста сообщения *Hello App Insights!* на этот раз.

1. Запустите проект.

   Пакет SDK WebJobs обрабатывает сообщения в очереди, и вы можете просматривать журнал в окне консоли.

1. Закройте окно консоли.

1. Вернитесь к ресурсу Application Insights на [портале Azure](https://portal.azure.com/).

2. Выберите **Поиск**.

   ![Выбор команды "Поиск"](./media/webjobs-sdk-get-started/select-search.png)

1. Если вы не видите сообщения *Hello App Insights!*, в течение нескольких минут периодически нажимайте кнопку **Обновить**. (Журналы не отображаются немедленно потому, что клиенту Application Insights требуется некоторое время на освобождение обрабатываемых журналов.)

   ![Журналы в Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. Закройте окно консоли.

## <a name="deploy-as-a-webjob"></a>Развертывание веб-задания

В этом разделе проект разворачивается как веб-задание. Развертывание выполняется для приложения службы приложений, [созданного ранее](#create-app-service-app-and-application-insights-instance). Чтобы проверить код во время его выполнения в Azure, нужно обеспечить вызов функции путем создания сообщения в очереди.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Publish as Azure WebJob** (Опубликовать как веб-задание Azure).

1. В диалоговом окне **Добавить веб-задание Azure** нажмите кнопку **OK**.

   ![Добавить Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio автоматически устанавливает пакет NuGet для публикации веб-задания.

1. В мастере **Публикация веб-сайта** на этапе **Профиль** выберите **Служба приложений Microsoft Azure**.

   ![Диалоговое окно "Опубликовать"](./media/webjobs-sdk-get-started/publish-dialog.png)

1. В диалоговом окне **Служба приложений** выберите свою **группу ресурсов > Приложение службы приложений**, а затем нажмите кнопку **OK**.

   ![Диалоговое окно "Служба приложений Azure"](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. На этапе **Подключение** мастера выберите команду **Опубликовать**.

## <a name="trigger-the-function-in-azure"></a>Активация функции в Azure

1. Убедитесь, что вы не работаете локально (закройте окно консоли, если оно еще открыто). В противном случае локальный экземпляр может сначала начать обрабатывать созданные вами сообщения в очереди.

1. Используйте **обозреватель сервера**, чтобы создать сообщение в очереди таким же образом, как и [ранее](#trigger-the-function), но на этот раз введите *Hello Azure!*.

7. Обновите страницу **Очередь** в Visual Studio, и новое сообщение исчезнет, поскольку функция, работающая в службе приложений Azure, обработает его.

   > [!TIP]
   > При тестировании в Azure используйте [режим разработки](webjobs-sdk-how-to.md#jobhost-development-settings), чтобы убедиться, что функция триггера очереди вызывается сразу же, и избежать задержки из-за [экспоненциально растущего интервала опроса очереди](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Просмотр журналов Application Insights

1. Вернитесь к ресурсу Application Insights на [портале Azure](https://portal.azure.com/).

2. Выберите **Поиск**.

1. Если вы не видите сообщение *Hello Azure!*, в течение нескольких минут периодически нажимайте кнопку **Обновить**.

   С помощью функции, работающей в веб-задании, можно просмотреть журналы и сообщение *Hello Azure!*, введенное в предыдущем разделе.

## <a name="add-an-input-binding"></a>Добавление привязки входных данных

Привязка входных данных упрощает код чтения данных. В этом примере сообщением в очереди будет имя большого двоичного объекта, используемое при поиске и чтении большого двоичного объекта в службе хранилища Azure.

1. В сценарии *Functions.cs* замените метод `ProcessQueueMessage` следующим кодом:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   В этом коде `queueTrigger` — это [выражение привязки](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), что означает, что его значение определяется во время выполнения.  Во время выполнения оно содержит сообщение в очереди.

2. Добавьте `using`:

   ```cs
   using System.IO;
   ```

3. Создайте контейнер BLOB-объектов в учетной записи хранения.

   a. В **обозревателе сервера** разверните узел своей учетной записи хранения, щелкните правой кнопкой мыши кнопку **Большие двоичные объекты** и выберите команду **Создать контейнер BLOB-объектов**.

   Б. В диалоговом окне **Создать контейнер BLOB-объектов** для имени контейнера введите *container*, а затем нажмите кнопку **OK**.

4. Отправьте файл *Program.cs* в контейнер больших двоичных объектов. (Этот файл используется здесь для примера; вы можете передать любой текстовый файл и создавать сообщение в очереди с его именем.)

   a. В **обозревателе сервера** дважды щелкните узел контейнера, который вы только что создали.

   Б. В окне **Контейнер** нажмите кнопку **Отправить**.

   ![Кнопка отправки BLOB-объекта](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Найдите и выберите файл *Program.cs*, а затем нажмите кнопку **OK**.

5. Создайте сообщение в ранее созданной очереди с *Program.cs* в качестве текста сообщения.

   ![Сообщение в очереди на основе сценария Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. Запустите проект.

   Сообщение в очереди активирует функцию, которая затем считывает большой двоичный объект и записывает в журнал его размер. В консоли отобразятся следующие сообщения:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Добавление выходной привязки

Привязки выходных данных упрощают код записи данных. Данный пример проще предыдущего, так как вместо регистрации размера большого двоичного объекта в журнале была сделана запись его копии.

1. Замените метод `ProcessQueueMessage` следующим кодом:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

5. Создайте другое сообщение в очереди с использованием *Program.cs* в качестве текста сообщения.

6. Запустите проект.

   Сообщение в очереди активирует функцию, которая затем считывает большой двоичный объект, регистрирует в журнале его размер и создает новый большой двоичный объект. В консоли отобразится аналогичное сообщение, но если перейти в окно контейнера большого двоичного объекта и нажать кнопку **Обновить**, вы увидите новый BLOB-объект с именем *copy-Program.cs*.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве показано создание, запуск и развертывание проекта пакета SDK WebJobs.

Чтобы отобразить все, что входит в пакет SDK WebJobs, приведены инструкции по созданию проекта с самого начала. Тем не менее при создании следующего проекта попробуйте использовать шаблон **Веб-задание Azure** в категории **Облако**. Этот шаблон создает проект с пакетами NuGet и уже настроенным образцом кода. Обратите внимание, что в образец кода может потребоваться внести изменения, чтобы воспользоваться новой платформой ведения журналов.

Дополнительные сведения см. в разделе [Использование пакета SDK WebJobs](webjobs-sdk-how-to.md).
