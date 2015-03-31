<properties
	pageTitle="Учебник по Azure "Многоуровневое приложение .NET""
	description="Учебник, посвященный разработке многоуровневого приложения в Azure, которое использует очереди Service Bus для взаимодействия между уровнями. Примеры на .NET."
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
	ms.date="02/26/2015"
	ms.author="sethm"/>





# Многоуровневое приложение .NET, использующее очереди Service Bus

## Введение

Разработка приложений в Azure очень проста благодаря применению Visual Studio 2013 и бесплатного пакета SDK для Azure для .NET. Если у вас еще нет приложения Visual Studio 2013, пакет SDK автоматически установит Visual Studio Express, что позволит вам начать разработку приложений для Azure абсолютно бесплатно. В данном учебнике предполагается, что у читателя нет опыта использования Microsoft Azure. По завершении этого учебника вы создадите приложение, которое использует несколько ресурсов Azure в локальной среде и демонстрирует принципы работы многоуровневого приложения.

Вы узнаете:

-   Как подготовить свой компьютер к разработке приложений для Azure, скачав и установив всего один пакет.
-   Как разработать приложение для Azure с помощью Visual Studio.
-   Как создать многоуровневое приложение в Azure с использованием рабочей роли и веб-роли.
-   Как реализовать связь между уровнями с использованием очередей Service Bus.

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

