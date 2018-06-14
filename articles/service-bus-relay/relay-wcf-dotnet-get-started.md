---
title: Приступая к работе с ретрансляторами WCF на основе ретранслятора Azure в .NET | Документация Майкрософт
description: Узнайте, как использовать ретрансляторы WCF на основе ретранслятора Azure для подключения двух приложений, размещенных в разных расположениях.
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 5493281a-c2e5-49f2-87ee-9d3ffb782c75
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: face684190456fbf4b78a84ac3afe7a4ead8995a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "26856088"
---
# <a name="how-to-use-azure-relay-wcf-relays-with-net"></a>Как использовать ретрансляторы WCF на основе ретранслятора Azure с помощью .NET
В этой статье описываются методы использования службы ретрансляции Azure. Примеры написаны на языке C# и используют API службы Windows Communication Foundation (WCF) с расширениями, содержащимися в сборке служебной шины. Дополнительные сведения о ретрансляторе Azure см. в статье [Что такое ретранслятор Azure?](relay-what-is-it.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-wcf-relay"></a>Что такое ретранслятор WCF?

Служба [*ретранслятора WCF*](relay-what-is-it.md) Azure позволяет вам создавать гибридные приложения, которые можно запускать как в центре обработки данных Azure, так и в локальной корпоративной среде. Служба ретранслятора упрощает работу, позволяя безопасно предоставлять службы Windows Communication Foundation (WCF), используемые в корпоративной сети предприятия, в общедоступном облаке, без необходимости открывать подключения брандмауэра или вносить значительные изменения в инфраструктуру корпоративной сети.

