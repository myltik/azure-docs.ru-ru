<properties
	pageTitle="Гибридные локальные и облачные приложения (.NET) | Microsoft Azure"
	description="Узнайте, как создать локальное или облачное гибридное приложение .NET с использованием ретранслятора служебной шины Azure."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Создание локального или облачного гибридного приложения .NET с использованием ретранслятора служебной шины Azure

## Введение

Разрабатывать гибридные облачные приложения в Microsoft Azure очень просто благодаря применению Visual Studio 2013 и бесплатного пакета SDK Azure для платформы .NET. Для работы с этот статьей опыт работы с платформой Azure не требуется. Менее чем за 30 минут вы получите приложение, которое использует несколько ресурсов Microsoft Azure и выполняется в облаке.

Вы узнаете:

-   Как создать или адаптировать существующую веб-службу для использования веб-решением.
-   Как использовать ретрансляцию служебной шины Azure для обмена данными между приложением Azure и веб-службой, которая размещается в другом месте.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Как ретрансляция служебной шины помогает работать с гибридными решениями

Бизнес-решения обычно состоят из сочетания пользовательского кода, написанного для удовлетворения новых и уникальных бизнес-требований, с существующими функциональными возможностями, предоставляемыми уже используемыми решениями и системами.

Архитекторы решений начинают использовать облако для упрощения реализации масштабирования и снижения эксплуатационных расходов. При этом они обнаруживают,что существующие активы служб, которые они хотели бы использовать в качестве стандартных блоков для своих решений, находятся за корпоративным брандмауэром, поэтому обращение к ним из облачного решения затруднено. Многие внутренние службы построены или размещены таким образом, который не позволяет легко предоставлять их на границе корпоративной сети.

