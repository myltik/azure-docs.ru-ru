<properties
   pageTitle="Приступая к работе с надежными субъектами | Microsoft Azure"
   description="Это пошаговое руководство проведет вас по этапам создания, отладки и развертывания примера службы HelloWorld с использованием надежных субъектов Service Fabric."
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Надежные субъекты: пошаговое руководство по началу работы на примере HelloWorld
В данной статье объясняются основы надежных субъектов Service Fabric Reliable Actors и описывается процесс создания, отладки и развертывания простого приложения HelloWorld в Visual Studio.

## Установка и настройка
Прежде чем приступить к работе, убедитесь, что на вашем компьютере установлена и настроена среда разработки Service Fabric. Подробные инструкции можно найти [здесь](service-fabric-get-started.md).

## Основные понятия
Перед началом работы с надежными субъектами необходимо ознакомиться с четырьмя основными понятиями.

* **Служба субъектов**. Надежные субъекты упаковываются в службы, которые могут быть развернуты в инфраструктуре Service Fabric. Служба может содержать как один, так и несколько субъектов. Далее мы подробно рассмотрим ограничения, связанные с использованием одного и нескольких субъектов в службе, а пока предположим, что нам требуется реализовать только один субъект.
* **Интерфейс субъекта**. Интерфейс субъекта представляет собой механизм общего доступа к субъекту. В субъектной модели он определяет тип сообщений, которые субъект понимает и может обработать. Интерфейс субъекта используется другими субъектами или клиентскими приложениями для «отправки» (асинхронно) сообщений субъекту. Надежные субъекты могут реализовывать несколько интерфейсов. Далее мы увидим, что субъект HelloWorld может реализовывать интерфейс IHelloWorld, а также интерфейс ILogging, определяющий другие сообщения и функциональные возможности.
* **Регистрация субъекта**. Чтобы платформа Service Fabric понимала тип субъекта и могла использовать его для создания новых субъектов, его необходимо зарегистрировать в службе субъектов.
* **Класс ActorProxy**. Класс ActorProxy используется для привязки к субъекту и вызова методов, доступных через его интерфейсы. Класс ActorProxy предоставляет две важные функциональные возможности:
	* Разрешение имен. Класс позволяет найти субъект в кластере (определить, на каком узле кластера размещается субъект).
	* Обработка сбоев. Класс может повторно вызывать методы и повторно определять расположение субъекта после, например, сбоя, в результате которого субъект перемещается на другой узел в кластере.

## Создание проекта в Visual Studio
Установив инструменты Visual Studio для работы с Service Fabric, можно приступать к созданию новых типов проекта. Для этого разверните группу «Облако» в диалоговом окне «Новый проект».


![Инструменты Service Fabric для VS — новый проект][1]

В следующем диалоговом окне вы можете выбрать тип проекта, который нужно создать.

![Шаблоны проектов Service Fabric][5]

Для проекта HelloWorld мы воспользуемся службой субъектов Service Fabric.

Созданное решение будет иметь такую структуру:

![Структура проекта Service Fabric][2]

## Простые стандартные блоки в решении на базе надежных субъектов

Как правило, решение на базе надежных субъектов состоит из трех проектов.

* Проект приложения (HelloWorldApplication). Он упаковывает все службы в пакет для последующего развертывания. Проект содержит файл ApplicationManifest.xml и сценарии PowerShell для управления приложением.

* Проект интерфейсов (HelloWorld.Interfaces). Он содержит определение интерфейса для субъекта. В этом проекте можно определить интерфейсы, которые будут использоваться субъектами в решении.

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* Проект службы (HelloWorld). Он используется для определения службы Service Fabric, которая будет хост-службой субъекта. Этот проект содержит реализацию субъекта и стандартный код, который в большинстве случаев изменять не требуется (ServiceHost.cs). Реализация субъекта включает в себя реализацию класса, производного от базового типа (Actor), и реализацию интерфейсов, определенных в проекте интерфейсов (.Interfaces).

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

Проект службы субъектов содержит код для создания службы Service Fabric. В определении службы регистрируются типы субъектов, чтобы она могла использовать их для создания экземпляров новых субъектов.

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

Если вы создаете новый проект Visual Studio и у вас есть только одно определение субъекта, то его регистрация по умолчанию включается в код, который создается программой Visual Studio. Если вы определяете другие субъекты в службе, зарегистрируйте их, используя следующую строку:

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## Отладка

Инструменты Visual Studio для работы с Service Fabric поддерживают отладку на локальном компьютере. Чтобы запустить сеанс отладки, нажмите клавишу F5. Visual Studio скомпилирует (при необходимости), упакует и развернет приложение в локальном кластере Service Fabric, а затем присоединит отладчик. Процесс в целом аналогичен отладке приложений ASP.NET. За ходом развертывания можно следить, используя окно вывода.

![Окно вывода отладки Service Fabric][3]


## Дальнейшие действия

- [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)
- [Справочная документация по API-интерфейсам Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Пример кода](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=Nov15_HO4-->