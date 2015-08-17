<properties
	pageTitle="Учебник по Azure «Многоуровневое приложение .NET»"
	description="Учебник, посвященный разработке многоуровневого приложения в Azure, которое использует очереди Service Bus для взаимодействия между уровнями. Примеры на .NET."
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
	ms.topic="hero-article"
	ms.date="07/02/2015"
	ms.author="sethm"/>

# Многоуровневое приложение .NET, использующее очереди служебной шины

## Введение

Разработка для Microsoft Azure очень проста благодаря применению Visual Studio 2013 и бесплатного пакета SDK для Azure для .NET. Если у вас еще нет приложения Visual Studio 2013, пакет SDK автоматически установит Visual Studio Express, что позволит вам начать разработку приложений для Azure абсолютно бесплатно. В данном руководстве предполагается, что у вас нет опыта использования Azure. По завершении этого учебника вы создадите приложение, которое использует несколько ресурсов Azure в локальной среде и демонстрирует принципы работы многоуровневого приложения.

Вы узнаете:

-   Как подготовить свой компьютер к разработке приложений для Azure, загрузив и установив всего один пакет.
-   Как использовать Visual Studio для разработки под Azure.
-   Как создать многоуровневое приложение в Azure с использованием рабочей роли и веб-роли.
-   Как реализовать связь между уровнями с использованием очередей Service Bus.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

