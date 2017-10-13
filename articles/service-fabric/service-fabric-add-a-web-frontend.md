---
title: "Создание веб-интерфейса для приложения Azure Service Fabric с помощью ASP.NET Core | Документация Майкрософт"
description: "Предоставление веб-доступа к приложению Service Fabric с помощью проекта ASP.NET Core и удаленное взаимодействие между службами."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: b19aaa652f2c15573ded632ca1348e1a6752f080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Создание внешнего интерфейса веб-службы для приложения с помощью ASP.NET Core
По умолчанию службы Azure Service Fabric не предоставляют общедоступный интерфейс для веб-служб. Чтобы сделать свое приложение функциональным для клиентов HTTP, вам нужно создать веб-проект, который будет работать в качестве точки входа и обмениваться данными с отдельными службами.

Мы продолжим с того места, на котором остановились в руководстве по [созданию первого приложения в Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) , и добавим веб-службу ASP.NET Core для службы счетчиков с отслеживанием состояния. Выполните соответствующие указания в этом руководстве, если вы этого еще не сделали.

## <a name="set-up-your-environment-for-aspnet-core"></a>Настройте среду для ASP.NET Core

Для работы с этим руководством требуется Visual Studio 2017. Подойдет любой выпуск. 

 - Установите [Visual Studio 2017](https://www.visualstudio.com/).

Для разработки приложений ASP.NET Core Service Fabric необходимо установить следующие рабочие нагрузки:
 - **разработка Azure** (в разделе *Web & Cloud* (Сеть и облако));
 - **ASP.NET и веб-разработка** (в разделе *Web & Cloud* (Сеть и облако));
 - **кроссплатформенная разработка .NET Core** (в разделе *Other Toolsets* (Другие наборы средств)).

>[!Note] 
>Средства .NET Core для Visual Studio 2015 больше не обновляются. Если по-прежнему используется Visual Studio 2015, нужно установить [средства .NET Core для Visual Studio 2015, предварительная версия 2](https://www.microsoft.com/net/download/core).

## <a name="add-an-aspnet-core-service-to-your-application"></a>Добавление службы ASP.NET Core в приложение
ASP.NET Core — это простое кросс-платформенное средство для разработки веб-страниц, позволяющее создавать современные пользовательские веб-интерфейсы и интерфейсы веб-API. 

Давайте добавим проект веб-API ASP.NET в существующее приложение.

1. В обозревателе решений в проекте приложения щелкните правой кнопкой мыши **Службы** и последовательно выберите **Добавить > Новая служба Service Fabric**.
   
    ![Добавление новой службы в существующее приложение][vs-add-new-service]
2. На странице **создания службы** выберите **ASP.NET Core** и укажите имя для этой службы.
   
    ![Выбор веб-службы ASP.NET в диалоговом окне создания службы][vs-new-service-dialog]

3. На следующей странице представлен набор шаблонов проекта ASP.NET Core. Обратите внимание, что здесь те же варианты решений, которые можно увидеть при создании проекта ASP.NET Core вне приложения Service Fabric, за исключением небольшого количества дополнительного кода для регистрации службы в среде Service Fabric. В этом руководстве мы будем использовать **веб-API**. Однако те же идеи можно использовать и при построении полноценного веб-приложения.
   
    ![Выбор типа проекта ASP.NET][vs-new-aspnet-project-dialog]
   
    После создания проекта веб-API в вашем приложении будут две службы. По мере построения приложения вам нужно будет добавлять дополнительные службы (таким же образом). Каждая из этих служб может быть отдельно обновлена, в том числе до определенной версии.

## <a name="run-the-application"></a>Выполнение приложения
Чтобы прояснить свои действия, мы развернем новое приложение и рассмотрим поведение по умолчанию, связанное с использованием шаблона веб-API ASP.NET Core.

1. В Visual Studio нажмите клавишу F5, чтобы начать отладку приложения.
2. После завершения развертывания Visual Studio запустит браузер и откроет в нем корневой каталог службы веб-API ASP.NET. Так как шаблон веб-API ASP.NET Core не предоставляет поведение по умолчанию для корневого каталога, в браузере будет показано сообщение об ошибке 404.
3. В браузере добавьте к адресу путь `/api/values` . При этом будет вызван метод `Get` объекта ValuesController в шаблоне веб-API. Он вернет ответ по умолчанию, предоставленный шаблоном — массив JSON, который содержит две строки:
   
    ![Значения по умолчанию, возвращаемые шаблоном веб-API ASP.NET Core][browser-aspnet-template-values]
   
    В конце этого руководства на странице отобразится последнее значением счетчика службы с отслеживанием состояния вместо строк по умолчанию.

## <a name="connect-the-services"></a>Подключение служб
Service Fabric позволяет пользователям самим определять способ взаимодействия со службами Reliable Services. В одном приложении можно иметь службы, доступные по TCP, другие службы, которые доступны через HTTP REST API, и третьи службы, которые доступны через веб-сокеты. Сведения о доступных возможностях и их преимуществах и недостатках см. в статье [Подключение к службам в Service Fabric и взаимодействие с ними](service-fabric-connect-and-communicate-with-services.md). В этом руководстве мы используем [удаленное взаимодействие со службой Service Fabric](service-fabric-reliable-services-communication-remoting.md), предоставленное в пакете SDK.

При использовании подхода удаленного взаимодействия со службой (на основе удаленных вызовов процедур, или RPC) определяется интерфейс, который будет выступать в качестве общедоступного контракта службы. Затем этот интерфейс используется для создания класса прокси для взаимодействия со службой.

### <a name="create-the-remoting-interface"></a>Создание интерфейса удаленного взаимодействия
Начнем с создания интерфейса, который будет выполнять роль контракта между службой с отслеживанием состояния и другими службами, включая веб-проект ASP.NET Core. Этот интерфейс должен совместно использоваться всеми службами, которые используют его для вызовов RPC, поэтому мы создадим его в отдельном проекте библиотеки классов.

1. В обозревателе решений щелкните решение правой кнопкой мыши и выберите **В браузере добавьте к адресу путь** > **Новый проект**.

2. В области навигации слева выберите **Visual C#**, а затем — шаблон **Библиотека классов**. Для платформы .NET Framework должна быть установлена версия **4.5.2**.
   
    ![Создание проекта интерфейса для службы с отслеживанием состояния][vs-add-class-library-project]

3. Установите пакет NuGet **Microsoft.ServiceFabric.Services.Remoting**. Найдите **Microsoft.ServiceFabric.Services.Remoting** в пакете диспетчера NuGet и установите его для всех проектов в решении, использующих удаленное взаимодействие со службой, включая:
   - проект библиотеки классов, содержащий интерфейс службы;
   - проект с отслеживанием состояния службы;
   - проект веб-службы ASP.NET Core.
   
    ![Добавление пакета NuGet для служб][vs-services-nuget-package]

4. В библиотеке классов создайте интерфейс с помощью одиночного метода `GetCountAsync`и выполните расширение интерфейса из `Microsoft.ServiceFabric.Services.Remoting.IService`. Интерфейс удаленного доступа должен быть производным от этого интерфейса для указания, что он является интерфейсом удаленного взаимодействия со службой.
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Реализация интерфейса в службе с отслеживанием состояния
Теперь, когда мы определили интерфейс, нам нужно реализовать его в нашей службе с отслеживанием состояния.

1. В службе с отслеживанием состояния добавьте ссылку на проект библиотеки классов, содержащий интерфейс.
   
    ![Добавление ссылки на проект библиотеки классов в службе с отслеживанием состояния][vs-add-class-library-reference]
2. Найдите класс, который наследуется от `StatefulService`, например `MyStatefulService`, и выполните его расширение для реализации интерфейса `ICounter`.
   
    ```c#
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. Теперь реализуйте одиночный метод `GetCountAsync`, определенный в интерфейсе `ICounter`.
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Предоставление службы с отслеживанием состояния с помощью прослушивателя удаленного взаимодействия в службе
После реализации интерфейса `ICounter` наконец нужно открыть коммуникационный канал удаленного взаимодействия со службой. Для служб с отслеживанием состояния Service Fabric предоставляет переопределяемый метод `CreateServiceReplicaListeners`. С помощью этого метода можно указать один или несколько прослушивателей связи, на основе типа связи, который вы хотите включить в службе.

> [!NOTE]
> Эквивалентный метод для открытия коммуникационного канала для службы без отслеживания состояния называется `CreateServiceInstanceListeners`.

В нашем случае мы заменим имеющийся метод `CreateServiceReplicaListeners` и укажем экземпляр `ServiceRemotingListener`, создающий конечную точку RPC, вызываемую с клиентов с помощью `ServiceProxy`.  

Метод расширения `CreateServiceRemotingListener` в интерфейсе `IService` позволяет легко создавать `ServiceRemotingListener` со всеми параметрами по умолчанию. Чтобы использовать этот метод расширения, обязательно импортируйте пространство имен `Microsoft.ServiceFabric.Services.Remoting.Runtime`. 

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Использование класса ServiceProxy для взаимодействия со службой
Теперь наша служба с отслеживанием состояния готова получать трафик от других служб по протоколу RPC. Поэтому все, что остается — добавить код для взаимодействия с ней из веб-службы ASP.NET.

1. В проекте ASP.NET добавьте ссылку на библиотеку классов, которая содержит интерфейс `ICounter` .

2. Ранее вы добавили пакет NuGet **Microsoft.ServiceFabric.Services.Remoting** для проекта ASP.NET. Этот пакет содержит класс `ServiceProxy`, который используется для вызовов RPC в службу с отслеживанием состояния. Убедитесь, что этот пакет NuGet установлен в проекте веб-службы ASP.NET Core.

4. В папке **Контроллеры** откройте класс `ValuesController`. Обратите внимание, что сейчас метод `Get` просто возвращает жестко запрограммированный строковый массив значений value1 и value2, соответствующих тем, которые мы видели ранее в браузере. Замените эту реализацию следующим кодом:
   
    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    Первая строка кода — ключевая. Чтобы добавить в службу с отслеживанием состояния прокси-сервер ICounter, необходимо предоставить два блока данных: идентификатор раздела и имя службы.
   
    Секционирование позволяет масштабировать службы с отслеживанием состояния, разбивая их состояние на разные сегменты на основе определенного ключа, например идентификатора клиента или почтового индекса. Так как в обычном приложении у службы с отслеживанием состояния есть только один раздел, ключ не имеет особого значения. При указании любого ключа будет создан один и тот же раздел. Дополнительные сведения о секционировании служб см. в статье [Секционирование служб Reliable Services в Service Fabric](service-fabric-concepts-partitioning.md).
   
    Имя службы — это универсальный код ресурса в формате fabric:/&lt;имя_приложения&gt;/&lt;имя_службы&gt;.
   
    Service Fabric использует эти два блока информации для уникальной идентификации компьютера, на который должны отправляться запросы. Класс `ServiceProxy` также согласованно переключает операции на другой компьютер, когда на компьютере с размещенной секцией службы с отслеживанием состояния происходит сбой. Эта абстракция значительно упрощает процесс написания кода клиента для работы с другими службами.
   
    После создания прокси-сервера мы просто вызываем метод `GetCountAsync` , который возвращает нужный результат.

5. Нажмите клавишу F5 еще раз, чтобы запустить измененное приложение. Visual Studio автоматически откроет в браузере корень веб-проекта. Добавьте путь api/values, и вы увидите возвращаемое текущее значение счетчика.
   
    ![Отображаемое в браузере значение счетчика с отслеживанием состояния][browser-aspnet-counter-value]
   
    Периодически обновляйте браузер, чтобы отслеживать актуальные показания счетчика.

## <a name="kestrel-and-weblistener"></a>Kestrel и WebListener

Для веб-сервера ASP.NET Core по умолчанию, известного как Kestrel, [не поддерживается обработка прямого трафика Интернета](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel). В результате ASP.NET Core шаблон службы без отслеживания состояния для Service Fabric использует [WebListener](https://docs.microsoft.com/aspnet/core/fundamentals/servers/weblistener) по умолчанию. 

Дополнительные сведения о Kestrel и WebListener в службах Service Fabric см. в статье [ASP.NET Core в Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="connecting-to-a-reliable-actor-service"></a>Подключение к службе Reliable Actors
В этом руководстве описывается добавление веб-интерфейса для обеспечения связи со службой с отслеживанием состояния. Однако сходную модель можно использовать для обмена данными с субъектами. При создании проекта Reliable Actor Visual Studio автоматически создает проект интерфейса. Этот интерфейс можно использовать для создания в веб-проекте прокси-сервера субъекта для взаимодействия с субъектом. Коммуникационный канал предоставляется автоматически. Поэтому вам не нужно предпринимать какие-либо действия по настройке `ServiceRemotingListener` , как в случае со службами с отслеживанием состояния.

## <a name="how-web-services-work-on-your-local-cluster"></a>Как работают веб-службы на локальном кластере
Развернутое в локальном кластере приложение Service Fabric, как правило, можно развернуть в кластере с несколькими компьютерами. Это приложение будет работать должным образом. Это объясняется тем, что локальный кластер представляет собой конфигурацию из пяти узлов, расположенную на одном компьютере.

Однако с веб-службами связана одна ключевая особенность. Если кластер не включен в подсистему балансировки нагрузки, как это предусмотрено в среде Azure, вам нужно убедиться, что веб-службы развернуты на каждом компьютере. Это необходимо, так как балансировщик нагрузки будет просто выполнять циклический перебор трафика между компьютерами. Это можно сделать, указав для параметра `InstanceCount` службы особое значение: "-1".

Напротив, при выполнении веб-службы локально необходимо убедиться, что запущен только один экземпляр службы. В противном случае возникнут конфликты с несколькими процессами, которые прослушивают тот же путь и тот же порт. Поэтому для локальных развертываний счетчику экземпляров веб-службы нужно присвоить значение 1.

Дополнительные сведения о настройке разных значений для разных сред см. в статье [Управление параметрами приложения для нескольких сред](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы настроили веб-интерфейс для приложения с помощью ASP.NET Core, получите дополнительные сведения о работе ASP.NET Core с Service Fabric в статье о [ASP.NET Core в Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md).

Далее [узнайте больше о взаимодействии со службами](service-fabric-connect-and-communicate-with-services.md) в целом, чтобы понять, как взаимодействуют службы в Service Fabric.

Разобравшись в этом, [создайте кластер в Azure и разверните приложение в облаке](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
