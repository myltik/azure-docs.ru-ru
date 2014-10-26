<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm"></tags>

# Многоуровневое приложение .NET, использующее очереди Service Bus

Разработка приложений в Azure очень проста благодаря применению Visual Studio 2013 и
бесплатного пакета SDK для Azure для .NET. Если у вас еще нет приложения Visual
Studio 2013, пакет SDK автоматически установит Visual Studio Express 2013, что позволит вам начать разработку приложений для Azure абсолютно
бесплатно. В данном учебнике
предполагается, что у читателя нет опыта использования Microsoft Azure. По завершении этого учебника вы создадите приложение, которое использует
несколько ресурсов Azure в локальной среде
и демонстрирует принципы работы многоуровневого приложения.

Вы узнаете:

-   Как подготовить свой компьютер к разработке приложений
    для Azure, скачав и установив всего один пакет.
-   Как разработать приложение для Azure с помощью Visual Studio.
-   Как создать многоуровневое приложение в Azure с использованием
    рабочей роли и веб-роли.
-   Как реализовать связь между уровнями с использованием очередей Service Bus.

[WACOM.INCLUDE [create-account-note][create-account-note]]

В этом учебнике вы создадите и запустите многоуровневое приложение в облачной службе Azure. Интерфейсная часть будет реализована с использованием веб-роли MVC ASP.NET, а серверная — с помощью рабочей роли. Вы также можете создать аналогичное многоуровневое приложение, интерфейсная часть которого будет реализована в виде веб-проекта и развернута на веб-сайте, а не в облачной службе Azure. Дополнительную информацию об отличиях при реализации интерфейсной части для веб-сайта Azure см. в разделе [Дальнейшие действия][Дальнейшие действия].

Снимок экрана готового приложения приведен ниже.

![][]

**Примечание.** В Azure также предоставляются функциональные возможности очереди хранилища. Дополнительную информацию об очередях хранилища Azure и очередях Service Bus см. в статье [Очереди Azure и очереди Azure Service Bus — сравнение и противопоставление][Очереди Azure и очереди Azure Service Bus — сравнение и противопоставление].

## <span class="short-header">Обмен данными между ролями</span>Обзор сценария: обмен данными между ролями

Чтобы отправить запрос на обработку, компонент пользовательского интерфейса,
выполняющийся в веб-роли, взаимодействует с логикой среднего уровня
в рабочей роли. В этом примере в качестве посредника при обмене данными
между уровнями выступает служба Service Bus.

Обмен сообщениями между веб-уровнем и средним уровнем через посредника позволяет разделить
два компонента. В отличие от прямого обмена сообщениями (TCP или HTTP)
веб-уровень не связан непосредственно со средним уровнем.
Вместо этого он передает рабочие единицы в виде сообщений в службу Service Bus, в которой они
хранятся до тех пор, пока не будут востребованы для обработки средним уровнем.

В службе Service Bus обмен сообщениями через посредник реализуется
с использованием очередей и разделов. Каждое сообщение, помещенное в очередь,
потребляется одним получателем. С помощью разделов реализуется модель публикации и подписки,
благодаря которой каждое опубликованное сообщение становится доступно
всем компонентам, зарегистрировавшим подписку на раздел. Для каждой подписки ведется собственная
логическая очередь сообщений. Кроме того, для подписки можно настроить
правила фильтрации, с помощью которых ограничивается набор сообщений,
передаваемых в соответствующую очередь. В данном примере используются
очереди Service Bus.

![][1]

По сравнению с прямым обменом сообщениями подобный механизм
взаимодействия обладает целым рядом преимуществ.

-   **Временное разделение.**В асинхронной модели обмена отправители
    и получатели сообщений не обязательно должны находиться в сети одновременно. Служба Service
    Bus хранит сообщения до тех пор, пока принимающая сторона не будет готова
    принять их. Это позволяет разделять компоненты
    распределенного приложения как в плановых ситуациях, например для обслуживания,
    так и в экстренных случаях, чтобы исключить влияние сбоя одного компонента
    на систему в целом. Более того, потребляющему приложению достаточно
    находиться в сети несколько раз в день в определенное время.