![Обзор ретранслятора WCF](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Ретранслятор Azure позволяет размещать службы WCF в уже существующей среде предприятия. Затем можно делегировать прослушивание входящих сеансов и запросов этих служб WCF службе ретранслятора, запущенной в Azure. Таким образом, доступ к этим службам могут получать код приложения, работающего в Azure, мобильные сотрудники или среды партнерских экстрасетей. Ретрансляция позволяет безопасно управлять доступом к этим службам с высокой точностью. Это обеспечивает эффективный и безопасный способ предоставления функциональных возможностей приложения и данных из существующих корпоративных решений и использовать их из облака

В данной статье рассматривается использование ретранслятора Azure для создания веб-службы WCF, доступной с помощью привязки канала TCP, который обеспечивает безопасную передачу данных между двумя сторонами.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Получение пакета NuGet для служебной шины
[Пакет NuGet для служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus) — это самый простой способ получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины. Для установки пакета NuGet в проекте сделайте следующее:

1. В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2. Выполните поиск по фразе «служебная шина» и выберите элемент **Служебная шина Microsoft Azure** . Нажмите кнопку **Установить**, чтобы выполнить установку, а затем закройте следующее диалоговое окно.
   
   ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="expose-and-consume-a-soap-web-service-with-tcp"></a>Предоставление и использование веб-службы SOAP при использовании протокола TCP
Чтобы предоставить существующую веб-службу WCF SOAP для внешнего использования, необходимо внести изменения в привязки службы и адреса. Это может потребовать изменения файла конфигурации или кода, в зависимости от настроек и конфигурации ваших служб WCF. Обратите внимание, что WCF разрешает использование нескольких конечных точек сети в одной и той же службе. Так вы можете сохранить имеющиеся внутренние конечные точки, одновременно добавляя конечные точки ретрансляции для внешнего доступа.

В этой задаче вам нужно создать простую службу приложения WCF и добавить к ней прослушиватель ретранслятора. Это предполагает определенное знакомство с Visual Studio, поэтому рассматриваются не все подробности создания проекта. Вместо этого главная роль уделяется коду.

Прежде чем выполнить описанные ниже действия, сделайте следующее, чтобы настроить свою среду:

1. В Visual Studio создайте в рамках решения консольное приложение, содержащее два проекта, Client и Service.
2. Добавьте пакет служебной шины NuGet в оба проекта. Он добавит в проекты все необходимые ссылки на сборки.

### <a name="how-to-create-the-service"></a>Создание службы
Во-первых, создайте саму службу. Любая служба WCF состроит из трех или более различных частей.

* Определение контракта, который описывает передаваемые сообщения и вызываемые операции.
* Реализация этого контракта.
* Узел, на котором размещается служба WCF и который предоставляет несколько конечных точек.

Примеры кода в данном разделе обращаются к каждому из названных компонентов.

Контракт определяет одну операцию, `AddNumbers`, которая складывает два числа и возвращает результат. Интерфейс `IProblemSolverChannel` упрощает для клиента управление временем жизни прокси-сервера. Рекомендуется создать этот интерфейс. Очень удобно размещение определения контракта в отдельном файле, так что вы можете к нему обратиться как из проекта Client, так и Service, а также скопировать код в оба проекта.

```csharp
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

При наличии контракта реализация заключается в следующем:

```csharp
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Программная настройка узла службы
После создания контракта и его реализации можно разместить службу. Размещение происходит внутри объекта [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/system.servicemodel.servicehost.aspx), который отвечает за управление экземплярами службы и в котором размещаются конечные точки, прослушивающие сообщения. Приведенный ниже код настраивает для службы обычную локальную конечную точку и конечную точку ретранслятора для отображения внутренних и внешних конечных точек. Замените строку *namespace* именем своего пространства имен, а *yourKey* — ключом SAS, полученным на предыдущем шаге настройки.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

В этом примере вы создаете две конечных точки в рамках одного и того же исполнения контракта. Одна из них является локальной, а вторая проецируется с помощью ретранслятора Azure. Основным различием между ними являются привязки [NetTcpBinding](https://msdn.microsoft.com/library/system.servicemodel.nettcpbinding.aspx) для локальной и [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding#microsoft_servicebus_nettcprelaybinding) для конечной точки ретранслятора и адресов. Для локальной конечной точки используется локальный сетевой адрес с отдельным портом. Адрес конечной точки ретранслятора включает в себя строку `sb`, имя вашего пространства имен и путь solver. Результатом является универсальный код ресурса (URI) `sb://[serviceNamespace].servicebus.windows.net/solver`, определяющий конечную точку службы как конечную точку службы TCP служебной шины (ретранслятора) с полным внешним DNS-именем. Если в функции `Main` приложения **Service** вместо заполнителей использовать код, будет создана функциональная служба. Если нужно, чтобы ваша служба ожидала передачи данных только на ретрансляторе, удалите объявление конечной локальной точки.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Настройка узла службы в файле App.config
С помощью файла App.config также можно настроить узел. В этом случае код размещения службы выглядит так, как показано в следующем примере.

```csharp
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Определения конечных точек перемещаются в файл App.config. Пакет NuGet уже добавил в файл App.config ряд определений, являющихся необходимыми расширениями для настройки ретранслятора Azure. Следующий пример, который является точным эквивалентом кода, приведенного выше, должен появиться прямо под элементом **system.serviceModel**. В этом примере кода предполагается, что пространство имен C# вашего проекта называется **Service**.
Замените заполнители именем пространства имен ретранслятора и ключом SAS.

```xml
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://<namespaceName>.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

После этих изменений служба запускается как и раньше, но с двумя работающими конечными точками: одной локальной и одной прослушивающей в облаке.

### <a name="create-the-client"></a>Создание клиента
#### <a name="configure-a-client-programmatically"></a>Программная настройка клиента
Для использования службы вы можете создать клиент WCF, работающий с объектом [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Служебная шина использует модель безопасности на основе маркеров, реализованную с помощью SAS. Класс [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) представляет собой поставщика маркеров безопасности со встроенными методами генерации, которые возвращают ряд хорошо известных поставщиков маркеров. В приведенном ниже примере используется метод [CreateSharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) для обработки получения соответствующего маркера SAS. Значения имени и ключа наследуются с портала, как описано в предыдущем разделе.

Во-первых, создайте в проекте клиента ссылку или скопируйте в него код контракта `IProblemSolver` из службы.

Затем замените код в `Main` методе клиента, снова подставив пространство имен ретранслятора и ключ SAS вместо текста заполнителя.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "<namespaceName>", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Теперь можно создать клиент и службу, а затем запустить их (сначала запустите службу). После этого клиент вызовет службу и выведет **9**. Можно запустить клиент и сервер на разных компьютерах, даже в разных сетях, и связь по-прежнему будет работать. Код клиента также может работать в облаке или локально.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Настройка клиента в файле App.config
В примере кода ниже показано, как настроить клиент с помощью файла App.config.

```csharp
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Определения конечных точек перемещаются в файл App.config. Следующий пример, который полностью эквивалентен приведенному выше коду, должен находиться непосредственно под элементом `<system.serviceModel>`. Здесь снова необходимо заменить заполнители пространством имен ретранслятора и ключом SAS.

```xml
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://<namespaceName>.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Дополнительная информация
Вы ознакомились с базовыми понятиями о ретрансляторе Azure. Дополнительные сведения см. по следующим ссылкам:

* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Обзор архитектуры служебной шины Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* Скачайте примеры для служебной шины со страницы [Примеры Azure][Azure samples] или прочитайте [обзор примеров служебной шины][overview of Service Bus samples].

[Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
[overview of Service Bus samples]: ../service-bus-messaging/service-bus-samples.md
