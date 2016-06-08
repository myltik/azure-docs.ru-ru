<properties
	pageTitle="Многоуровневое приложение .NET | Microsoft Azure"
	description="Руководство для .NET, посвященное разработке многоуровневого приложения в Azure, в котором для взаимодействия между уровнями используются очереди служебной шины."
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
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Многоуровневое приложение .NET, использующее очереди служебной шины Azure

## Введение

С Visual Studio и бесплатным пакетом Azure SDK для .NET разрабатывать решения для Microsoft Azure очень просто. Если у вас еще нет приложения Visual Studio, пакет SDK автоматически установит выпуск Visual Studio Express, что позволит вам начать разработку решений для Azure абсолютно бесплатно. Для работы с этот статьей опыт работы с платформой Azure не требуется. Пройдя этот учебник, вы создадите приложение, в котором используется несколько ресурсов Azure, выполняемых в локальной среде, и которое демонстрирует принципы работы многоуровневого приложения.

Вы узнаете:

-   Как подготовить свой компьютер к разработке приложений для Azure, загрузив и установив всего один пакет.
-   Как разработать приложение для Azure с помощью Visual Studio.
-   Как создать многоуровневое приложение в Azure с использованием рабочей роли и веб-роли.
-   Обеспечение обмена данными между уровнями с помощью очередей служебной шины.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

