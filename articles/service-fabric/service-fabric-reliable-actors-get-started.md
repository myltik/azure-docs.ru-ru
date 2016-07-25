<properties
   pageTitle="Приступая к работе с Service Fabric Reliable Actors | Microsoft Azure"
   description="В этом учебнике последовательно описаны этапы создания, отладки и развертывания простой службы на основе субъекта с использованием Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# Приступая к работе с Reliable Actors
В этой статье объясняются основы Reliable Actors в Azure Service Fabric. Также описывается процесс создания, отладки и развертывания простого приложения Reliable Actors в Visual Studio.

## Установка и настройка
Приступая к работе, убедитесь, что на вашем компьютере установлена и настроена среда разработки Service Fabric. Подробные инструкции по настройке среды разработки см. [здесь](service-fabric-get-started.md).

## Основные понятия
Перед началом работы с субъектами Reliable Actors необходимо ознакомиться с четырьмя основными понятиями.

* **Служба субъекта**. Субъекты Reliable Actors упаковываются в службы Reliable Services, которые могут быть развернуты в инфраструктуре Service Fabric. Служба может содержать как один, так и несколько субъектов. Далее мы подробно рассмотрим особенности использования одного и нескольких субъектов в службе. А пока предположим, что нам нужно реализовать только один субъект.
* **Интерфейс субъекта**. Интерфейс субъекта используется для определения строго типизированного общедоступного интерфейса субъекта. В терминах модели Reliable Actors интерфейс субъекта определяет типы сообщений, которые субъект может понять и обработать. Интерфейс субъекта используется другими субъектами или клиентскими приложениями для отправки (асинхронной) сообщений субъекту. Субъекты Reliable Actors могут реализовывать несколько интерфейсов. Далее мы увидим, что субъект HelloWorld может реализовывать интерфейс IHelloWorld, а также интерфейс ILogging, определяющий другие сообщения и функциональные возможности.
* **Регистрация субъекта**. Тип субъекта должен быть зарегистрирован в службе Reliable Actors. Таким образом, вы сообщите платформе Service Fabric о новом типе, который можно использовать для создания новых субъектов.
* **Класс ActorProxy**. Класс ActorProxy используется клиентскими приложениями для вызова методов, доступных через его интерфейсы. Класс ActorProxy предоставляет две важные функциональные возможности:
	* Разрешение имен. Класс позволяет найти субъект в кластере (определить, на каком узле кластера размещается субъект).
	* Обработка сбоев. Класс может повторно вызывать методы и повторно определять расположение субъекта, например после сбоя, в результате которого субъект перемещается на другой узел в кластере.

Следует упомянуть правила, которые относятся к интерфейсу субъекта:

- методы интерфейса субъекта не могут быть перегружены;
- методы интерфейса субъекта не должны иметь выходных, ссылочных или необязательных параметров.
- универсальные интерфейсы не поддерживаются.

## Создание проекта в Visual Studio
Установив инструменты Visual Studio для работы с Service Fabric, можно приступать к созданию новых типов проекта. Для этого в диалоговом окне **Новый проект** разверните категорию **Облако**.


![Средства Service Fabric для Visual Studio — новый проект][1]

В следующем диалоговом окне вы можете выбрать тип проекта, который нужно создать.

![Шаблоны проекта Service Fabric][5]

Для проекта HelloWorld мы воспользуемся службой Reliable Actors платформы Service Fabric.

Созданное решение будет иметь такую структуру:

![Структура проекта Service Fabric][2]

## Простые стандартные блоки в решении на базе надежных субъектов

Как правило, решение на основе модели Reliable Actors состоит из трех проектов.

* **Проект приложения (MyActorApplication)**. Он упаковывает все службы в пакет для последующего развертывания. Проект содержит файл *ApplicationManifest.xml* и сценарии PowerShell для управления приложением.

* **Проект интерфейса (MyActor.Interfaces)**. Он содержит определение интерфейса для субъекта. В проекте MyActor.Interfaces можно определить интерфейсы, которые будут использоваться субъектами в решении. Интерфейсы субъекта можно определить в любом проекте с любым именем, однако интерфейс определяет контракт субъекта, который совместно используется реализацией субъекта и клиентами, вызывающими субъект. Поэтому обычно имеет смысл определить его в сборке, которая отличается от реализации субъекта и может совместно использоваться несколькими проектами.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Проект службы субъекта (MyActor)**. Он используется для определения службы Service Fabric, которая будет хост-службой субъекта. Этот проект содержит реализацию субъекта. Реализация субъекта — это класс, производный от базового типа `Actor`. Он реализует интерфейсы, определенные в проекте MyActor.Interfaces.

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class MyActor : Actor, IMyActor
{
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

> [AZURE.TIP] В среде выполнения субъектов Service Fabric предусмотрены некоторые [события и счетчики производительности, связанные с методами субъекта](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Они полезны при диагностике и мониторинге производительности.


## Отладка

Инструменты Visual Studio для работы с Service Fabric поддерживают отладку на локальном компьютере. Чтобы запустить сеанс отладки, нажмите клавишу F5. Visual Studio скомпилирует (при необходимости) пакеты. Кроме того, программа развернет приложение в локальном кластере Service Fabric, а затем присоединит отладчик.

Вы можете следить за ходом развертывания в окне **Вывод**.

![Окно вывода отладки Service Fabric][3]


## Дальнейшие действия
 - [Использование платформы Service Fabric надежными субъектами](service-fabric-reliable-actors-platform.md)
 - [Управление состоянием субъекта](service-fabric-reliable-actors-state-management.md)
 - [Жизненный цикл субъектов и сбор мусора](service-fabric-reliable-actors-lifecycle.md)
 - [Справочная документация по API субъектов](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Пример кода](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0713_2016-->