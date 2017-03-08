---
title: "Устранение неполадок с помощью трассировки событий | Документация Майкрософт"
description: "Самые распространенные неполадки, возникающие при развертывании служб в Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: mattrowmsft
manager: timlt
editor: 
ms.assetid: 5eb8ef21-da04-4ac8-8b9a-5f7ff1e0a180
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/31/2016
ms.author: mattrow
translationtype: Human Translation
ms.sourcegitcommit: c9730b553e59d12b8720bbf3a06cc956912e27de
ms.openlocfilehash: c920a206d6f3288f6cba0d2658d556749fc4d574


---
# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Устранение распространенных проблем при развертывании служб в Azure Service Fabric
При запуске служб на компьютере разработчика можно без труда использовать [средства отладки Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Работу с удаленными кластерами всегда лучше всего начать со знакомства с [отчетами о работоспособности](service-fabric-view-entities-aggregated-health.md) . Самым простым способом доступа к этим отчетам является использование PowerShell или [SFX](service-fabric-visualizing-your-cluster.md). В этой статье предполагается, что вы выполняете отладку удаленного кластера и имеете базовые знания о применении этих инструментов.

## <a name="application-crash"></a>Сбой приложения
Вывод отчета «Число секций меньше количества целевых реплик или экземпляров» означает, что произошел сбой службы. Чтобы узнать, где произошел отказ, требуется выполнить ряд действий по дополнительному анализу. Если служба масштабируется, незаменимыми будут тщательно продуманные трассировки.  Мы рекомендуем попробовать применить [систему диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md) для сбора этих трассировок и использовать такое решение, как [Azure Application Insight](https://azure.microsoft.com/services/application-insights/), для просмотра и поиска трассировок.

![Работоспособность секции SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

### <a name="during-service-or-actor-initialization"></a>Во время инициализации службы или субъекта
Все исключения, возникающие до инициализации типа службы, приведут к сбою процесса. Для этих типов сбоев в журнале событий приложений отображается ошибка из службы.
Ниже приведены наиболее общие исключения, которые следует просмотреть до инициализации службы.

***System.IO.FileNotFoundException***

Эта ошибка часто возникает из-за отсутствия зависимостей сборки. Проверьте свойство CopyLocal в Visual Studio или в глобальном кэше сборок для узла.

***System.Runtime.InteropServices.COMException***
 *в System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)*

 Указывает, что зарегистрированное имя типа службы не соответствует манифесту службы.

[Службу диагностики Azure](service-fabric-diagnostics-how-to-setup-wad.md) можно настроить для автоматической отправки журнала событий приложений для всех узлов.

### <a name="runasync-or-onactivateasync"></a>RunAsync() или OnActivateAsync()
Если сбой происходит во время инициализации или выполнения зарегистрированного типа службы или субъекта, Azure Service Fabric перехватывает исключение. Их можно просмотреть у поставщиков EventSource, указанных в разделе «Дальнейшие действия».

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о существующих видах диагностики, предоставляемых Service Fabric:

* [Диагностика Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Диагностика Reliable Services](service-fabric-reliable-services-diagnostics.md)




<!--HONumber=Jan17_HO2-->


