<properties linkid="dev-net-tutorials-hybrid-solution" urlDisplayName="Гибридное приложение" pageTitle="Гибридное локальное/облачное приложение (.NET) - Azure" metaKeywords="Учебник по использованию Azure Service Bus, гибридная платформа .NET" description="Узнайте, как создать локальное/облачное гибридное приложение .NET с использованием ретрансляции Azure Service Bus." metaCanonical="" services="service-bus" documentationCenter=".NET" title="Создание локального/облачного гибридного приложения .NET с использованием ретрансляции Service Bus" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />





# Создание локального/облачного гибридного приложения .NET с использованием ретрансляции Service Bus

<h2><span class="short-header">ВВЕДЕНИЕ</span>ВВЕДЕНИЕ</h2>

Разрабатывать гибридные облачные приложения в Azure очень просто
благодаря применению Visual Studio 2013 и бесплатного пакета SDK Azure для платформы .NET. В данном руководстве
предполагается, что у читателя нет опыта использования платформы Azure. Менее чем
за 30 минут вы получите приложение, которое использует несколько ресурсов Windows
Azure и выполняется в облаке.

Вы узнаете:

-   Как создать или адаптировать существующую веб-службу для использования
    веб-решением.
-   Как использовать ретрансляцию Azure Service Bus для обмена данными между
    приложением Azure и веб-службой, которая размещается в другом месте.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### КАК РЕТРАНСЛЯЦИЯ SERVICE BUS ПОМОГАЕТ РАБОТАТЬ С ГИБРИДНЫМИ РЕШЕНИЯМИ

Бизнес-решения обычно состоят из сочетания пользовательского
кода, написанного для удовлетворения новых и уникальных бизнес-требований, с существующими функциональными
возможностями, предоставляемыми уже используемыми
решениями и системами.

Архитекторы решений начинают использовать облако для упрощения реализации
масштабирования и снижения эксплуатационных расходов. При этом они обнаруживают,
что существующие активы служб, которые они хотели бы использовать в качестве стандартных блоков
для своих решений, находятся внутри корпоративного брандмауэра, поэтому
обращение к ним облачного решения затруднено. Многие внутренние службы построены или размещены
таким образом, который не позволяет легко предоставлять их
в пограничной корпоративной сети.

*Ретрансляция Service Bus* предназначена для случая, когда существующие
веб-службы Windows Communication Foundation (WCF) безопасно предоставляются для решений,
находящихся вне периметра корпоративной сети, без внесения существенных изменений в инфраструктуру
корпоративной сети. Такие службы ретрансляции Service Bus по-прежнему размещаются
внутри существующей среды, однако они делегируют функции прослушивания
входящих сеансов и запросов размещенной в облаке службе Service Bus. Service Bus
также защищает эти службы от несанкционированного доступа с помощью
Azure Active Directory Access Control.

### СЦЕНАРИЙ РЕШЕНИЙ

В этом учебнике вы создадите веб-сайт ASP.NET MVC 4, который
позволит просматривать список продуктов на странице складских запасов.

![][0]

В учебнике предполагается размещение сведений о продуктах в существующей
локальной системе, для доступа к которой используется ретрансляция Service Bus. Это имитируется веб-службой, запущенной в простом
консольном приложении, и размещенным в памяти набором продуктов. Вы
сможете запустить это консольное приложение на своем компьютере и
развернуть веб-роль в Azure. Таким образом, вы увидите,
как веб-роль, выполняемая в центре обработки данных Azure, действительно вызывает
ваш компьютер, даже если он почти наверняка будет
находиться хотя бы за одним брандмауэром и слоем преобразования сетевых адресов
(NAT).

Снимок экрана начальной страницы готового веб-приложения
приведен ниже.

![][1]

<h2><span class="short-header">НАСТРОЙКА СРЕДЫ</span>НАСТРОЙКА СРЕДЫ РАЗРАБОТКИ</h2>

Прежде чем начать разработку приложения для Azure, необходимо подготовить нужные инструменты и настроить среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, щелкните приведенную ниже кнопку.

    [Получить инструменты и SDK][]

2. 	Щелкните **Установить SDK**.