-   **Выравнивание нагрузки.** Во многих приложениях уровень нагрузки на систему меняется с течением времени,
    тогда как время, затрачиваемое на обработку каждой рабочей единицы, как правило,
    постоянно. Благодаря обмену сообщениями через посредника
    с использованием очереди потребляющее приложение (рабочая роль) достаточно
    подготовить для обработки средней,
    а не пиковой нагрузки. В таких случаях при колебаниях входящей нагрузки просто
    изменяется глубина очереди. Это позволяет заметно сократить расходы на
    инфраструктуру, необходимую для обработки нагрузки приложения.

-   **Балансировка нагрузки.**По мере возрастания нагрузки могут потребоваться дополнительные
    рабочие процессы для чтения из очереди. Каждое сообщение обрабатывается
    одним рабочим процессом. Кроме того, балансировка нагрузки по опросу
    обеспечивает оптимальное использование ресурсов рабочих машин
    с разной вычислительной мощностью, поскольку они
    смогут извлекать сообщения с максимально доступной им скоростью. Такая концепция часто называется
    моделью конкурирующих потребителей.

    ![][2]

В следующих разделах описывается код, позволяющий
реализовать подобную архитектуру.

## <span class="short-header">Настройка среды</span>Настройка среды разработки

Прежде чем начать разработку приложения для Azure, необходимо
подготовить нужные инструменты и настроить среду разработки.

1.  Чтобы установить пакет Azure SDK для .NET, нажмите кнопку ниже.

    [Получить инструменты и SDK][Получить инструменты и SDK]

2.  Щелкните **Установить SDK**.

3.  Щелкните ссылку, соответствующую используемой версии Visual Studio. В этом учебнике приводятся инструкции для работы с версией Visual Studio 2013.

    ![][3]

4.  При появлении запроса на выполнение или сохранение файла установки нажмите
    **Выполнить**:

    ![][4]

5.  В окне установщика веб-платформы щелкните **Установить** и продолжайте установку.

    ![][5]

6.  
     После завершения установки у вас будут все компоненты, необходимые для начала разработки. В состав пакета SDK входят инструменты для
    простой разработки приложений Azure в Visual Studio. Если у вас
    не установлено приложение Visual Studio, будет автоматически установлена бесплатная версия
    Visual Studio Express для Web.

## <span class="short-header">Настройка пространства имен</span>Настройка пространства имен Service Bus

На следующем шаге вы создадите пространство имен службы и
получите общий секретный ключ. Пространство имен службы определяет границы каждого приложения,
предоставляемого через Service Bus. Общий секретный ключ
создается автоматически одновременно
с пространством имен службы. Сочетание пространства имен и общего секретного ключа
определяет учетные данные, на основе которых служба Service Bus осуществляет аутентификацию при доступе
к приложению.

Заметьте, что для управления пространствами имен и сущностями обмена сообщениями Service Bus можно также использовать обозреватель серверов Visual Studio, но новые пространства имен можно создавать только с портала.

### Настройка пространства имен с помощью портала управления

1.  Выполните вход на [портал управления Azure][портал управления Azure].

2.  В левой области навигации портала управления нажмите кнопку
    **Service Bus**.

3.  В нижней части портала управления нажмите кнопку **Создать**.

    ![][6]

4.  В диалоговом окне **Добавление нового пространства имен** введите имя пространства имен.
     Система сразу проверит, доступно ли имя.
    ![][7]

5.  Убедившись, что имя пространства имен доступно, выберите страну
     или регион, где будет размещаться пространство имен (убедитесь, что
     используете страну или регион, где развертываете вычислительные
     ресурсы).

    ВАЖНО! Выберите **тот же регион**, который собираетесь выбрать
     для развертывания приложения. Это обеспечит наилучшую производительность.

6.  Щелкните значок галочки. Теперь система создает пространство
     имен службы и включает его. Возможно, придется подождать несколько минут,
     пока система выделит ресурсы для вашей учетной записи.

    ![][8]

7.  Щелкните имя созданного пространства имен службы в главном окне.

    ![][9]

8.  Нажмите кнопку **Сведения о подключении**

    ![][10]

9.  В области **Сведения по доступу к подключению** найдите значения **Издатель по умолчанию** и **Ключ по умолчанию**.

10. Запишите ключ или скопируйте его в буфер обмена.