В этом учебнике вы создадите и запустите многоуровневое приложение в облачной службе Azure. Интерфейсная часть будет реализована с использованием веб-роли MVC ASP.NET, а серверная — с помощью рабочей роли. Вы также можете создать аналогичное многоуровневое приложение, интерфейсная часть которого будет реализована в виде веб-проекта и развернута на веб-сайте Azure, а не в облачной службе. Дополнительную информацию об отличиях при реализации интерфейсной части для веб-сайта Azure см. в разделе [Дальнейшие действия](#nextsteps).

Снимок экрана готового приложения приведен ниже.

![][0]

**Примечание.** В Azure также предоставляются функциональные возможности очереди хранилища. Дополнительную информацию об очередях хранилища Azure и очередях Service Bus см. в статье [Очереди Azure и очереди Azure Service Bus — сравнение и противопоставление][sbqueuecomparison].

## Обзор сценария: обмен данными между ролями

Чтобы отправить запрос на обработку, компоненту пользовательского интерфейса, выполняющемуся в веб-роли, требуется взаимодействовать с логикой среднего уровня в рабочей роли. В этом примере в качестве посредника при обмене данными между уровнями выступает служба Service Bus.

Обмен сообщениями между веб-уровнем и средним уровнем через посредника позволяет разделить два компонента. В отличие от прямого обмена сообщениями (TCP или HTTP), веб-уровень не связан непосредственно со средним уровнем. Вместо этого он передает рабочие единицы в виде сообщений в службу Service Bus, в которой они хранятся до тех пор, пока не будут востребованы для обработки средним уровнем.

В службе Service Bus обмен сообщениями через посредника реализуется с использованием двух видов сущностей — очередей и тем. При использовании очередей каждое сообщение, помещенное в очередь, потребляется одним получателем. С помощью тем реализуется модель публикации и подписки, благодаря которой каждое опубликованное сообщение становится доступно всем компонентам, зарегистрировавшим подписку на тему. Для каждой подписки ведется собственная логическая очередь сообщений. Кроме того, для подписки можно настроить правила фильтрации, с помощью которых ограничивается набор сообщений, передаваемых в очередь подписки. В данном примере используются очереди Service Bus.

![][1]

По сравнению с прямым обменом сообщениями подобный механизм взаимодействия обладает целым рядом преимуществ:

-   **Временное разделение.** В асинхронной модели обмена отправители и получатели сообщений не обязательно должны находиться в сети одновременно. Служба Service Bus хранит сообщения до тех пор, пока принимающая сторона не будет готова принять их. Это позволяет разделять компоненты распределенного приложения как в плановых ситуациях, например для обслуживания, так и в экстренных случаях, чтобы исключить влияние сбоя одного компонента на систему в целом. Более того, потребляющему приложению достаточно находиться в сети несколько раз в день в определенное время.

-   **Выравнивание нагрузки.** Во многих приложениях уровень нагрузки на систему меняется с течением времени, тогда как время, затрачиваемое на обработку каждой рабочей единицы, как правило, постоянно. Благодаря обмену сообщениями через посредника с использованием очереди потребляющее приложение (рабочая роль) достаточно подготовить для обработки средней, а не пиковой нагрузки. В таких случаях при колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет заметно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения.

-   **Балансировка нагрузки.** По мере возрастания нагрузки могут потребоваться дополнительные рабочие процессы для чтения из очереди. Каждое сообщение обрабатывается одним рабочим процессом. Кроме того, балансировка нагрузки по запросу обеспечивает оптимальное использование ресурсов рабочих машин с разной вычислительной мощностью, поскольку они смогут извлекать сообщения с максимально доступной им скоростью. Такая концепция часто называется моделью конкурирующих потребителей.

    ![][2]

В следующих разделах описывается код, позволяющий реализовать подобную архитектуру.

## Настройка среды разработки

Прежде чем начать разработку приложения для Azure, необходимо загрузить нужные инструменты и настроить среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, нажмите кнопку ниже.

    [Получить инструменты и SDK][]

2. 	Щелкните ссылку, соответствующую используемой версии Visual Studio. В этом учебнике приводятся инструкции для работы с версией Visual Studio 2013.

	![][32]

4. 	При появлении запроса на выполнение или сохранение файла установки нажмите **Выполнить**:

    ![][3]

5.  В окне установщика веб-платформы щелкните **Установить** и продолжайте установку.

    ![][33]

6.  После завершения установки вы получите все компоненты, необходимые для начала разработки. В состав пакета SDK входят инструменты для разработки приложений Azure в Visual Studio. Если у вас не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия Visual Studio Express for Web.

## Настройка пространства имен Service Bus

На следующем шаге необходимо будет создать пространство имен службы и получите ключ Shared Access Signature (SAS). Пространство имен службы определяет границы каждого приложения, предоставляемого через Service Bus. Ключ SAS автоматически создается системой при создании пространства имен. Сочетание пространства имен службы и ключа SAS определяет учетные данные, на основе которых Service Bus осуществляет аутентификацию при доступе к приложению.

Заметьте, что для управления пространствами имен и сущностями обмена сообщениями Service Bus можно также использовать обозреватель серверов Visual Studio, но новые пространства имен можно создавать только с портала.

### Настройка пространства имен с помощью портала управления

1.  Выполните вход на [портал управления Azure][].

2.  В левой области навигации портала управления нажмите кнопку **Service Bus**.

3.  В нижней части портала управления нажмите кнопку **Создать**.

    ![][6]

4.  В диалоговом окне**Добавление нового пространства имен** введите имя пространства имен. Система немедленно проверит, доступно ли это имя. ![][7]

5.  Убедившись, что имя пространства имен доступно, выберите страну или регион, в котором будет размещено ваше пространство имен (необходимо указать страну и регион развертывания своих вычислительных ресурсов). Кроме того, убедитесь, что выбраны настройки **Обмен сообщениями** в поле **Тип** и **Стандартный** в поле **Уровень обмена сообщениями**.

    ВАЖНО! Выберите **тот же регион**, который собираетесь выбрать для развертывания приложения. Это обеспечит наилучшую производительность.

6.  Щелкните значок галочки. Теперь система создает пространство имен службы и включает его. Возможно, вам придется подождать несколько минут, пока система выделит ресурсы для вашей учетной записи.

	![][27]

7.  Щелкните имя созданного пространства имен службы в главном окне.

	![][30]

8. Нажмите кнопку **Сведения о подключении**

	![][31]

9.  В области **Сведения о доступе к подключению** найдите строку подключения, которая содержит ключ SAS и имя ключа.

    ![][35]

10.  Запишите эти учетные данные или скопируйте их в буфер обмена.

## Создание веб-роли

В этом разделе описывается построение интерфейсной части вашего приложения. Сначала вы создадите различные страницы, которые будут отображаться в приложении. После этого необходимо добавить код для отправки элементов в очередь Service Bus и отображения сведений о ее состоянии.

### Создание проекта

1.  Используя привилегии администратора, запустите Microsoft Visual Studio 2013 или Microsoft Visual Studio Express. Для запуска Visual Studio с привилегиями администратора щелкните правой кнопкой мыши **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите **Запуск от имени администратора**. Для работы эмулятора среды выполнения приложений Azure, который обсуждается в последующих разделах этого руководства, требуется запустить Visual Studio с привилегиями администратора.

    В меню **Файл** Visual Studio выберите **Создать**, а затем **Проект**.

    ![][8]


2.  В разделе **Установленные шаблоны** области **Visual C#** щелкните **Облако**, а затем щелкните **Облачная служба Azure**. Присвойте проекту имя **MultiTierApp**. Нажмите кнопку **ОК**.

    ![][9]

3.  В ролях **.NET Framework 4.5** дважды щелкните **Веб-роль ASP.NET**.

    ![][10]

4.  Наведите указатель мыши на элемент **WebRole1** в разделе **Решение облачной службы Azure**, щелкните значок карандаша и переименуйте веб-роль в **FrontendWebRole**. Нажмите кнопку **ОК**. (Обратите внимание на написание слова «Frontend» со строчной буквой «e» — не «FrontEnd».)

    ![][11]

5.  В диалоговом окне **Новый проект ASP.NET**, в списке **Выберите шаблон** щелкните **MVC** и нажмите кнопку **ОК**.

    ![][12]

6.  В **Обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Управление пакетами NuGet...** или **Добавить ссылку на пакет библиотеки**.

7.  В левой части диалогового окна выберите **Интернет**. Выполните поиск «**служебная шина**» и выберите пункт **Служебная шина Microsoft Azure**. Выполните установку и закройте это диалоговое окно.

    ![][13]

8.  Заметьте, что добавлены ссылки на требуемые клиентские сборки, а также новые файлы кода.

9.  В **Обозревателе решений** щелкните правой кнопкой мыши **Модели**, выберите **Добавить**, а затем щелкните **Класс**. В поле **Имя** введите имя **OnlineOrder.cs**. Нажмите кнопку **Добавить**.

### Написание кода своей веб-роли

В этом разделе вы создадите различные страницы, которые будут отображаться в приложении.

1.  В файле **OnlineOrder.cs** в Visual Studio замените существующее определение пространства имен следующим кодом:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  В **Обозревателе решений** дважды щелкните **Controllers\\HomeController.cs**. Добавьте в начало файла следующие инструкции **using**, чтобы включить пространства имен для созданной модели и службы Service Bus:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Кроме того, в файле **HomeController.cs** в Visual Studio замените существующее определение пространства имен следующим кодом. Этот код содержит методы для обработки отправки элементов в очередь.

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and
				// cross-site request forgery  
    			[ValidateAntiForgeryToken]
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  В меню **Сборка** щелкните **Собрать решение** для проверки корректности работы на текущий момент.

5.  Далее необходимо создать представление для ранее созданного метода **Submit()**. Щелкните метод Submit() правой кнопкой мыши и выберите **Добавить представление**.

    ![][14]

6.  Откроется диалоговое окно создания представления. В раскрывающемся списке **Шаблон** выберите **Создать**. В раскрывающемся списке **Класс модели** выберите класс **OnlineOrder**.

    ![][15]

7.  Щелкните **Добавить**.

8.  Укажите новое отображаемое имя приложения. В **обозревателе решений** дважды щелкните файл **Views\\Shared\\\_Layout.cshtml**, чтобы открыть его в редакторе Visual Studio.

9.  Замените все вхождения элемента **Мое приложение ASP.NET** текстом **Продукты LITWARE**.

10. Удалите ссылки **Главная**, **О программе** и **Связь**. Удалите выделенный код.

	![][28]

11. Наконец, добавьте на страницу отправки необходимые сведения об очереди. В **Обозревателе решений** дважды щелкните файл **Views\\Home\\Submit.cshtml**, чтобы открыть его в редакторе Visual Studio. Добавьте следующую строку после **&lt;h2>Submit&lt;/h2>**. Сейчас элемент **ViewBag.MessageCount** пуст. Вы заполните его позже.

        <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>


12. Пользовательский интерфейс реализован. Нажмите клавишу **F5**, чтобы запустить приложение и убедиться в его работоспособности.

    ![][17]

### Написание кода для отправки элементов в очередь Service Bus

Далее вам необходимо добавить код, реализующий отправку элементов в очередь. Сначала требуется создать класс, содержащий информацию о подключении к очереди служебной шины. Далее следует инициализировать подключение из **Global.aspx.cs**. Наконец, необходимо обновить код, созданный ранее в файле **HomeController.cs**, чтобы реализовать фактическую отправку элементов в очередь служебной шины.

1.  В обозревателе решений щелкните правой кнопкой мыши **FrontendWebRole** (проект, а не роль). Нажмите кнопку **Добавить** и выберите **Класс**.

2.  Присвойте классу имя **QueueConnector.cs**. Нажмите кнопку **Добавить**, чтобы создать класс.

3.  Теперь необходимо добавить код, который инкапсулирует сведения о подключении и инициализирует подключение к очереди Service Bus. В QueueConnector.cs добавьте следующий код и введите значения **Namespace** (пространство имен службы) и **yourKey** (ваш ключ), который представляет собой ключ SAS, полученный ранее с [портала управления Azure][Azure Management Portal].

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    Обратите внимание, что позже в этом учебнике вы узнаете, как сохранить имя вашего **пространства имен** и значение ключа SAS в файле конфигурации.

4.  Теперь убедитесь, что ваш метод **Инициализация** вызывается. В **обозревателе решений** дважды щелкните **Global.asax\\Global.asax.cs**.

5.  Добавьте в конец метода **Application\_Start** следующую строку:

        FrontendWebRole.QueueConnector.Initialize();

6.  Наконец, обновите ранее созданный код веб-уровня, чтобы отправить элементы в очередь. В **Обозревателе решений** дважды щелкните **Controllers\\HomeController.cs**.

7.  Внесите следующие изменения в метод **Submit()**, чтобы реализовать счетчик сообщений очереди:

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Внесите следующие изменения в метод **Submit(OnlineOrder order)**, чтобы отправить сведения о заказе в очередь:

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);

                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Теперь можно снова запустить приложение. Каждый раз при отправке заказа значение счетчика сообщений увеличивается.

    ![][18]