В этом учебнике вы выполните сборку и запуск многоуровневого приложения в облачной службе Azure. Интерфейсная часть будет реализована с использованием веб-роли MVC ASP.NET, а серверная - с помощью рабочей роли. Вы также можете создать аналогичное многоуровневое приложение, интерфейсная часть которого будет реализована в виде веб-проекта и развернута на веб-сайте, а не в облачной службе Azure. Дополнительные сведения об отличиях при реализации интерфейсной части для веб-сайта Azure см. в разделе [Дальнейшие действия](#nextsteps) .

Снимок экрана готового приложения приведен ниже.

![][0]

**Примечание** В Azure также предоставляются функциональные возможности очереди хранилища. Дополнительные сведения об очередях службы хранилища Azure и очередях Service Bus см. в разделе [Очереди Azure и очереди Azure Service Bus - сравнение и противопоставление][sbqueuecomparison].  

## Обзор сценария: обмен данными между ролями

Чтобы отправить запрос на обработку, компонент пользовательского интерфейса, выполняющийся в веб-роли, взаимодействует с логикой среднего уровня в рабочей роли. В этом примере в качестве посредника при обмене данными между уровнями выступает служба Service Bus.

Обмен сообщениями между веб-уровнем и средним уровнем через посредника позволяет разделить два компонента. В отличие от прямого обмена сообщениями (TCP или HTTP), веб-уровень не связан непосредственно со средним уровнем. Вместо этого он передает рабочие единицы в виде сообщений в службу Service Bus, в которой они хранятся до тех пор, пока не будут востребованы для обработки средним уровнем.

В службе Service Bus обмен сообщениями через посредника реализуется с использованием очередей и разделов. Каждое сообщение, помещенное в очередь, потребляется одним получателем. С помощью разделов реализуется модель публикации и подписки, благодаря которой каждое опубликованное сообщение становится доступно всем компонентам, зарегистрировавшим подписку на раздел. Для каждой подписки ведется собственная логическая очередь сообщений. Кроме того, для подписки можно настроить правила фильтрации, с помощью которых ограничивается набор сообщений, передаваемых в соответствующую очередь. В этом примере используются
Очереди служебной шины.

![][1]

По сравнению с прямым обменом сообщениями подобный механизм взаимодействия обладает целым рядом преимуществ.

-   **Временное разделение.** Благодаря шаблону асинхронного обмена сообщениями производителям и потребителям не нужно быть в сети одновременно. Служба Service Bus надежно сохраняет сообщения, пока принимающая сторона не будет готова принять их. Это позволяет компонентам распределенного приложения отключаться добровольно, например для обслуживания, или по причине сбоя без последствий для всей системы. Кроме того, принимающее приложение можно подключать только в определенное время дня.

-   **Выравнивание нагрузки**. Во многих приложениях уровень нагрузки на систему меняется с течением времени, тогда как время, затрачиваемое на обработку каждой рабочей единицы, как правило, постоянно. Благодаря обмену сообщениями через посредника с использованием очереди потребляющее приложение (рабочая роль) достаточно подготовить для обработки средней, а не пиковой нагрузки. В таких случаях при колебаниях входящей нагрузки просто изменяется глубина очереди. Это позволяет заметно сократить расходы на инфраструктуру, необходимую для обработки нагрузки приложения.

-   **Балансировка нагрузки.** По мере возрастания нагрузки можно добавить дополнительные рабочие процессы для чтения из очереди. Каждое сообщение обрабатывается только одним рабочим процессом. Кроме того, такая нагрузка, регулируемая в зависимости от запроса, позволяет оптимально использовать ресурсы рабочих машин, даже если рабочие машины обладают разной вычислительной мощностью, поскольку они будут обрабатывать сообщения со своей максимальной скоростью. Этот шаблон часто называют шаблоном конкурирующих потребителей.

    ![][2]

В следующих разделах описывается код, позволяющий реализовать подобную архитектуру.

## Настройка среды разработки

Прежде чем начать разработку приложения для Azure, подготовьте нужные инструменты и настройте среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, нажмите кнопку ниже.

    [Получить инструменты и SDK][]

2. 	Щелкните ссылку, соответствующую используемой версии Visual Studio. В этом учебнике приводятся инструкции для работы с версией Visual Studio 2013.

	![][32]

4. 	При появлении запроса на выполнение или сохранение файла установки нажмите **Запуск**:

    ![][3]

5.  В окне установщика веб-платформы щелкните **Установить** и продолжайте установку.

    ![][33]

6.  После завершения установки вы получите все компоненты, необходимые для разработки. В состав пакета SDK входят инструменты для эффективной разработки приложений Azure в Visual Studio. Если у вас не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия Visual Studio Express для Web.

## Настройка пространства имен Service Bus

На следующем шаге вы создадите пространство имен службы и получите ключ совместного доступа к подписи (SAS). Пространство имен определяет границы приложения для каждого приложения, предоставляемого через Service Bus. Ключ SAS создает система при создании пространства имен. Сочетание пространства имен и ключа совместного доступа к подписи предоставляет учетные данные, на основе которых служба Service Bus осуществляет проверку подлинности и дает доступ к приложению.

Заметьте, что для управления пространствами имен и сущностями обмена сообщениями Service Bus можно также использовать обозреватель серверов Visual Studio, но новые пространства имен можно создавать только с портала.

### Настройка пространства имен с помощью портала управления

1.  Выполните вход на [Портал управления Azure][].

2.  В левой области навигации портала управления щелкните **Service Bus**.

3.  В нижней части портала управления нажмите кнопку **Создать**.

    ![][6]

4.  В диалоговом окне **Добавить новое пространство имен** введите имя пространства имен. Система немедленно проверяет, доступно ли оно.
    ![][7]

5.  Убедившись, что имя пространства имен доступно, выберите страну или регион, в котором будет размещено ваше пространство имен (необходимо указать страну и регион развертывания своих вычислительных ресурсов). Кроме того, убедитесь, что установлен флажок **Обмен сообщениями** в пространстве имен в поле **Тип** и флажок **Стандартный** в поле **Уровень обмена сообщениями**.

    ВАЖНО! Выберите **регион**, в котором будет развернуто ваше приложение. Это обеспечит наилучшую производительность.

6.  Щелкните значок галочки. Теперь система создает пространство имен службы и включает его. Вероятно, придется подождать несколько минут, пока система не выделит ресурсы для вашей учетной записи.

	![][27]

7.  Щелкните имя созданного пространства имен службы в главном окне.

	![][30]

8. Щелкните **Сведения о подключении**.

	![][31]

9.  В области **Доступ к сведениям о подключении** найдите строку подключения, которая содержит ключ SAS и имя ключа.

    ![][35]

10.  Запишите ключ или скопируйте его в буфер обмена.

## Управление пространствами имен или сущностями обмена сообщениями с помощью обозревателя серверов Visual Studio

<<<<<<< HEAD
Чтобы вместо портала управления использовать Visual Studio для управления пространством имен и получения сведений о подключении, выполните процедуру **Подключение к Azure из Visual Studio** на странице [Приступая к работе с инструментами Azure для Visual
Studio](http://msdn.microsoft.com/library/ff687127.aspx). При входе в **обозреватель серверов** Azure пункт **Service Bus** под деревом **Microsoft
Azure** автоматически заполняется пространствами имен, уже созданными в вашей подписке. Щелкните правой кнопкой мыши любое пространство имен и выберите **Свойства**, чтобы увидеть строку подключения и другие метаданные, связанные с этим пространством имен, которые отображаются в области **Свойства** Visual Studio.
=======
Чтобы вместо портала управления использовать Visual Studio для управления пространством имен и получения сведений о подключении, выполните процедуру, описанную [здесь](http://msdn.microsoft.com/library/ff687127.aspx), в разделе **Подключение к Azure из Visual Studio**. При входе в Azure узел **Service Bus** в дереве **Microsoft Azure** в обозревателе серверов автоматически заполняется уже созданными пространствами имен. Щелкните правой кнопкой мыши любое пространство имен и выберите **Свойства**, чтобы увидеть строку подключения и другие метаданные, связанные с этим пространством имен, которые отображаются в области **Свойства** Visual Studio.
>>>>>>> 2076695a45ab90a31cffe94a32399a2407565b39

Запишите значение **SharedAccessKey** или скопируйте его в буфер обмена.

![][34]

**Примечание** Можно также использовать **обозреватель серверов** для управления пространством имен Service Bus в другой подписке с помощью следующей процедуры:

1. Выберите **Вид** в строке меню Visual Studio и щелкните **Обозреватель сервера**. В иерархии обозревателя серверов узел **Service Bus** отображается в узле **Azure** (см. следующий рисунок).

	![][21]

2. В обозревателе серверов разверните узел **Microsoft Azure**, щелкните правой кнопкой мыши узел **Service Bus** и выберите **Новое подключение**.

3. В диалоговом окне **Добавить подключение** введите имя пространства имен службы, а также имя и ключ издателя, или вставьте в строке подключения для пространства имен. Разрешения, связанные с ключом издателя, определяют операции, которые можно выполнить в этом пространстве имен. Нажмите кнопку **ОК**, чтобы установить подключение.

	![][22]

## Создание веб-роли

В этом разделе описывается создание интерфейсной части вашего приложения. Сначала необходимо создать страницы, которые будут отображаться в приложении.
После этого вы добавите код для отправки элементов в очередь Service Bus
и отображения информации о состоянии очереди.

### Создание проекта

1.  Используя привилегии администратора, запустите Microsoft Visual Studio 2013 или Microsoft Visual Studio Express. Для запуска Visual Studio с привилегиями администратора щелкните правой кнопкой мыши **Microsoft Visual Studio 2013 (или Microsoft Visual Studio Express)** и выберите **Запуск от имени администратора**. Для работы эмулятора вычислений Azure, который рассматривается в последующих разделах этого руководства, требуется запустить Visual Studio с привилегиями администратора.

    В меню **Файл** Visual Studio последовательно выберите **Создать** и **Проект**.

    ![][8]


2.  В разделе **Установленные шаблоны** области **Visual C#** щелкните **Облако**, а затем щелкните **Облачная служба Azure**. Присвойте проекту имя **MultiTierApp**. Затем нажмите кнопку **ОК**.

    ![][9]

3.  В ролях **.NET Framework 4.5** дважды щелкните **Веб-роль ASP.NET**.

    ![][10]

4.  Наведите указатель мыши на элемент **WebRole1** в разделе **Решение облачной службы Azure**, щелкните значок карандаша и присвойте веб-роли имя **FrontendWebRole**. Нажмите кнопку **ОК**. (Обратите внимание на написание слова Frontend (не FrontEnd).

    ![][11]

5.  В диалоговом окне **Новый проект ASP.NET** в списке **Выберите шаблон** щелкните **MVC** и нажмите кнопку **OK**.

    ![][12]

6.  В окне **Обозреватель решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Управление пакетами NuGet...** или **Добавить ссылку на пакет библиотеки**.

7.  В левой части диалогового окна выберите **Интернет**. Найдите **Service Bus** и выберите пункт **Microsoft Azure Service Bus**. Выполните установку и закройте это диалоговое окно.

    ![][13]

8.  Обратите внимание, что добавлены ссылки на обязательные клиентские сборки, а также новые файлы кода.

9.  В окне **Обозреватель решений** щелкните правой кнопкой мыши элемент **Модели**, выберите **Добавить** и щелкните элемент **Класс**. В поле "Имя" введите имя **OnlineOrder.cs**. Нажмите кнопку **Добавить**.

### Написание кода своей веб-роли

В этом разделе вы создадите страницы, которые будут отображаться в приложении.

1.  В файле **OnlineOrder.cs** fв Visual Studio замените существующее определение пространства имен следующим кодом:

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  В **обозревателе решений** дважды щелкните **Controllers\HomeController.cs**. Добавьте в начало файла следующие инструкции **using**, чтобы включить пространства имен для созданной модели и службы Service Bus:

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

4.  В меню **Сборка** выберите **Построить решение**.

5.  Далее необходимо создать представление для ранее созданного метода **Submit()**. Щелкните метод Submit() правой кнопкой мыши и выберите **Добавить представление**.

    ![][14]

6.  Откроется диалоговое окно создания представления. Выберите класс **OnlineOrder** в раскрывающемся списке **Класс модели** и элемент **Создать** в раскрывающемся списке **Шаблон**.

    ![][15]

7.  Щелкните **Добавить**.

8.  Укажите новое отображаемое имя приложения. В **обозревателе решений** дважды щелкните файл **Views\Shared\\_Layout.cshtml**, чтобы открыть его в редакторе Visual Studio.

9.  Замените все вхождения элемента **My ASP.NET Application** текстом **Продукты LITWARE**.

11. Удалите ссылки **Главная**, **О программе** и **Связь**. Удалите выделенный код.

	![][28]


12. Наконец, добавьте на страницу отправки необходимую информацию об очереди. В **обозревателе решений** дважды щелкните файл **Views\Home\Submit.cshtml**, чтобы открыть его в редакторе Visual Studio. Добавьте следующую строку после **&lt;h2>Submit&lt;/h2>**. Сейчас элемент **ViewBag.MessageCount** пуст. Вы заполните его позже.

        <p>Текущее число ожидающих обработки заказов в очереди: @ViewBag.MessageCount</p>


13. Пользовательский интерфейс реализован. Нажмите клавишу **F5**, чтобы запустить приложение и убедиться в его работоспособности.

    ![][17]

### Написание кода для отправки элементов в очередь Service Bus

Далее вам необходимо добавить код, реализующий отправку элементов в очередь. Сначала требуется создать класс, содержащий информацию о подключении к очереди Service Bus. Далее следует инициализировать подключение из файла
**Global.aspx.cs**. Наконец, необходимо обновить код, созданный ранее в файле **HomeController.cs**, чтобы реализовать фактическую отправку элементов в
очередь Service Bus.

1.  В обозревателе решений щелкните правой кнопкой мыши **FrontendWebRole** (проект, а не роль). Нажмите кнопку **Добавить** и выберите **Класс**.

2.  Присвойте классу имя **QueueConnector.cs**. Нажмите кнопку **Добавить**, чтобы создать класс.

3.  Далее необходимо добавить код, который инкапсулирует сведения о подключении и инициализирует подключение к очереди Service Bus. В файле QueueConnector.cs добавьте следующий код и введите значения для **Пространства имен** (пространство имен вашей службы) и **yourKey** - ключ SAS, ранее полученный из [Портал управления Azure][].

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

    **Примечание** Далее в этом учебнике вы узнаете, как сохранить имя вашего **Пространства имен** и значение вашего ключа SAS в файле конфигурации.

4.  Затем реализуйте вызов метода **Initialize**. В **обозревателе решений** дважды щелкните **Global.asax\Global.asax.cs**.

5.  Добавьте в конец метода **Application_Start** следующую строку:

        FrontendWebRole.QueueConnector.Initialize();

6.  Наконец, обновите ранее созданный код веб-уровня, чтобы отправить элементы в очередь. В **обозревателе решений** дважды щелкните файл **Controllers\HomeController.cs**, созданный ранее.

7.  Внесите следующие изменения в метод **Submit()**, чтобы реализовать счетчик сообщений очереди:

        public ActionResult Submit()
        {
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Внесите следующие изменения в метод **Submit(OnlineOrder order)**, чтобы отправить информацию о заказе в очередь:

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

9.  Запустите свое приложение. При каждой отправке
    заказа количество сообщений увеличивается.

    ![][18]

## Диспетчер конфигурации облака

Метод **GetSettings** в
классе **Microsoft.WindowsAzure.Configuration.CloudConfigurationManager** позволяет считывать параметры конфигурации из хранилища конфигураций для вашей платформы. Например, если код выполняется в рабочей или веб-роли, метод **GetSettings** считывает файл ServiceConfiguration.cscfg и, если код выполняется в стандартном приложении для консоли, метод **GetSettings** считывает файл app.config.

Если сохранить строку подключения в файле конфигурации пространства имен Service Bus, можно использовать метод **GetSettings** для чтения строки подключения, которая используется для создания объекта **NamespaceManager**. Можно использовать экземпляр **NamespaceManager** для программной настройки пространства имен Service Bus. Можно использовать ту же строку подключения для создания клиентских объектов (например, объектов **QueueClient**, **TopicClient** и **EventHubClient**), которые служат для выполнения таких операций, как отправка и получение сообщений.

### Строка подключения

Чтобы создать экземпляр клиента (например, Service Bus **QueueClient**), можно представить сведения о конфигурации в виде строки подключения. На стороне клиента используется метод **CreateFromConnectionString()**, который создает экземпляр клиента такого типа с помощью заданной строки подключения. Рассмотрим следующий раздел конфигурации.

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

Сейчас вы создадите рабочую роль, которая будет обрабатывать отправку заказов. В этом примере используется шаблон проекта Visual Studio **Рабочая роль с очередью Service Bus**. На первом этапе вы получаете учетные данные с использованием обозревателя сервера в Visual Studio.

1. Убедитесь, что Visual Studio подключено к вашей учетной записи в Azure так, как описано в разделе [Управление пространствами имен и обменом сообщениями с помощью обозревателя серверов Visual Studio](./cloud-services-dotnet-multi-tier-app-using-service-bus-queues/#manage-namespaces-and-messaging-entities-using-the-visual-studio-server-explorer).

2.  В **обозревателе решений** Visual Studio щелкните правой кнопкой мыши папку **Роли** в проекте **MultiTierApp**.

3.  Щелкните **Добавить** и выберите **Создать проект рабочей роли**. Откроется диалоговое окно **Добавить новый проект роли**.

	![][26]

4.  В диалоговом окне **Добавить новый проект роли** щелкните элемент **Рабочая роль с очередью Service Bus** (см. следующий рисунок).

	![][23]

5.  В поле **Имя** введите имя проекта **OrderProcessingRole**. Нажмите кнопку **Добавить**.

6.  В обозревателе сервера щелкните правой кнопкой мыши имя пространства имен службы, а затем выберите **Свойства**. В области **Свойства** Visual Studio первая запись определяет строку подключения, которая содержит конечную точку пространства имен службы с необходимыми для авторизации учетными данными. См. пример на следующем рисунке. Дважды щелкните **ConnectionString**, а затем нажмите клавиши **Ctrl+C**, чтобы скопировать строку в буфер обмена.

	![][24]

7.  В обозревателе решений щелкните правой кнопкой мыши элемент **OrderProcessingRole**, созданный на шаге 5. (Обратите внимание, что необходимо щелкнуть правой кнопкой **OrderProcessingRole** в разделе **Роли**, а не класс). Выберите пункт **Свойства**.

8.  На вкладке **Настройки** диалогового окна **Свойства** щелкните в поле **Значение** элемент **Microsoft.ServiceBus.ConnectionString**, и вставьте значение конечной точки, скопированное на шаге 6.

	![][25]

9.  Создайте класс **OnlineOrder**, который будет представлять заказы из очереди по мере их обработки. При необходимости используйте ранее созданный класс. В обозревателе решений щелкните правой кнопкой мыши проект **OrderProcessingRole** (проект, а не роль). Выберите команду **Добавить**, а затем - **Существующий элемент**.

10. Перейдите к вложенной папке **FrontendWebRole\Models** и дважды щелкните файл **OnlineOrder.cs**, чтобы добавить его в проект.

11. В файле WorkerRole.cs замените значение переменной **QueueName** в **WorkerRole.cs** с `"ProcessingQueue"` на `"OrdersQueue"`, как указано в следующем коде:

		// The name of your queue
		const string QueueName = "OrdersQueue";

12. Добавьте в начало файла WorkerRole.cs следующую инструкцию using:

		using FrontendWebRole.Models;

13. В функции `Run()`  в вызове  `OnMessage` добавьте следующий код в операторе  `try`:

		Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
		// View the message as an OnlineOrder
		OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
		Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
		receivedMessage.Complete();

14. Приложение готово. Чтобы проверить все приложение, щелкните правой кнопкой мыши проект MultiTierApp в обозревателе решений при выборе действия **Назначить запускаемым проектом**, а затем нажмите кнопку F5. Обратите внимание, что значение счетчика сообщений не увеличивается, поскольку рабочая роль обрабатывает элементы из очереди и помечает их выполненными. Результаты трассировки рабочей роли вы можете просмотреть в пользовательском интерфейсе эмулятора вычислений Azure. Для этого щелкните правой кнопкой мыши значок эмулятора в области уведомлений на панели задач и выберите **Показать пользовательский интерфейс эмулятора вычислений**.

    ![][19]

    ![][20]

## Дальнейшие действия  

Дополнительную информацию о Service Bus см. на следующих ресурсах.  

* [Azure Service Bus][sbmsdn]
* [Практические руководства по работе со службой Service Bus][sbwacom]
* [Использование очередей Service Bus][sbwacomqhowto]

Дополнительную информацию о многоуровневых сценариях и развертывании приложений в облачной службе см. в следующей статье.  

* [Многоуровневое приложение .NET, использующее таблицы, очереди и большие двоичные объекты в службе хранилища][mutitierstorage]

Возможно, вам понадобится реализовать интерфейсную часть многоуровневого приложения на веб-сайте Azure, а не в облачной службе Azure. Дополнительные сведения о различиях между веб-сайтами и облачными службами см. в разделе [Модели выполнения Azure][executionmodels].

Если вы хотите реализовать проект, описываемый в этом учебнике, в виде стандартного веб-проекта, а не веб-роли облачной службы, повторите приведенные здесь шаги, но обратите внимание на следующие отличия.

1. При создании проекта выберите шаблон **Веб-приложение ASP.NET MVC** в категории **Веб** вместо шаблона **Облачная служба** из категории **Облако**. Выполните инструкции по созданию приложения MVC вплоть до раздела **Диспетчер конфигурации облачного приложения**.

2. Создайте рабочую роль в новом отдельном решении, как изначально описывается в инструкциях к веб-роли. Далее в этом случае вы создаете только рабочую роль в проекте облачной службы. Выполните остальные инструкции по созданию рабочей роли.

3. Вы можете протестировать интерфейсную и серверную части по отдельности или запустить их одновременно в разных экземплярах Visual Studio.

Дополнительные сведения о развертывании приложения на веб-сайте Azure см. в разделе [Развертывание веб-приложения ASP.NET на веб-сайте Azure](http://azure.microsoft.com/develop/net/tutorials/get-started/). Дополнительные сведения о развертывании серверной части в облачной службе Azure см. в разделе [Многоуровневое приложение .NET, использующее таблицы, очереди и большие двоичные объекты в службе хранилища][mutitierstorage].


  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [sbqueuecomparison]: http://msdn.microsoft.com/library/hh767287.aspx
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png



  [Портал управления Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [32]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [33]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [34]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [35]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/multi-web-45.png
  [sbmsdn]: http://msdn.microsoft.com/library/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: /develop/net/how-to-guides/service-bus-queues/  
  [mutitierstorage]: /develop/net/tutorials/multi-tier-web-site/1-overview/
  [executionmodels]: http://azure.microsoft.com/develop/net/fundamentals/compute/

<!--HONumber=47-->
