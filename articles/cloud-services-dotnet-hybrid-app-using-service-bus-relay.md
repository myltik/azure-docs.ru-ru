<properties 
	pageTitle="Гибридные локальные и облачные приложения (.NET) в Azure" 
	description="Узнайте, как создать локальное или облачное гибридное приложение .NET с использованием ретранслятора служебной шины Azure." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor="mattshel"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/17/2015" 
	ms.author="sethm"/>






#Создание локального или облачного гибридного приложения .NET с использованием ретрансляции служебной шины

##Введение

Очень просто разрабатывать гибридные облачные приложения в Microsoft Azure с помощью
Visual Studio 2013 и бесплатного пакета SDK Azure для .NET. В данном руководстве предполагается, что у читателя нет опыта использования платформы Azure. Менее чем за 30 минут вы получите приложение, которое использует несколько ресурсов Microsoft Azure и выполняется в облаке.

Вы узнаете:

-   Как создать или адаптировать существующую веб-службу, которая будет использоваться
    веб-решением.
-   Как использовать служебную шину Azure для обмена данными между
    приложением Azure и веб-службой, размещенной в другом месте.

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

##Как ретрансляция служебной шины помогает работать с гибридными решениями

Бизнес-решения обычно состоят из сочетания пользовательского кода, написанного для удовлетворения новых и уникальных бизнес-требований, с существующими функциональными возможностями, предоставляемыми уже используемыми решениями и системами.

Архитекторы решений начинают использовать облако для упрощения реализации масштабирования и снижения эксплуатационных расходов. При этом они обнаруживают,что существующие активы служб, которые они хотели бы использовать в качестве стандартных блоков для своих решений, находятся за корпоративным брандмауэром, поэтому обращение к ним из облачного решения затруднено. Многие внутренние службы построены или размещены таким образом, который не позволяет легко предоставлять их на границе корпоративной сети.

 *Service Bus Relay* предназначен для следующего варианта использования