### Управление пространствами имен или сущностями обмена сообщениями с помощью обозревателя серверов Visual Studio

Чтобы вместо портала управления использовать Visual Studio для управления пространством имен и получения сведений о подключении, выполните процедуру, описанную [здесь][здесь], в разделе **Подключение к Azure из Visual Studio**. При входе в Azure узел **Service Bus** в дереве **Microsoft Azure** в обозревателе серверов автоматически заполняется уже созданными пространствами имен. Щелкните правой кнопкой мыши любое пространство имен и выберите **Свойства**, чтобы увидеть строку подключения и другие метаданные, связанные с этим пространством имен, которые отображаются в области **Свойства** Visual Studio.

Запишите значение **SharedAccessKey** или скопируйте его в буфер обмена.

![][11]

## <span class="short-header">Создание веб-роли</span>Создание веб-роли

В этом разделе описывается построение интерфейсной части вашего приложения. Сначала
необходимо создать страницы, которые будут отображаться в приложении.
После этого необходимо добавить код для отправки элементов в очередь Service Bus
и отображения сведений о ее состоянии.

### Создание проекта

1.  Используя привилегии администратора, запустите Microsoft Visual Studio
    2013 или Microsoft Visual Studio Express. Для запуска Visual
    Studio с привилегиями администратора щелкните правой кнопкой мыши **Microsoft Visual Studio 2013 (или Microsoft Visual
    Studio Express)** и
    выберите **Запуск от имени администратора**. Для работы
    эмулятора вычислений Azure, который рассматривается в последующих разделах этого руководства,
    требуется запустить Visual Studio с привилегиями администратора.

    В меню **Файл** Visual Studio последовательно выберите **Создать**
     и **Проект**.

    ![][12]

2.  В разделе **Установленные шаблоны** области **Visual C#** щелкните **Облако**, а затем
    щелкните **Облачная служба Azure**. Присвойте проекту имя
    **MultiTierApp**. Нажмите кнопку **ОК**.

    ![][13]

3.  В ролях **.NET Framework 4.5** дважды щелкните **Веб-роль
    ASP.NET**.

    ![][14]

4.  Наведите указатель мыши на элемент **WebRole1** в разделе **Решение облачной службы Azure**, щелкните
    значок карандаша и присвойте веб-роли имя **FrontendWebRole**. Нажмите кнопку **ОК**. Обратите внимание на написание слова "Frontend" (не "FrontEnd").

    ![][15]

5.  В диалоговом окне **Новый проект ASP.NET**, в списке **Выберите шаблон** щелкните **MVC** и
    нажмите кнопку **ОК**.

    ![][16]

6.  В окне **Обозреватель решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите
    **Управление пакетами NuGet** или **Добавить ссылку на пакет библиотеки**.

7.  В левой части диалогового окна выберите **Интернет**. Найдите элемент
    **WindowsAzure** и выберите пункт **Azure Service
    Bus**. Выполните установку и закройте это диалоговое окно.

    ![][17]

8.  Заметьте, что добавлены ссылки на требуемые клиентские сборки, а также
    новые файлы кода.

9.  В **обозревателе решений** щелкните правой кнопкой мыши **Модели**,
    выберите **Добавить** и затем щелкните **Класс**. В поле "Имя" введите имя
    **OnlineOrder.cs**. Нажмите кнопку **Добавить**.

### Написание кода своей веб-роли

В этом разделе вы создадите страницы, которые будут отображаться в
приложении.

1.  В файле **OnlineOrder.cs** в Visual Studio замените
    существующее определение пространства имен следующим кодом.

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  В **обозревателе решений** дважды щелкните
    **Controllers\\HomeController.cs**. Добавьте в начало файла следующие инструкции **using**,
    чтобы включить пространства
    имен для созданной модели и службы Service Bus:

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Кроме того, в файле **HomeController.cs** в Visual Studio замените
    существующее определение пространства имен следующим кодом. Этот код
    содержит методы для обработки отправки элементов в очередь:

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

5.  Далее необходимо создать представление для ранее созданного метода
    **Submit()**. Щелкните метод Submit() правой кнопкой мыши и выберите
    **Добавить представление**.

    ![][18]

