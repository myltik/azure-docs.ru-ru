---
title: Руководство по REST с использованием ретранслятора Azure | Документация Майкрософт
description: Создание простого ведущего приложения ретранслятора служебной шины Azure, которое предоставляет интерфейс на основе REST.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: sethm
ms.openlocfilehash: 7a5a2916514a125d0b7443ced42e5ec600c68857
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
ms.locfileid: "24008042"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Руководство по REST для ретранслятора WCF Azure

В этом руководстве описано, как создать простое ведущее приложение ретранслятора Azure, предоставляющее интерфейс на основе REST. REST позволяет веб-клиенту, например веб-браузеру, получить доступ к интерфейсам API служебной шины с помощью HTTP-запросов.

В этом руководстве для создания службы REST в ретрансляторе Azure используется модель программирования REST Windows Communication Foundation (WCF). Дополнительные сведения см. в разделах [Модель программирования REST WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) и [Разработка и реализация служб](/dotnet/framework/wcf/designing-and-implementing-services) в документации по WCF.

## <a name="step-1-create-a-namespace"></a>Шаг 1. Создание пространства имен

Чтобы начать использовать функции ретранслятора Azure, необходимо сначала создать пространство имен службы. Пространство имен предоставляет контейнер для адресации ресурсов Azure в вашем приложении. Выполните [эти инструкции](relay-create-namespace-portal.md), чтобы создать пространство имен ретранслятора.

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Шаг 2. Определение контракта службы WCF на основе REST, используемого в ретрансляторе Azure

При создании службы WCF на основе REST необходимо определить контракт. Контракт определяет, какие операции поддерживает узел. Операцию службы можно рассматривать как метод веб-службы. Контракты создаются путем определения интерфейса C++, C# или Visual Basic. Каждый метод в интерфейсе соответствует определенной операции службы. К каждому интерфейсу должен быть применен атрибут [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute), а к каждой операции — атрибут [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Если у метода в интерфейсе с атрибутом [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) нет атрибута [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), такой метод не предоставляется. Код для выполнения этих задач показан в примере, приведенном после описания последовательности выполнения действий.

