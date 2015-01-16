<properties urlDisplayName="Hybrid Application" pageTitle="Гибридные локальные и облачные приложения (.NET) в Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Узнайте, как создать приложение локальное или облачное гибридное приложение .NET с использованием ретранслятора Azure Service Bus." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />






# Создание локального или облачного гибридного приложения .NET с использованием ретрансляции служебной шины

<h2>ВВЕДЕНИЕ</h2>

Разрабатывать гибридные облачные приложения в Azure очень просто с помощью
Visual Studio 2013 и бесплатного пакета SDK для Azure для .NET. В этом руководстве
предполагается, что у вас нет опыта использования платформы Azure. Менее чем за
30 минут вы получите приложение, которое использует множество
ресурсов Azure и выполняется в облаке.

Вы узнаете:

-   Как создать или адаптировать существующую веб-службу, которая будет использоваться
    веб-решением.
-   Как использовать Azure Service Bus для обмена данными между
    приложением Azure и веб-службой, размещенной в другом месте.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### КАК РЕТРАНСЛЯЦИЯ СЛУЖЕБНОЙ ШИНЫ ПОМОГАЕТ РАБОТАТЬ С ГИБРИДНЫМИ РЕШЕНИЯМИ

Бизнес-решения обычно сочетают в себе пользовательский
код, написанный для удовлетворения новых и уникальных бизнес-требований, и существующие
функциональные возможности, предоставленные используемыми решениями и системами
.

Архитекторы решений начинают все чаще использовать облака, чтобы упростить соблюдение
требований к масштабированию и снизить эксплуатационные расходы. Используя такой подход, они поняли,
что существующие ресурсы служб, которые они хотели бы использовать в качестве стандартных блоков
для своих решений, обычно размещены в пределах корпоративного брандмауэра и легко
доступны для облачного решения. Многие внутренние службы создаются и размещаются
таким образом, что это не позволяет максимально эффективно использовать
ресурсы корпоративной сети.

*Ретранслятор служебной шины* предназначен для следующего варианта использования:
к существующим веб-службам Windows Communication Foundation (WCF)
предоставляется безопасный доступ для решений, которые находятся за пределами
корпоративной сети, таким образом, чтобы не требовалось вносить существенные изменения в организацию
сетевой инфраструктуры. Службы ретрансляции служебной шины по-прежнему размещаются
внутри существующей среды, однако им поручается прослушивать
входящие сеансы и запросы к размещенной в облаке среде Service Bus.
Service Bus также защищает эти службы от несанкционированного доступа
с использованием контроля доступа Azure Active Directory.

### СЦЕНАРИЙ РЕШЕНИЙ

В этом учебнике вы создадите веб-сайт ASP.NET MVC 4, который
позволит просмотреть список продуктов на странице складских запасов.

![][0]

Учебник предполагает, что информация о продуктах содержится в существующей
локальной системе и для доступа к ней используется ретранслятор служебной шины.
 Такое поведение системы моделируется веб-службой, которая представляет собой простое
консольное приложение с предварительно заданным набором продуктов. Вы
сможете запустить данное консольное приложение на локальном компьютере и
выполнить развертывание веб-роли в Azure. Это позволит увидеть, как
веб-роли из центра обработки данных Azure на самом деле обращаются 
к вашему компьютеру, даже если он
находится по крайней мере за одним брандмауэром и слоем преобразования сетевых адресов
(NAT).

Снимок экрана начальной страницы готового веб-приложения приведен ниже.

![][1]

<h2>НАСТРОЙКА СРЕДЫ РАЗРАБОТКИ</h2>

Прежде чем начать разработку своего приложения Azure, вам нужно
убедиться в наличии всех инструментов и настроить среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, нажмите кнопку ниже.

    [Получить инструменты и SDK][]

2. 	Щелкните **Install the SDK** (Установить пакет SDK).

3. 	Выберите ссылку, соответствующую используемой версии Visual Studio. В этом учебнике приводятся указания для работы с Visual Studio 2013:

	![][42]

4.  При появлении запроса на выполнение или сохранение файла **WindowsAzureSDKForNet.exe** щелкните
    **Выполнить**:

    ![][2]

5.  В окне установщика веб-платформы щелкните **Установить** и продолжайте установку:

    ![][3]

6.  После завершения установки у вас будет все
    необходимое, чтобы приступить к разработке. Пакет SDK включает в себя инструменты, позволяющие
    легко разрабатывать приложения Azure в Visual Studio. Если у вас
    не установлено приложение Visual Studio, он также устанавливает бесплатное приложение
    Visual Studio Express.