6.  Откроется диалоговое окно создания представления. Выберите класс
    **OnlineOrder** в раскрывающемся списке **Класс модели** и элемент
    **Создать** в раскрывающемся списке **Шаблон**.

    ![][19]

7.  Щелкните **Добавить**.

8.  Укажите новое отображаемое имя приложения. В окне
    **Обозреватель решений** дважды щелкните файл
    **Views\Shared\\_Layout.cshtml** , чтобы открыть его в редакторе Visual
    Studio.

9.  Замените все вхождения элемента **Мое приложение MVC ASP.NET** текстом
    **Специальное предложение от LITWARE**.

10. Замените элемент **Ваша эмблема** текстом **Специальное предложение от LITWARE**.

    ![][20]

11. Удалите ссылки **Главная**, **О программе** и **Связь**. Удалите выделенный код.

    ![][21]

12. Наконец, добавьте на страницу отправки необходимую информацию
    об очереди. В **обозревателе решений** дважды щелкните файл
    **Views\\Home\\Submit.cshtml**, чтобы открыть его
    в редакторе Visual Studio. Добавьте следующую строку после **\<h2\>Submit\</h2\>**. Сейчас
    элемент **ViewBag.MessageCount** пуст. Вы заполните его позже.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>

13. Пользовательский интерфейс реализован. Нажмите клавишу **F5**, чтобы запустить приложение
    и убедиться в его работоспособности.

    ![][22]

### Написание кода для отправки элементов в очередь Service Bus

Далее вам необходимо добавить код, реализующий отправку элементов в очередь. Сначала
требуется создать класс, содержащий информацию о подключении к очереди
Service Bus. Далее следует инициализировать подключение из
**Global.aspx.cs**. Наконец, необходимо обновить код, созданный ранее
в файле **HomeController.cs**, чтобы реализовать фактическую
отправку элементов в очередь Service Bus.

1.  В обозревателе решений щелкните правой кнопкой мыши **FrontendWebRole** (проект, а не роль). Нажмите кнопку **Добавить** и выберите **Класс**.

2.  Присвойте классу имя **QueueConnector.cs**. Нажмите кнопку **Добавить**, чтобы создать класс.

3.  Далее необходимо вставить код, который инкапсулирует информацию о
    подключении и содержит методы для инициализации подключения к
    очереди Service Bus. В QueueConnector.cs вставьте следующий код и введите значения
    для атрибутов **Namespace**, **IssuerName** и **IssuerKey**. Эти
    значения можно получить на [портале управления Azure][портал управления Azure] или в обозревателе серверов Visual Studio в узле **Service Bus**.

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
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
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

4.  Теперь вы обеспечите вызов метода **Initialize**. В **обозревателе решений** дважды щелкните **Global.asax\\Global.asax.cs**.

5.  Добавьте в конец метода **Application\_Start**
    следующую строку:

        FrontendWebRole.QueueConnector.Initialize();

6.  Обновите ранее созданный код веб-приложения, чтобы
    реализовать отправку элементов в очередь. В **обозревателе решений**
    дважды щелкните файл **Controllers\\HomeController.cs**, созданный
    ранее.

7.  Внесите следующие изменения в метод **Submit()**, чтобы реализовать счетчик
    сообщений в очереди:

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

8.  Внесите следующие изменения в метод **Submit(OnlineOrder order)**,
    чтобы отправить информацию о заказе в очередь:

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

9.  Запустите свое приложение. Каждый раз при отправке
    заказа значение счетчика сообщений увеличивается.

    ![][23]

## <span class="short-header">Диспетчер конфигурации</span>Диспетчер конфигурации облака

Azure поддерживает набор управляемых API-интерфейсов, с помощью которых реализуется согласованный процесс создания новых экземпляров клиентов службы Azure (например, Service Bus) в облачных службах Майкрософт. Эти API позволяют создавать экземпляры таких клиентов (например, **CloudBlobClient**, **QueueClient**, **TopicClient**) независимо от того, где размещается приложение: в локальной среде, в облачной службе Майкрософт, на веб-сайтах или в постоянной роли виртуальной машины. Также можно использовать эти API-интерфейсы для получения сведений о конфигурации, необходимых для создания экземпляров таких клиентов и изменения конфигурации без повторного развертывания вызывающего приложения. API-интерфейсы находятся в классе [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]. Также реализованы API-интерфейсы на стороне клиента.

