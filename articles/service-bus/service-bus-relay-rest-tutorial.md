<properties
   pageTitle="Учебник по REST в служебной шине с использованием обмена сообщениями с ретрансляцией | Microsoft Azure"
   description="Создание простого хост-приложения ретранслятора служебной шины, которое предоставляет интерфейс на основе REST."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/14/2015"
   ms.author="sethm" />

# Учебник по REST в служебной шине

В этом учебнике описано, как создать простое хост-приложение служебной шины, предоставляющее интерфейс на основе REST. REST позволяет веб-клиенту, например веб-браузеру, получить доступ к интерфейсам API служебной шины с помощью HTTP-запросов.

В этом учебнике для создания службы REST в служебной шине используется модель программирования REST Windows Communication Foundation (WCF). Дополнительные сведения см. в разделе [Модель программирования REST WCF](https://msdn.microsoft.com/library/bb412169.aspx) и [Разработка и реализация служб](https://msdn.microsoft.com/library/ms729746.aspx) в документации по WCF.

## Шаг 1. Зарегистрируйте учетную запись Azure

Первым шагом является создание пространства имен службы и получение ключа подписанного URL-адреса (SAS). Пространство имен определяет границы каждого приложения, предоставляемого через служебную шину. Ключ SAS автоматически создается системой при создании пространства имен службы. Сочетание пространства имен и ключа совместного доступа к подписи предоставляет учетные данные, на основе которых служебная шина осуществляет проверку подлинности и дает доступ к приложению.

### Создание пространства имен службы и получение ключа SAS

1. Чтобы создать пространство имен службы, перейдите на [классический портал Azure][]. В левой части щелкните **Служебная шина** и нажмите кнопку **Создать**. Введите имя для пространства имен, а затем щелкните значок с изображением флажка.

2. В главном окне портала щелкните имя пространства имен службы, созданного на предыдущем шаге.

3. Щелкните **Настройка**, чтобы просмотреть политики общего доступа для пространства имен.

4. Запишите значение первичного ключа для политики **RootManageSharedAccessKey** или скопируйте его в буфер обмена. Это значение потребуется позже.

## Шаг 2. Определите контракт службы WCF на основе REST, используемого в служебной шине

Как и в случае с другими службами служебной шины, при создании службы на основе REST необходимо определить контракт. Контракт определяет, какие операции поддерживает узел. Операцию службы можно рассматривать как метод веб-службы. Контракты создаются путем определения интерфейса C++, C# или Visual Basic. Каждый метод в интерфейсе соответствует определенной операции службы. К каждому интерфейсу должен быть применен атрибут [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx), а к каждой операции — атрибут [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx). Если у метода в интерфейсе с атрибутом [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) нет атрибута [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), такой метод не предоставляется. Код для выполнения этих задач показан в примере, приведенном после описания последовательности выполнения действий.

Основное различие между базовым контрактом служебной шины и контрактом REST заключается в добавлении свойства к атрибуту [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Это свойство позволяет сопоставить метод в интерфейсе с методом на другой стороне интерфейса. В нашем примере атрибут [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) используется для связывания метода с HTTP GET. Это позволяет служебной шине точно извлекать и интерпретировать команды, отправляемые в интерфейс.

### Создание контракта служебной шины с помощью интерфейса

1. Откройте Visual Studio с правами администратора, щелкнув программу правой кнопкой мыши в меню **Пуск** и выбрав **Запуск от имени администратора**.

2. Создайте новый проект консольного приложения. В меню **Файл** выберите **Создать**, а затем — **Проект**. В диалоговом окне **Новый проект** выберите **Visual C#** (если **Visual C#** не отображается, откройте список **Другие языки**). Затем выберите шаблон **Консольное приложение** и назовите его **ImageListener**. Используйте **расположение** по умолчанию. Нажмите кнопку **ОК**, чтобы создать проект.

3. Для проекта C# в Visual Studio создается файл с именем `Program.cs`. Этот класс содержит пустой метод `Main()`, необходимый для правильной сборки проекта консольного приложения.

4. Добавьте в проект ссылку на файл **System.ServiceModel.dll**:

	а. В обозревателе решений в папке проекта щелкните правой кнопкой мыши папку **Ссылки** и выберите **Добавить ссылку**.

	b. В диалоговом окне **Добавление ссылки** перейдите на вкладку **.NET** и прокрутите окно вниз до элемента **System.ServiceModel**. Выберите его, а затем нажмите кнопку **ОК**.

5. Повторите предыдущий шаг, чтобы добавить ссылку на сборку **System.ServiceModel.Web.dll**.

6. Добавьте операторы `using` для пространств имен **System.ServiceModel**, **System.ServiceModel.Channels**, **System.ServiceModel.Web** и **System.IO**.

	```c
  	using System.ServiceModel;
  	using System.ServiceModel.Channels;
  	using System.ServiceModel.Web;
  	using System.IO;
	```

	[System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) — это пространство имен, которое предоставляет программный доступ к основным функциям WCF. Служебная шина использует множество объектов и атрибутов WCF для определения контрактов службы. Это пространство имен будет использоваться в большинстве приложений ретранслятора служебной шины. Аналогичным образом [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) помогает определить канал, то есть объект, посредством которого служебная шина взаимодействует с веб-браузером клиента. Наконец, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) содержит типы, позволяющие создавать веб-приложения.

7. Переименуйте пространство имен для программы, заданное в Visual Studio по умолчанию, в **Microsoft.ServiceBus.Samples**.

 	```
	namespace Microsoft.ServiceBus.Samples
	{
		...
	```

8. Сразу после объявления пространства имен определите новый интерфейс с именем **IImageContract** и примените к нему атрибут **ServiceContractAttribute** со значением `http://samples.microsoft.com/ServiceModel/Relay/`. Значение пространства имен отличается от пространства имен, которое используется во всей области кода. Значение пространства имен служит в качестве уникального идентификатора для данного контракта и должно содержать сведения о версии. Дополнительные сведения см. в статье [Управление версиями службы](http://go.microsoft.com/fwlink/?LinkID=180498). Явное указание пространства позволяет предотвратить добавление значение пространства имен по умолчанию к имени контракта.

	```
	[ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
	public interface IImageContract
	{
	}
	```

9. В интерфейсе `IImageContract` объявите метод для отдельной операции, которую контракт `IImageContract` предоставляет в интерфейсе. Затем примените атрибут `OperationContractAttribute` к методу, который требуется предоставить как часть открытого контракта служебной шины.

	```
	public interface IImageContract
	{
		[OperationContract]
		Stream GetImage();
	}
	```

10. После атрибута **OperationContract** примените атрибут **WebGet**.

	```
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}
	```

	Это позволит служебной шине перенаправлять HTTP-запросы GET в **GetImage** и преобразовывать возвращаемые значения **GetImage** в HTTP-ответ GETRESPONSE. Далее в этом учебнике для доступа к этому методу и отображения изображения в браузере будет использоваться веб-браузер.

11. Сразу после определения `IImageContract` объявите канал, наследующий от интерфейсов `IImageContract` и `IClientChannel`.

	```
	[ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IImageContract
	{
		[OperationContract, WebGet]
		Stream GetImage();
	}

	public interface IImageChannel : IImageContract, IClientChannel { }
	```

	Канал представляет собой объект WCF, посредством которого служба и клиент обмениваются информацией. Канал в своем хост-приложении вы создадите позже. Служебная шина использует этот канал для передачи HTTP-запросов GET из браузера в реализацию **GetImage**. Он также требуется для получения возвращаемого значения **GetImage** и его преобразования в ответ HTTP GETRESPONSE для браузера клиента.

12. В меню **Сборка** щелкните **Сборка решения**, чтобы проверить правильность выполнения действий.

### Пример

Ниже приведен код базового интерфейса, определяющего контракт служебной шины.

```
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

## Шаг 3. Реализуйте контракт службы WCF на основе REST, используемого в служебной шине

Перед созданием службы служебной шины на основе REST необходимо сначала создать контракт, который определяется с помощью интерфейса. Следующим шагом является реализация интерфейса. Она предполагает создание класса с именем **ImageService**, который реализует пользовательский интерфейс **IImageContract**. После реализации контракта необходимо настроить интерфейс с помощью файла App.config. Файл конфигурации содержит сведения, необходимые для приложения, такие как имя службы, имя контракта и тип протокола, используемого для взаимодействия со служебной шиной. Код для выполнения этих задач приведен в примере после описания последовательности выполнения действий.

Как и на предыдущих шагах, разница между реализацией контракта REST и базовым контрактом служебной шины небольшая.

### Реализация контракта REST служебной шины

1. Создайте новый класс с именем **ImageService** сразу после определения интерфейса **IImageContract**. Класс **ImageService** реализует интерфейс **IImageContract**.

	```
	class ImageService : IImageContract
	{
	}
	```
	Как и в случае с другими реализациями интерфейсов, определение можно реализовать в другом файле. Но в этом руководстве реализация осуществляется в том же файле, что и определение интерфейса и метод `Main()`.

2. Примените атрибут [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) к классу **IImageService**, чтобы указать, что класс является реализацией контракта WCF:

	```
	[ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class ImageService : IImageContract
	{
	}
	```

	Как упоминалось ранее, это пространство имен не является обычным. Оно является частью архитектуры WCF, которая идентифицирует контракт. Дополнительные сведения см. в разделе [Имена контрактов данных](https://msdn.microsoft.com/library/ms731045.aspx) в документации по WCF.

3. Добавьте в проект изображение в формате JPG.

	Это рисунок, отображаемый службой в браузере-получателе. Щелкните проект правой кнопкой мыши и выберите **Добавить**. Затем выберите **Существующий элемент**. В диалоговом окне **Добавление существующего элемента** найдите необходимый файл в формате JPG и нажмите кнопку **Добавить**.

	При добавлении файла убедитесь, что в раскрывающемся списке рядом с полем **Имя файла** выбран пункт **Все файлы**. Далее в этом учебнике предполагается, что изображение называется image.jpg. При использовании другого файла необходимо переименовать изображение или изменить код соответствующим образом.

4. Чтобы убедиться, что запущенная служба может найти файл изображения, в **обозревателе решений** щелкните файл изображения правой кнопкой мыши. На панели **Свойства** задайте для параметра **Копировать в выходной каталог** значение **Копировать, если новее**.

5. Добавьте в проект ссылки на сборки **System.Drawing.dll**, **System.Runtime.Serialization.dll** и **Microsoft.ServiceBus.dll**, а также следующие связанные операторы `using`.

	```
	using System.Drawing;
	using System.Drawing.Imaging;
	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Web;
	```

6. В классе **ImageService** добавьте следующий конструктор, который загружает точечный рисунок и подготавливает его для отправки в браузер клиента.

	```
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

	```
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

### Определение конфигурации для запуска веб-службы в служебной шине

1. Щелкните правой кнопкой мыши проект **ImageListener**. Выберите **Добавить**, а затем **Новый элемент**.

2. В **обозревателе решений** дважды щелкните файл **App.config**, который сейчас содержит следующие XML-элементы.

	```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	</configuration>
	```

	Файл конфигурации похож на файл конфигурации WCF и включает имя службы, конечную точку (то есть местоположение, которое служебная шина предоставляет клиентам и узлам для взаимодействия) и привязку (тип протокола, используемый для обмена данными). Основное различие состоит в том, что настроенная конечная точка службы ссылается на привязку [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx), которая не является частью платформы .NET Framework. Дополнительные сведения о настройке приложения служебной шины см. в статье [Настройка службы WCF для регистрации с помощью служебной шины](https://msdn.microsoft.com/library/ee173579.aspx).


3. Добавьте XML-элемент `<system.serviceModel>` в файл App.config. Это элемент WCF, который определяет одну или несколько служб. Здесь он используется для определения имени службы и конечной точки.

	```
	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<system.serviceModel>

		</system.serviceModel>

	</configuration>
	```

4. В элемент `system.serviceModel` добавьте элемент `<bindings>` с указанным ниже содержанием. Оно определяет привязки, используемые в приложении. Можно определить несколько привязок, но в этом учебнике определяется только одна.

	```
	<bindings>
		<!-- Application Binding -->
		<webHttpRelayBinding>
			<binding name="default">
				<security relayClientAuthenticationType="None" />
			</binding>
		</webHttpRelayBinding>
	</bindings>
	```

	На этом этапе определяется привязка [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) служебной шины, в которой для свойства **relayClientAuthenticationType** установлено значение **None**. Этот параметр указывает, что для конечной точки, использующей эту привязку, не требуются учетные данные клиента.

5. После элемента `<bindings>` добавьте элемент `<services>`. Как и в случае с привязками, в одном файле конфигурации можно определить несколько служб. Но в этом учебнике определяется только одна.

	```
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

6. После элемента `<services>` создайте элемент `<behaviors>` с указанным ниже содержанием, заменив SAS\_KEY ключом *подписанного URL-адреса* (SAS), полученным на [классическом портале Azure][] на этапе 1.

	```
	<behaviors>
		<endpointBehaviors>
			<behavior name="sbTokenProvider">
				<transportClientEndpointBehavior>
					<tokenProvider>
						<sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
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

7. В меню **Сборка** выберите **Сборка решения**, чтобы создать решение.

### Пример

В следующем коде показана реализация контракта и службы для службы REST, запущенной в служебной шине с помощью привязки **WebHttpRelayBinding**.

```
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

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <bindings>
      <!-- Application Binding -->
      <webHttpRelayBinding>
        <binding name="default">
          <!-- Turn off client authentication so that client does not need to present credential through browser or fiddler -->
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
              <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
            </tokenProvider>
          <transportClientEndpointBehavior>
        </behavior>
      </endpointBehaviors>
      <serviceBehaviors>
        <behavior name="default">
          <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
        </behavior>
      </serviceBehaviors>
    </behaviors>

  </system.serviceModel>
</configuration>
```

## Шаг 4. Разместите службы WCF на основе REST, используемой в служебной шине

В этом шаге описан запуск веб-службы в служебной шине с помощью консольного приложения. Полный код этого шага представлен в примере после описания последовательности выполнения действий.

### Создание базового адреса для службы

1. В объявлении функции `Main()` создайте переменную с пространством имен вашего проекта служебной шины.

	```
	string serviceNamespace = "InsertServiceNamespaceHere";
	```
	Пространство имен используется в служебной шине для создания уникального URI.

2. Создайте экземпляр `Uri` для базового адреса службы на основе пространства имен.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
	```

### Создание и настройка узла веб-службы

- Создайте узел веб-службы с помощью URI-адреса, созданного на предыдущем шаге.

	```
	WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
	```
	Узел службы представляет собой объект WCF, который создает экземпляры хост-приложения. В этом примере ему передаются тип создаваемого узла (**ImageService**) и адрес, по которому предоставляется ведущее приложение.

### Запуск узла веб-службы

1. Откройте службу.

	```
	host.Open();
	```
	Теперь служба запущена.

2. Отобразите сообщение о том, что служба запущена, и о том, как остановить ее.

	```
	Console.WriteLine("Copy the following address into a browser to see the image: ");
	Console.WriteLine(address + "GetImage");
	Console.WriteLine();
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

3. После завершения закройте узел службы.

	```c
	host.Close();
	```

## Пример

В следующем примере показаны контракт и реализация службы, созданные на предыдущих шагах, а также размещение службы в консольном приложении. Скомпилируйте следующий код в исполняемый файл с именем "ImageListener.exe":

```
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

### Компиляция кода

После построения решения выполните следующие действия, чтобы запустить приложение:

1. Запустите службу из командной строки (ImageListener\\bin\\Debug\\ImageListener.exe).

2. Скопируйте из командной строки адрес и вставьте его в браузере, чтобы увидеть изображение.

## Дальнейшие действия

Вы научились создавать приложение, которое использует службу ретранслятора служебной шины. Дополнительные сведения об обмене сообщениями с ретрансляцией можно найти в следующих статьях:

- [Обзор архитектуры служебной шины Azure](service-bus-fundamentals-hybrid-solutions.md#relays)

- [Как использовать службу ретранслятора служебной шины](service-bus-dotnet-how-to-use-relay.md)

[классический портал Azure]: http://manage.windowsazure.com
[классическом портале Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0121_2016-->