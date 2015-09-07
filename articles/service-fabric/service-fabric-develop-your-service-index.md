<properties
   pageTitle="Разработка службы Service Fabric"
	description="Тематические статьи и учебники, помогающие научиться разрабатывать службы Service Fabric с использованием моделей программирования на основе надежных субъектов и служб."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="NA"
	ms.date="08/20/2015"
	ms.author="ryanwi"/>

# Разработка службы Service Fabric
На этой странице приведены ссылки на общие сведения и тематические статьи и справочные разделы, которые помогут вам глубже изучить разработку служб Service Fabric. Платформа Service Fabric предлагает две высокоуровневые модели программирования для создания служб: интерфейсы API надежных субъектов и надежных служб. Хотя обе модели основаны на одних и тех же принципах Service Fabric, они характеризуются разным соотношением простоты и гибкости с точки зрения параллельной работы, секционирования и обмена данными. Полезно изучить обе модели, чтобы выбрать ту из них, которая лучше всего подходит для разработки конкретной службы приложения.

- [Выбор модели программирования](service-fabric-choose-framework.md)
- [Общие сведения о субъектной модели Service Fabric](service-fabric-reliable-actors-introduction.md)
- [Общие сведения о модели программирования на основе надежных служб](../Service-Fabric/service-fabric-reliable-services-introduction.md)

## Модель программирования на основе надежных субъектов
 Надежные субъекты — это асинхронная однопоточная субъектная модель. Субъекты представляют собой единицы состояний и вычислений, распределенные по кластеру для обеспечения высокого уровня масштабируемости. Модель надежных субъектов использует распределенное хранилище, предоставляемое платформой Service Fabric. Разработчики приложений могут воспользоваться преимуществами высокой доступности и единообразного управления состояниями. Дополнительные сведения см. в следующих статьях:

- [Приступая к работе с надежными субъектами](service-fabric-reliable-actors-get-started.md)
- [Жизненный цикл субъектов и сбор мусора](service-fabric-reliable-actors-lifecycle.md)
- [Использование платформы Service Fabric субъектами](service-fabric-reliable-actors-platform.md)
- [Заметки о сериализации типов субъектов в Azure Service Fabric](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Взаимодействие с субъектами описано в следующих статьях:

- [Общие сведения о субъектной модели Service Fabric](service-fabric-reliable-actors-introduction.md#actor-communication)
- [Взаимодействие со службами](service-fabric-connect-and-communicate-with-services.md)

В этих статьях рассматриваются шаблоны проектирования и связанные с ними сценарии:

- [Шаблоны проектирования в субъектной модели](service-fabric-reliable-actors-patterns-introduction.md)  
- [Шаблон: смарт-кэш](service-fabric-reliable-actors-pattern-smart-cache.md)
- [Шаблон: распределенные сети и графы](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)
- [Шаблон: управление ресурсами](service-fabric-reliable-actors-pattern-resource-governance.md)
- [Шаблон: построение службы с отслеживанием состояния](service-fabric-reliable-actors-pattern-stateful-service-composition.md)
- [Шаблон: Интернет вещей](service-fabric-reliable-actors-pattern-internet-of-things.md)
- [Шаблон: распределенные вычисления](service-fabric-reliable-actors-pattern-distributed-computation.md)
- [Некоторые антишаблоны](service-fabric-reliable-actors-anti-patterns.md)

Методы надежных субъектов поддерживают простой «поочередный» параллелизм. Параллелизм, таймеры и напоминания, а также реентерабельность описаны в следующих статьях:

- [Параллелизм](service-fabric-reliable-actors-introduction.md#concurrency)
- [События и счетчики производительности, связанные с параллелизмом](service-fabric-reliable-actors-diagnostics.md)
- [Реентерабельность субъектов](service-fabric-reliable-actors-reentrancy.md)
- [Таймеры субъектов](service-fabric-reliable-actors-timers-reminders.md)

Сведения о настройках надежных субъектов можно найти здесь:

- [Настройка KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)  
- [Настройка надежных субъектов: ReliableDictionaryActorStateProvider](../service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

Для надежных субъектов предусмотрены события и счетчики производительности, которые можно использовать для мониторинга и диагностики работоспособности вашей службы:

- [Диагностика субъектов](service-fabric-reliable-actors-diagnostics.md)
- [События субъектов](service-fabric-reliable-actors-events.md)


## Модель программирования на основе надежных служб
Надежные службы — это простая, мощная, верхнеуровневая модель программирования, открывающая широкие и гибкие возможности для разработки приложений. Дополнительные сведения см. в следующих статьях:

- [Приступая к работе с надежными службами](service-fabric-reliable-services-quick-start.md)
- [Обзор моделей программирования](../service-fabric-reliable-services-service-overview.md)  
- [Архитектура](service-fabric-reliable-services-platform-architecture.md)
- [Надежные коллекции](service-fabric-reliable-services-reliable-collections.md)
- [Настройка надежных служб с отслеживанием состояния](../Service-Fabric/service-fabric-reliable-services-configuration.md)
- [Расширенные возможности модели программирования на основе надежных служб](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

Взаимодействие с надежными службами и абстракциями, которые клиенты могут использовать для обнаружения конечных точек служб и связи с ними, описаны в следующих статьях:

- [Взаимодействие со службами](service-fabric-connect-and-communicate-with-services.md)
- [Модель взаимодействия со службами](service-fabric-reliable-services-communication.md)
- [Коммуникационный стек, предоставляемый по умолчанию средой надежных служб](service-fabric-reliable-services-communication-default.md)
- [Коммуникационный стек WCF для надежных служб](service-fabric-reliable-services-communication-wcf.md)
- [Начало работы со службами веб-API Microsoft Azure Service Fabric с собственным хостом OWIN (VS 2015 RC)](service-fabric-reliable-services-communication-webapi.md)

Для надежных служб предусмотрены события и счетчики производительности, которые можно использовать для мониторинга и диагностики работоспособности вашей службы:

- [Диагностика надежных служб с отслеживанием состояния](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=August15_HO9-->