Ретранслятор служебной шины предназначен для случаев, когда существующие веб-службы Windows Communication Foundation (WCF) безопасно предоставляются для решений, находящихся вне периметра корпоративной сети, без внесения существенных изменений в инфраструктуру корпоративной сети. Такие службы ретранслятора служебной шины по-прежнему размещаются внутри существующей среды, однако они делегируют функции прослушивания входящих сеансов и запросов размещенной в облаке службе Service Bus. Служебная шина также защищает эти службы от несанкционированного доступа с помощью проверки подлинности [Подписи общего доступа](https://msdn.microsoft.com/library/dn170478.aspx) (SAS).

## Сценарий решений

В этом учебнике будет создан веб-сайт ASP.NET MVC, который позволит просматривать список продуктов на странице складских запасов.

![][0]

В учебнике предполагается, что имеются сведения о продуктах в существующей локальной системе, для доступа к которой используется ретрансляция служебной шины. Это имитируется веб-службой, запущенной в простом консольном приложении и поддерживаемой набором продуктов в памяти. Можно запустить это консольное приложение на своем компьютере и развернуть веб-роль в Azure. Таким образом вы увидите, как веб-роль, выполняемая в центре обработки данных Azure, действительно вызывает ваш компьютер, даже если он (почти наверняка) будет находиться хотя бы за одним брандмауэром и слоем преобразования сетевых адресов (NAT).

Снимок экрана начальной страницы готового веб-приложения приведен ниже.

![][1]

## Настройка среды разработки

Прежде чем начать разработку приложения для Azure, подготовьте нужные инструменты и настройте среду разработки.

1.  Установите пакет SDK Azure для .NET в разделе [Получить инструменты и SDK][].

2. 	Щелкните **Установить пакет SDK** для используемой версии Visual Studio. В описанных в этом учебнике примерах используется Visual Studio 2013.

	![][42]

4.  При появлении запроса на выполнение или сохранение файла установки нажмите **Выполнить**.

    ![][2]

5.  В **установщике веб-платформы** щелкните **Установить**, чтобы продолжить установку.

    ![][3]

6.  После завершения установки у вас будут все компоненты, необходимые для начала разработки приложения. В состав пакета SDK входят инструменты для эффективной разработки приложений Azure в Visual Studio. Если у вас не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия Visual Studio Express.

## Создание пространства имен службы

Чтобы начать использование функций служебной шины в Azure, необходимо сначала создать пространство имен службы. Пространство имен предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении.

Пространствами имен и сущностями обмена сообщениями служебной шины можно управлять с помощью [классического портала Azure][] или обозревателя сервера Visual Studio, но новые пространства имен можно создавать только на портале.

### Чтобы создать пространство имен службы с помощью портала, сделайте следующее:

1.  Войдите на [классический портал Azure][].

2.  На портале в области навигации слева щелкните элемент **Служебная шина**.

3.  На портале на панели внизу нажмите кнопку **Создать**.

    ![][5]

4.  В диалоговом окне **Добавление нового пространства имен** введите имя пространства имен. Система немедленно проверит, доступно ли это имя. ![][6]

5.  Убедившись, что имя пространства имен доступно, выберите страну или регион, где будет размещено ваше пространство имен (необходимо указать страну и регион развертывания своих вычислительных ресурсов).

    > [AZURE.IMPORTANT]Выберите *тот же регион*, который собираетесь выбрать для развертывания приложения. Это обеспечит наилучшую производительность.

6.	Оставьте в остальных полях диалогового окна значения по умолчанию (**Обмен сообщениями** и **Уровень Standard**), а затем установите флажок. Теперь система создает пространство имен и включает его. Вероятно, придется подождать несколько минут, пока система не выделит ресурсы для вашей учетной записи.

	![][38]

Созданное пространство имен появится на классическом портале Azure, при этом его активация может занять некоторое время. Прежде чем продолжать, подождите, пока состояние не изменится на **Активное**.

## Получение учетных данных управления по умолчанию для пространства имен

Для выполнения операций управления для нового пространства имен, например для создания сущностей обмена сообщениями, необходимо получить учетные данные для пространства имен.

1.  В главном окне щелкните имя созданного пространства имен службы.

	![][39]

2.  Нажмите кнопку **Сведения о подключении**

	![][40]

3.  В области **Сведения о доступе к подключению** найдите строку подключения, которая содержит ключ SAS и имя ключа.

	![][45]

4.  Запишите эти учетные данные или скопируйте их в буфер обмена.

## Создание локального сервера

В первую очередь нужно создать (макетную) локальную систему каталогов продукции. Она будет довольно простой. Это выглядит как представление фактической локальной системы каталогов продукции в виде полнофункциональной службы, которую мы пытаемся интегрировать.

Этот проект запускается как консольное приложение Visual Studio. Данный проект использует пакет NuGet служебной шины для включения библиотек и параметров настройки служебной шины. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express. Пакет NuGet для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины. Дополнительные сведения об использовании NuGet и пакета служебной шины см. в разделе [Использование пакета NuGet для служебной шины][].

### Создание проекта

1.  Используя привилегии администратора, запустите Microsoft Visual Studio 2013 или Microsoft Visual Studio Express. Для запуска Visual Studio с привилегиями администратора щелкните правой кнопкой мыши **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите **Запуск от имени администратора**.

2.  В меню **Файл** Visual Studio выберите **Создать**, а затем **Проект**.

    ![][10]

3.  В разделе **Visual C#** области **Установленные шаблоны** щелкните элемент **Консольное приложение**. В поле **Имя** введите имя **ProductsServer**:

    ![][11]

4.  Нажмите кнопку **ОК**, чтобы создать проект **ProductsServer**.

5.  В окне "Обозреватель решений" щелкните правой кнопкой мыши элемент **ProductsServer** и выберите пункт **Свойства**.

6.  Выберите расположенную слева вкладку **Приложение** и убедитесь, что в списке **Требуемая версия .NET Framework:** отображается **.NET Framework 4** или **.NET Framework 4.5**. Если такое значение отсутствует, выберите его в списке и нажмите кнопку **Да** в появившемся запросе о перезагрузке проекта.

    ![][12]

7.  Если диспетчер пакетов NuGet для Visual Studio уже установлен, пропустите следующий шаг. В противном случае посетите сайт [NuGet][] и щелкните элемент [Установить NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Следуйте инструкциям на экране для установки диспетчера пакетов NuGet, а затем перезапустите Visual Studio.

7.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.

8.  В левом столбце диалогового окна **NuGet** щелкните элемент **В сети**.

9. 	В правом столбце выберите поле **Поиск**, введите "**служебная шина**" и выберите элемент **Служебная шина Microsoft Azure**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![][13]

    Обратите внимание, что на необходимые клиентские сборки теперь имеется ссылка.

9.  Добавьте новый класс для контракта на свою продукцию. В окне "Обозреватель решений" щелкните правой кнопкой проект **ProductsServer**, а затем выберите **Добавить** и **Класс**.

    ![][14]

10. В поле **Имя** введите имя **ProductsContract.cs**. Нажмите кнопку **Добавить**.

11. В **ProductsContract.cs** замените определение пространства имен на следующий код, определяющий контракт для службы.

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties.
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

12. В Program.cs замените определение пространства имен на следующий код, добавляющий службу профилей и узел для нее.

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface.
            class ProductsService : IProducts
            {

                // Populate array of products for display on website.
                ProductData[] products =
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock",
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper",
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors",
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well",
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application
                // when the list of products is retrieved.
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application.
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

13. В окне "Обозреватель решений" дважды щелкните файл **App.config**, чтобы открыть его в редакторе Visual Studio. Замените содержимое **&lt;system.ServiceModel&gt;** на приведенный ниже XML-код. Не забудьте заменить *yourServiceNamespace* именем пространства имен вашей службы, а *yourKey* — ключом SAS, полученным ранее на классическом портале Azure.

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14. Нажмите клавишу F6 или щелкните элемент **Построить решение** в меню **Сборка**, чтобы построить приложение для проверки точности выполненной работы.

## Создание приложения ASP.NET MVC

В этом разделе будет выполнено построение простого приложения ASP.NET, которое будет отображать данные, полученные из службы продукции.

### Создание проекта

1.  Убедитесь, что система Visual Studio запущена с правами администратора. Если нет, то для запуска Visual Studio с привилегиями администратора щелкните правой кнопкой мыши **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите **Запуск от имени администратора**. Для работы эмулятора вычислений Microsoft Azure, который обсуждается далее в этой статье, требуется запустить Visual Studio с правами администратора.

2.  В меню **Файл** Visual Studio выберите **Создать**, а затем **Проект**.

3.  В разделе **Visual C#** области **Установленные шаблоны** щелкните элемент **Веб-приложение ASP.NET**. Присвойте проекту имя **ProductsPortal**. Нажмите кнопку **ОК**.

    ![][15]

4.  В списке **Выбор шаблона** щелкните **MVC** и нажмите кнопку **ОК**.

    ![][16]

5.  В обозревателе решений щелкните **Модели** правой кнопкой мыши, а затем последовательно щелкните **Добавить** и **Класс**. В поле **Имя** введите имя **Product.cs**. Нажмите кнопку **Добавить**.

    ![][17]

### Изменение веб-приложения

1.  В Visual Studio замените существующее определение пространства имен в файле на следующий код.

        // Declare properties for the products inventory.
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  В Visual Studio замените существующее определение пространства имен в файле HomeController.cs на следующий код.

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory.
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product>
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  В обозревателе решений разверните папку "Views\\Shared".

    ![][18]

4.  Дважды щелкните файл **\_Layout.cshtml**, чтобы открыть его в редакторе Visual Studio.

5.  Замените все вхождения элемента **Мое приложение MVC ASP.NET** текстом **Продукты LITWARE**.

6. Удалите ссылки **Главная**, **О программе** и **Связь**. В следующем примере удалите выделенный код.

	![][41]

7.  В обозревателе решений разверните папку "Views\\Home".

    ![][20]

8.  Дважды щелкните файл **Index.cshtml**, чтобы открыть его в редакторе Visual Studio. Замените все содержимое файла следующим кодом.

		@model IEnumerable<ProductsWeb.Models.Product>

		@{
    		ViewBag.Title = "Index";
		}

		<h2>Prod Inventory</h2>

		<table>
    		<tr>
        		<th>
            		@Html.DisplayNameFor(model => model.Name)
        		</th>
                <th></th>
        		<th>
            		@Html.DisplayNameFor(model => model.Quantity)
        		</th>
    		</tr>

		@foreach (var item in Model) {
    		<tr>
        		<td>
            		@Html.DisplayFor(modelItem => item.Name)
        		</td>
        		<td>
            		@Html.DisplayFor(modelItem => item.Quantity)
        		</td>
    		</tr>
		}

		</table>

9.  Чтобы проверить правильность работы на данный момент, можно нажать **F6** или **CTRL + SHIFT + B** для сборки проекта.


### Локальный запуск приложения

Запустите приложение, чтобы убедиться, что оно работает.

1.  Убедитесь, что **ProductsPortal** является активным проектом. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите команду **Назначить запускаемым проектом**.
2.  В **Visual Studio** нажмите клавишу F5.
3.  Приложение должно начать выполняться в браузере.

    ![][21]

## Подготовка приложения к развертыванию в Azure

Можно выполнить развертывание приложения в облачной службе Azure или на веб-сайте Azure. Дополнительную информацию о различиях между веб-узлами и облачными службами см. в статье [Модели выполнения Azure][executionmodels]. Дополнительные сведения о развертывании приложения на веб-сайте Azure содержатся в разделе [Развертывание веб-приложения ASP.NET на веб-сайте Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Этот раздел содержит подробные инструкции по развертыванию приложения в облачной службе Azure.

Чтобы развернуть приложение в облачной службе, необходимо добавить в решение проект развертывания проекта облачной службы. Проект развертывания содержит сведения о конфигурации, необходимые для правильного запуска приложения в облаке.

1.  Чтобы обеспечить возможность развертывания приложения в облаке, щелкните правой кнопкой мыши проект **ProductsPortal** в обозревателе решений и выберите пункт **Преобразовать**, затем щелкните **Преобразовать в проект облачной службы Microsoft Azure**.

    ![][22]

2.  Чтобы протестировать приложение, нажмите клавишу F5.

3.  Это приведет к запуску эмулятора вычислений Azure. Эмулятор среды выполнения приложений использует локальный компьютер для эмуляции вашего приложения, работающего в среде Azure. Чтобы убедиться, что эмулятор запущен, обратитесь к панели задач.

       ![][23]

4.  В браузере все еще будет отображаться локально работающее приложение, которое выглядит и функционирует точно так же, как оно работало при предыдущем запуске в качестве обычного приложения ASP.NET MVC 4.

## Объединение элементов

Следующим шагом является подключение локального сервера продуктов к приложению ASP.NET MVC.

1.  В Visual Studio повторно откройте проект **ProductsPortal**, созданный в разделе "Создание приложения ASP.NET MVC".

2.  Аналогично шагу в разделе "Создание локального сервера" добавьте пакет NuGet в ссылки проекта. В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.

3.  Выполните поиск по фразе "служебная шина" и выберите элемент **Служебная шина Microsoft Azure**. Завершите установку и закройте это диалоговое окно.

4.  В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Добавить**, а затем выберите **Существующий элемент**.

5.  Перейдите к файлу **ProductsContract.cs** в консольном проекте **ProductsServer**. Щелкните, чтобы выделить ProductsContract.cs. Щелкните стрелку вниз рядом с пунктом **Добавить**, затем щелкните **Добавить как связь**.

	![][24]

6.  Теперь откройте файл **HomeController.cs** в редакторе Visual Studio и замените существующее определение пространства имен следующим кодом. Не забудьте заменить *yourServiceNamespace* на имя пространства имен вашей службы, а *yourKey* — на ключ SAS. Это позволит клиенту вызывать локальную службу, возвращая результат в вызов.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory.
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication.
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                            TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                                "RootManageSharedAccessKey", "yourKey") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory.
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name,
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal** и выберите пункт **Добавит**, а затем щелкните **Существующий проект**.

8.  Пройдите к проекту **ProductsServer**, а затем дважды щелкните файл решения **ProductsServer.csproj**, чтобы добавить его.

9.  В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal**, а затем выберите пункт **Свойства**.

10. В левой части окна выберите **Запускаемый проект**. В правой части окна выберите **Несколько запускаемых проектов**. Убедитесь, что **ProductsServer**, **ProductsPortal.Azure** и **ProductsPortal** отображаются именно в таком порядке, при этом для **ProductsServer** и **ProductsPortal.Azure** назначено действие **Запуск**, а для **ProductsPortal** — действие **Нет**.

      ![][25]

11. В левой части диалогового окна **Свойства** щелкните элемент **ProjectDependencies**.

12. В списке **Проекты** щелкните элемент **ProductsServer**. Убедитесь, что флажок **ProductsPortal** не установлен, а флажок **ProductsPortal.Azure** установлен. Нажмите кнопку **ОК**.

    ![][26]

## Выполнение приложения

1.  В меню **Файл** Visual Studio выберите пункт **Сохранить все**.

2.  Нажмите клавишу F5, чтобы создать и запустить приложение. Первым должен запуститься локальный сервер (консольное приложение **ProductsServer**), затем приложение **ProductsWeb** должно запуститься в окне браузера, как показано на снимке экрана ниже. На этот раз вы сможете проследить получение данных списка складских запасов продукции из локальной системы службы продукции.

    ![][1]

## Развертывание приложения в Azure

1.  В окне "Обозреватель решений" щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Опубликовать в Microsoft Azure**.

2.  Возможно, понадобится выполнить регистрацию для просмотра всех подписок.

    Щелкните **Войти, чтобы просмотреть дополнительные подписки**.

    ![][27]

3.  Выполните выход с использованием своей учетной записи Майкрософт.

8.  Нажмите кнопку **Далее**. Если подписка еще не содержит размещенные службы, вам будет предложено создать такую службу. Размещенная служба выступает в качестве контейнера для приложения в подписке Azure. Введите имя, идентифицирующее приложение, и выберите регион, для которого необходимо оптимизировать приложение. (Можно ожидать ускорения загрузки для пользователей, осуществляющих доступ из этой области.)

9.  Выберите размещенную службу, в которую вы хотите опубликовать приложение. Для остальных параметров оставьте значения по умолчанию, как показано ниже. Нажмите кнопку **Далее**.

    ![][33]

10. На последней странице нажмите кнопку **Опубликовать** для запуска процесса развертывания.

    ![][34]Это займет приблизительно 5–7 минут. Поскольку это первая публикация, Azure выполняет подготовку виртуальной машины (ВМ), повышает уровень безопасности, создает на виртуальной машине веб-роль для размещения приложения, развертывает код в этой веб-роли и, наконец, настраивает подсистему балансировки нагрузки и сетевые функции, чтобы приложение было доступно всем желающим.

11. Во время выполнения публикации можно будет отслеживать операции в окне **Журнал действий Azure**, которое обычно прикреплено к нижней части экрана Visual Studio или Visual Web Developer.

    ![][35]

12. После развертывания веб-сайт можно просмотреть, щелкнув ссылку **URL-адрес веб-сайта** в окне мониторинга.

    ![][36]

    Правильная работа веб-сайта зависит от локального сервера, поэтому необходимо запустить приложение **ProductsServer** локально для этого веб-сайта. При выполнении запросов для облачного веб-сайта можно наблюдать поступление запросов в локальное консольное приложение, как указано в выходных данных "Вызванный GetProducts" на снимке экрана ниже.

    ![][37]

Дополнительную информацию о различиях между веб-узлами и облачными службами см. в статье [Модели выполнения Azure][executionmodels].

## Остановка и удаление приложения

После развертывания приложения может потребоваться отключить его, чтобы можно было построить и развернуть другие приложения в рамках 750 свободных часов серверного времени в месяц (31 день в месяце).

Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии. Бесплатная учетная запись предоставляет 750 часов серверного времени в месяц (31 день в месяце) в виде выделенной виртуальной машины для размещения этих экземпляров веб-роли.

Ниже показано, как остановить и удалить приложение.

1.  Войдите на [классический портал Azure][], выберите пункт **Облачные службы**, а затем щелкните имя своей службы.

2.  Выберите вкладку **Панель мониторинга**, а затем щелкните **Прервать**, чтобы временно остановить работу приложения. Его можно будет снова запустить, щелкнув элемент **Запуск**. Щелкните **Удалить**, чтобы полностью удалить приложение из Azure без возможности восстановления.

	![][43]

## Дальнейшие действия  

Дополнительную информацию о Service Bus см. на следующих ресурсах.

* [Служебная шина Azure][sbwacom]  
* [Использование очередей служебной шины][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-42-webpi.png


  [классический портал Azure]: http://manage.windowsazure.com
  [классического портала Azure]: http://manage.windowsazure.com
  [5]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png



  [Использование пакета NuGet для служебной шины]: https://msdn.microsoft.com/library/azure/dn741354.aspx
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg

  [20]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png


  [30]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [45]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [executionmodels]: ../cloud-services/fundamentals-application-models.md

<!---HONumber=AcomDC_1203_2015-->