## Диспетчер конфигурации облака

Метод **GetSettings** в классе **Microsoft.WindowsAzure.Configuration.CloudConfigurationManager** позволяет считывать параметры конфигурации из хранилища конфигураций для вашей платформы. Например, если код выполняется в веб-роли или рабочей роли, метод **GetSettings** считывает файл ServiceConfiguration.cscfg, а если код выполняется в стандартном консольном приложении, метод **GetSettings** считывает файл app.config.

Если сохранить строку подключения для пространства имен Service Bus в файле конфигурации, можно использовать метод **GetSettings** для чтения строки подключения, с помощью которой можно создать экземпляр объекта **NamespaceMananger**. Экземпляр **NamespaceMananger** можно использовать для программной настройки пространства имен Service Bus. С помощью той же строки подключения можно создавать экземпляры клиентских объектов (таких как **QueueClient**, **TopicClient** и **EventHubClient**), которые можно использовать для выполнения операций времени выполнения, например отправки и получения сообщений.

### Строка подключения

Чтобы создать экземпляр клиента (например, Service Bus **QueueClient**), можно представить сведения о конфигурации в виде строки подключения. На стороне клиента используется метод `CreateFromConnectionString()`, который создает экземпляр клиента такого типа с помощью заданной строки подключения. Рассмотрим следующий раздел конфигурации.

	<ConfigurationSettings>
    ...
    	<Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=RootManageSharedAccessKey;SharedSecretValue=[yourKey]" />
	</ConfigurationSettings>

