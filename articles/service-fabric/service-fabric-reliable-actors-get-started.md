---
title: "Создание первой микрослужбы Azure на основе субъектов на языке C# | Документация Майкрософт"
description: "В этом учебнике последовательно описаны этапы создания, отладки и развертывания простой службы на основе субъекта с использованием Service Fabric Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 3f447e049ccd33c77f422e8aa703ad6646f9ffa2
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
# <a name="getting-started-with-reliable-actors"></a>Приступая к работе с Reliable Actors
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-actors-get-started.md)
> * [Java в Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

В этой статье объясняются основы Reliable Actors в Azure Service Fabric. Также описывается процесс создания, отладки и развертывания простого приложения Reliable Actors в Visual Studio.

## <a name="installation-and-setup"></a>Установка и настройка
Приступая к работе, убедитесь, что на вашем компьютере установлена и настроена среда разработки Service Fabric.
Подробные инструкции по настройке среды разработки см. [здесь](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Основные понятия
Чтобы приступить к работе с субъектами Reliable Actors, необходимо ознакомиться только с несколькими основными понятиями.

* **Служба субъекта**. Субъекты Reliable Actors упаковываются в службы Reliable Services, которые могут быть развернуты в инфраструктуре Service Fabric. Экземпляры субъектов активируются в именованном экземпляре службы.
* **Регистрация субъекта**. Как и в случае Reliable Services, служба Reliable Actor должна быть зарегистрирована в среде выполнения Service Fabric. Кроме того, тип субъекта должен быть зарегистрирован в среде выполнения субъектов.
* **Интерфейс субъекта**. Интерфейс субъекта используется для определения строго типизированного общедоступного интерфейса субъекта. В терминах модели Reliable Actors интерфейс субъекта определяет типы сообщений, которые субъект может понять и обработать. Интерфейс субъекта используется другими субъектами или клиентскими приложениями для отправки (асинхронной) сообщений субъекту. Субъекты Reliable Actors могут реализовывать несколько интерфейсов.
* **Класс ActorProxy**. Класс ActorProxy используется клиентскими приложениями для вызова методов, предоставляемых через интерфейсы субъекта. Класс ActorProxy предоставляет две важные функциональные возможности:
  
  * Разрешение имен. Класс позволяет найти субъект в кластере (определить, на каком узле кластера размещается субъект).
  * Обработка сбоев. Класс может повторно вызывать методы и повторно сопоставить расположение субъекта, например после сбоя, в результате которого субъект перемещается на другой узел в кластере.

Следует упомянуть правила, которые относятся к интерфейсу субъекта:

* методы интерфейса субъекта не могут быть перегружены;
* методы интерфейса субъекта не должны иметь выходных, ссылочных или необязательных параметров.
* универсальные интерфейсы не поддерживаются.

## <a name="create-a-new-project-in-visual-studio"></a>Создание проекта в Visual Studio
Запустите Visual Studio 2015 или Visual Studio 2017 от имени администратора и создайте проект приложения Service Fabric.

![Средства Service Fabric для Visual Studio — новый проект][1]

В следующем диалоговом окне вы можете выбрать тип проекта, который нужно создать.

![Шаблоны проекта Service Fabric][5]

Для проекта HelloWorld мы воспользуемся службой Reliable Actors платформы Service Fabric.

Созданное решение будет иметь такую структуру:

![Структура проекта Service Fabric][2]

## <a name="reliable-actors-basic-building-blocks"></a>Простые стандартные блоки в решении на базе надежных субъектов
Как правило, решение на основе модели Reliable Actors состоит из трех проектов.

* **Проект приложения (MyActorApplication)**. Он упаковывает все службы в пакет для последующего развертывания. Проект содержит файл *ApplicationManifest.xml* и сценарии PowerShell для управления приложением.
* **Проект интерфейса (MyActor.Interfaces)**. Он содержит определение интерфейса для субъекта. В проекте MyActor.Interfaces можно определить интерфейсы, которые будут использоваться субъектами в решении. Интерфейсы субъекта можно определить в любом проекте с любым именем, однако интерфейс определяет контракт субъекта, который совместно используется реализацией субъекта и клиентами, вызывающими субъект. Поэтому обычно имеет смысл определить его в сборке, которая отличается от реализации субъекта и может совместно использоваться несколькими проектами.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Проект службы субъекта (MyActor)**. Он используется для определения службы Service Fabric, которая будет хост-службой субъекта. Этот проект содержит реализацию субъекта. Реализация субъекта — это класс, производный от базового типа `Actor`. Он реализует интерфейсы, определенные в проекте MyActor.Interfaces. Класс актера также должен реализовать конструктор, принимающий экземпляр `ActorService` и `ActorId` и передающий их в базовый класс `Actor`. Это позволяет внедрять зависимости платформы как зависимости конструктора.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Служба субъекта должна быть зарегистрирована в среде выполнения Service Fabric с указанием типа службы. Чтобы служба субъекта выполняла экземпляры вашего субъекта, его тип также нужно зарегистрировать в службе субъекта. Метод регистрации `ActorRuntime` выполняет это действие для субъектов.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Если вы создаете новый проект Visual Studio и у вас есть только одно определение субъекта, его регистрация по умолчанию включается в код, который создается программой Visual Studio. Если вы определяете другие субъекты в службе, зарегистрируйте их, используя следующую строку:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> В среде выполнения субъектов Service Fabric предусмотрены некоторые [события и счетчики производительности, связанные с методами субъекта](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Они полезны при диагностике и мониторинге производительности.
> 
> 

## <a name="debugging"></a>Отладка
Инструменты Visual Studio для работы с Service Fabric поддерживают отладку на локальном компьютере. Чтобы запустить сеанс отладки, нажмите клавишу F5. Visual Studio скомпилирует (при необходимости) пакеты. Кроме того, программа развернет приложение в локальном кластере Service Fabric, а затем присоединит отладчик.

Вы можете следить за ходом развертывания в окне **Вывод** .

![Окно вывода отладки Service Fabric][3]

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о том, [как Reliable Actors использует платформу Service Fabric](service-fabric-reliable-actors-platform.md).

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