В этом учебнике вы создадите и запустите многоуровневое приложение в облачной службе Azure. Интерфейсная часть будет реализована с использованием веб-роли MVC ASP.NET, а серверная — с помощью рабочей роли. Вы также можете создать аналогичное многоуровневое приложение. Его внешний интерфейс будет реализован в виде веб-проекта, развернутого на веб-сайте Azure, а не в облачной службе. Дополнительные сведения о разных способах реализации внешнего интерфейса веб-сайта Azure см. в разделе [Дальнейшие действия](#nextsteps).

На следующем рисунке показано завершенное приложение.

![][0]

> [AZURE.NOTE] Azure также предоставляет возможности очереди хранилища. Дополнительную информацию об очередях хранилища Azure и очередях Service Bus см. в статье [Очереди Azure и очереди Azure Service Bus — сравнение и противопоставление][sbqueuecomparison].

## Обзор сценария: обмен данными между ролями

Чтобы отправить заказ на обработку, внешнему компоненту с пользовательским интерфейсом, выполняющемуся в веб-роли, необходимо взаимодействовать с логикой среднего уровня в рабочей роли. В этом примере в качестве посредника при обмене данными между уровнями выступает служба Service Bus.

Обмен сообщениями между веб-уровнем и средним уровнем через посредника позволяет разделить два компонента. В отличие от прямого обмена сообщениями (TCP или HTTP), веб-уровень не связан непосредственно со средним уровнем. Вместо этого он передает рабочие единицы в виде сообщений в служебную шину, где они хранятся до тех пор, пока не будут востребованы для обработки средним уровнем.

В службе Service Bus обмен сообщениями через посредника реализуется с использованием двух видов сущностей — очередей и тем. При использовании очередей каждое сообщение, помещенное в очередь, потребляется одним получателем. С помощью тем реализуется модель публикации и подписки, благодаря которой каждое опубликованное сообщение становится доступно всем компонентам, зарегистрировавшим подписку на тему. Для каждой подписки ведется собственная логическая очередь сообщений. Кроме того, для подписки можно настроить правила фильтрации, с помощью которых ограничивается набор сообщений, передаваемых в очередь подписки. В этом примере используются очереди служебной шины.

![][1]

По сравнению с прямым обменом сообщениями этот механизм взаимодействия имеет ряд преимуществ.

-   **Временное разделение.** В асинхронной модели обмена отправители и получатели сообщений не обязательно должны находиться в сети одновременно. Служба Service Bus хранит сообщения до тех пор, пока принимающая сторона не будет готова принять их. Это позволяет разделять компоненты распределенного приложения как в плановых ситуациях (например, для обслуживания), так и в экстренных случаях, чтобы исключить влияние сбоя одного компонента на систему в целом. Более того, потребляющему приложению достаточно находиться в сети несколько раз в день в определенное время.

-   **Выравнивание нагрузки.** Во многих приложениях уровень нагрузки на систему меняется с течением времени, тогда как время, затрачиваемое на обработку каждой рабочей единицы, как правило, постоянно. Благодаря обмену сообщениями через посредника с использованием очереди потребляющее приложение (рабочая роль) достаточно подготовить для обработки средней, а не пиковой нагрузки. При колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет заметно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения.

-   **Балансировка нагрузки.** По мере возрастания нагрузки могут потребоваться дополнительные рабочие процессы для чтения из очереди. Каждое сообщение обрабатывается одним рабочим процессом. Кроме того, балансировка нагрузки по запросу обеспечивает оптимальное использование ресурсов рабочих машин с разной вычислительной мощностью, поскольку каждая машина может извлекать сообщения со своей максимальной скоростью. Такой подход часто называют моделью *конкурирующих потребителей*.

    ![][2]

В следующих разделах описывается код, позволяющий реализовать подобную архитектуру.

## Настройка среды разработки

Прежде чем начать разработку приложения для Azure, необходимо загрузить нужные инструменты и настроить среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, щелкните следующую ссылку.

    [Получить инструменты и SDK][]

2. 	Щелкните ссылку, соответствующую используемой версии Visual Studio. В описанных в этом учебнике примерах используется Visual Studio 2013.

	![][32]

4. 	Когда появится запрос на выполнение или сохранение установочного файла, нажмите кнопку **Выполнить**.

    ![][3]

5.  В установщике веб-платформы щелкните элемент **Установить**, чтобы продолжить установку.

    ![][33]

6.  После завершения установки у вас будет все необходимое, чтобы начать разработку приложения. В состав пакета SDK входят инструменты для разработки приложений Azure в Visual Studio. Если у вас не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия Visual Studio Express for Web.

## Настройка пространства имен служебной шины

Далее нужно создать пространство имен службы и получить ключ подписанного URL-адреса (SAS). Пространство имен определяет границы каждого приложения, предоставляемого через служебную шину. Ключ SAS автоматически создается системой при создании пространства имен. Сочетание пространства имен и ключа SAS дает учетные данные, на основе которых служебная шина осуществляет проверку подлинности и предоставляет доступ к приложению.

### Настройка пространства имен с помощью классического портала Azure

1.  Войдите на [классический портал Azure][].

2.  На портале в области навигации слева щелкните элемент **Служебная шина**.

3.  На портале на панели внизу нажмите кнопку **Создать**.

    ![][6]

4.  На странице **Добавить новое пространство имен** введите имя пространства имен. Система немедленно проверяет, доступно ли оно.

    ![][7]

5.  Убедившись, что имя пространства имен доступно, выберите страну или регион, где будет размещено ваше пространство имен (необходимо указать страну и регион развертывания своих вычислительных ресурсов). Кроме того, убедитесь, что выбраны настройки **Обмен сообщениями** в поле **Тип** и **Стандартный** в поле **Уровень обмена сообщениями**.

    > [AZURE.IMPORTANT] Выберите **тот же регион**, в котором вы собираетесь развертывать свое приложение. Это обеспечит наилучшую производительность.

6.  Нажмите кнопку с флажком «ОК». Теперь система создает пространство имен службы и включает его. Возможно, вам придется подождать несколько минут, пока система выделит ресурсы для вашей учетной записи.

	![][27]

7.  Щелкните имя созданного пространства имен службы в главном окне.

	![][30]

8. Нажмите кнопку **Сведения о подключении**

	![][31]

9.  В области **Сведения о доступе к подключению** найдите строку подключения, которая содержит ключ SAS и имя ключа.

    ![][35]

10.  Запишите эти учетные данные или скопируйте их в буфер обмена.

## Создание веб-роли

В этом разделе описывается создание интерфейсной части вашего приложения. Сначала вы создадите разные страницы, которые будут отображаться в приложении, а затем — добавите код для отправки элементов в очередь служебной шины и отображения сведений о ее состоянии.

### Создание проекта

1.  Используя привилегии администратора, запустите Microsoft Visual Studio 2013 или Microsoft Visual Studio Express. Для запуска Visual Studio с привилегиями администратора щелкните правой кнопкой мыши **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите **Запуск от имени администратора**. Для работы эмулятора вычислений Azure, который обсуждается далее в этой статье, требуется запустить Visual Studio с правами администратора.

    В меню **Файл** Visual Studio выберите **Создать**, а затем **Проект**.

    ![][8]

2.  В разделе **Установленные шаблоны** области **Visual C#** щелкните **Облако**, а затем щелкните **Облачная служба Azure**. Присвойте проекту имя **MultiTierApp**. Нажмите кнопку **ОК**.

    ![][9]

3.  В ролях **.NET Framework 4.5** дважды щелкните **Веб-роль ASP.NET**.

    ![][10]

4.  Наведите указатель мыши на элемент **WebRole1** в разделе **Решение облачной службы Azure**, щелкните значок карандаша и переименуйте веб-роль в **FrontendWebRole**. Нажмите кнопку **ОК**. (Обратите внимание на написание слова «Frontend» со строчной буквой «e» — не «FrontEnd».)

    ![][11]

5.  В диалоговом окне **Новый проект ASP.NET** в области **Выберите шаблон** щелкните элемент **MVC** и нажмите кнопку **ОК**.

    ![][12]

6.  В **обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите элемент **Управление пакетами NuGet** или **Добавить ссылку на пакет библиотеки**.

7.  В левой части диалогового окна выберите элемент **Интернет**. Выполните поиск по фразе **служебная шина** и выберите элемент **Служебная шина Microsoft Azure**. Завершите установку и закройте это диалоговое окно.

    ![][13]

8.  Заметьте, что добавлены ссылки на требуемые клиентские сборки, а также новые файлы кода.

9.  В **обозревателе решений** щелкните правой кнопкой мыши элемент **Модели**, а затем последовательно щелкните пункты **Добавить** и **Класс**. В поле **Имя** введите имя **OnlineOrder.cs**. Нажмите кнопку **Добавить**.

### Написание кода веб-роли

В этом разделе вы создадите разные страницы, которые будут отображаться в приложении.

1.  В файле OnlineOrder.cs в Visual Studio замените существующее определение пространства имен следующим кодом:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  В **Обозревателе решений** дважды щелкните **Controllers\\HomeController.cs**. Добавьте в начало файла инструкцию **using**, чтобы включить служебную шину и пространства имен для созданной модели.

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Кроме того, в файле HomeController.cs в Visual Studio замените существующее определение пространства имен следующим кодом. Этот код содержит методы, обрабатывающие операции отправки элементов в очередь.

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application.
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit.
                // Controller method for a view you will create for the submission
                // form.
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit.
                // Controller method for handling submissions from the submission
                // form.
                [HttpPost]
				// Attribute to help prevent cross-site scripting attacks and
				// cross-site request forgery.  
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

4.  В меню **Сборка** щелкните команду **Собрать решение**, чтобы проверить правильность кода.

5.  Теперь создайте представление для ранее созданного метода **Submit()**. Щелкните правой кнопкой мыши метод **Submit()** и выберите пункт **Добавить представление**.

    ![][14]

6.  Откроется диалоговое окно создания представления. В списке **Шаблон** выберите пункт **Создать**. В списке **Класс модели** выберите класс **OnlineOrder**.

    ![][15]

7.  Щелкните **Добавить**.

8.  Укажите новое отображаемое имя приложения. В **обозревателе решений** дважды щелкните файл **Views\\Shared\\_Layout.cshtml**, чтобы открыть его в редакторе Visual Studio.

9.  Замените все вхождения элемента **Мое приложение ASP.NET** текстом **Продукты LITWARE**.

10. Удалите ссылки **Главная**, **О программе** и **Связь**. Удалите выделенный код.

	![][28]

11. Наконец, добавьте на страницу отправки необходимые сведения об очереди. В **Обозревателе решений** дважды щелкните файл **Views\\Home\\Submit.cshtml**, чтобы открыть его в редакторе Visual Studio. Добавьте следующую строку после **&lt;h2>Submit&lt;/h2>**. Сейчас элемент **ViewBag.MessageCount** пуст. Вы заполните его позже.

        <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>

12. Пользовательский интерфейс реализован. Нажмите клавишу **F5**, чтобы запустить приложение и убедиться в его работоспособности.

    ![][17]

### Написание кода для отправки элементов в очередь Service Bus

Теперь добавьте код для отправки элементов в очередь. Сначала вам нужно создать класс, содержащий информацию о подключении к очереди служебной шины, а затем — инициализировать подключение из Global.aspx.cs. И наконец, измените код отправки, созданный ранее в файле HomeController.cs, чтобы элементы отправлялись в очередь служебной шины.

1.  В **обозревателе решений** щелкните правой кнопкой мыши проект **FrontendWebRole** (проект, не роль). Нажмите кнопку **Добавить** и выберите **Класс**.

2.  Присвойте классу имя QueueConnector.cs. Нажмите кнопку **Добавить**, чтобы создать класс.

3.  Теперь добавьте код, который инкапсулирует сведения о подключении и инициализирует подключение к очереди служебной шины. Добавьте в файл QueueConnector.cs следующий код и введите значения для элементов **Namespace** (пространство имен вашей службы) и **yourKey** (ключ SAS, полученный ранее на [классическом портале Azure][]).

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

                // Obtain these values from the portal.
                public const string Namespace = "your service bus namespace";

                // The name of your queue.
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations.
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey");
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls.
                    ServiceBusEnvironment.SystemConnectivity.Mode =
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to
                    // management operations.
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already.
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue.
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address,
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

    Далее из этого учебника вы узнаете, как сохранить имя вашего пространства имен (**Namespace**) и значение ключа SAS в файле конфигурации.

4.  Теперь вам нужно обеспечить вызов метода **Initialize**. В **обозревателе решений** дважды щелкните **Global.asax\\Global.asax.cs**.

5.  Добавьте в конец метода **Application\_Start** следующую строку.

        FrontendWebRole.QueueConnector.Initialize();

6.  Наконец, обновите ранее созданный код веб-уровня, чтобы отправить элементы в очередь. В **Обозревателе решений** дважды щелкните **Controllers\\HomeController.cs**.

7.  Внесите следующие изменения в метод **Submit()**, чтобы получить количество сообщений в очереди.

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

8.  Внесите следующие изменения в метод **Submit(OnlineOrder order)**, чтобы отправить сведения о заказе в очередь.

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order.
                var message = new BrokeredMessage(order);

                // Submit the order.
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

Метод [GetSetting][] в классе [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][] позволяет считывать параметры конфигурации из хранилища конфигураций для вашей платформы. Например, если код выполняется в веб-роли или рабочей роли, метод [GetSetting][] обращается к файлу ServiceConfiguration.cscfg, а если код выполняется в стандартном консольном приложении, метод [GetSetting][] обращается к файлу app.config.

Если сохранить строку подключения для пространства имен служебной шины в файле конфигурации, вы сможете использовать метод [GetSetting][] для чтения строки подключения, с помощью которой можно создать экземпляр объекта [NamespaceMananger][]. Экземпляр [NamespaceMananger][] можно использовать для программной настройки пространства имен служебной шины. С помощью этой же строки подключения можно создавать экземпляры клиентских объектов (например, [QueueClient][], [TopicClient][] и [EventHubClient][]), которые можно использовать для выполнения операций среды выполнения, например отправки и получения сообщений.

### Строка подключения

Чтобы создать экземпляр клиента (например, Service Bus [QueueClient][]), можно представить сведения о конфигурации в виде строки подключения. На стороне клиента используется метод `CreateFromConnectionString()`, который создает экземпляр клиента такого типа с помощью заданной строки подключения. В качестве примера рассмотрим следующий раздел конфигурации.

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

	// Initialize the connection to Service Bus queue.
	Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

Код в следующем разделе использует класс [CloudConfigurationManager][Microsoft.WindowsAzure.Configuration.CloudConfigurationManager].

## Создание рабочей роли

Сейчас вы создадите рабочую роль, которая будет обрабатывать отправку заказов. В этом примере используется шаблон проекта Visual Studio **Рабочая роль с очередью Service Bus**. Сначала вы получите необходимые учетные данные с использованием Обозревателя серверов в Visual Studio.

1. Убедитесь, что среда Visual Studio подключена к вашей учетной записи Azure.

2.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши папку **Roles** (Роли) в проекте **MultiTierApp**.

3.  Щелкните **Добавить** и выберите **Создать проект рабочей роли**. Откроется диалоговое окно **Добавление нового проекта роли**.

	![][26]

4.  В окне **Добавление нового проекта роли** щелкните элемент **Рабочая роль с очередью служебной шины**.

	![][23]

5.  В поле **Имя** введите **OrderProcessingRole**. Нажмите кнопку **Добавить**.

6.  В **обозревателе сервера** щелкните правой кнопкой мыши имя пространства имен службы, а затем щелкните элемент **Свойства**. В области **Свойства** Visual Studio первая запись определяет строку подключения, которая содержит конечную точку пространства имен с необходимыми для авторизации учетными данными. См. пример на следующем снимке экрана. Дважды щелкните **ConnectionString**, а затем нажмите клавиши **Ctrl+C**, чтобы скопировать строку в буфер обмена.

	![][24]

7.  В **обозревателе решений** щелкните правой кнопкой мыши элемент **OrderProcessingRole**, созданный на этапе 5 (это должен быть элемент **OrderProcessingRole** в разделе **Роли**, а не класс с аналогичным названием). Выберите пункт **Свойства**.

8.  В диалоговом окне **Свойства** на вкладке **Настройки** щелкните поле **Значение** для элемента **Microsoft.ServiceBus.ConnectionString** и вставьте значение конечной точки, скопированное на этапе 6.

	![][25]

9.  Создайте класс **OnlineOrder**, который будет представлять заказы из очереди по мере их обработки. При необходимости используйте ранее созданный класс. В **обозревателе решений** щелкните правой кнопкой мыши проект **OrderProcessingRole** (проект, не роль). Выберите команду **Добавить**, а затем — **Существующий элемент**.

10. Перейдите во вложенную папку **FrontendWebRole\\Models** и дважды щелкните файл **OnlineOrder.cs**, чтобы добавить его в проект.

11. В файле **WorkerRole.cs** измените значение переменной **QueueName** с `"ProcessingQueue"` на `"OrdersQueue"`, как показано в следующем коде.

		// The name of your queue.
		const string QueueName = "OrdersQueue";

12. Добавьте следующую инструкцию using в начало файла WorkerRole.cs.

		using FrontendWebRole.Models;

13. В функции `Run()` в рамках вызова `OnMessage` добавьте следующий код в предложение `try`.

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder.
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
* [Использование очередей служебной шины][sbwacomqhowto]  

Дополнительные сведения о многоуровневых сценариях см. здесь:

* [Многоуровневое приложение .NET, использующее таблицы, очереди и BLOB-объекты хранилища.][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: service-bus-azure-and-service-bus-queues-compared-contrasted.md
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [классический портал Azure]: http://manage.windowsazure.com
  [классическом портале Azure]: http://manage.windowsazure.com
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
  [33]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-42-webpi.png
  [35]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx
  [sbwacom]: /documentation/services/service-bus/
  [sbwacomqhowto]: service-bus-dotnet-how-to-use-queues.md
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  

<!---HONumber=AcomDC_0601_2016-->