3. 	Щелкните ссылку, соответствующую используемой версии Visual Studio. В этом учебнике приводятся инструкции для работы с версией Visual Studio 2013.

	![][42]

4.  При появлении запроса на выполнение или сохранение файла **WindowsAzureSDKForNet.exe** нажмите
    **Выполнить**:

    ![][2]

5.  В окне установщика веб-платформы щелкните **Установить** и продолжайте установку.

    ![][3]

6.  После завершения установки у вас будут все компоненты,
    необходимые для начала разработки. В состав пакета SDK входят инструменты
    для эффективной разработки приложений Azure в Visual Studio. Если у вас
    не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия
    Visual Studio Express.

<h2><span class="short-header">СОЗДАНИЕ ПРОСТРАНСТВА ИМЕН</span>СОЗДАНИЕ ПРОСТРАНСТВА ИМЕН СЛУЖБЫ</h2>

Чтобы начать использование функций Service Bus в Azure, необходимо сначала
создать пространство имен службы. Это пространство имен службы предоставляет область ограничений для адресации ресурсов Service Bus в вашем приложении. 

Можно управлять пространствами имен и сущностями обмена сообщениями Service Bus с помощью [портала управления Azure][портал управления Azure] или обозревателя серверов Visual Studio, но новые пространства имен можно создавать только с портала.

###Чтобы создать пространство имен службы с помощью портала:

1.  Выполните вход на [портал управления Azure][].

2.  В левой области навигации портала управления щелкните
    **Service Bus**.

3.  В нижней области портала управления щелкните **Создать**.   
    ![][5]

4.  В диалоговом окне **Добавить новое пространство имен** введите имя пространства имен.
    Система немедленно проверяет, доступно ли оно.   
    ![][6]

5.  Убедившись, что имя пространства имен доступно, выберите страну
    или регион, где будет размещаться пространство имен (обязательно
    используйте страну или регион, где развертываете ресурсы среды
    выполнения приложений).

    ВАЖНО! Выберите **тот же регион**, который собираетесь выбрать
    для развертывания приложения. Это обеспечит наилучшую производительность.

6.	Установите флажок. Теперь система создает пространство
    имен службы и включает его. Возможно, вам придется подождать несколько минут, пока система выделит ресурсы для вашей учетной записи.

	![][38]

Созданное пространство имен появится в портале управления; его активация займет некоторое время. Прежде чем продолжать, подождите, пока состояние
не станет **Активным**.

<h2><span class="short-header">ПОЛУЧЕНИЕ УЧЕТНЫХ ДАННЫХ УПРАВЛЕНИЯ</span>ПОЛУЧЕНИЕ УЧЕТНЫХ ДАННЫХ УПРАВЛЕНИЯ ПО УМОЛЧАНИЮ ДЛЯ ПРОСТРАНСТВА ИМЕН</h2>

Для выполнения операций управления, таких как создание очереди,
над новым пространством имен необходимо получить учетные данные
управления для пространства имен.

1.  В главном окне щелкните имя созданного пространства имен службы.   

	![][39]
  

2.  Щелкните **Сведения о подключении**.   

	![][40]


3.  В области **Сведения по доступу к подключению** найдите записи **Издатель по умолчанию** и **Ключ по умолчанию**.   
    

4.  Запишите ключ или скопируйте его в буфер обмена.

###Управление пространством имен службы с помощью обозревателя серверов Visual Studio

