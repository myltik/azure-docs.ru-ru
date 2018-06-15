---
title: Руководство по использованию ретранслятора WCF служебной шины Azure | Документация Майкрософт
description: Создание клиентского приложения и приложения службы с помощью ретранслятора WCF.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 53dfd236-97f1-4778-b376-be91aa14b842
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: sethm
ms.openlocfilehash: 82e26571c88460436e6ca5ee70323cd680c82bdc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642314"
---
# <a name="azure-wcf-relay-tutorial"></a>Руководство по ретранслятору WCF Azure

В этом руководстве описывается создание простого клиентского приложения ретранслятора WCF и службы ретрансляции WCF с помощью ретранслятора Azure. Аналогичное руководство по [обмену сообщениями в служебной шине](../service-bus-messaging/service-bus-messaging-overview.md) представлено в статье [Начало работы с очередями служебной шины](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Выполнив задания в этом руководстве, вы получите представление о том, как создать клиентское приложение ретранслятора WCF и службу ретрансляции WCF. Как и в WCF, под службой понимается конструкция, которая предоставляет одну или несколько конечных точек, каждая из которых предоставляет одну или несколько операций службы. Конечная точка службы задает адрес, по которому можно найти службу, привязку, содержащую сведения, которые клиент должен передавать службе, а также контракт, определяющий функциональность, предоставляемую службой клиентам. Основное отличие между WCF и ретранслятором WCF состоит в том, что в последнем конечная точка размещается в облаке, а не на локальном компьютере.

Проработав это руководство, вы создадите рабочую службу и клиент, который сможет вызывать операции службы. В первом разделе мы рассмотрим процесс настройки учетной записи. В последующих разделах описывается определение службы, использующей контракт, способы реализации службы и настройка службы с помощью кода. В них также описывается размещение и запуск службы. Создаваемая служба является резидентной, то есть и клиент, и служба выполняются на одном компьютере. Вы можете настроить службу с помощью кода или файла конфигурации.

В заключительных трех действиях описывается создание клиентского приложения, его настройка, а также создание и запуск клиента с доступом к функциональности хоста.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником требуется:

* [Microsoft Visual Studio 2015 или более поздней версии](http://visualstudio.com). В этом учебнике используется Visual Studio 2017.
* Активная учетная запись Azure. Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/free/).

## <a name="create-a-service-namespace"></a>Создание пространства имен службы

Сначала необходимо создать пространство имен и получить ключ [подписанного URL-адреса (SAS)](../service-bus-messaging/service-bus-sas.md). Пространство имен определяет границы каждого приложения, предоставляемого через службу ретрансляции. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа SAS образует учетные данные, на основе которых Azure осуществляет аутентификацию доступа к приложению. Выполните [эти инструкции](relay-create-namespace-portal.md), чтобы создать пространство имен ретранслятора.

## <a name="define-a-wcf-service-contract"></a>Определение контракта службы WCF

Контракт службы указывает, какие операции поддерживает служба (в контексте веб-служб под операциями понимаются методы и функции). Контракты создаются путем определения интерфейса C++, C# или Visual Basic. Каждый метод в интерфейсе соответствует определенной операции службы. К каждому интерфейсу должен быть применен атрибут [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx), а к каждой операции— атрибут [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Если у метода в интерфейсе с атрибутом [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) нет атрибута [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), то он не предоставляется. Код для выполнения задач приведен в примерах после описания последовательности действий. Подробное обсуждение контрактов и служб см. в статье [Разработка и реализация служб](https://msdn.microsoft.com/library/ms729746.aspx) в документации по WCF.

### <a name="create-a-relay-contract-with-an-interface"></a>Создание контракта ретранслятора с помощью интерфейса

1. Откройте Visual Studio с правами администратора, щелкнув правой кнопкой мыши имя программы в меню **Пуск** и выбрав пункт **Запуск от имени администратора**.
2. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем — **Проект**. В диалоговом окне **Новый проект** нажмите кнопку **Visual C#** (если **Visual C#** не отображается, откройте список **Другие языки**). Выберите шаблон **Консольное приложение (.NET Framework)** и назовите его **EchoService**. Нажмите кнопку **ОК** , чтобы создать проект.

    ![][2]

3. Установка пакета NuGet для служебной шины. Этот пакет автоматически добавляет ссылки на библиотеки служебной шины, а также элемент WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) — это пространство имен, которое предоставляет программный доступ к основным функциям WCF. Служебная шина использует множество объектов и атрибутов WCF для определения контрактов службы.

    В обозревателе решений щелкните правой кнопкой мыши проект и выберите пункт **Управление пакетами NuGet**. Щелкните вкладку **Обзор**, а затем найдите **WindowsAzure.ServiceBus**. Убедитесь, что имя проекта указано в поле **Версии**. Щелкните **Установить**и примите условия использования.

    ![][3]