Следующий код получает строку подключения, создает очередь и инициализирует подключение к ней.

	QueueClient Client;

	string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString);

	if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

	// Initialize the connection to Service Bus Queue
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

Код в следующем разделе использует класс **CloudConfigurationManager**.

## Создание рабочей роли

Сейчас вы создадите рабочую роль, которая будет обрабатывать отправку заказов. В этом примере используется шаблон проекта Visual Studio **Рабочая роль с очередью Service Bus**. Сначала вы получите необходимые учетные данные с использованием Обозревателя серверов в Visual Studio.

1. Убедитесь, что вы подключили Visual Studio к своей учетной записи Azure, как описано в разделе «Управление пространствами имен и сущностями обмена сообщениями с помощью Обозревателя серверов Visual Studio».

2.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши папку **Roles** (Роли) в проекте **MultiTierApp**.

3.  Щелкните **Добавить** и выберите **Создать проект рабочей роли**. Откроется диалоговое окно **Добавить новый проект роли**.

	![][26]

4.  В диалоговом окне **Добавить новый проект роли** щелкните элемент **Рабочая роль с очередью Service Bus** (см. следующий рисунок).

	![][23]

5.  В поле **Имя** введите **OrderProcessingRole**. Нажмите кнопку **Добавить**.

6.  В обозревателе сервера щелкните правой кнопкой мыши имя пространства имен службы, а затем выберите **Свойства**. В области **Свойства** Visual Studio первая запись определяет строку подключения, которая содержит конечную точку пространства имен с необходимыми для авторизации учетными данными. См. пример на следующем рисунке. Дважды щелкните **ConnectionString**, а затем нажмите клавиши **Ctrl+C**, чтобы скопировать строку в буфер обмена.

	![][24]