Чтобы вместо портала управления использовать Visual Studio для управления пространством имен и получения сведений о подключении, выполните процедуру, описанную [здесь](http://http://msdn.microsoft.com/ru-ru/library/windowsazure/ff687127.aspx), в разделе **Подключение к Azure из Visual Studio**. При входе в Azure узел **Service Bus** в дереве **Microsoft Azure** в обозревателе серверов автоматически заполняется уже созданными пространствами имен. Щелкните правой кнопкой мыши любое пространство имен и выберите **Свойства**, чтобы увидеть строку подключения и другие метаданные, связанные с этим пространством имен, которые отображаются в области **Свойства** Visual Studio. 

![][44]

Запишите значение **SharedAccessKey** или скопируйте его в буфер обмена.


<h2><span class="short-header">СОЗДАНИЕ ЛОКАЛЬНОГО СЕРВЕРА</span>СОЗДАНИЕ ЛОКАЛЬНОГО СЕРВЕРА</h2>

Во-первых, нужно создать (макетную) локальную систему каталогов продукции. Она будет довольно простой. Это выглядит, как представление фактической
локальной системы каталогов продукции в виде полной поверхности службы, которую
мы пытаемся интегрировать.

Этот проект будет запущен как консольное приложение Visual Studio. Данный
проект использует пакет Service Bus NuGet для включения
библиотек и параметров конфигурации служебной шины. Расширение NuGet для Visual Studio
упрощает установку и обновления библиотек и инструментов в Visual Studio
и Visual Studio Express. Пакет Service Bus NuGet является самым простым
способом получить API Service Bus и настроить для приложения
все зависимости Service Bus. Дополнительные сведения об использовании NuGet
и пакета Service Bus см. в разделе [Использование пакета NuGet для Service Bus][].

### СОЗДАНИЕ ПРОЕКТА

1.  Запустите Microsoft Visual
    Studio 2013 или Microsoft Visual Studio Express с правами администратора. Для запуска Visual
    Studio с правами администратора щелкните правой кнопкой мыши элемент **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите пункт **Запуск от имени администратора**.
2.  В меню **Файл** Visual Studio последовательно выберите  **Создать**
    и щелкните **Проект**.

    ![][10]

3.  В разделе **Visual C#** области **Установленные шаблоны** щелкните элемент **Консольное
    приложение**. В поле **Имя** введите имя
    **ProductsServer**:

    ![][11]

4.  Нажмите кнопку **ОК** для создания проекта **ProductsServer**.

5. В окне **Обозреватель решений** щелкните правой кнопкой мыши элемент **ProductsServer** и
выберите пункт **Свойства**.
6.  Выберите расположенную слева вкладку **Приложение и убедитесь**, что в раскрывающемся списке **Требуемая версия .NET Framework** отображается значение **.NET Framework 4** или **.NET Framework 4.5**. Если такое значение отсутствует, выберите его в раскрывающемся списке и нажмите кнопку **Да**
    в появившемся запросе о перезагрузке проекта.

    ![][12]

7.  Если вы уже установили диспетчер пакетов NuGet для Visual Studio, пропустите следующий шаг. В противном случае посетите сайт [NuGet][] и щелкните элемент [Установить NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Следуйте инструкциям на экране для установки диспетчера пакетов NuGet, а затем перезапустите Visual Studio.

7.  В **обозревателе решений** щелкните правой кнопкой мыши
    **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
8.  В левом столбце диалогового окна NuGet щелкните элемент **В сети**.

9. 	В правом столбце выберите поле **Поиск**, введите "**WindowsAzure**" и выберите элемент **Azure Service Bus**. Щелкните **Установить**, чтобы выполнить
    установку, а затем закройте это диалоговое окно.

    ![][13]

    Обратите внимание, что на необходимые клиентские сборки теперь имеется ссылка.

9.  Добавьте новый класс для контракта на свою продукцию. В окне **Обозреватель решений**
    щелкните правой кнопкой проект **ProductsServer**, а затем выберите **Добавить** и
    **Класс**.

    ![][14]

10. В поле **Имя** введите имя **ProductsContract.cs**. Нажмите
    кнопку **Добавить**.
11.  В **ProductsContract.cs** замените определение пространства имен
    на следующий код, определяющий контракт для службы:

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

12. В Program.cs замените определение пространства имен на
    следующий код, добавляющий службу профилей и узел для нее:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {
                
                // Populate array of products for display on Web site
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

13.  В окне **Обозреватель решений** дважды щелкните файл **app.config**, чтобы
    открыть его в редакторе **Visual Studio**. Замените содержимое
    **&lt;system.ServiceModel>** на приведенный ниже XML-код. Не забудьте заменить
    *yourServiceNamespace* на имя пространства имен вашей службы,
    а *yourIssuerSecret* — на ключ, полученный ранее с портала
    управления Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
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
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

14.  Нажмите клавишу **F6** или щелкните элемент **Построить решение** в меню **Сборка**, чтобы построить приложение для проверки точности выполненной работы.

<h2><span class="short-header">СОЗДАНИЕ ПРИЛОЖЕНИЯ ASP.NET MVC</span>СОЗДАНИЕ ПРИЛОЖЕНИЯ ASP.NET MVC</h2>

В этом разделе будет выполнено построение простого приложения ASP.NET, которое будет
отображать данные, полученные из службы продукции.

### СОЗДАНИЕ ПРОЕКТА

1. Убедитесь, что система Microsoft Visual Studio 2013 запущена с правами администратора. В противном случае для запуска Visual
    Studio с правами администратора щелкните правой кнопкой мыши элемент **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите пункт **Запуск от имени администратора**. Для работы
    эмулятора вычислений Azure, который рассматривается в последующих разделах этого руководства, требуется запустить
    Visual Studio с правами администратора.

2.  В меню **Файл** Visual Studio последовательно выберите  **Создать**
    и щелкните **Проект**.

3. В разделе **Visual C#** области **Установленные шаблоны** щелкните элемент **Веб-приложение ASP.NET**. Присвойте проекту имя **ProductsPortal**. Нажмите
    кнопку **ОК**.

    ![][15]

4.  В списке **Выбор шаблона** щелкните **MVC**
    и нажмите кнопку **ОК**.

    ![][16]

5.  В **обозревателе решений** щелкните правой кнопкой мыши **Модели**,
    выберите **Добавить** и затем щелкните **Класс**. В поле **Имя** введите имя
    **Product.cs**. Нажмите кнопку **Добавить**.

    ![][17]

### ИЗМЕНЕНИЕ ВЕБ-ПРИЛОЖЕНИЯ

1.  В файле Product.cs в Visual Studio замените
    существующее определение пространства имен следующим кодом.

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

2. В Visual Studio замените существующее определение пространства имен в файле HomeController.cs
    на следующий код.

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

3.  В **обозревателе решений** разверните "Views\Shared":

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


9.  Чтобы проверить правильность работы на данный момент, можно нажать **F6** или
    **Ctrl + Shift + B** для построения проекта.


### ЛОКАЛЬНЫЙ ЗАПУСК ПРИЛОЖЕНИЯ

Запустите приложение, чтобы убедиться, что оно работает.

1.  Убедитесь, что **ProductsPortal** является активным проектом. Щелкните правой кнопкой мыши
    имя проекта в **обозревателе решений**, затем выберите **Задать как
    Запускаемый проект**
2.  в **Visual Studio** и нажмите **F5**.
3.  Приложение должно начать выполняться в браузере:

    ![][21]

    <h2><span class="short-header">РАЗВЕРТЫВАНИЕ В AZURE</span>ПОДГОТОВКА ПРИЛОЖЕНИЯ К РАЗВЕРТЫВАНИЮ В AZURE</h2>

    Можно развернуть приложение в облачной службе Azure или на веб-сайте Azure. Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в статье [Модели выполнения Azure] [executionmodels]. Дополнительные сведения о развертывании приложения на веб-сайте Azure содержатся в разделе [Развертывание веб-приложения ASP.NET на веб-сайте Azure](http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/). Этот раздел содержит подробные инструкции по развертыванию приложения в облачной службе Azure.

    Чтобы развернуть приложение в облачной службе, необходимо добавить в решение проект развертывания проекта облачной службы.
    Проект развертывания содержит сведения о конфигурации,
    необходимые для правильного запуска приложения в облаке.

    1.  Чтобы сделать приложение развертываемым в облаке, щелкните
          правой кнопкой мыши проект **ProductsPortal** в **обозревателе решений**
        и щелкните **Преобразовать**, затем щелкните **Преобразовать в проект облачной службы Azure**.

        ![][22]

    2.  Чтобы протестировать приложение, нажмите клавишу **F5**.
    3.  Это приведет к запуску эмулятора среды выполнения приложений Azure. Эмулятор
        среды выполнения приложений использует локальный компьютер для эмуляции
        вашего приложения, работающего в среде Azure. Можно убедиться в запуске эмулятора,
        посмотрев на панель задач:

        ![][23]

    4.  В браузере все еще будет отображаться локально работающее приложение,
         которое выглядит и функционирует точно так же, как оно работало
         при предыдущем запуске в качестве обычного приложения ASP.NET MVC 4.

    <h2><span class="short-header">ФОРМИРОВАНИЕ ЦЕЛЬНОГО РЕШЕНИЯ</span> ФОРМИРОВАНИЕ ЦЕЛЬНОГО РЕШЕНИЯ</h2>

    Следующим шагом является подключение локального сервера продуктов к
    приложению ASP.NET MVC.

    1.  В Visual Studio повторно откройте проект
        **ProductsPortal**, созданный в разделе "Создание приложения
        ASP.NET MVC".

    2.  Аналогично шагу в разделе "Создание локального сервера"
         добавьте пакет NuGet в ссылки проекта. В
        обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите
        команду **Управление пакетами NuGet**.

    3.  Найдите элемент WindowsAzure.ServiceBus и выберите пункт **Windows
          Azure Service Bus**. Выполните установку и 
        закройте это диалоговое окно.

    4.  В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal**
         и выберите пункт **Добавить**, а затем выберите **Существующий проект**.

    5.  Перейдите к файлу **ProductsContract.cs** в проекте
          консоли **ProductsServer**. Щелкните, чтобы выделить
        ProductsContract.cs. Щелкните стрелку вниз рядом с пунктом **Добавить**,
         затем щелкните **Добавить как ссылку**.

        ![][24]

    6.  Теперь откройте файл **HomeController.cs** в редакторе Visual Studio 
          и замените существующее определение пространства имен следующим
          кодом. Не забудьте заменить *yourServiceNamespace* на имя
           пространства имен вашей службы, а *yourIssuerSecret* — на ключ.
        Это позволит клиенту вызывать локальную службу,
        возвращая результат в вызов.

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
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
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

    7.  В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal**
         и выберите пункт **Добавить**, а затем щелкните **Существующий проект**.

    8.  Перейдите к проекту **ProductsServer**, а затем дважды щелкните
        файл решения **ProductsServer.csproj**, чтобы добавить его.

    9.  В обозревателе решений щелкните правой кнопкой мыши решение **ProductsPortal**,
        а затем выберите пункт **Свойства**.

    10.  В левой части окна выберите **Запускаемый проект**. В правой части окна
        выберите **Несколько запускаемых проектов**. Убедитесь, что
        **ProductsServer**, **ProductsPortal.Azure** и
        **ProductsPortal** отображаются именно в таком порядке, при этом для **ProductsServer** и **ProductsPortal.Azure** назначено действие **Запуск**, а для **ProductsPortal** — действие **Нет**. Например:
        

        ![][25]

    11.  В левой части диалогового окна "Свойства"
        щелкните элемент **ProjectDependencies**.

    12.  В раскрывающемся списке **Проекты** щелкните элемент
        **ProductsServer**. Убедитесь, что флажок **ProductsPortal** не установлен,
        а флажок **ProductsPortal.Azure** установлен. Нажмите кнопку **ОК**.

        ![][26]

    <h2><span class="short-header">ЗАПУСК ПРИЛОЖЕНИЯ</span>ЗАПУСК ПРИЛОЖЕНИЯ</h2>

    1.  В меню **Файл** Visual Studio выберите пункт **Сохранить все**.

    2.  Нажмите клавишу **F5**, чтобы создать и запустить приложение. Первым должен
        запуститься локальный сервер (консольное приложение **ProductsServer**),
        затем приложение **ProductsWeb** должно запуститься в
        окне браузера, как показано на следующем снимке экрана. На этот раз вы сможете проследить получение данных списка складских запасов продукции из локальной системы службы продукции.

        ![][1]

    <h2><span class="short-header">РАЗВЕРТЫВАНИЕ ПРИЛОЖЕНИЯ</span>РАЗВЕРТЫВАНИЕ ПРИЛОЖЕНИЯ В AZURE</h2>

    1. В окне **Обозреватель решений** щелкните правой кнопкой мыши проект
        **ProductsPortal** и выберите пункт **Опубликовать в Azure**.

    2.  Возможно, понадобится выполнить вход для просмотра всех подписок.

        Щелкните **Войти, чтобы просмотреть дополнительные подписки**.

        ![][27]

    3.  Выполните вход с использованием своей учетной записи Майкрософт.


    8.  Нажмите кнопку **Далее**. Если подписка еще не содержит размещенные службы,
        вам будет предложено создать такую службу. Размещенная служба
        выступает в качестве контейнера для приложения в подписке
        Azure. Введите имя, идентифицирующее приложение,
        и выберите регион, для которого необходимо
        оптимизировать приложение. (Можно ожидать ускорения загрузки для пользователей, осуществляющих доступ из этой области.)

        ![][32]

    9.  Выберите размещенную службу, в которую вы хотите опубликовать
        приложение. Для остальных параметров оставьте значения по умолчанию,
        как показано ниже. Нажмите кнопку **Далее**.

        ![][33]

    10. На последней странице нажмите кнопку **Опубликовать** для запуска процесса
        развертывания:

        ![][34]

        Это займет приблизительно 5–7 минут. Поскольку это
        первая публикация, Azure выполняет подготовку
        виртуальной машины (ВМ), повышает уровень безопасности, создает
        на виртуальной машине веб-роль для размещения приложения, развертывает код
        в этой веб-роли и, наконец, настраивает подсистему балансировки нагрузки и
        сетевые функции, чтобы приложение было доступно всем желающим.

    11. Во время выполнения публикации можно будет отслеживать
        операции в окне **Журнал действий Azure**, которое обычно
        прикреплено к нижней части экрана Visual Studio или Visual Web
        Developer:

        ![][35]

    12. После развертывания веб-сайт можно просмотреть,
        щелкнув ссылку **URL-адрес веб-сайта** в окне мониторинга.

        ![][36]

        Правильная работа веб-сайта зависит от локального сервера, поэтому необходимо запустить приложение **ProductsServer** локально для этого
        веб-сайта. При выполнении запросов для облачного веб-сайта
        можно наблюдать поступление запросов в локальное консольное приложение,
        как указано в выходных данных "Вызванный GetProducts" на следующем
        снимке экрана.

        ![][37]

Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в статье [Модели выполнения Azure][executionmodels].

<h2><span class="short-header">УДАЛЕНИЕ ПРИЛОЖЕНИЯ</span>ОСТАНОВКА И УДАЛЕНИЕ ПРИЛОЖЕНИЯ</h2>

После развертывания приложения может потребоваться отключить его, чтобы
можно было построить и развернуть другие приложения в течение 750 свободных
часов серверного времени в месяц (31 день в месяце).

Для экземпляров веб-роли Azure выставляет счета 
за почасовое использование серверного времени. Серверное время используется с момента развертывания приложения,
даже если экземпляры не запущены и находятся в остановленном состоянии.
Бесплатная учетная запись предоставляет 750 часов серверного времени в месяц (31 день в месяце) в виде выделенной
виртуальной машины для размещения этих экземпляров веб-роли.

Ниже показано, как остановить и удалить
приложение.

1.  Выполните вход на  [портал управления Azure],
        щелкните пункт "Облачные службы", а затем щелкните имя своей службы.

2.  Выберите вкладку **Панель мониторинга**, а затем щелкните **Прервать**, чтобы временно остановить работу приложения. Его можно
        будет снова запустить, щелкнув элемент "Запуск". Щелкните **Удалить**,
        чтобы полностью удалить приложение из Azure
        без возможности восстановления.

	![][43]

<h2><a name="nextsteps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>  

Дополнительные сведения о Service Bus см. на следующих ресурсах.  
  
* [Azure Service Bus][sbmsdn]  
* [Практические руководства по работе со службой Service Bus][sbwacom]  
* [Использование очередей Service Bus][sbwacomqhowto]  


  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  
  
  [Портал управления Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  
  
  
  [Использование пакета NuGet для Service Bus]: http://go.microsoft.com/fwlink/?LinkId=234589
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

  [sbmsdn]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee732537.aspx  
  [sbwacom]: /ru-ru/manage/services/service-bus/  
  [sbwacomqhowto]: /ru-ru/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/ru-ru/develop/net/fundamentals/compute/