<h2>СОЗДАНИЕ ПРОСТРАНСТВА ИМЕН СЛУЖБЫ</h2>

Чтобы начать использовать функций Service Bus в Azure, необходимо
создать пространство имен службы. Пространство имен службы предоставляет контейнер области
для адресации ресурсов Service Bus из вашего приложения. 

Вы можете управлять пространствами имен и сущностями обмена сообщениями Service Bus с помощью [портала управления Azure][] или обозревателя сервера Visual Studio, однако новые пространства имен можно создавать только с помощью портала.

###Чтобы создать пространство имен службы с помощью портала:

1.  Выполните вход на [портал управления Azure][].

2.  В левой области навигации портала управления щелкните
    **Service Bus**.

3.  В нижней области портала управления щелкните **Создать**.   
    ![][5]

4.  В диалоговом окне **Добавить новое пространство имен** введите имя пространства имен.
    Система немедленно проверяет, доступно ли оно.   
    ![][6]

5.  Убедившись, что пространство имен доступно, выберите
    страну или регион, где оно должно быть размещено (это должна быть
    страна или регион, где вы развертываете свои
    вычислительные ресурсы).

    ВАЖНО! Выберите **тот же регион**, который собираетесь выбрать
    для развертывания приложения. Это обеспечит наилучшую производительность.

6.	Установите флажок. Система создаст ваше пространство имен службы
    и включит его. Может потребоваться подождать несколько минут, пока
    система подготовит к работе ресурсы для вашей учетной записи.

	![][38]

Созданное пространство имен появится на портале управления.
Для его активации может потребоваться некоторое время. Прежде чем продолжить, дождитесь изменения состояния на **Активно**
.

<h2>ПОЛУЧЕНИЕ УЧЕТНЫХ ДАННЫХ УПРАВЛЕНИЯ ПО УМОЛЧАНИЮ ДЛЯ ПРОСТРАНСТВА ИМЕН</h2>

Для выполнения операций управления, таких как создание очереди для нового пространства имен,
необходимо получить учетные данные управления для
пространства имен.

1.  Щелкните имя созданного пространства имен службы в главном окне.   

	![][39]
  

2.  Щелкните **Сведения о подключении**.   

	![][40]


3.  В области **Сведения по доступу к подключению** найдите записи **Издатель по умолчанию** и **Ключ по умолчанию**.   
    

4.  Запишите ключ или скопируйте его в буфер обмена.

###Управление пространством имен службы с помощью обозревателя серверов Visual Studio

