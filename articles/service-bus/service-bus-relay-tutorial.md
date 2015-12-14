<properties 
   pageTitle="Учебник по обмену сообщений с ретрансляцией через служебную шину | Microsoft Azure"
   description="Построение приложения службы и клиентского приложения служебной шины и обмен сообщениями с ретрансляцией через служебную шину."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/11/2015"
   ms.author="sethm" />

# Учебник по обмену сообщениями с ретрансляцией через служебную шину

В этом учебнике рассматривается создание клиентского приложения и службы служебной шины с использованием возможностей обмена сообщениями с ретрансляцией. Учебник, в котором описывается создание приложения, использующего возможности обмена сообщениями через посредника, см. в статье [Руководство по обмену сообщениями через посредника в служебной шине для .NET](https://msdn.microsoft.com/library/hh367512.aspx). Аналогичный учебник по [обмену сообщениями через посредника](service-bus-messaging-overview.md/#Brokered-messaging) в служебной шине см. в статье [Руководство по обмену сообщениями через посредника в служебной шине для .NET](https://msdn.microsoft.com/library/hh367512.aspx).

Выполнив задания в этом учебнике, вы получите представление о том, как создать клиентское приложение и службу служебной шины. Как и в WCF, под службой понимается конструкция, которая предоставляет одну или несколько конечных точек, каждая из которых предоставляет одну или несколько операций службы. Конечная точка службы задает адрес, по которому можно найти службу, привязку, содержащую сведения, которые клиент должен передавать службе, а также контракт, определяющий функциональность, предоставляемую службой клиентам. Основное отличие между службами WCF и службами служебной шины состоит в том, что в последних конечная точка размещается в облаке, а не на локальном компьютере.

В рамках этого учебника вы создадите рабочую службу и клиент, который сможет вызывать операции службы. В первом разделе рассматривается настройка учетной записи. В последующих разделах описывается определение службы, использующей контракт, способы реализации службы и настройка службы с помощью кода. В них также описывается размещение и запуск службы. Создаваемая служба является резидентной, то есть и клиент, и служба выполняются на одном компьютере. Вы можете настроить службу с помощью кода или файла конфигурации. Дополнительные сведения см. в статьях [Настройка службы WCF для регистрации в служебной шине](https://msdn.microsoft.com/library/ee173579.aspx) и [Создание службы для служебной шины](https://msdn.microsoft.com/library/ee173564.aspx).

В заключительных трех действиях описывается создание клиентского приложения, его настройка, а также создание и запуск клиента с доступом к функциональности хоста. Дополнительные сведения см. в статье [Создание клиентского приложения служебной шины](https://msdn.microsoft.com/library/ee173543.aspx) и [Обнаружение и предоставление службы служебной шины](https://msdn.microsoft.com/library/dd582704.aspx).

Во всех разделах данного учебника в качестве среды разработки используется Visual Studio.

## Регистрация учетной записи

Прежде всего необходимо создать пространство имен службы служебной шины и получить подписанный URL-адрес (SAS). Пространство имен службы определяет границы каждого приложения, предоставляемого через служебную шину. Пространство имен и ключ SAS — это учетные данные, на основе которых служба служебной шины осуществляет проверку подлинности и предоставляет доступ к приложению.

Чтобы создать пространство имен, выполните действия, описанные в разделе [Практическое руководство. Создание или изменение пространства имен службы служебной шины](https://msdn.microsoft.com/library/hh690931.aspx).

>[AZURE.NOTE]Для клиентского приложения и службы можно использовать разные пространства имен.

1. В главном окне [классического портала Azure][] щелкните имя пространства имен службы, созданного на предыдущем шаге.

2. Нажмите кнопку **Настроить**, чтобы просмотреть стандартные политики общего доступа для пространства имен службы.

3. Запишите значение первичного ключа для политики **RootManageSharedAccessKey** или скопируйте его в буфер обмена. Этот ключ потребуется позже.

## Определение контракта службы WCF, используемого в служебной шине

Контракт службы указывает, какие операции поддерживает служба (в контексте веб-служб под операциями понимаются методы и функции). Контракты создаются путем определения интерфейса C++, C# или Visual Basic. Каждый метод в интерфейсе соответствует определенной операции службы. К каждому интерфейсу должен быть применен атрибут [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx), а к каждой операции — атрибут [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Если у метода в интерфейсе с атрибутом [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) нет атрибута [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), то он не предоставляется. Код для выполнения задач приведен в примерах после описания последовательности действий. Дополнительные сведения об определении контракта см. в разделе [Разработка контракта WCF для служебной шины](https://msdn.microsoft.com/library/ee173585.aspx). Подробное обсуждение контрактов и служб см. в статье [Разработка и реализация служб](https://msdn.microsoft.com/library/ms729746.aspx) в документации по WCF.

### Создание контракта служебной шины с помощью интерфейса

1. Откройте Visual Studio с правами администратора, щелкнув правой кнопкой мыши имя программы в меню **Пуск** и выбрав пункт **Запуск от имени администратора**.

1. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем **Проект**. В диалоговом окне **Новый проект** нажмите кнопку **Visual C#** (если **Visual C#** не отображается, откройте список **Другие языки**). Щелкните шаблон **Консольное приложение** и назовите его **EchoService**. Используйте **расположение** по умолчанию. Нажмите кнопку **ОК**, чтобы создать проект.

1. Добавьте в проект ссылку на `System.ServiceModel.dll`. Для этого перейдите в папку проекта в обозревателе решений, щелкните правой кнопкой мыши папку **Ссылки** и выберите команду **Добавить ссылку**. В диалоговом окне **Добавление ссылки** перейдите на вкладку **.NET** и найдите элемент **System.ServiceModel**. Выберите его, а затем нажмите кнопку **ОК**.

1. В обозревателе решений дважды щелкните файл Program.cs, чтобы открыть его в редакторе.

1. Добавьте инструкцию `using` для пространства имен System.ServiceModel.

	```
	using System.ServiceModel;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) — это пространство имен, которое предоставляет программный доступ к основным функциям WCF. Служебная шина использует множество объектов и атрибутов WCF для определения контрактов службы.

1. Замените имя пространства имен по умолчанию (**EchoService**) именем **Microsoft.ServiceBus.Samples**.

	>[AZURE.IMPORTANT]В этом учебнике используется пространство имен **Microsoft.ServiceBus.Samples** (C#). Оно относится к управляемому контрактом типу и используется в файле конфигурации на шаге 6 «Настройка клиента WCF». Для компиляции примера можно указать любое пространство имен. Однако при этом потребуется соответствующим образом изменить пространства имен контракта и службы в файле конфигурации приложения. В файле App.config должно быть указано то же самое пространство имен, что и в файлах C#.

1. Сразу после объявления пространства имен `Microsoft.ServiceBus.Samples`, но внутри этого пространства, определите новый интерфейс с именем `IEchoContract` и примените к нему атрибут `ServiceContractAttribute` со значением ****http://samples.microsoft.com/ServiceModel/Relay/**. Значение пространства имен отличается от пространства имен, которое используется во всей области кода. Оно используется в качестве уникального идентификатора данного контракта. Явное указание пространства позволяет предотвратить добавление значение пространства имен по умолчанию к имени контракта.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	}
	```

	>[AZURE.NOTE]Как правило, пространство имен контракта службы содержит схему именования, которая включает сведения о версии. Включение сведений о версии в пространство имен контракта службы позволяет службам выносить значительные изменения в новый контракт службы с новым пространством имен и предоставлять к нему доступ через новую конечную точку. Таким образом, клиенты могут продолжать использовать старый контракт без обновлений. Сведения о версии могут включать дату и номер сборки. Дополнительные сведения см. в статье [Управление версиями службы](http://go.microsoft.com/fwlink/?LinkID=180498). Схема именования пространства имен контракта службы, используемая в этом учебнике, не включает сведения о версии.

1. Объявите в интерфейсе IEchoContract метод для единственной операции, которую предоставляет контракт `IEchoContract` в этом интерфейсе, и примените атрибут `OperationContractAttribute` к методу, который вы хотите предоставить как часть открытого контракта служебной шины.

	```
	[OperationContract]
	string Echo(string text);
	```

1. Вне контракта объявите канал между интерфейсами `IEchoChannel` и `IClientChannel`, как показано ниже:

	```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    publicinterface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

	Канал представляет собой объект WCF, посредством которого служба и клиент обмениваются информацией. Далее мы напишем код канала для обмена данными между двумя приложениями.

1. В меню **Сборка** выберите команду **Собрать решение** или нажмите клавишу F6, чтобы проверить правильность выполнения действий.

### Пример

Ниже приведен код базового интерфейса, определяющего контракт служебной шины.

```
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

## Реализация контракта WCF для использования служебной шины

Прежде чем создавать службу служебной шины, необходимо создать контракт, который определяется с помощью интерфейса. Сведения о создании интерфейса см. в описании предыдущего шага. Следующим шагом является реализация интерфейса. Она предполагает создание класса с именем `EchoService`, который реализует пользовательский интерфейс `IEchoContract`. После реализации интерфейса необходимо настроить его с помощью файла конфигурации App.config. Файл конфигурации содержит сведения, необходимые для приложения, такие как имя службы, имя контракта и тип протокола, используемого для взаимодействия со служебной шиной. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий. Общие сведения о реализации контракта службы см. в статье [Реализация контрактов служб](https://msdn.microsoft.com/library/ms733764.aspx) в документации по Windows Communication Foundation (WCF).

1. Создайте новый класс с именем `EchoService` сразу после определения интерфейса `IEchoContract`. Класс `EchoService` реализует интерфейс `IEchoContract`. 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	Как и в случае с другими реализациями интерфейсов, определение можно реализовать в другом файле. В этом учебнике реализация включается в тот же файл, что и определение интерфейса и метод `Main`.

1. Примените атрибут [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx), указывающий имя и пространство имен службы.

	```[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. Реализуйте метод `Echo`, определенный в интерфейсе `IEchoContract`, в классе `EchoService`.

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. В меню **Сборка** выберите команду **Собрать решение**, чтобы проверить правильность выполнения действий.

### Определение конфигурации хоста службы

1. Файл конфигурации аналогичен файлу конфигурации WCF. Он включает имя службы, конечную точку (то есть расположение, которое служебная шина предоставляет клиентам и хостам для взаимодействия) и привязку (тип протокола, используемый для обмена данными). Основное отличие заключается в том, что настроенная конечная точка службы ссылается на привязку [netTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx), которая не является частью платформы .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) — это одна из привязок, определяемых служебной шиной.

1. В **обозревателе решений** щелкните файл App.config. На данный момент он содержит следующие XML-элементы:

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	</configuration>
	```

1. Добавьте XML-элемент `<system.serviceModel>` в файл App.config. Это элемент WCF, который определяет одну или несколько служб. В этом примере он используется для определения имени службы и конечной точки.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
  
	  </system.serviceModel>
	</configuration>
	```

1. Добавьте элемент `<services>` внутри тегов `<system.serviceModel>`. В одном файле конфигурации можно определить несколько приложений служебной шины. Однако в данном учебнике определяется только одно приложение.
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. Добавьте элемент `<service>` в элемент `<services>` для определения имени службы.

	```
	<servicename="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. Внутри элемента `<service>` определите расположение контракта конечной точки, а также тип привязки для нее.

	```
	<endpointcontract="Microsoft.ServiceBus.Samples.IEchoContract"binding="netTcpRelayBinding"/>
	```

	Конечная точка определяет расположение, в котором клиент будет искать хост-приложение. Результаты этого шага потребуются позднее для создания универсального кода ресурса (URI), который предоставляет полный доступ к хосту через служебную шину. Привязка объявляет, что для обмена данными со служебной шиной используется протокол TCP.


1. Добавьте сразу после элемента `<services>` следующее расширение привязки:
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. В меню **Сборка** выберите команду **Собрать решение**, чтобы проверить правильность выполнения действий.

### Пример

Следующий код показывает реализацию контракта службы.

```
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

```
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
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Размещение и запуск базовой веб-службы для регистрации в служебной шине

Этот шаг описывает запуск базовой службы служебной шины.

### Создание учетных данных служебной шины

1. Добавьте в проект ссылку на файл Microsoft.ServiceBus.dll (см. статью [Использование пакета NuGet для служебной шины](https://msdn.microsoft.com/library/dn741354.aspx)).

	>[AZURE.NOTE]При использовании компилятора командной строки необходимо указывать путь к сборкам.

1. Добавьте в файл Program.cs инструкцию `using` для пространства имен Microsoft.ServiceBus.

	```
	using Microsoft.ServiceBus;
	```

1. Создайте в `Main()` две переменные для хранения пространства имен и ключа SAS, которые считываются из окна консоли.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	Далее ключ SAS потребуется для доступа к проекту служебной шины. Пространство имен службы передается в `CreateServiceUri` в качестве параметра для создания универсального кода ресурса (URI) службы.

4. Используя объект [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx), объявите, что в качестве учетных данных будет использоваться ключ SAS. Добавьте следующий код сразу после кода, добавленного на предыдущем шаге:

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### Создание базового адреса для службы

1. Создайте экземпляр `Uri` базового адреса службы после кода, добавленного на предыдущем шаге. Этот универсальный код ресурса (URI) указывает схему, пространство имен и путь к интерфейсу службы служебной шины.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	SB — это сокращение, используемое для схемы служебной шины; оно указывает, что используется протокол TCP. Этот протокол также задан в файле конфигурации с помощью привязки [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx).
	
	В этом учебнике используется универсальный код ресурса (URI) `sb://putServiceNamespaceHere.windows.net/EchoService`.

### Создание и настройка хоста службы

1. Установите режим подключения `AutoDetect`.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	Режим подключения описывает протокол, используемый службой для связи со служебной шиной (HTTP или TCP). Если используется значение по умолчанию (`AutoDetect`), служба пытается подключиться к служебной шине по протоколу TCP, если он доступен, и по протоколу HTTP, если TCP недоступен. Обратите внимание, что этот параметр не влияет на протокол, который задается в службе для обмена данными с клиентами. Этот протокол определяется используемой привязкой. Например, служба может использовать привязку [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), которая указывает, что ее конечная точка (предоставляется служебной шиной) связывается с клиентами по протоколу HTTP. Для той же службы можно задать привязку **ConnectivityMode.AutoDetect**, чтобы обмен данными со служебной шиной осуществлялся по протоколу TCP.

1. Создайте хост службы с помощью универсального кода ресурса (URI), созданного на предыдущем шаге.

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	Хост службы представляет собой объект WCF, который создает экземпляры службы. На этом шаге нужно передать тип службы, который требуется создать (тип `EchoService`), и адрес, по которому служба должна быть доступна.

1. В начале файла Program.cs добавьте ссылки на [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) и [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. Включите в `Main()` общий доступ к конечной точке.

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	Этот шаг информирует служебную шину о том, что ваше приложение может быть обнаружено в результате изучения канала ATOM служебной шины для вашего проекта. Если для **DiscoveryType** задано значение **private**, клиент все равно может получить доступ к службе. Однако она не будет отображаться в результатах поиска по пространству имен служебной шины. Клиенту потребуется знать путь к конечной точке.

1. Примените учетные данные службы к конечным точкам службы, определенным в файле App.config.

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	Как говорилось на предыдущем шаге, в файле конфигурации можно определить несколько служб и конечных точек. Если вы это сделали, код пройдет по всему файлу конфигурации и найдет все конечные точки, к которым следует применить учетные данные. В этом учебнике в файле конфигурации задана только одна конечная точка.

### Открытие службы

1. Откройте службу.

	```
	host.Open();
	```

1. Сообщите пользователю о том, что служба работает, и объясните, как ее отключить.

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. После завершения закройте узел службы.

	```
	host.Close();
	```

1. Нажмите клавишу F6, чтобы скомпилировать проект.

### Пример

В следующем примере показаны контракт и реализация службы, созданные на предыдущих шагах, а также размещение службы в консольном приложении. Скомпилируйте следующий код в исполняемый файл с именем EchoService.exe.

```
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

            // Add the Service Bus credentials to all endpoints specified in configuration.
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

## Создание клиента WCF для контракта службы

На этом шаге вы создадите базовое клиентское приложение служебной шины и определите контракт службы, которые будут реализованы на следующих шагах. Обратите внимание, что многие шаги повторяют действия по созданию службы: определение контракта, изменение файла App.config, использование учетных данных для подключения к служебной шине и т. д. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

1. Создайте в текущем решении Visual Studio новый проект для клиента. Для этого выполните следующие действия.
	1. В обозревателе решений щелкните правой кнопкой мыши решение (не проект), которое содержит службу, и нажмите кнопку **Добавить**. Щелкните **Создать проект**.
	2. В диалоговом окне **Добавление нового проекта** выберите **Visual C#** (если **Visual C#** не отображается, откройте список **Другие языки**). Выберите шаблон **Консольное приложение** и назовите его **EchoClient**.
	3. Нажмите кнопку **ОК**. <br />

1. В обозревателе решений дважды щелкните файл Program.cs в проекте **EchoClient**, чтобы открыть его в редакторе.

1. Измените имя пространства имен с `EchoClient` (имя по умолчанию) на `Microsoft.ServiceBus.Samples`.

1. Добавьте в проект ссылку на файл System.ServiceModel.dll:
	1. В обозревателе решений щелкните правой кнопкой мыши пункт **Ссылки** в проекте **EchoClient**. Нажмите кнопку **Добавить ссылку**.
	2. Поскольку вы уже добавили ссылку на эту сборку на первом шаге этого учебника, теперь она находится на вкладке **Последние**. Щелкните **Последние**, а затем выберите пункт **System.ServiceModel.dll** в списке. Нажмите кнопку **ОК**. Если **System.ServiceModel.dll** не отображается на вкладке **Последние**, щелкните вкладку **Обзор** и перейдите к папке **C:\\Windows\\Microsoft.NET\\Framework\\v3.0\\Windows Communication Foundation**. Выберите сборку в этой папке. <br />

1. Добавьте в файл Program.cs инструкцию `using` для пространства имен [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx).

	```
	using System.ServiceModel;
	```

1. Повторите предыдущие шаги, чтобы добавить в проект ссылку на файл Microsoft.ServiceBus.dll и пространство имен [Microsoft.ServiceBus](https://msdn.microsoft.com/library/microsoft.servicebus.aspx).

1. Добавьте определение контракта службы в пространство имен, как показано в следующем примере. Обратите внимание, что это определение идентично определению в проекте **Service**. Этот код следует добавить в верхнюю часть пространства имен `Microsoft.ServiceBus.Samples`.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	publicinterface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	publicinterface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. Нажмите клавишу F6, чтобы скомпилировать клиент.

### Пример

В следующем коде показано текущее состояние файла Program.cs в проекте EchoClient.

```
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

## Настройка клиента WCF

На этом шаге вы создадите файл конфигурации App.config для базового клиентского приложения, которое обращается к службе, созданной ранее в этом учебнике. Файл App.config определяет контракт, привязку и имя конечной точки. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

1. В обозревателе решений выберите проект клиента и дважды щелкните файл **App.config**, чтобы открыть его. На данный момент файл содержит следующие XML-элементы:

	```
	<?xmlversion="1.0"?>
	<configuration>
	  <startup>
	    <supportedRuntimeversion="v4.0"sku=".NETFramework,Version=v4.0"/>
	  </startup>
	</configuration>
	```

1. Добавьте в файл App.config XML-элемент для `system.serviceModel`.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	
	  </system.serviceModel>
	</configuration>
	```
	
	Этот элемент объявляет, что приложение использует конечные точки WCF. Как уже отмечалось, большая часть настройки приложения служебной шины совпадает с настройкой приложения WCF. Основное отличие заключается в расположении, на которое указывает файл конфигурации.

1. Добавьте элемент `<client>` в элемент system.serviceModel.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	На этом шаге вы объявляете, что определяете клиентское приложение WCF.

1. Определите имя, контракт и тип привязки для конечной точки в элементе `client`.

	```
	<endpointname="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	Это действие определяет имя конечной точки, контракт, определенный в службе, а также использование клиентским приложением протокола TCP для взаимодействия со служебной шиной. На следующем шаге имя конечной точки используется для связывания ее конфигурации с универсальным кодом ресурса (URI) службы.

1. Добавьте следующее расширение привязки сразу после элемента <client>.
 
	```
	<extensions>
	  <bindingExtensions>
	    <addname="netTcpRelayBinding"type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
	  </bindingExtensions>
	</extensions>
	```

1. В меню **Файл** выберите команду **Сохранить все**.

## Пример

В следующем коде показан файл App.config для клиента Echo.

```
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
        <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Реализация вызова от клиента WCF к служебной шине

На этом шаге вы реализуете базовое клиентское приложение, которое обращается к службе, которую вы создали ранее в этом учебнике. Для доступа к служебной шине клиент выполняет многие операции, аналогичные операциям, выполняемым службой:

1. Задает режим подключения.

1. Создает универсальный код ресурса (URI) расположения хоста службы.

1. Определяет учетные данные для безопасного доступа.

1. Применяет учетные данные к подключению.

1. Открывает подключение.

1. Выполняет специфические задачи приложения.

1. Закрывает подключение.

Однако одно из основных различий заключается в том, что клиентское приложение использует для подключения к служебной шине канал, а служба —вызов **ServiceHost**. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

### Реализация клиентского приложения

1. Установите режим подключения **AutoDetect**. Добавьте следующий код в метод `Main()` клиентского приложения.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. Определите переменные для хранения значений пространства имен службы и ключа SAS, которые считываются из консоли.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. Создайте универсальный код ресурса (URI), определяющий расположение хоста в проекте служебной шины.

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. Создайте объект учетных данных для конечной точки пространства имен службы.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. Создайте фабрику каналов, загружающую конфигурацию, описанную в файле App.config.

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	Фабрика каналов — это объект WCF, который создает канал взаимодействия между службой и клиентскими приложениями.

1. Примените учетные данные служебной шины.

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. Создайте и откройте канал к службе.

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. Напишите простой пользовательский интерфейс и функциональность для Echo.

	```
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

1. Закройте канал и фабрику.

	```
	channel.Close();
	channelFactory.Close();
	```

## Запуск клиентского приложения

1. Нажмите клавишу F6, чтобы скомпилировать решение. Будет создана сборка проектов клиента и службы, созданных в предыдущих разделах этого учебника. Для обоих будут созданы исполняемые файлы.

1. Прежде чем запустить клиентское приложение, убедитесь, что приложение службы работает.

	Созданный исполняемый файл для приложения службы Echo с именем EchoService.exe будет располагаться в папке проекта службы \\bin\\Debug\\EchoService.exe (для отладки) или \\bin\\Release\\EchoService.exe (для выпуска). Дважды щелкните файл, чтобы запустить приложение службы.

1. Откроется окно консоли с запросом на ввод пространства имен. Укажите пространство имен службы и нажмите клавишу ВВОД.

1. Далее появится запрос на ввод ключа SAS. Введите ключ SAS и нажмите клавишу ВВОД.

	В окне консоли вы увидите примерно следующее. Обратите внимание, что здесь приведены лишь примеры возможных значений.

	`Your Service Namespace: myNamespace`

	`Your SAS Key: <SAS key value>`

	Приложение службы запускается, и в окне консоли отображается адрес, по которому оно выполняет прослушивание (см. следующий пример).

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`
    
1. Запустите клиентское приложение. Созданный исполняемый файл для клиентского приложения Echo с именем EchoClient.exe будет располагаться в папке проекта клиента \\bin\\Debug\\EchoClient.exe (для отладки) или \\bin\\Release\\EchoClient.exe (для выпуска). Дважды щелкните файл, чтобы запустить клиентское приложение.

1. Откроется окно консоли с запросом на ввод тех же данных, что были введены ранее для приложения службы. Следуйте приведенным инструкциям, чтобы ввести значения пространства имен службы, имени издателя и секрета издателя.

1. После ввода значений клиент открывает канал к службе и предлагает ввести некоторый текст, как показано в следующем примере.

	`Enter text to echo (or [Enter] to exit):`

	Введите текст для отправки в приложение службы и нажмите клавишу ВВОД. Этот текст отправляется службе посредством операции службы Echo и отображается в окне консоли службы, как показано ниже.

	`Echoing: My sample text`

	Результат операции `Echo` (исходный текст) возвращается в клиентское приложение и отображается в окне консоли. Вы увидите примерно следующее.

	`Server echoed: My sample text`

1. Вы можете отправить еще несколько текстовых сообщений от клиента к службе. Закончив, нажмите клавишу ВВОД в окнах консоли клиента и службы, чтобы завершить работу приложений.

## Пример

В следующем примере показано, как создать клиентское приложение, как вызывать операции службы и как закрыть клиент после завершения вызова операции.

```
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

Прежде чем запускать клиент, убедитесь, что запущена служба.

## Дальнейшие действия

В этом учебнике мы рассмотрели создание приложения клиента и службы служебной шины с использованием обмена сообщениями с ретрансляцией через служебную шину. Аналогичный учебник по [обмену сообщениями через брокер](service-bus-messaging-overview.md/#Brokered-messaging) в служебной шине см. в статье [Руководство по обмену сообщениями через брокер в служебной шине для .NET](https://msdn.microsoft.com/library/hh367512.aspx).

Дополнительную информацию о служебной шине см. в следующих статьях.

- [Основные сведения об обмене сообщениями через служебную шину](service-bus-messaging-overview.md)
- [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
- [Архитектура служебной шины](service-bus-architecture.md)

[классического портала Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->