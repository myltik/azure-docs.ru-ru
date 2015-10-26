<properties
   pageTitle="Настройка Application Insights для приложения Service Fabric"
   description="Получение событий Service Fabric для приложения в Application Insights."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2015"
   ms.author="mattrow"/>

# Настройка Application Insights для приложения Service Fabric
 В этой статье приводится пошаговое руководство по настройке Application Insights для приложения Service Fabric.

## Предварительные требования

В статье предполагается, что приложение Service Fabric уже создано в Visual Studio. Чтобы узнать, как это сделать, [щелкните здесь](service-fabric-reliable-services-quick-start.md).

## Установка пакета NuGet
В пакет Service Fabric SDK включена предварительная версия пакета NuGet Microsoft.ServiceFabric.Telemetry.ApplicationInsights. Этот пакет объединяет события EventSource платформы Service Fabric и решение Application Insights, позволяя автоматизировать управление приложением Service Fabric. Новые события, генерируемые вашим приложением, будут добавляться в него автоматически.

Чтобы установить пакет:

1. Откройте диспетчер пакетов NuGet для вашего приложения Service Fabric. Для этого щелкните правой кнопкой мыши проект в Visual Studio и выберите пункт «Управление пакетами NuGet».
2. Чтобы вывести список пакетов, включенных в пакет Service Fabric SDK, выберите Microsoft Azure Service Fabric в качестве источника пакета. ![Диспетчер пакетов NuGet в VS2015](media/service-fabric-diagnostics-application-insights-setup/AI-nuget-package-manager.jpg)
3. Выберите слева пакет Microsoft.ServiceFabric.Telemetry.ApplicationInsights.
4. Чтобы приступить к установке, нажмите кнопку «Установить».
5. Прочитайте и примите условия лицензионного соглашения.

## Включение событий Service Fabric
Чтобы получать события Service Fabric в Application Insights автоматически, необходимо включить прослушиватель. Для этого вставьте в свое приложение следующую строку кода.

```csharp
    Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
```
 
### Пример для StatefulActor\\Program.cs:

```csharp
    public static void Main(string[] args)
    {
        Microsoft.ServiceFabric.Telemetry.ApplicationInsights.Listener.Enable(EventLevel.Verbose);
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(StatefulActor));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
```

Дополнительные сведения о событиях, возникающих в рабочей среде надежных субъектов см. [здесь](service-fabric-reliable-actors-diagnostics.md), а сведения о среде надежных служб — [здесь](service-fabric-reliable-services-diagnostics.md).

Обратите внимание, что для вызова методов среды выполнения надежных субъектов необходимо использовать EventLevel.Verbose (см. примеры выше).

## Настройка Application Insights
Приложение Service Fabric связывается с ресурсом Application Insights с помощью ключа инструментирования. Сведения о получении ключа администрирования см. в [Руководстве по Application Insights](../app-insights-create-new-resource.md#create-an-application-insights-resource). При создании ресурсов выбирайте тип приложения «Другой».

![Выберите тип приложения «Другой» в AI](media/service-fabric-diagnostics-application-insights-setup/AI-app-type-other.JPG)

Полученный ключ инструментирования можно вставить в файл ApplicationInsights.config:

```xml
    <InstrumentationKey>INSERT YOUR KEY HERE</InstrumentationKey>
```

## Просмотр данных
Вы можете [задать параметры в колонке Application Insights](../app-insights-metrics-explorer.md) в соответствии со своими потребностями. Большинство событий Service Fabric будут отображаться как «Пользовательские события», а вызовы методов для субъектов и RunAsync() для службы будут отображаться как запросы. Моделирование этих событий в виде запросов позволяет при построении диаграмм задействовать параметр «имя запроса» и метрику «продолжительность запроса». Со временем коллекции диаграмм, метрик и событий будут пополняться, и вы сможете использовать их в будущем.

## Дальнейшие действия
Узнайте больше об использовании Application Insights для инструментирования приложений Service Fabric.

- [Приступая к работе с Application Insights](../app-insights-get-started.md)
- [Создание собственных событий и метрик](../app-insights-custom-events-metrics-api.md)
 

<!---HONumber=Oct15_HO3-->