Чтобы вместо портала управления для управления пространством имен и получения сведений о подключении использовать Visual Studio, выполните процедуру, описанную [здесь](http://http://msdn.microsoft.com/ru-ru/library/windowsazure/ff687127.aspx), в разделе **Подключение к Azure из Visual Studio**. При входе в Azure узел **Service Bus** в дереве **Microsoft Azure** в обозревателе серверов автоматически заполняется уже созданными пространствами имен. Щелкните правой кнопкой мыши любое пространство имен и выберите **Свойства**, чтобы увидеть строку подключения и другие метаданные, связанные с этим пространством имен, которые отображаются в области **Свойства** Visual Studio. 

![][44]

Запишите значение **SharedAccessKey** или скопируйте его в буфер обмена.


<h2>СОЗДАНИЕ ЛОКАЛЬНОГО СЕРВЕРА</h2>

В первую очередь нужно создать (макетную) локальную систему каталогов продукции. Это
будет довольно просто. Фактически, это будет локальная система каталога продукции
с полным представлением служб,
которые мы пытаемся интегрировать.

Этот проект будет запущен как консольное приложение Visual Studio. 
Проект использует пакет NuGet для Service Bus для включения библиотек
и параметров конфигурации Service Bus. Расширение NuGet для Visual Studio
упрощает установку и обновление библиотек и инструментов в Visual Studio
и Visual Studio Express. Пакет NuGet для Service Bus - это наиболее простой
способ получить доступ к API Service Bus и настроить свое приложение с
учетом всех зависимостей Service Bus. Дополнительную информацию об использовании NuGet и
пакета Service Bus см. в разделе [Использование пакета NuGet для Service Bus][].

### СОЗДАНИЕ ПРОЕКТА

1.  Запустите с привилегиями администратора Microsoft Visual
    Studio 2013 или Microsoft Visual Studio Express. Чтобы
    запустить Visual Studio с привилегиями администратора, щелкните правой кнопкой мыши
    **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите **Запуск от имени администратора**.
2.  В меню **Файл** Visual Studio выберите **Создать** и 
    щелкните **Проект**.

    ![][10]

3.  В разделе **Установленные шаблоны** области **Visual C#** щелкните элемент "Консольное
    приложение". В поле **Имя** введите имя
    **ProductsServer**:

    ![][11]

4.  Нажмите кнопку **ОК**, чтобы создать проект **ProductsServer**.

5.  В **обозревателе решений** щелкните правой кнопкой мыши элемент **ProductsServer** и
    выберите пункт **Свойства**.
6.  Перейдите на вкладку **Приложение** в левой части экрана, а затем убедитесь, что ".NET
    Framework 4" или **.NET Framework 4.5** отображается в раскрывающемся списке **Требуемая версия .NET Framework:**  . Если нет, выберите соответствующий пункт из раскрывающегося списка и затем нажмите кнопку **Да**
    при появлении запроса на перезагрузку проекта.

    ![][12]

7.   Если вы уже установили диспетчер пакетов NuGet для Visual Studio, пропустите следующий шаг. В противном случае посетите сайт [NuGet][] и щелкните элемент [Установить NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Следуйте указаниям на экране, чтобы установить диспетчер пакетов NuGet, а затем перезапустите Visual Studio.

7.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки**, а затем
    **Управление пакетами NuGet**...
8.  В левом столбце диалогового окна NuGet щелкните элемент **В сети**.

9. 	В правом столбце щелкните поле **Поиск**, введите **MicrosoftAzure** и выберите **
    Azure Service Bus**. Для завершения установки щелкните **Установить**
    и затем закройте данное диалоговое окно.

    ![][13]

    Обратите внимание, что на необходимые клиентские сборки теперь имеется ссылка.

9.   Добавьте новый класс для контракта на свою продукцию. В **обозревателе решений**
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

13. В **обозревателе решений** дважды щелкните файл **app.config**, чтобы
    открыть его в редакторе **Visual Studio**. Замените содержимое
    **<<system.ServiceModel>** следующим XML-кодом. Не забудьте
    заменить имя службы *yourServiceNamespace* именем
    пространства имен своей службы, а *yourIssuerSecret* - ключом, полученным ранее
    на портале управления Azure:

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

14. Нажмите клавишу **F6** или щелкните элемент **Построить решение** в меню **Сборка**, чтобы выполнить сборку приложения и убедиться, что все действия выполнены правильно.

<h2>СОЗДАНИЕ ПРИЛОЖЕНИЯ ASP.NET MVC</h2>

В этом разделе вы создадите простое приложение ASP.NET, которое будет
отображать данные, полученные из службы продуктов.

### СОЗДАНИЕ ПРОЕКТА

1.  Убедитесь, что система Microsoft Visual Studio 2013 запущена с правами администратора. В противном случае,
    чтобы запустить Visual Studio с привилегиями администратора, щелкните правой кнопкой мыши
    **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите пункт **Запуск от имени администратора**. Эмулятор вычислений Microsoft
    Azure, который будет описан далее в этом руководстве, требует
    запуска Visual Studio с привилегиями администратора.

2.  В меню **Файл** Visual Studio выберите **Создать** и 
    щелкните **Проект**.

3.  В разделе **Установленные шаблоны** области **Visual C#** щелкните элемент **Веб-приложение ASP.NET**. Присвойте проекту имя **ProductsPortal**. Затем
    нажмите кнопку **ОК**.

    ![][15]

4.  В списке **Выбор шаблона** щелкните **MVC**
    и нажмите кнопку **ОК**.

    ![][16]

5.  В **обозревателе решений** щелкните правой кнопкой мыши **Модели**, выберите **Добавить**
    и щелкните **Класс**. В поле **Имя** введите имя
    **Product.cs**. Затем нажмите кнопку **Добавить**.

    ![][17]

### ИЗМЕНЕНИЕ ВЕБ-ПРИЛОЖЕНИЯ

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

2.  In the HomeController.cs file in Visual Studio, replace the existing
    namespace definition with the following code:

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

3.  В **обозревателе решений** разверните элемент "Представления\Общие":

    ![][18]

4.  Затем дважды щелкните файл _Layout.cshtml, чтобы открыть его в редакторе Visual Studio.

5.  Замените все вхождения элемента **Мое приложение MVC ASP.NET** текстом **Продукты LITWARE**.

6. Удалите ссылки главную страницу **Домашняя страница**, "О программе" и "Контакт". Удалите выделенный код:

	![][41]

7.  В **обозревателе решений** разверните "Представления\Домашняя страница":

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


9.  Чтобы убедиться, что все действия выполнены правильно, нажмите клавишу **F6** или клавиши
    **Ctrl + Shift + B**, чтобы выполнить сборку проекта.


### ЛОКАЛЬНЫЙ ЗАПУСК ПРИЛОЖЕНИЯ

Запустите приложение, чтобы убедиться, что оно работает.

1.   Убедитесь, что **ProductsPortal** является активным проектом. Щелкните правой кнопкой мыши
    имя проекта в **обозревателе решений** и выберите "Назначить
    запускаемым проектом".
В 2.  **Visual Studio** нажмите клавишу **F5**.
3.  Приложение должно начать выполняться в браузере:

    ![][21]

    <h2>ПОДГОТОВКА ПРИЛОЖЕНИЯ К РАЗВЕРТЫВАНИЮ В AZURE</h2>

    Вы можете развернуть приложение в облачной службе Azure или на веб-сайте Azure. Дополнительную информацию о различиях между веб-сайтами и облачными службами см. в разделе [Модели выполнения Azure][]. Дополнительные сведения о развертывании приложения на веб-узле Azure содержатся в разделе [[Развертывание веб-приложения ASP.NET на веб-сайте Azure](http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/)](http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/). Этот раздел содержит подробные инструкции по развертыванию приложения в облачной службе Azure.

    Чтобы развернуть приложение в облачной службе, необходимо добавить в решение проект развертывания проекта облачной службы.
    Проект развертывания содержит необходимую информацию о конфигурации
    для правильной работы приложения
    в облаке.

    1.  Чтобы сделать возможным развертывание приложения в облаке, щелкните правой кнопкой мыши
        проект **ProductsPortal** в **обозревателе решений**, затем выберите
        **Преобразовать** и **Преобразовать в проект облачной службы Azure**.

        ![][22]

    2.  Чтобы протестировать приложение, нажмите клавишу **F5**.
    3.   Это приведет к запуску эмулятора среды выполнения приложений Azure. Эмулятор
        вычислений использует локальный компьютер для эмуляции запуска вашего приложения
        в Azure. Чтобы убедиться, что эмулятор
        запущен, посмотрите на область уведомлений:

        ![][23]

    4.  Браузер по-прежнему будет показывать, что ваше приложение работает локально,
        и оно будет выглядеть и функционировать таким же образом, как при запуске
        ранее в виде обычного приложения ASP.NET MVC 4.

    <h2>ОБЪЕДИНЕНИЕ ЭЛЕМЕНТОВ</h2>

    Следующим шагом является подключение локального сервера продуктов к
    приложению ASP.NET MVC.

    1.  Если он еще не открыт, откройте повторно в Visual Studio проект
        **ProductsPortal**, который мы создали в разделе "Создание приложения
        ASP.NET MVC".

    2.  Как и на шаге из раздела "Создание локального сервера",
        добавьте в проекте ссылку на пакет NuGet. В
        обозревателе решений щелкните правой кнопкой мыши **Ссылки**, а затем щелкните
        **Управление пакетами NuGet**.

    3.  Выполните поиск "MicrosoftAzure.ServiceBus" и выберите элемент
        Microsoft Azure Service Bus". Завершите установку и
        закройте диалоговое окно.

    4.  В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal**
        и нажмите кнопку **Добавить**, а затем выберите **Существующий элемент**.

    5.  Перейдите к файлу **ProductsContract.cs** из
консольного проекта         **ProductsServer**. Выберите файл
        ProductsContract.cs. Щелкните стрелку вниз рядом с кнопкой **Добавить**, а затем
        щелкните **Добавить как ссылку**.

        ![][24]

    6.  Теперь откройте файл **HomeController.cs** в редакторе Visual Studio
        и замените определение пространства имен следующим
        кодом. Не забудьте заменить имя *yourServiceNamespace* именем
        своего пространства имен службы, а *yourIssuerSecret* - значением своего ключа.
        Это позволит клиенту вызывать локальную службу,
        которая возвращает результат вызова.

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
        , щелкните **Добавить** и затем выберите **Существующий проект**.

    8.  Перейдите к проекту **ProductsServer**, затем дважды щелкните
        файл решения **ProductsServer.csproj**, чтобы добавить его.

    9.  В обозревателе решений щелкните правой кнопкой мыши проект **ProductsPortal**
        решение и выберите **Свойства**.

    10.  В левой части окна выберите **Запускаемый проект**. В
        правой части окна выберите **Несколько запускаемых проектов**. Убедитесь, что
        **ProductsServer**, **ProductsPortal.Azure** и
        **ProductsPortal** отображаются в указанном порядке, при этом свойство **Запустить** назначено
        элементам **ProductsServer** и **ProductsPortal.Azure**,
        а значение **Нет** выбрано для элемента **ProductsPortal**. 
        Пример:

        ![][25]

    11. В диалоговом окне свойств щелкните **ProjectDependencies**
        в левой части окна.

    12. Из раскрывающегося списка **Проекты** выберите элемент
        **ProductsServer**. Убедитесь, что флажок **ProductsPortal** снят
        и установлен флажок **ProductsPortal.Azure**. Нажмите кнопку **ОК**.

        ![][26]

    <h2>ЗАПУСК ПРИЛОЖЕНИЯ</h2>

    1.  В меню **Файл** Visual Studio выберите пункт **Сохранить все**.

    2.   Нажмите клавишу **F5**, чтобы создать и запустить приложение. Локальный
        сервер (консольное приложение **ProductsServer**) должен запуститься первым
        , а затем в окне браузера должно отобразиться приложение **ProductsWeb**
        , как показано на приведенном ниже снимке экрана. На этот раз вы
        увидите данные списков складских запасов, полученные от
        локальной системы службы продуктов.

        ![][1]

    <h2>РАЗВЕРТЫВАНИЕ ПРИЛОЖЕНИЯ В AZURE</h2>

    1.  Щелкните правой кнопкой мыши проект **ProductsPortal** в обозревателе
        решений и щелкните **Опубликовать в Azure**.

    2.  Возможно, понадобится выполнить регистрацию для просмотра всех подписок.

        Щелкните **Sign in to see more subscriptions** (Войти, чтобы просмотреть дополнительные подписки).

        ![][27]

    3.  Выполните регистрацию с использованием своей учетной записи Майкрософт.


    8.  Щелкните **Далее**. Если ваши подписки не содержат размещенных
        служб, вам будет предложено создать службу. Размещенная служба
        выступает в роли контейнера для приложения в подписке
        Microsoft Azure. Введите имя для своего
        приложения и выберите регион, для которого приложение
        необходимо оптимизировать. (Можно ожидать ускорения загрузки для
        пользователей, осуществляющих доступ из этого региона.)

        ![][32]

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

Дополнительную информацию о различиях между веб-сайтами и облачными службами см. в разделе [Модели выполнения Azure][].

<h2>ОСТАНОВКА И УДАЛЕНИЕ ПРИЛОЖЕНИЯ</h2>

После развертывания приложения может понадобиться отключить его,
чтобы можно было выполнить сборку и развернуть другие приложения в пределах свободного
времени сервера исходя из квоты 750 часов в месяц (при 31 дне в месяце).

Azure выставляет счета для экземпляров веб-ролей за использование серверного времени на почасовой основе
. Серверное время используется после развертывания приложения,
даже если экземпляры не запущены и находятся в остановленном состоянии.
Для размещения экземпляров веб-роли виртуальной машине бесплатно выделяется
750 часов серверного времени в месяц (при 31 дне в месяце).

Ниже показано, как останавливать и удалять
приложения.

1.  Войдите на [портал управления Azure],
        щелкните "Облачные службы" и выберите имя своей службы.

2.   Выберите вкладку **Панель мониторинга**, а затем щелкните **Прервать**, чтобы временно остановить работу приложения. Чтобы
        выполнить повторный запуск, щелкните "Запустить". Нажмите кнопку **Удалить**, чтобы полностью удалить приложение из Azure
        без возможности восстановления.

	![][43]

<h2><a name="nextsteps"></a>Дальнейшие действия</h2>  

Дополнительную информацию о Service Bus см. на следующих ресурсах.  
  
* [Azure Service Bus][]  
* [Практические руководства по работе со службой Service Bus][]  
* [Использование очередей Service Bus][]  


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
  [sbwacom]: /ru-ru/documentation/services/service-bus/  
  [sbwacomqhowto]: /ru-ru/develop/net/how-to-guides/service-bus-queues/
  [executionmodels]: http://www.windowsazure.com/ru-ru/develop/net/fundamentals/compute/
