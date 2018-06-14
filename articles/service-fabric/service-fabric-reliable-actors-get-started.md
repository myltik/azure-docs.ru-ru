---
title: Создание первой микрослужбы Azure на основе субъектов на языке C# | Документация Майкрософт
description: В этом учебнике последовательно описаны этапы создания, отладки и развертывания простой службы на основе субъекта с использованием Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: 32d3fa09c863c47753267e97e7c4730dff869887
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211411"
---
# <a name="getting-started-with-reliable-actors"></a>Приступая к работе с Reliable Actors
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-actors-get-started.md)
> * [Java в Linux](service-fabric-reliable-actors-get-started-java.md)

В этой статье описывается создание и отладка простого приложения Reliable Actor в Visual Studio. Дополнительные сведения о Reliable Actors см. в статье [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>предварительным требованиям

Приступая к работе, убедитесь, что на вашем компьютере установлена и настроена среда разработки Service Fabric, включая Visual Studio. Дополнительные сведения см. в разделе [Подготовка среды разработки в Linux](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Создание проекта в Visual Studio

Запустите Visual Studio 2015 или более поздней версии от имени администратора и создайте **проект приложения Service Fabric**.

![Средства Service Fabric для Visual Studio — новый проект][1]

В следующем диалоговом окне выберите **Служба субъекта** в разделе **.Net Core 2.0** и введите имя службы.

![Шаблоны проекта Service Fabric][5]

Структура созданного проекта приведена ниже.

![Структура проекта Service Fabric][2]

## <a name="examine-the-solution"></a>Изучение решения

Это решение содержит три проекта:

* **Проект приложения (MyApplication)**. В этом проекте упакованы все службы для последующего развертывания. Проект содержит файл *ApplicationManifest.xml* и сценарии PowerShell для управления приложением.

* **Проект интерфейса (HelloWorld.Interfaces)**. Этот проект содержит определение интерфейса субъекта. Интерфейсы субъекта могут определяться в любом проекте с любым именем.  Интерфейс определяет контракт субъекта, который совместно используется реализацией субъекта и клиентами, вызывающими этот субъект.  Так как проекты клиента могут зависеть от него, обычно имеет смысл определить интерфейс в сборке, отличной от реализации субъекта.

* **Проект службы субъекта (HelloWorld)**. Этот проект используется для определения службы Service Fabric, в которой будет размещен субъект. Он содержит реализацию субъекта, *HellowWorld.cs*. Реализация субъекта — это класс, производный от базового типа `Actor`. Он реализует интерфейсы, определенные в проекте *MyActor.Interfaces*. Класс актера также должен реализовать конструктор, принимающий экземпляр `ActorService` и `ActorId` и передающий их в базовый класс `Actor`.
    
    Этот проект также содержит файл *Program.cs*, который регистрирует классы субъекта в среде выполнения Service Fabric с помощью `ActorRuntime.RegisterActorAsync<T>()`. Класс `HelloWorld` уже зарегистрирован. Все дополнительные реализации субъектов, добавляемые в проект, также должны быть зарегистрированы в методе `Main()`.

## <a name="customize-the-helloworld-actor"></a>Настройка субъекта HelloWorld

Шаблон проекта определяет некоторые методы в интерфейсе `IHelloWorld` и реализует их в реализации субъекта `HelloWorld`.  Замените эти методы таким образом, чтобы служба субъекта возвращала простую строку "Hello World".

В проекте *HelloWorld.Interfaces* в файле *IHelloWorld.cs* замените определение интерфейса следующим образом.

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

В проекте **HelloWorld** в файле **HelloWorld.cs** замените все определение класса следующим образом.

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Нажмите клавиши **CTRL+SHIFT+B**, чтобы выполнить сборку проекта и убедиться, что все скомпилировалось.

## <a name="add-a-client"></a>Добавление клиента

Создайте простое консольное приложение для вызова службы субъекта.

1. В обозревателе решений щелкните решение правой кнопкой мыши и выберите **Добавить** > **Новый проект**.

2. В разделе типов проектов **.NET Core** выберите **Консольное приложение (.NET Core)**.  Назовите проект *ActorClient*.
    
    ![Диалоговое окно "Добавление проекта"][6]    
    
    > [!NOTE]
    > Как правило, консольное приложение не используется в качестве клиента в Service Fabric, но оно является удобным примером для отладки и тестирования с помощью локального кластера Service Fabric.

3. Консольное приложение должно быть 64-разрядным, чтобы обеспечить совместимость с проектом интерфейса и другими зависимостями.  В обозревателе решений щелкните правой кнопкой мыши проект **ActorClient** и выберите **Свойства**.  На вкладке **Сборка** для параметра **Целевая платформа** выберите значение **x64**.
    
    ![Свойства сборки][8]

4. Для проекта клиента требуется пакет NuGet для Reliable Actors.  Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.  В консоли диспетчера пакетов введите следующую команду.
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Пакет NuGet и все его зависимости будут установлены в проект ActorClient.

5. В проекте клиента также требуется ссылка на проект интерфейсов.  В проекте ActorClient щелкните правой кнопкой мыши **Зависимости** и выберите **Добавить ссылку**.  Выберите **Проекты > Решение** (если не выбрали ранее), затем установите флажок рядом с проектом **HelloWorld.Interfaces**.  Последовательно выберите **ОК**.
    
    ![Диалоговое окно "Добавление ссылки"][7]

6. В проекте ActorClient замените все содержимое *Program.cs* следующим кодом.
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Выполнение и отладка

Нажмите клавишу **F5**, чтобы выполнить сборку приложения, развернуть и запустить его локально в кластере Service Fabric разработки.  Вы можете следить за ходом развертывания в окне **Вывод** .

![Окно вывода отладки Service Fabric][3]

Если результат содержит текст *The application is ready* (Приложение готово), то службу можно проверить с помощью приложения ActorClient.  В обозревателе решений щелкните правой кнопкой мыши проект **ActorClient** и выберите **Отладка** > **Запустить новый экземпляр**.  Приложение командной строки должно отобразить выходные данные из службы субъекта.

![Выходные данные приложения][9]

> [!TIP]
> В среде выполнения субъектов Service Fabric предусмотрены некоторые [события и счетчики производительности, связанные с методами субъекта](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Они полезны при диагностике и мониторинге производительности.

## <a name="next-steps"></a>Дополнительная информация
Узнайте больше о том, [как Reliable Actors использует платформу Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png