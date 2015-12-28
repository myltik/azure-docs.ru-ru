<properties
   pageTitle="Устранение распространенных неполадок | Microsoft Azure"
   description="Самые распространенные неполадки, возникающие при развертывании служб в Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>


# Устранение распространенных неполадок
При запуске служб на компьютере разработчика можно без труда использовать [средства отладки Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Работу с удаленными кластерами всегда лучше всего начать со знакомства с [отчетами о работоспособности](service-fabric-view-entities-aggregated-health.md). Самым простым способом доступа к этим отчетам является использование PowerShell или [SFX](service-fabric-visualizing-your-cluster.md). В этой статье предполагается, что вы выполняете отладку удаленного кластера и имеете базовые знания о применении этих инструментов.

##Сбой приложения
Вывод отчета «Число секций меньше количества целевых реплик или экземпляров» означает, что произошел сбой службы. Чтобы узнать, где произошел отказ, требуется выполнить ряд действий по дополнительному анализу. Если служба масштабируется, незаменимыми будут тщательно продуманные трассировки. Для сбора и просмотра данных этих трассировок мы рекомендуем использовать [систему диагностики Azure](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md).

![Работоспособность секции SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###Во время инициализации службы или субъекта
Все исключения, возникающие до инициализации типа службы, приведут к сбою процесса. Для этих типов сбоев в журнале событий приложений отображается ошибка из службы. Ниже приведены наиболее общие исключения, которые следует просмотреть до инициализации службы.

| Ошибка | Описание |
| --- | --- |
| System.IO.FileNotFoundException | Эта ошибка часто возникает из-за отсутствия зависимостей сборки. Проверьте свойство CopyLocal в Visual Studio или в глобальном кэше сборок для узла.
| System.Runtime.InteropServices.COMException в System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)|Указывает, что зарегистрированное имя типа службы не соответствует манифесту службы. |

[Службу диагностики Azure](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) можно настроить для автоматической отправки журнала событий приложений для всех узлов.

###RunAsync() или OnActivateAsync()
Если сбой происходит во время инициализации или выполнения зарегистрированного типа службы или субъекта, Azure Service Fabric перехватывает исключение. Их можно просмотреть у поставщиков EventSource, указанных в разделе «Дальнейшие действия».

## Дальнейшие действия

Узнайте больше о существующих видах диагностики, предоставляемых Service Fabric:

* [Диагностика Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Диагностика Reliable Services](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=AcomDC_1217_2015-->