### Строка подключения

Чтобы создать экземпляр клиента (например, Service Bus **QueueClient**), можно представить сведения о конфигурации в виде строки подключения. На стороне клиента используется метод **CreateFromConnectionString()**, который создает экземпляр клиента такого типа с помощью заданной строки подключения. Рассмотрим следующий раздел конфигурации.

    <ConfigurationSettings>
    ...
        <Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
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

Код в следующем разделе использует эти API-интерфейсы управления конфигурацией.

## <span class="short-header">Создание рабочей роли</span>Создание рабочей роли

Сейчас вы создадите рабочую роль, которая будет обрабатывать отправку
заказов. В этом примере используется шаблон проекта Visual Studio **Рабочая роль с очередью Service Bus**. На первом этапе вы получаете учетные данные с использованием обозревателя сервера в Visual Studio.

1.  Если вы уже подключили Visual Studio к своей учетной записи Azure, как это описано в разделе **Настройка пространства имен с использованием обозревателя серверов Visual Studio**, перейдите к шагу 5.

2.  Выберите **Вид** в строке меню Visual Studio и щелкните **Обозреватель сервера**. В иерархии обозревателя серверов узел **Service Bus** отображается в узле **Azure** (см. следующий рисунок).

    ![][24]

3.  Разверните узел **Azure** в обозревателе сервера, щелкните правой кнопкой мыши элемент **Service Bus** и выберите **Добавить новое подключение**.

4.  В диалоговом окне **Добавить подключение** введите имя пространства имен службы, а также имя и ключ издателя. Нажмите кнопку **ОК**, чтобы установить подключение.

    ![][25]

5.  В окне **Обозреватель решений** Visual Studio щелкните правой кнопкой мыши папку
    **Роли** в проекте **MultiTierApp**.

6.  Щелкните **Добавить** и выберите **Создать проект рабочей роли**. Откроется диалоговое окно **Добавить новый проект роли**.

    ![][26]

7.  В диалоговом окне **Добавить новый проект роли** щелкните элемент **Рабочая роль с очередью Service Bus** (см. следующий рисунок).

    ![][27]

8.  В поле **Имя** введите **OrderProcessingRole**. Нажмите кнопку **Добавить**.

9.  В обозревателе сервера щелкните правой кнопкой мыши имя пространства имен службы, а затем выберите **Свойства**. В области **Свойства** Visual Studio первая запись определяет строку подключения, которая содержит конечную точку пространства имен службы с необходимыми для авторизации учетными данными. См. пример на следующем рисунке. Дважды щелкните **ConnectionString**, а затем нажмите клавиши **Ctrl+C**, чтобы скопировать строку в буфер обмена.

    ![][28]

10. В обозревателе решений щелкните правой кнопкой мыши элемент **OrderProcessingRole**, созданный на шаге 7. Обратите внимание, что необходимо щелкнуть элемент **OrderProcessingRole** в разделе **Роли**, а не класс. Выберите пункт **Свойства**.

11. На вкладке **Настройки** диалогового окна **Свойства** щелкните в поле **Значение** элемента **Microsoft.ServiceBus.ConnectionString** и вставьте значение конечной точки, скопированное на шаге 8.

    ![][29]

12. Создайте класс **OnlineOrder**, который будет представлять заказы из очереди по мере их обработки. При необходимости используйте ранее созданный класс. В обозревателе решений щелкните правой кнопкой мыши проект **OrderProcessingRole** (проект, а не роль). Выберите команду **Добавить**, а затем — **Существующий элемент**.

13. Перейдите к вложенной папке **FrontendWebRole\\Models** и дважды щелкните файл **OnlineOrder.cs**, чтобы добавить его в проект.

14. В файле WorkerRole.cs замените значение переменной **QueueName** в **WorkerRole.cs** с `"ProcessingQueue"` на `"OrdersQueue"`, как указано в следующем коде:

        // The name of your queue
        const string QueueName = "OrdersQueue";

15. Добавьте в начало файла WorkerRole.cs следующую инструкцию using:

        using FrontendWebRole.Models;

