---
title: Мониторинг и диагностика служб ASP.NET Core в Azure Service Fabric | Документация Майкрософт
description: Из этого руководства вы узнаете, как настроить мониторинг и диагностику приложения ASP.NET Core в Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/14/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 17b2f1b65463f87f81ffe06bae5ac559a84bcb2a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797708"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric"></a>Руководство. Мониторинг и диагностика приложения ASP.NET Core в Service Fabric
Это руководство представляет собой пятую часть цикла. В нем описывается настройка мониторинга и диагностики приложения ASP.NET Core, запущенного в кластере Service Fabric, с помощью Application Insights. Мы будем собирать данные телеметрии из приложения, разработанного в первой части цикла, [Создание приложения .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md). 

В четвертом руководстве из цикла вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * настройка Application Insights для приложения;
> * сбора данных телеметрии ответов для трассировки связи по протоколу HTTP между службами;
> * использование функции карты приложений в Application Insights;
> * добавление настраиваемых событий с помощью API Application Insights.

Из этого цикла руководств вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * [Создание приложения .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md).
> * [Развертывание приложения в удаленном кластере](service-fabric-tutorial-deploy-app-to-party-cluster.md).
> * [Добавление конечной точки HTTPS к интерфейсной службе ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Настройка непрерывной интеграции и непрерывного развертывания с помощью Visual Studio Team Services](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * Настройка мониторинга и диагностики приложения

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим руководством выполните следующие действия:
- Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Установите Visual Studio 2017](https://www.visualstudio.com/), а также рабочие нагрузки **разработка Azure** и **ASP.NET и веб-разработка**.
- [Установка пакета SDK для Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Скачивание примера приложения для голосования
Если вы не создавали пример приложения для голосования [в первой части этой серии руководств](service-fabric-tutorial-create-dotnet-app.md), вы можете скачать его. В командном окне или окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения на локальный компьютер.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Настройка ресурса Application Insights
Application Insights — это платформа для управления производительностью приложений Azure, которую рекомендуется использовать для мониторинга и диагностики приложений в Service Fabric. Чтобы создать ресурс Application Insights, перейдите на [портал Azure](https://portal.azure.com). Щелкните **Создать ресурс** в меню навигации слева, чтобы открыть Azure Marketplace. Щелкните **Мониторинг и управление** и выберите **Application Insights**.

![Создание ресурса Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Теперь необходимо будет ввести необходимые сведения об атрибутах создаваемого ресурса. Укажите соответствующие *имя*, *группу ресурсов* и *подписку*. Укажите *расположение* для предстоящего развертывания кластера Service Fabric. В этом руководстве мы развернем приложение в локальном кластере, поэтому поле *Расположение* не используется. В поле *Тип приложения* следует оставить значение "Веб-приложение ASP.NET". 

![Атрибуты ресурса Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

После ввода требуемых сведений нажмите кнопку **Создать**, чтобы подготовить ресурсы. Это займет около минуты. 
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Добавление Application Insights в службы приложения

Запустите Visual Studio 2017 с более высоким уровнем привилегий. Для этого можно щелкнуть правой кнопкой мыши значок Visual Studio в меню "Пуск" и выбрать **Запуск от имени администратора**. Выберите **Файл** > **Открыть** > **Решение или проект** и перейдите к приложению Voting (созданному в первой части руководства или клонированному из Git). Откройте файл *Voting.sln*. Если появится запрос на восстановление пакетов NuGet приложения, щелкните **Да**.

Выполните следующие действия, чтобы настроить Application Insights для служб VotingWeb и VotingData.
1. Щелкните правой кнопкой мыши имя службы и выберите **Настроить Application Insights**.

    ![Настройка Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)

2. Щелкните **Начать бесплатно**.
3. Войдите в свою учетную запись (с помощью которой была настроена подписка Azure) и выберите подписку, в которой был создан ресурс Application Insights. Найдите ресурс в разделе *Существующий ресурс Application Insights* в раскрывающемся списке "Ресурс". Щелкните **Зарегистрировать**, чтобы добавить Application Insights в службу.

    ![Регистрация Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Нажмите кнопку **Готово** диалоговом окне, появившемся по завершении действия.
    
Обязательно выполните описанные выше действия для **обеих** служб в приложении, чтобы завершить настройку Application Insights для него. Для служб используется один и тот же ресурс Application Insights, чтобы отслеживать входящие и исходящие запросы, а также обмен данными между службами.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Добавление пакета NuGet Microsoft.ApplicationInsights.ServiceFabric.Native в службы

В Application Insights доступны два специальных пакета NuGet для Service Fabric, которые могут использоваться в зависимости от ситуации. Один из них используется для собственных служб Service Fabric, а другой — для контейнеров и гостевых исполняемых файлов. В этом случае мы будем использовать Microsoft.ApplicationInsights.ServiceFabric.Native NuGet, чтобы анализировать передаваемый контекст службы. Чтобы узнать больше о пакете SDK для Application Insights и пакетах NuGet для Service Fabric, ознакомьтесь с разделом [Microsoft Application Insights for Service Fabric](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) (Microsoft Application Insights для Service Fabric). 

Ниже приведены инструкции по установке пакета NuGet.
1. Верху области обозревателя решений щелкните правой кнопкой мыши **Решение "Voting"** и выберите **Управление пакетами NuGet для решения**.
2. Щелкните **Обзор** в верхнем меню навигации окна "NuGet — решение" и установите флажок **Включить предварительные выпуски** рядом с панелью поиска.
3. Выполните поиск `Microsoft.ApplicationInsights.ServiceFabric.Native` и щелкните соответствующий пакет NuGet.

>[!NOTE]
>Если у вас нет предустановленного пакета Application Insights, вам нужно установить пакет Microsoft.ServiceFabric.Diagnistics.Internal таким же образом.

4. Справа установите флажки рядом с двумя службами в приложении, **VotingWeb** и **VotingData**, и нажмите кнопку **Установить**.
    ![Пакет SDK ИИ для Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Нажмите кнопку **ОК** в появившемся диалоговом окне *Проверка изменений* и примите *условия лицензионного соглашения*. Добавление пакета NuGet в службы будет завершено.
6. Теперь нужно настроить инициализатор телеметрии в двух службах. Для этого откройте файлы *VotingWeb.cs* и *VotingData.cs*. Выполните в них следующие два действия.
    1. Добавьте следующие две инструкции *using* в начало каждого файла *\<имя_службы>.cs*.
    
    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```
    
    2. Добавьте `.AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))` во вложенную инструкцию *return* метода *CreateServiceInstanceListeners()* или *CreateServiceReplicaListeners()* в разделе *ConfigureServices* > *services*, между двумя объявленными одноэлементными службами. При этом в данные телеметрии будет добавлен *контекст службы*, что позволит определить источник данных телеметрии в Application Insights. Вложенная инструкция *return* в *VotingWeb.cs* должна выглядеть следующим образом.
    
    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Аналогично, в *VotingData.cs* она должна иметь следующий вид.

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

Убедитесь, что метод `UseApplicationInsights()` вызывается в обоих файлах, как показано выше. 

>[!NOTE]
>Этот пример приложения использует протокол HTTP для обмена данными между службами. При разработке приложения с помощью решения для удаленного взаимодействия со службой версии 2 вам нужно добавить следующие строки кода в том же месте, как описано выше.

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

На этом этапе все готово к развертыванию приложения. Нажмите кнопку **Запустить** вверху (или клавишу **F5**), и Visual Studio выполнит сборку и упаковку приложения, настроит локальный кластер и развернет в нем приложение. 

После завершения развертывания приложения перейдите по адресу [localhost: 8080](localhost:8080), по которому должен быть доступен пример одностраничного приложения Voting. Проголосуйте за несколько элементов на свой выбор, чтобы создать примеры данных и данные телеметрии. А я пока схожу за десертом.

![Примеры голосования в Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Вы можете также *удалить* некоторые параметры голосования после добавления нескольких голосов.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Просмотр данных телеметрии и карты приложений в Application Insights 

Перейдите к ресурсу Application Insights на портале Azure.

Щелкните **Обзор**, чтобы вернуться на целевую страницу ресурса. Щелкните **Поиск** вверху, чтобы отобразить поступающие трассировки. Для появления трассировок в Application Insights требуется несколько минут. В случае, если они не отображаются, подождите минуту и нажмите кнопку **Обновить** вверху.
![Просмотр трассировок в Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

Прокрутите вниз окно *Поиск*, чтобы просмотреть все входящие данные телеметрии, передаваемые по умолчанию Application Insights. Для каждого действия в приложении Voting должны отправляться исходящий запрос PUT из *VotingWeb* (PUT Votes/Put [имя]), входящий запрос PUT из *VotingData* (PUT VoteData/Put [имя]) и пара запросов GET для обновления отображаемых данных. Также будет отображена трассировка зависимостей для протокола HTTP в localhost, так как это HTTP-запросы. Ниже приведен пример данных, отображаемых при добавлении одного голоса: ![пример трассировки запроса в Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png).

Можно щелкнуть одну из трассировок, чтобы просмотреть дополнительные сведения о ней. Application Insights предоставляет полезную информацию о запросе, включая *время отклика* и *URL-адрес запроса*. Кроме того, так как вы добавили специальный пакет NuGet для Service Fabric, ниже в разделе *Пользовательские данные* также будут отображены данные о приложении в контексте кластера Service Fabric. Сюда входит контекст службы, поэтому можно просмотреть *PartitionID* и *ReplicaId* источника запроса. Это позволяет лучше локализовать проблемы при диагностике ошибок в приложении.

![Сведения о трассировке в Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Кроме того, можно щелкнуть *Схема приложений* в меню слева на странице "Обзор" или щелкнуть значок **Схема приложений**, чтобы перейти к схеме приложения, на которой показаны две подключенные службы.

![Сведения о трассировке в Application Insights](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Карта приложения поможет лучше понять топологию приложения, особенно в том случае, если вы начнете добавлять несколько разных служб, которые работают совместно. Она также позволяет получить основные данные о проценте успешных запросов и может помочь диагностировать невыполненные запросы, чтобы понять, где происходит сбой. Чтобы узнать больше об использовании карты приложения, ознакомьтесь с разделом [Схема сопоставления приложений в Application Insights](../application-insights/app-insights-app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Добавление пользовательского инструментария для приложения

Хотя Application Insights предоставляет много данных телеметрии без дополнительной настройки, можно добавить дополнительное пользовательское инструментирование. Это может быть обусловлено потребностями организации или улучшением диагностики при возникновении ошибок в приложении. В Application Insights есть API для приема пользовательских событий и метрик, дополнительные сведения о котором можно получить [здесь](../application-insights/app-insights-api-custom-events-metrics.md).

Давайте добавим пользовательские события в *VoteDataController.cs* (в раздел *VotingData* > *Controllers*) для отслеживания добавления и удаления голосов в базовом *votesDictionary*. 
1. Добавьте `using Microsoft.ApplicationInsights;` в конец еще одной инструкции using.
2. Объявите новый *TelemetryClient* в начале класса, при создании *IReliableStateManager*: `private TelemetryClient telemetry = new TelemetryClient();`.
3. Добавьте в функцию *Put()* событие, которое подтверждает, что голос был добавлен. Добавьте `telemetry.TrackEvent($"Added a vote for {name}");` после завершения транзакции, прямо перед инструкцией возвращения результата *OkResult*.
4. В *Delete()* задан цикл if/else на основе условия, что *votesDictionary* содержит голоса для заданного параметра голосования. 
    1. Добавьте событие, подтверждающее удаление голоса, в инструкцию *if* после *await tx.CommitAsync()*:`telemetry.TrackEvent($"Deleted votes for {name}");`.
    2. Добавьте событие, чтобы показать, что удаление не было выполнено, в инструкцию *else* перед инструкцией return: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`.

Ниже приведен пример того, как могут выглядеть функции *Put()* и *Delete()* после добавления событий.

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

После внесения этих изменений **запустите** приложение, чтобы выполнить сборку его последней версии и развернуть ее. После развертывания приложения перейдите по адресу [localhost: 8080](localhost:8080), после чего добавьте и удалите несколько параметров голосования. Затем вернитесь к ресурсу Application Insights, чтобы просмотреть трассировки для последнего выполнения (как и прежде, трассировки могут появиться в Application Insights через 1–2 минуты). Для всех добавленных и удаленных голосов теперь должно отображаться пользовательское событие и все данные телеметрии ответа. 

![Пользовательские события](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнить следующие задачи:
> [!div class="checklist"]
> * настройка Application Insights для приложения;
> * сбора данных телеметрии ответов для трассировки связи по протоколу HTTP между службами;
> * использование функции карты приложений в Application Insights;
> * добавление настраиваемых событий с помощью API Application Insights.

Теперь, когда вы настроили мониторинг и диагностику приложения ASP.NET, попробуйте выполнить следующее:
- [Дальнейшее изучение данных мониторинга и диагностики в Service Fabric](service-fabric-diagnostics-overview.md)
- [Анализ событий Service Fabric с помощью Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
- Чтобы узнать больше об Application Insights, ознакомьтесь с [документацией по Application Insights](https://docs.microsoft.com/azure/application-insights/).