4. В обозревателе решений дважды щелкните файл Program.cs, чтобы открыть его в редакторе.
5. Добавьте в начало файла следующие операторы using:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```
6. Замените имя пространства имен по умолчанию (**EchoService**) именем **Microsoft.ServiceBus.Samples**.

   > [!IMPORTANT]
   > В этом руководстве используется пространство имен C# **Microsoft.ServiceBus.Samples**. Оно относится к управляемому типу на основе контракта и используется в файле конфигурации на этапе [настройки клиента WCF](#configure-the-wcf-client). Для компиляции примера можно указать любое пространство имен. Однако при этом потребуется соответствующим образом изменить пространства имен контракта и службы в файле конфигурации приложения. В файле App.config должно быть указано то же самое пространство имен, что и в файлах C#.
   >
   >
7. Сразу после объявления пространства имен `Microsoft.ServiceBus.Samples`, но внутри этого пространства, определите новый интерфейс `IEchoContract` и примените к нему атрибут `ServiceContractAttribute` со значением `http://samples.microsoft.com/ServiceModel/Relay/`. Значение пространства имен отличается от пространства имен, которое используется во всей области кода. Оно используется в качестве уникального идентификатора данного контракта. Явное указание пространства позволяет предотвратить добавление значение пространства имен по умолчанию к имени контракта. Вставьте приведенный ниже фрагмент кода после объявления пространства имен.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

   > [!NOTE]
   > Как правило, пространство имен контракта службы содержит схему именования, которая включает сведения о версии. Включение сведений о версии в пространство имен контракта службы позволяет службам выносить значительные изменения в новый контракт службы с новым пространством имен и предоставлять к нему доступ через новую конечную точку. Таким образом, клиенты могут продолжать использовать старый контракт службы без обновлений. Сведения о версии могут включать дату и номер сборки. Дополнительные сведения см. в статье [Управление версиями службы](http://go.microsoft.com/fwlink/?LinkID=180498). Схема именования пространства имен контракта службы, используемая в этом учебнике, не включает сведения о версии.
   >
   >
8. В интерфейсе `IEchoContract` объявите метод для отдельной операции, которую контракт `IEchoContract` предоставляет в интерфейсе. Затем примените атрибут `OperationContractAttribute` к методу, который требуется предоставить как часть общедоступного контракта ретранслятора WCF, следующим образом:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```
9. Сразу после определения интерфейса `IEchoContract` объявите канал, наследующий от интерфейсов `IEchoContract` и `IClientChannel`, как показано здесь:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Канал представляет собой объект WCF, посредством которого служба и клиент обмениваются информацией. Далее мы напишем код канала для обмена данными между двумя приложениями.
10. В меню **Сборка** выберите **Собрать решение** или нажмите сочетание клавиш **CTRL+SHIFT+B**, чтобы проверить правильность выполнения действий.

### <a name="example"></a>Пример

Ниже приведен код базового интерфейса, определяющего контракт ретранслятора WCF.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Теперь, когда интерфейс создан, можно его реализовать.

## <a name="implement-the-wcf-contract"></a>Реализация контракта WCF

Прежде чем создавать ретранслятор Azure, необходимо создать контракт, который определяется с помощью интерфейса. Сведения о создании интерфейса см. в описании предыдущего шага. Следующим шагом является реализация интерфейса. Она предполагает создание класса с именем `EchoService`, который реализует пользовательский интерфейс `IEchoContract`. После реализации интерфейса необходимо настроить его с помощью файла конфигурации App.config. Файл конфигурации содержит сведения, необходимые для приложения, такие как имя службы, имя контракта и тип протокола, используемого для взаимодействия с службой ретрансляции. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий. Общие сведения о реализации контракта службы см. в статье [Реализация контрактов служб](https://msdn.microsoft.com/library/ms733764.aspx) в документации WCF.

1. Создайте новый класс с именем `EchoService` сразу после определения интерфейса `IEchoContract`. Класс `EchoService` реализует интерфейс `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Как и в случае с другими реализациями интерфейсов, определение можно реализовать в другом файле. В этом учебнике реализация включается в тот же файл, что и определение интерфейса и метод `Main`.
2. Примените атрибут [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) к интерфейсу `IEchoContract`. Атрибут определяет имя службы и пространство имен. После этого класс `EchoService` будет выглядеть так:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```
3. Реализуйте метод `Echo`, определенный в интерфейсе `IEchoContract`, в классе `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```
4. В меню **Сборка** выберите команду **Собрать решение**, чтобы проверить правильность выполнения действий.

### <a name="define-the-configuration-for-the-service-host"></a>Определение конфигурации узла службы

1. Файл конфигурации аналогичен файлу конфигурации WCF. Он содержит имя службы, конечную точку (то есть расположение, которое ретранслятор Azure предоставляет клиентам и узлам для взаимодействия) и привязку (тип протокола, используемый для связи). Основное отличие заключается в том, что настроенная конечная точка службы ссылается на привязку [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding), которая не является частью платформы .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) — это одна из привязок, определяемых службой.
2. В **обозревателе решений** дважды щелкните файл App.config, чтобы открыть его в редакторе Visual Studio.
3. В элементе `<appSettings>` замените местозаполнители именем пространства имен службы и ключом SAS, скопированным на предыдущем шаге.
4. Добавьте элемент `<services>` внутри тегов `<system.serviceModel>`. В отдельном файле конфигурации можно определить несколько приложений ретранслятора. Однако в данном учебнике определяется только одно приложение.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```
5. Добавьте элемент `<service>` в элемент `<services>` для определения имени службы.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```
6. Внутри элемента `<service>` определите расположение контракта конечной точки, а также тип привязки для нее.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Конечная точка определяет расположение, в котором клиент будет искать хост-приложение. Результаты этого шага потребуются позднее для создания универсального кода ресурса (URI), который предоставляет полный доступ к узлу через ретранслятор Azure. Привязка объявляет, что для обмена данными со службой ретрансляции используется протокол TCP.
7. В меню **Сборка** выберите команду **Собрать решение**, чтобы проверить правильность выполнения действий.

### <a name="example"></a>Пример

Следующий код показывает реализацию контракта службы.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Следующий код показывает базовый формат файла App.config, связанного с хостом службы.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-the-relay-service"></a>Размещение и запуск базовой веб-службы для регистрации в службе ретрансляции

В этом разделе описывается запуск службы ретрансляции Azure.

### <a name="create-the-relay-credentials"></a>Создание учетных данных ретранслятора

1. Создайте в `Main()` две переменные для хранения пространства имен и ключа SAS, которые считываются из окна консоли.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Ключ SAS потребуется позже для доступа к проекту. Пространство имен передается в качестве параметра в `CreateServiceUri` для создания универсального кода ресурса (URI) службы.
2. Используя объект [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior), объявите, что в качестве учетных данных будет использоваться ключ SAS. Добавьте следующий код сразу после кода, добавленного на предыдущем шаге:

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Создание базового адреса для службы

Создайте экземпляр `Uri` для базового адреса службы после кода, добавленного на предыдущем шаге. Этот универсальный код ресурса (URI) указывает схему, пространство имен и путь к интерфейсу службы служебной шины.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

SB — это сокращение, используемое для схемы служебной шины; оно указывает, что используется протокол TCP. Этот протокол также задан в файле конфигурации с помощью привязки [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx).

В этом учебнике используется универсальный код ресурса (URI) `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Создание и настройка узла службы

1. Установите режим подключения `AutoDetect`.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Режим подключения описывает протокол, используемый службой для связи со службой ретрансляции (HTTP или TCP). Если используется значение по умолчанию (`AutoDetect`), то служба пытается подключиться к ретранслятору Azure по протоколу TCP, если он доступен, и по протоколу HTTP, если TCP недоступен. Обратите внимание, что этот параметр не влияет на протокол, который задается в службе для обмена данными с клиентами. Этот протокол определяется используемой привязкой. Например, служба может использовать привязку [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), которая указывает, что ее конечная точка связывается с клиентами по протоколу HTTP. Для той же службы можно задать привязку **ConnectivityMode.AutoDetect**, чтобы обмен данными с ретранслятором Azure осуществлялся по протоколу TCP.
2. Создайте хост службы с помощью универсального кода ресурса (URI), созданного на предыдущем шаге.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Хост службы представляет собой объект WCF, который создает экземпляры службы. На этом шаге нужно передать тип службы, который требуется создать (тип `EchoService`), и адрес, по которому служба должна быть доступна.
3. В начале файла Program.cs добавьте ссылки на [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) и [Microsoft.ServiceBus.Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```
4. Включите в `Main()` общий доступ к конечной точке.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Этот шаг информирует службу ретрансляции о том, что ваше приложение общедоступно и может быть обнаружено в результате изучения веб-канала ATOM для вашего проекта. Если для **DiscoveryType** задано значение **private**, клиент все равно может получить доступ к службе. Однако она не будет отображаться в результатах поиска в пространстве имен ретранслятора. Клиенту потребуется знать путь к конечной точке.
5. Примените учетные данные службы к конечным точкам службы, определенным в файле App.config.

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Как говорилось на предыдущем шаге, в файле конфигурации можно определить несколько служб и конечных точек. Если вы это сделали, код пройдет по всему файлу конфигурации и найдет все конечные точки, к которым следует применить учетные данные. В этом учебнике в файле конфигурации задана только одна конечная точка.

### <a name="open-the-service-host"></a>Открытие узла службы

1. Откройте службу.

    ```csharp
    host.Open();
    ```
2. Сообщите пользователю о том, что служба работает, и объясните, как ее отключить.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. После завершения закройте узел службы.

    ```csharp
    host.Close();
    ```
4. Скомпилируйте проект, нажав клавиши **CTRL+SHIFT+B**.

### <a name="example"></a>Пример

Готовый код службы должен выглядеть так, как показано ниже. Он включает в себя контракт и реализацию службы, созданные в предыдущих шагах учебника. Служба размещается в консольном приложении.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Создание клиента WCF для контракта службы

На этом шаге вы создадите клиентское приложение и определите контракт службы, который будет реализован на следующих шагах. Обратите внимание, что многие шаги повторяют действия по созданию службы: определение контракта, изменение файла App.config, использование учетных данных для подключения к службе ретрансляции и т. д. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

1. Создайте в текущем решении Visual Studio новый проект для клиента. Для этого выполните следующие действия.

   1. В обозревателе решений щелкните правой кнопкой мыши решение (не проект), которое содержит службу, и нажмите кнопку **Добавить**. Выберите **Создать проект**.
   2. В диалоговом окне **Добавление нового проекта** выберите **Visual C#** (если элемент **Visual C#** не отображается, откройте список **Другие языки**). Выберите шаблон **Консольное приложение (.NET Framework)** и назовите его **EchoClient**.
   3. Последовательно выберите **ОК**.
      <br />
2. В обозревателе решений дважды щелкните файл Program.cs в проекте **EchoClient**, чтобы открыть его в редакторе, если он еще не открыт.
3. Измените имя пространства имен с `EchoClient` (имя по умолчанию) на `Microsoft.ServiceBus.Samples`.
4. Установите [пакет NuGet для служебной шины](https://www.nuget.org/packages/WindowsAzure.ServiceBus). В обозревателе решений щелкните правой кнопкой мыши проект **EchoClient** и выберите **Управление пакетами NuGet**. Щелкните вкладку **Обзор** и выполните поиск `Microsoft Azure Service Bus`. Щелкните **Установить**и примите условия использования.

    ![][3]
5. Добавьте в файл Program.cs инструкцию `using` для пространства имен [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx).

    ```csharp
    using System.ServiceModel;
    ```
6. Добавьте определение контракта службы в пространство имен, как показано в следующем примере. Обратите внимание, что это определение идентично определению в проекте **Service**. Этот код следует добавить в верхнюю часть пространства имен `Microsoft.ServiceBus.Samples`.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```
7. Чтобы построить клиент, нажмите клавиши **CTRL+SHIFT+B**.

### <a name="example"></a>Пример

В следующем коде показано текущее состояние файла Program.cs в проекте **EchoClient**.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Настройка клиента WCF

На этом шаге вы создадите файл конфигурации App.config для базового клиентского приложения, которое обращается к службе, созданной ранее в этом учебнике. Файл App.config определяет контракт, привязку и имя конечной точки. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

1. В обозревателе решений в проекте **EchoClient** дважды щелкните **App.config**, чтобы открыть файл в редакторе Visual Studio.
2. В элементе `<appSettings>` замените местозаполнители именем пространства имен службы и ключом SAS, скопированным на предыдущем шаге.
3. Добавьте элемент `<client>` в элемент system.serviceModel.

    ```xml
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    На этом шаге вы объявляете, что определяете клиентское приложение WCF.
4. Определите имя, контракт и тип привязки для конечной точки в элементе `client`.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Это действие определяет имя конечной точки, контракт, определенный в службе, а также использование клиентским приложением протокола TCP для взаимодействия с ретранслятором Azure. На следующем шаге имя конечной точки используется для связывания ее конфигурации с универсальным кодом ресурса (URI) службы.
5. В меню **Файл** выберите **Сохранить все**.

## <a name="example"></a>Пример

В следующем коде показан файл App.config для клиента Echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Реализация клиента WCF
На этом шаге вы реализуете базовое клиентское приложение, которое обращается к службе, которую вы создали ранее в этом учебнике. Для доступа к ретранслятору Azure клиент выполняет многие операции, аналогичные операциям, выполняемым службой:

1. Задает режим подключения.
2. Создает универсальный код ресурса (URI) расположения хоста службы.
3. Определяет учетные данные для безопасного доступа.
4. Применяет учетные данные к подключению.
5. Открывает подключение.
6. Выполняет специфические задачи приложения.
7. Закрывает подключение.

Однако одно из основных различий заключается в том, что клиентское приложение использует для подключения к службе ретрансляции канал, а служба — вызов **ServiceHost**. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

### <a name="implement-a-client-application"></a>Реализация клиентского приложения
1. Установите режим подключения **AutoDetect**. Добавьте следующий код в метод `Main()` приложения **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```
2. Определите переменные для хранения значений пространства имен службы и ключа SAS, которые считываются из консоли.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```
3. Создайте универсальный код ресурса (URI), определяющий расположение узла в проекте ретранслятора.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```
4. Создайте объект учетных данных для конечной точки пространства имен службы.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```
5. Создайте фабрику каналов, загружающую конфигурацию, описанную в файле App.config.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Фабрика каналов — это объект WCF, который создает канал взаимодействия между службой и клиентскими приложениями.
6. Примените учетные данные.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```
7. Создайте и откройте канал к службе.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```
8. Напишите простой пользовательский интерфейс и функциональность для Echo.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Обратите внимание, что в качестве прокси для службы в этом коде используется экземпляр объекта канала.
9. Закройте канал и фабрику.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

## <a name="example"></a>Пример

Готовый код должен выглядеть так, как показано ниже. В нем создается клиентское приложение, вызываются операции службы и клиент закрывается после завершения вызова операции.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Запуск приложений

1. Чтобы построить решение, нажмите клавиши **CTRL+SHIFT+B**. Будут скомпилированы клиентский проект и проект службы, созданный на предыдущих этапах.
2. Прежде чем запустить клиентское приложение, убедитесь, что приложение службы работает. В Visual Studio в обозревателе решений щелкните правой кнопкой мыши решение **EchoService** и выберите пункт **Свойства**.
3. В диалоговом окне свойств решения выберите **Запускаемый проект** и нажмите кнопку **Несколько запускаемых проектов**. Убедитесь, что проект **EchoService** отображается первым в списке.
4. В поле **Действие** для проектов **EchoService** и **EchoClient** выберите значение **Запуск**.

    ![][5]
5. Щелкните **Зависимости проектов**. В поле **Проекты** выберите **EchoClient**. Убедитесь, что в поле **Зависит от** указано значение **EchoService**.

    ![][6]
6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства**.
7. Нажмите клавишу **F5**, чтобы запустить оба проекта.
8. Откроются два окна консоли с предложением указать пространство имен. Сначала нужно запустить службу, поэтому в окне консоли **EchoService** введите пространство имен и нажмите клавишу **ВВОД**.
9. Далее появится запрос на ввод ключа SAS. Введите ключ SAS и нажмите клавишу ВВОД.

    В окне консоли вы увидите примерно следующее. Обратите внимание, что здесь приведены лишь примеры возможных значений.

    `Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

    Приложение службы выведет в окне консоли адрес, который оно прослушивает (см. следующий пример).

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
10. В окне консоли **EchoClient** введите данные, которые использовались для приложения службы. Выполните приведенные выше инструкции, чтобы ввести те же значения пространства имен службы и ключа SAS для клиентского приложения.
11. После ввода значений клиент открывает канал к службе и предлагает ввести некоторый текст, как показано в следующем примере.

    `Enter text to echo (or [Enter] to exit):`

    Введите текст для отправки в приложение службы и нажмите клавишу ВВОД. Этот текст отправляется службе посредством операции службы Echo и отображается в окне консоли службы, как показано ниже.

    `Echoing: My sample text`

    Результат операции `Echo` (исходный текст) возвращается в клиентское приложение и отображается в окне консоли. Вы увидите примерно следующее.

    `Server echoed: My sample text`
12. Вы можете отправить еще несколько текстовых сообщений от клиента к службе. Закончив, нажмите клавишу ВВОД в окнах консоли клиента и службы, чтобы завершить работу приложений.

## <a name="next-steps"></a>Дополнительная информация

В этом учебнике мы рассмотрели создание клиентского приложения ретранслятора Azure и службы ретрансляции Azure с использованием возможностей ретранслятора WCF служебной шины. Аналогичное руководство по [обмену сообщениями в служебной шине](../service-bus-messaging/service-bus-messaging-overview.md) представлено в статье [Начало работы с очередями служебной шины](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Дополнительные сведения о ретрансляторе Azure см. в следующих разделах:

* [Обзор архитектуры служебной шины Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Как использовать ретранслятор WCF служебной шины с .NET](relay-wcf-dotnet-get-started.md)

[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