16. Добавьте следующий код в предложение `try` функции `Run()` внутри вызова `OnMessage`.

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // View the message as an OnlineOrder
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

17. Приложение готово. Чтобы проверить его работоспособность,
    нажмите клавишу F5. Обратите внимание, что значение счетчика сообщений не увеличивается, поскольку рабочая роль обрабатывает элементы из очереди и помечает их выполненными. Результаты трассировки рабочей роли
    вы можете просмотреть в пользовательском интерфейсе эмулятора вычислений Azure. Для
    этого щелкните правой кнопкой мыши значок эмулятора в области
    уведомлений на панели задач и выберите **Show Compute Emulator UI** (Показать пользовательский интерфейс эмулятора вычислений).

    ![][30]

    ![][31]

## <a name="nextsteps"></a><span class="short-header">Дальнейшие действия </span>Дальнейшие действия

Дополнительную информацию о Service Bus см. на следующих ресурсах.

-   [Azure Service Bus][Azure Service Bus]
-   [Практические руководства по работе со служебной шиной][Практические руководства по работе со служебной шиной]
-   [Использование очередей Service Bus][Использование очередей Service Bus]

Дополнительную информацию о многоуровневых сценариях и развертывании приложений в облачной службе см. в следующей статье.

-   [Многоуровневое веб-приложение ASP.NET, использующее таблицы, очереди и большие двоичные объекты службы хранилища Azure][Многоуровневое веб-приложение ASP.NET, использующее таблицы, очереди и большие двоичные объекты службы хранилища Azure]

В некоторых случаях требуется реализовать интерфейсную часть многоуровневого приложения на веб-сайте Azure, а не в облачной службе Azure. Дополнительную информацию о различиях между веб-узлами и облачными службами см. в статье [Модели выполнения Azure][Модели выполнения Azure].

Если вы хотите реализовать проект, описываемый в этом учебнике, в виде стандартного веб-проекта, а не веб-роли облачной службы, повторите приведенные здесь шаги, но обратите внимание на следующие отличия.

1.  При создании проекта выберите шаблон **Веб-приложение ASP.NET MVC 4** в категории **Веб** вместо шаблона **Облачная служба** из категории **Облако**. Выполните инструкции по созданию приложения MVC вплоть до раздела **Диспетчер конфигурации облачного приложения**.

2.  Создайте рабочую роль в новом отдельном решении, как изначально описывается в инструкциях к веб-роли. Далее в этом случае вы создаете только рабочую роль в проекте облачной службы. Выполните остальные инструкции по созданию рабочей роли.

3.  Вы можете протестировать интерфейсную и серверную части по отдельности или запустить их одновременно в разных экземплярах Visual Studio.

Дополнительную информацию о развертывании приложения на веб-сайте Azure см. в разделе [Развертывание веб-приложения ASP.NET на веб-сайте Azure][Развертывание веб-приложения ASP.NET на веб-сайте Azure]. Дополнительную информацию о развертывании серверной части в облачной службе Azure см. в статье [Многоуровневое приложение .NET, использующее таблицы, очереди и BLOB-объекты хранилища][Многоуровневое веб-приложение ASP.NET, использующее таблицы, очереди и большие двоичные объекты службы хранилища Azure].

  [create-account-note]: ../includes/create-account-note.md
  [Дальнейшие действия]: #nextsteps
  []: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [Очереди Azure и очереди Azure Service Bus — сравнение и противопоставление]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh767287.aspx
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Получить инструменты и SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [портал управления Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [здесь]: http://http://msdn.microsoft.com/ru-ru/library/windowsazure/ff687127.aspx
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [29]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [Azure Service Bus]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee732537.aspx
  [Практические руководства по работе со служебной шиной]: /ru-ru/documentation/services/service-bus/
  [Использование очередей Service Bus]: /ru-ru/develop/net/how-to-guides/service-bus-queues/
  [Многоуровневое веб-приложение ASP.NET, использующее таблицы, очереди и большие двоичные объекты службы хранилища Azure]: /ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Модели выполнения Azure]: http://www.windowsazure.com/ru-ru/develop/net/fundamentals/compute/
  [Развертывание веб-приложения ASP.NET на веб-сайте Azure]: http://www.windowsazure.com/ru-ru/develop/net/tutorials/get-started/