7.  В обозревателе решений щелкните правой кнопкой мыши элемент **OrderProcessingRole**, созданный на шаге 5. Обратите внимание, что правой кнопкой мыши необходимо щелкнуть элемент **OrderProcessingRole** в разделе **Roles** (Роли), а не класс. Выберите пункт **Свойства**.

8.  На вкладке **Настройки** диалогового окна **Свойства** щелкните внутри поля **Значение** элемента **Microsoft.ServiceBus.ConnectionString** и вставьте значение конечной точки, скопированное на шаге 6.

	![][25]

9.  Создайте класс **OnlineOrder**, который будет представлять заказы из очереди по мере их обработки. При необходимости используйте ранее созданный класс. В обозревателе решений щелкните правой кнопкой мыши проект **OrderProcessingRole** (проект, а не роль). Выберите команду **Добавить**, а затем — **Существующий элемент**.

10. Перейдите к вложенной папке **FrontendWebRole\\Models** и дважды щелкните файл **OnlineOrder.cs**, чтобы добавить его в проект.

11. В файле WorkerRole.cs замените значение переменной **QueueName** в **WorkerRole.cs** с `"ProcessingQueue"` на `"OrdersQueue"`, как указано в следующем коде:

		// The name of your queue
		const string QueueName = "OrdersQueue";

12. Добавьте в начало файла WorkerRole.cs следующую инструкцию using:

		using FrontendWebRole.Models;

13. В функции`Run()` внутри вызова `OnMessage` добавьте следующий код в предложение `try`:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

14. Приложение готово. Готовое приложение можно протестировать, щелкнув правой кнопкой мыши проект MultiTierApp в обозревателе решений, выбрав команду **Назначить запускаемым проектом**, а затем нажав клавишу F5. Обратите внимание, что значение счетчика сообщений не увеличивается, поскольку рабочая роль обрабатывает элементы из очереди и помечает их выполненными. Результаты трассировки рабочей роли вы можете просмотреть в пользовательском интерфейсе эмулятора среды выполнения приложений Azure. Для этого щелкните правой кнопкой мыши значок эмулятора в области уведомлений на панели задач и выберите **Показать пользовательский интерфейс эмулятора вычислений**.

    ![][19]

    ![][20]

## Дальнейшие действия  

Дополнительную информацию о Service Bus см. на следующих ресурсах.

* [Azure Service Bus][sbmsdn]  
* [Страница службы Service Bus][sbwacom]  
* [Использование очередей Service Bus][sbwacomqhowto]  

Дополнительную информацию о многоуровневых сценариях и развертывании приложений в облачной службе см. в следующей статье.

* [Многоуровневое приложение .NET, использующее таблицы, очереди и BLOB-объекты хранилища][mutitierstorage].  

В некоторых случаях требуется реализовать интерфейсную часть многоуровневого приложения на веб-сайте Azure, а не в облачной службе Azure. Дополнительную информацию о различиях между веб-узлами и облачными службами см. в статье [Модели выполнения Azure][executionmodels].

Если вы хотите реализовать проект, описываемый в этом учебнике, в виде стандартного веб-проекта, а не веб-роли облачной службы, повторите приведенные здесь шаги, но обратите внимание на следующие отличия.

1. При создании проекта выберите шаблон **Веб-приложение ASP.NET MVC 4** в категории **Интернет** вместо шаблона **Облачная служба** в категории **Облако**. Выполните инструкции по созданию приложения MVC вплоть до раздела **Диспетчер конфигурации облачного приложения**.

2. Создайте рабочую роль в новом отдельном решении, как изначально описывается в инструкциях к веб-роли. Далее в этом случае вы создаете только рабочую роль в проекте облачной службы. Выполните остальные инструкции по созданию рабочей роли.

3. Вы можете протестировать интерфейсную и серверную части по отдельности или запустить их одновременно в разных экземплярах Visual Studio.

Дополнительную информацию о развертывании приложения на веб-сайте Azure см. в разделе [Развертывание веб-приложения ASP.NET на веб-сайте Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Дополнительную информацию о развертывании серверной части в облачной службе Azure см. в статье [Многоуровневое приложение .NET, использующее таблицы, очереди и BLOB-объекты хранилища][mutitierstorage].


  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/library/hh767287.aspx
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png



  [Azure Management Portal]: http://manage.windowsazure.com
  [портал управления Azure]: http://manage.windowsazure.com
  [6]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [35]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/
  [mutitierstorage]: /develop/net/tutorials/multi-tier-web-site/1-overview/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/
 

<!---HONumber=August15_HO6-->