Основное различие между WCF и контрактом REST заключается в добавлении свойства к атрибуту [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Это свойство позволяет сопоставить метод в интерфейсе с методом на другой стороне интерфейса. В этом примере для связывания метода с HTTP GET используется атрибут [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Это позволяет служебной шине точно извлекать и интерпретировать команды, отправляемые в интерфейс.

### <a name="to-create-a-contract-with-an-interface"></a>Создание контракта с помощью интерфейса

1. Откройте Visual Studio с правами администратора, щелкнув программу правой кнопкой мыши в меню **Пуск** и выбрав **Запуск от имени администратора**.
2. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем — **Проект**. В диалоговом окне **Новый проект** щелкните **Visual C#**, выберите шаблон **Консольное приложение** и назовите его **ImageListener**. Используйте **расположение** по умолчанию. Нажмите кнопку **ОК** , чтобы создать проект.
3. Для проекта C# в Visual Studio создается файл с именем `Program.cs`. Этот класс содержит пустой метод `Main()`, необходимый для правильной сборки проекта консольного приложения.
4. Добавьте в проект ссылки на служебную шину и файл **System.ServiceModel.dll**. Для этого установите пакет NuGet для служебной шины. Этот пакет автоматически добавляет ссылки на библиотеки служебной шины, а также элемент WCF **System.ServiceModel**. В обозревателе решений щелкните правой кнопкой мыши проект **ImageListener** и выберите пункт **Управление пакетами NuGet**. Щелкните вкладку **Обзор** и выполните поиск `Microsoft Azure Service Bus`. Щелкните **Установить**и примите условия использования.
5. Явным образом добавьте в проект ссылку на библиотеку **System.ServiceModel.Web.dll**.
   
    a. В обозревателе решений в папке проекта щелкните правой кнопкой мыши папку **Ссылки** и выберите **Добавить ссылку**.
   
    Б. В диалоговом окне **Добавление ссылки** откройте вкладку **Платформа** в области слева и в поле **поиска** введите **System.ServiceModel.Web**. Установите флажок **System.ServiceModel.Web**, а затем нажмите кнопку **ОК**.
6. Добавьте следующие инструкции `using` в начало файла Program.cs.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](/dotnet/api/system.servicemodel) — это пространство имен, которое предоставляет программный доступ к основным функциям WCF. Ретранслятор WCF использует множество объектов и атрибутов WCF для определения контрактов службы. Это пространство имен будет использоваться в большинстве приложений ретранслятора. Аналогичным образом [System.ServiceModel.Channels](/dotnet/api/system.servicemodel.channels) помогает определить канал, т. е. объект, посредством которого ретранслятор Azure взаимодействует с веб-браузером клиента. Наконец, [System.ServiceModel.Web](/dotnet/api/system.servicemodel.web) содержит типы, позволяющие создавать веб-приложения.
7. Переименуйте пространство имен `ImageListener` в **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Сразу после открывающей фигурной скобки в объявлении пространства имен определите новый интерфейс с именем **IImageContract** и примените к нему атрибут **ServiceContractAttribute** со значением `http://samples.microsoft.com/ServiceModel/Relay/`. Значение пространства имен отличается от пространства имен, которое используется во всей области кода. Значение пространства имен служит в качестве уникального идентификатора для данного контракта и должно содержать сведения о версии. Дополнительные сведения см. в статье [Управление версиями службы](http://go.microsoft.com/fwlink/?LinkID=180498). Явное указание пространства позволяет предотвратить добавление значение пространства имен по умолчанию к имени контракта.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. В интерфейсе `IImageContract` объявите метод для отдельной операции, которую контракт `IImageContract` предоставляет в интерфейсе. Затем примените атрибут `OperationContractAttribute` к методу, который требуется предоставить как часть открытого контракта служебной шины.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. В атрибут **OperationContract** добавьте значение **WebGet**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Это позволит службе ретрансляции перенаправлять HTTP-запросы GET в `GetImage` и преобразовывать возвращаемые значения `GetImage` в HTTP-ответ GETRESPONSE. Далее в этом учебнике для доступа к этому методу и отображения изображения в браузере будет использоваться веб-браузер.
11. Сразу после определения `IImageContract` объявите канал, наследующий от интерфейсов `IImageContract` и `IClientChannel`.
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Канал представляет собой объект WCF, посредством которого служба и клиент обмениваются информацией. Канал в своем хост-приложении вы создадите позже. Ретранслятор Azure использует этот канал для передачи HTTP-запросов GET из браузера в реализацию **GetImage**. Канал также требуется ретранслятору для получения возвращаемого значения **GetImage** и его преобразования в HTTP-ответ GETRESPONSE для браузера клиента.
12. В меню **Сборка** щелкните **Построить решение**, чтобы проверить правильность выполнения действий.

### <a name="example"></a>Пример
Ниже приведен код базового интерфейса, определяющего контракт ретранслятора WCF.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Шаг 3. Реализуйте контракт службы WCF на основе REST, используемого в служебной шине
Перед созданием службы ретрансляции WCF на основе REST необходимо сначала создать контракт, который определяется с помощью интерфейса. Следующим шагом является реализация интерфейса. Она предполагает создание класса с именем **ImageService**, который реализует пользовательский интерфейс **IImageContract**. После реализации контракта необходимо настроить интерфейс с помощью файла App.config. Файл конфигурации содержит сведения, необходимые для приложения, такие как имя службы, имя контракта и тип протокола, используемого для взаимодействия с службой ретрансляции. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

Как и на предыдущих шагах, разница между реализацией контракта REST и контракта ретранслятора WCF невелика.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Реализация контракта REST служебной шины
1. Создайте новый класс с именем **ImageService** сразу после определения интерфейса **IImageContract**. Класс **ImageService** реализует интерфейс **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Как и в случае с другими реализациями интерфейсов, определение можно реализовать в другом файле. Но в этом руководстве реализация осуществляется в том же файле, что и определение интерфейса и метод `Main()`.
2. Примените атрибут [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) к классу **IImageService**, чтобы указать, что класс является реализацией контракта WCF.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Как упоминалось ранее, это пространство имен не является обычным. Оно является частью архитектуры WCF, которая идентифицирует контракт. Дополнительные сведения см. в статье [Имена контрактов данных](https://msdn.microsoft.com/library/ms731045.aspx) документации по WCF.
3. Добавьте в проект изображение в формате JPG.  
   
    Это рисунок, отображаемый службой в браузере-получателе. Щелкните проект правой кнопкой мыши и выберите **Добавить**. Затем выберите **Существующий элемент**. В диалоговом окне **Добавление существующего элемента** найдите необходимый файл в формате JPG и нажмите кнопку **Добавить**.
   
    При добавлении файла убедитесь, что в раскрывающемся списке рядом с полем **Имя файла** выбран пункт **Все файлы**. Далее в этом учебнике предполагается, что изображение называется image.jpg. При использовании другого файла необходимо переименовать изображение или изменить код соответствующим образом.
4. Чтобы убедиться, что запущенная служба может найти файл изображения, в **обозревателе решений** щелкните файл изображения правой кнопкой мыши, а затем выберите пункт **Свойства**. На панели **Свойства** задайте для параметра **Копировать в выходной каталог** значение **Копировать, если новее**.
5. Добавьте в проект ссылку на сборку **System.Drawing.dll** и добавьте следующие связанные инструкции `using`.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. В классе **ImageService** добавьте следующий конструктор, который загружает точечный рисунок и подготавливает его для отправки в браузер клиента.
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Сразу после предыдущего кода добавьте следующий метод **GetImage** в класс **ImageService** для возврата HTTP-сообщения, содержащего изображение.
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Эта реализация использует класс **MemoryStream** для извлечения изображения и его подготовки к потоковой передаче в браузер. Он начинает позицию потока с нуля, объявляет содержимое потока как jpeg и передает информацию.
8. В меню **Сборка** выберите **Построить решение**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Определение конфигурации для запуска веб-службы в служебной шине
1. В **обозревателе решений** дважды щелкните файл **App.config**, чтобы открыть его в редакторе Visual Studio.
   
    Файл **App.config** также содержит имя службы, конечную точку (то есть расположение, которое ретранслятор Azure предоставляет клиентам и узлам для взаимодействия) и привязку (тип протокола, используемый для связи). Основное различие состоит в том, что настроенная конечная точка службы ссылается на привязку [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding).
2. Элемент XML `<system.serviceModel>` представляет собой элемент WCF, определяющий одну или несколько служб. Здесь он используется для определения имени службы и конечной точки. В нижней части элемента `<system.serviceModel>` (но не выходя за рамки элемента `<system.serviceModel>`) добавьте элемент `<bindings>` с указанным ниже содержимым. Оно определяет привязки, используемые в приложении. Можно определить несколько привязок, но в этом учебнике определяется только одна.
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    Предыдущий код определяет привязку [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) ретранслятора WCF, в которой для свойства **relayClientAuthenticationType** установлено значение **None**. Этот параметр указывает, что для конечной точки, использующей эту привязку, не требуются учетные данные клиента.
3. После элемента `<bindings>` добавьте элемент `<services>`. Как и в случае с привязками, в одном файле конфигурации можно определить несколько служб. Но в этом учебнике определяется только одна.
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    На этом этапе выполняется настройка службы, которая использует заданную ранее привязку по умолчанию **webHttpRelayBinding**. Здесь также используется значение по умолчанию **sbTokenProvider**, которое будет определено на следующем этапе.
4. После элемента `<services>` создайте элемент `<behaviors>` с указанным ниже содержимым, заменив SAS_KEY ключом *подписанного URL-адреса* (SAS), полученным ранее [на портале Azure][Azure portal].
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. В файле App.config в элементе `<appSettings>` целиком замените значение строки подключения значением, полученным ранее на портале. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. В меню **Сборка** выберите **Построить решение**, чтобы создать решение полностью.

### <a name="example"></a>Пример
В следующем коде показана реализация контракта и службы для службы REST, запущенной в служебной шине с помощью привязки **WebHttpRelayBinding**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Ниже представлен пример файла App.config, связанного со службой.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey="YOUR_SAS_KEY"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-azure-relay"></a>Шаг 4. Размещение службы WCF на основе REST для использования ретранслятора Azure
В этом разделе описан запуск веб-службы с помощью консольного приложения и ретранслятора WCF. Полный код этого шага представлен в примере после описания последовательности выполнения действий.

### <a name="to-create-a-base-address-for-the-service"></a>Создание базового адреса для службы
1. В объявлении функции `Main()` создайте переменную для хранения пространства имен своего проекта. Не забудьте заменить `yourNamespace` именем созданного ранее пространства имен ретранслятора.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    Пространство имен используется в служебной шине для создания уникального URI.
2. Создайте экземпляр `Uri` для базового адреса службы на основе пространства имен.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Создание и настройка узла веб-службы
* Создайте узел веб-службы с помощью URI-адреса, созданного на предыдущем шаге.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    Узел службы представляет собой объект WCF, который создает экземпляры хост-приложения. В этом примере ему передаются тип создаваемого узла (**ImageService**) и адрес, по которому предоставляется ведущее приложение.

### <a name="to-run-the-web-service-host"></a>Запуск узла веб-службы
1. Откройте службу.
   
    ```csharp
    host.Open();
    ```
    Теперь служба запущена.
2. Отобразите сообщение о том, что служба запущена, и о том, как остановить ее.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. После завершения закройте узел службы.
   
    ```csharp
    host.Close();
    ```

## <a name="example"></a>Пример
В следующем примере показаны контракт и реализация службы, созданные на предыдущих шагах, а также размещение службы в консольном приложении. Скомпилируйте следующий код в исполняемый файл с именем "ImageListener.exe":

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Компиляция кода
После построения решения выполните следующие действия, чтобы запустить приложение:

1. Нажмите клавишу **F5** или перейдите в расположение исполняемого файла (ImageListener\bin\Debug\ImageListener.exe), чтобы запустить службу. Оставьте приложение в работающем состоянии, так как это необходимо для выполнения следующего этапа.
2. Скопируйте из командной строки адрес и вставьте его в браузере, чтобы увидеть изображение.
3. По завершении откройте окно командной строки и нажмите клавишу **ВВОД**, чтобы закрыть приложение.

## <a name="next-steps"></a>Дополнительная информация
Вы научились создавать приложение, которое использует службу ретранслятора Azure. Дополнительные сведения можно найти в следующих статьях:

* [Обзор архитектуры служебной шины Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Как использовать ретранслятор WCF служебной шины с .NET](relay-wcf-dotnet-get-started.md)

[Azure portal]: https://portal.azure.com