Windows Communication Foundation (WCF) безопасно предоставляются для решений,находящихся вне периметра корпоративной сети, без внесения существенных изменений в инфраструктуру корпоративной сети. Такие службы ретрансляции все еще размещаются внутри существующей среды, но делегируют прослушивание входящих сессий и запросов размещенной в облаке служебной шины. Служебная шина также защищает эти службы от неавторизованного доступа за счет использования проверки подлинности [Подписанный URL-адрес](https://msdn.microsoft.com/library/dn170478.aspx) (SAS).

##Сценарий решений

В этом учебнике будет создан веб-сайт ASP.NET MVC, который позволит просматривать список продуктов на странице складских запасов.

![][0]

В учебнике предполагается, что имеются сведения о продуктах в существующей локальной системе, для доступа к которой используется ретрансляция служебной шины. Это имитируется веб-службой, запущенной в простом консольном приложении и поддерживаемой набором продуктов в памяти. Можно запустить это консольное приложение на своем компьютере и развернуть веб-роль в Azure. Таким образом вы увидите, как веб-роль, выполняемая в центре обработки данных Azure, действительно вызывает ваш компьютер, даже если он (почти наверняка) будет находиться хотя бы за одним брандмауэром и слоем преобразования сетевых адресов (NAT).

Снимок экрана начальной страницы готового веб-приложения приведен ниже.

![][1]

##Настройка среды разработки

Прежде чем начать разработку приложения для Azure, подготовьте нужные инструменты и настройте среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, щелкните приведенную ниже ссылку:

    [Получить инструменты и SDK][]

2. 	Щелкните **Установить пакет SDK** для используемой версии Visual Studio. В этом учебнике приводятся инструкции для работы с версией Visual Studio 2013.

	![][42]

4.  При появлении запроса на выполнение или сохранение файла установки нажмите **Выполнить**:

    ![][2]

5.  В **установщике веб-платформы** щелкните **Установить** и продолжайте установку.

    ![][3]

6.  После завершения установки у вас будет все
    необходимое, чтобы приступить к разработке. Пакет SDK включает в себя инструменты, позволяющие
    легко разрабатывать приложения Azure в Visual Studio. Если у вас
    не установлено приложение Visual Studio, SDK также устанавливает бесплатное приложение
    Visual Studio Express.

## Создание пространства имен службы

Чтобы начать использование функций служебной шины в Azure, необходимо сначала создать пространство имен службы. Пространство имен предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении. 

Можно управлять пространствами имен и сущностями обмена сообщениями служебной шины с помощью [портала управления Azure][] или обозревателя серверов Visual Studio, но новые пространства имен можно создавать только с портала.

### Чтобы создать пространство имен службы с помощью портала:

1.  Выполните вход на [портал управления Azure][].

2.  В левой области навигации портала управления щелкните
    **Служебная шина**.

3.  В нижней части портала управления нажмите кнопку **Создать**.   
    ![][5]

4.  В диалоговом окне **Добавить новое пространство имен** введите имя пространства имен.
    Система немедленно проверяет, доступно ли оно.   
    ![][6]

5.  Убедившись, что имя пространства имен доступно, выберите
    страну или регион, где оно должно быть размещено (это должна быть
    страна или регион, где вы развертываете свои
    вычислительные ресурсы).

    ВАЖНО! Выберите **тот же регион**, который собираетесь выбрать
    для развертывания приложения. Это обеспечит наилучшую производительность.

6.	Оставьте в остальных полях диалогового окна значений по умолчанию (**Обмен сообщениями** и **Уровень Standard**), затем установите флажок. Теперь система создает пространство имен и включает его. Вероятно, придется подождать несколько минут, пока система не выделит ресурсы для вашей учетной записи.

	![][38]

Созданное пространство имен появится на портале управления, хотя его активация может занять некоторое время. Прежде чем продолжать, подождите, пока состояние не изменится на **Активное**.

## Получение учетных данных управления по умолчанию для пространства имен

Для выполнения операций управления для нового пространства имен, например для создания сущностей обмена сообщениями, необходимо получить учетные данные для пространства имен.

1.  Щелкните имя созданного пространства имен службы в главном окне.   

	![][39]
  

2.  Щелкните **Сведения о подключении**.   

	![][40]


3.  В области **Доступ к сведениям о подключении** найдите строку подключения, которая содержит ключ SAS и имя ключа.   

	![][45]
    

4.  Запишите эти учетные данные или скопируйте их в буфер обмена.

## Управление пространством имен службы с помощью обозревателя серверов Visual Studio

Чтобы вместо портала управления использовать Visual Studio для управления пространством имен и получения сведений о подключении, выполните процедуру, описанную [здесь](http://http://msdn.microsoft.com/library/ff687127.aspx), в разделе **Подключение к Azure из Visual Studio**. При входе в Azure узел **служебной шины** в дереве **Microsoft Azure** в обозревателе серверов автоматически заполняется уже созданными пространствами имен. Щелкните правой кнопкой мыши любое пространство имен и выберите **Свойства**, чтобы увидеть строку подключения и другие метаданные, связанные с этим пространством имен, которые отображаются в области **Свойства** Visual Studio. 

![][44]

Запишите значение **SharedAccessKey** или скопируйте его в буфер обмена.


## Создание локального сервера

В первую очередь нужно создать (макетную) локальную систему каталогов продукции. Она будет довольно простой. Это выглядит как представление фактической локальной системы каталогов продукции в виде полнофункциональной службы, которую мы пытаемся интегрировать.

Этот проект запускается как консольное приложение Visual Studio. Данный проект использует пакет NuGet служебной шины для включения библиотек и параметров настройки служебной шины. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express. Пакет NuGet для служебной шины является самым простым способом получить интерфейс API служебной шины и настроить свое приложение с учетом всех зависимостей служебной шины. Дополнительные сведения об использовании NuGet и пакета служебной шины см. в разделе [Использование пакета NuGet для служебной шины][].

### Создание проекта

1.  Запустите с привилегиями администратора Microsoft Visual
    Studio 2013 или Microsoft Visual Studio Express. Чтобы
    запустить Visual Studio с правами администратора, щелкните правой кнопкой мыши
    **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите пункт **Запуск от имени администратора**.

2.  В Visual Studio в меню **Файл** выберите **Создать** и нажмите
    **Проект**.

    ![][10]

3.  В разделе **Установленные шаблоны** области **Visual C#** щелкните элемент **Консольное
    приложение**. В поле **Имя** введите имя
    **ProductsServer**:

    ![][11]

4.  Нажмите кнопку **ОК**, чтобы создать проект **ProductsServer**.

5.  В **обозревателе решений** щелкните правой кнопкой мыши элемент **ProductsServer** и
    выберите пункт **Свойства**.

6.  Перейдите на вкладку **Приложение** в левой части экрана, а затем убедитесь, что **.NET
    Framework 4** или **.NET Framework 4.5** отображается в раскрывающемся списке **Требуемая версия .NET Framework:**. Если нет, выберите соответствующий пункт из раскрывающегося списка и затем нажмите кнопку **Да**
    при появлении запроса на перезагрузку проекта.

    ![][12]

7.  Если диспетчер пакетов NuGet для Visual Studio уже установлен, пропустите следующий шаг. В противном случае посетите сайт [NuGet][] и щелкните элемент [Установить NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Следуйте инструкциям на экране для установки диспетчера пакетов NuGet, а затем перезапустите Visual Studio.

7.  Щелкните в **обозревателе решений** правой кнопкой мыши пункт **Ссылки**, а затем выберите
    **Управление пакетами NuGet**.

8.  В левом столбце диалогового окна NuGet щелкните элемент **В сети**.

9. 	В правом столбце выберите поле **Поиск**, введитe **служебная шина** и выберите элемент **Майкрософт
    служебная шина Azure**. Для завершения установки щелкните **Установить**
    и затем закройте данное диалоговое окно.

    ![][13]

    Обратите внимание, что на необходимые клиентские сборки теперь имеется ссылка.

9.  Добавьте новый класс для контракта на свою продукцию. В **обозревателе решений**
    щелкните правой кнопкой мыши проект **ProductsServer** и нажмите кнопку **Добавить**, после чего выберите пункт
    **Класс**.

    ![][14]

10. В поле **Имя** введите имя **ProductsContract.cs**. Затем
    щелкните **Добавить**.

11. В файле **ProductsContract.cs** замените определение пространства имен
    следующим кодом, который определит контракт для службы:

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
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

12. В Program.cs замените определение пространства имен следующим
    кодом, который добавит службу профилей и узел для нее:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {
                
                // Populate array of products for display on Website
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
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
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

13. В **обозревателе решений** дважды щелкните файл **App.config**, чтобы
    открыть его в редакторе **Visual Studio**. Замените содержимое
    **&lt;system.ServiceModel&gt;** следующим XML-кодом. Не забудьте
    заменить *yourServiceNamespace* именем пространства имен
    своей службы, а *yourKey* - ключом SAS, полученным ранее
    на портале управления Azure:

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

14. Нажмите клавишу **F6** или щелкните элемент **Построить решение** в меню **Сборка**, чтобы построить приложение для проверки точности выполненной работы.

## Создание приложения ASP.NET MVC

В этом разделе будет выполнено построение простого приложения ASP.NET, которое будет отображать данные, полученные из службы продукции.

### Создание проекта

1.  Убедитесь, что система Visual Studio запущена с правами администратора. В противном случае для
    запуска Visual Studio с правами администратора щелкните правой кнопкой мыши
    **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите пункт **Запуск от имени администратора**. Эмулятор вычислений Azure, который будет описан далее в этом руководстве, требует
    запуска Visual Studio с правами администратора.

2.  В Visual Studio в меню **Файл** выберите **Создать** и затем
    **Проект**.

3.  В разделе **Visual C#** области **Установленные шаблоны** щелкните элемент **Веб-приложение ASP.NET**. Присвойте проекту имя **ProductsPortal**. Затем
    нажмите **OK**.

    ![][15]

4.  В списке **Выбор шаблона** щелкните **MVC**, а затем
    **OK**.

    ![][16]

5.  В **обозревателе решений** щелкните правой кнопкой мыши **Модели**, выберите **Добавить** и
    нажмите **Класс**. В поле **Имя** введите имя
    **Product.cs**. Нажмите кнопку **Добавить**.

    ![][17]

### Изменение веб-приложения

1.  В файле Product.cs в Visual Studio замените существующее
    определение пространства имен следующим кодом:

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  В файле HomeController.cs в Visual Studio замените существующее
    определение пространства имен следующим кодом:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  В **обозревателе решений** разверните "Представления\Общие":

    ![][18]

4.  Затем дважды щелкните файл _Layout.cshtml, чтобы открыть его в редакторе Visual Studio.

5.  Замените все вхождения элемента **Мое приложение MVC ASP.NET** текстом **Продукты LITWARE**.

6. Удалите ссылки **Главная**, **О программе** и **Связь**. Удалите выделенный код.

	![][41]

7.  В **обозревателе решений** разверните "Представления\Главная":

    ![][20]

8.  Дважды щелкните файл Index.cshtml, чтобы открыть его в редакторе Visual Studio.
    Замените все содержимое файла на код, приведенный ниже.
	
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


9.  Для проверки того, что все действия выполнены правильно, нажмите клавишу **F6** или
    **Ctrl+Shift+B** для сборки проекта.


### Локальный запуск приложения

Запустите приложение, чтобы убедиться, что оно работает.

1.  Убедитесь, что **ProductsPortal** является активным проектом. Щелкните правой кнопкой мыши
    имя проекта в **обозревателе решений** и выберите **Назначить
    запускаемым проектом**.
2.  В **Visual Studio** нажмите **F5**.
3.  Приложение должно начать выполняться в браузере:

    ![][21]

## Подготовка приложения к развертыванию в Azure

Можно развернуть приложение в облачной службе Azure или на веб-сайте Azure. Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в разделе [Модели выполнения Azure][executionmodels]. Дополнительные сведения о развертывании приложения на веб-узле Azure см. в разделе [Развертывание веб-приложения ASP.NET на веб-сайте Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Этот раздел содержит подробные инструкции по развертыванию приложения в облачной службе Azure.

Чтобы развернуть приложение в облачной службе, необходимо добавить в решение проект развертывания проекта облачной службы. Проект развертывания содержит сведения о конфигурации, необходимые для правильного запуска приложения в облаке.

1.  Чтобы сделать приложение развертываемым в облаке, щелкните правой кнопкой мыши проект **ProductsPortal** в **обозревателе решений** и щелкните **Преобразовать**, затем щелкните **Преобразовать в проект облачной службы Azure**.

    ![][22]

2.  Чтобы протестировать приложение, нажмите клавишу **F5**.

3.  Это приведет к запуску эмулятора вычислений Azure. Эмулятор
        вычислений использует локальный компьютер для эмуляции запуска вашего приложения
        в Azure. Чтобы убедиться, что эмулятор
        запущен, посмотрите на область уведомлений:

       ![][23]

4.  Браузер по-прежнему будет показывать, что ваше приложение работает локально,
        и оно будет выглядеть и функционировать таким же образом, как при запуске
        ранее в виде обычного приложения ASP.NET MVC 4.

## Объединение элементов

Следующим шагом является подключение локального сервера продуктов к приложению ASP.NET MVC.

1.  Если он еще не открыт, откройте повторно в Visual Studio проект
        **ProductsPortal**, который был создан в разделе "Создание приложения
        ASP.NET MVC".

2.  Как и на шаге из раздела "Создание локального сервера",
        добавьте в проект ссылку на пакет NuGet. В
        обозревателе решений щелкните правой кнопкой мыши **Ссылки**, а затем выберите
        **Управление пакетами NuGet**.

3.  Выполните поиск "служебная шина" и выберите пункт **Служебная шина Microsoft Azure**. Выполните установку и закройте это диалоговое окно.

4.  В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal** и выберите пункт **Добавить**, а затем выберите **Существующий проект**.

5.  Перейдите к файлу **ProductsContract.cs** в проекте консоли **ProductsServer**. Щелкните, чтобы выделить ProductsContract.cs. Щелкните стрелку вниз рядом с пунктом **Добавить**, затем щелкните **Добавить как ссылку**.

	![][24]

6.  Теперь откройте файл **HomeController.cs** в редакторе Visual Studio и замените существующее определение пространства имен следующим кодом. Не забудьте заменить *yourServiceNamespace* на имя пространства имен вашей службы, а *yourKey* - на ключ SAS. Это позволит клиенту вызывать локальную службу, возвращая результат в вызов.

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
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
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
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }
7.  В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal**,
        нажмите **Добавить** и выберите **Существующий проект**.

8.  Перейдите к проекту **ProductsServer**, затем дважды щелкните
        файл решения **ProductsServer.csproj**, чтобы добавить его.

9.  В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal**
        и выберите **Свойства**.

10. В левой части окна выберите **Запускаемый проект**. С
        правой стороны выберите **Несколько запускаемых проектов**. Убедитесь, что
        **ProductsServer**, **ProductsPortal.Azure** и
        **ProductsPortal** отображаются в указанном порядке, при этом свойство **Запустить** назначено
        в качестве действия для элементов **ProductsServer** и **ProductsPortal.Azure**,
        а значение **Нет** установлено в качестве действия для элемента **ProductsPortal**. Для
        Пример:

      ![][25]

11. В диалоговом окне свойств щелкните **ProjectDependencies**
        в левой части окна.

12. Из раскрывающегося списка **Проекты** выберите элемент
        **ProductsServer**. Убедитесь, что флажок **ProductsPortal** снят
        и установлен флажок **ProductsPortal.Azure**. Нажмите кнопку **ОК**.

    ![][26]

## Запуск приложения

1.  В меню **Файл** Visual Studio выберите пункт **Сохранить все**.

2.  Нажмите клавишу **F5** для построения и запуска приложения. Локальный
        сервер (консольное приложение **ProductsServer**) должен запуститься
        первым, затем в окне браузера должно отобразиться приложение **ProductsWeb**,
        как показано на приведенном ниже снимке экрана. На этот раз вы
        увидите данные списков складских запасов, полученные от
        локальной системы службы продуктов.

    ![][1]

## Развертывание приложения в Azure

1.  Щелкните правой кнопкой мыши проект **ProductsPortal** в **обозревателе
        решений** и выберите **Опубликовать в Microsoft Azure**.

2.  Возможно, понадобится выполнить регистрацию для просмотра всех подписок.

    Щелкните **Войти, чтобы просмотреть дополнительные подписки**.

    ![][27]

3.  Выполните выход с использованием своей учетной записи Майкрософт.


8.  Нажмите кнопку **Далее**. Если ваши подписки не содержат размещенных
        служб, вам будет предложено создать службу. Размещенная служба
        выступает в роли контейнера для приложения в 
        подписке Azure. Введите имя для своего
        приложения и выберите регион, для которого приложение
        необходимо оптимизировать. (Можно ожидать ускорения загрузки для
        пользователей, осуществляющих доступ из этого региона.)

9.  Выберите размещенную службу, для которой нужно опубликовать
        приложение. Для остальных параметров оставьте значения по умолчанию,
        как показано ниже. Нажмите кнопку **Далее**.

    ![][33]

10. На последней странице нажмите кнопку **Опубликовать**, чтобы начать процесс
        развертывания:

    ![][34]

Это займет приблизительно 5-7 минут. Поскольку эта
        публикация выполняется впервые, Microsoft Azure выполнит подготовку
        виртуальной машины, повысит уровень безопасности, создаст роль веб-роль
        на виртуальной машине для размещения вашего приложения, развернет для нее код
        и, наконец, настроит подсистему балансировки нагрузки и
        сетевых подключений, чтобы ваше приложение стало доступно всем желающим.

11. Во время публикации можно отслеживать
        действия в окне **Журнал действий Azure**, которое
        обычно прикреплено в нижней части окна Visual Studio или Visual Web
        Developer:

    ![][35]

12. После завершения развертывания, чтобы отобразить веб-сайт, необходимо
        щелкнуть ссылку **URL-адрес веб-сайта** в окне мониторинга.

    ![][36]

    Веб-сайт зависит от локального сервера, поэтому необходимо
        запустить приложение **ProductsServer** локально для веб-сайта,
        чтобы обеспечить правильную работу. При выполнении запросов к облачному
        веб-сайту вы увидите запросы, поступающие в локальное
        консольное приложение, как указано в выходных данных "GetProducts called"
        на снимке экрана ниже.

    ![][37]

Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в разделе [Модели выполнения Azure][executionmodels].

## Остановка и удаление приложения

После развертывания приложения может потребоваться отключить его, чтобы можно было построить и развернуть другие приложения в рамках 750 свободных часов серверного времени в месяц (31 день в месяце).

Для экземпляров веб-роли Azure выставляет счета за почасовое использование серверного времени. Время использования сервера отсчитывается с момента развертывания приложения, даже если экземпляры не запущены и пребывают в остановленном состоянии.
Бесплатная учетная запись предоставляет 750 часов серверного времени в месяц (31 день в месяце) в виде выделенной виртуальной машины для размещения этих экземпляров веб-роли.

Ниже показано, как остановить и удалить приложение.

1.  Выполните вход на [портал управления Azure], щелкните пункт **Облачные службы**, а затем щелкните имя своей службы.

2.  Выберите вкладку **Панель мониторинга**, а затем щелкните **Прервать**, чтобы временно остановить работу приложения. Вы сможете
        запустить его снова, нажав "Пуск". Щелкните **Удалить**, чтобы полностью удалить приложение из Azure без возможности восстановления.

	![][43]

## Дальнейшие действия  

Дополнительную информацию о служебной шине см. на следующих ресурсах.  
  
* [служебная шина Azure][sbmsdn]
* [Практические руководства по работе со служебной шиной][sbwacom]
* [Использование очередей служебной шины][sbwacomqhowto]


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Портал управления Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Использование пакета NuGet для служебной шины]: http://go.microsoft.com/fwlink/?LinkId=234589
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  
  
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-36.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-37.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [35]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [36]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [39]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [40]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [41]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [42]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [43]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [44]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png
  [45]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-45.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/


<!--HONumber=52-->