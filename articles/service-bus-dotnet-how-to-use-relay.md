<properties linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Service Bus Relay" pageTitle="How to use Service Bus relay (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Как использовать службу ретрансляции Service Bus

В этом руководстве показано, как использовать службу ретрансляции Service Bus. Примеры написаны на языке C# и используют интерфейс API Windows Communication Foundation с расширениями, содержащимися в сборке Service Bus, которая входит в состав библиотек .NET для Azure. Дополнительную информацию о ретрансляторе Service Bus см. в разделе [Дальнейшие действия][Дальнейшие действия].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span class="short-header">Что такое ретранслятор Service Bus</span>Что такое ретранслятор Service Bus

Служба **ретрансляции** Service Bus позволяет создавать **гибридные приложения**, работающие как в центре обработки данных Azure, так и в локальной среде предприятий. Ретранслятор Service Bus упрощает работу, позволяя безопасно предоставлять службы Windows Communication Foundation (WCF), используемые в корпоративной сети предприятия, в общедоступном облаке, без необходимости открывать подключения брандмауэра или вносить значительные изменения в инфраструктуру корпоративной сети.

![Основные понятия ретрансляции][Основные понятия ретрансляции]

Ретранслятор Service Bus позволяет размещать службы WCF в уже существующей среде предприятия. После этого прослушивание входящих сеансов и запросов к этим службам WCF можно делегировать Service Bus, запущенной в среде Azure. В результате к этим службам сможет получать доступ код приложения, выполняемого в Azure, мобильные сотрудники, а также среды экстрасетей партнеров. Service Bus позволяет безопасно управлять предоставлением доступа к этим службам на детальном уровне. Это обеспечивает мощный и безопасный способ предоставления функциональных возможностей приложения и данных используемых корпоративных решений, позволяя пользоваться ими из облака.

В этом руководстве показано, как использовать ретранслятор Service Bus для создания веб-службы WCF, доступной с помощью привязки канала TCP, который обеспечивает безопасную передачу данных между двумя сторонами.

## <span class="short-header">Создание пространства имен службы</span>Создание пространства имен службы

Чтобы приступить к использованию ретранслятора Service Bus в Azure, сначала необходимо создать пространство имен службы. Это пространство имен службы предоставляет контейнер для адресации ресурсов служебной шины в вашем приложении.

Создание пространства имен службы:

1.  Выполните вход на [портал управления Azure][портал управления Azure].

2.  В левой области навигации портала управления нажмите кнопку **Service Bus**.

3.  В нижней части портала управления нажмите кнопку **Создать**.

    ![][0]

4.  В диалоговом окне **Добавление нового пространства имен** введите имя пространства имен. Система сразу проверит, доступно ли имя.

    ![][1]

5.  Убедившись, что имя пространства имен доступно, выберите страну или регион, где будет размещаться пространство имен (убедитесь, что используете страну или регион, где развертываете вычислительные ресурсы).

    ВАЖНО! Выберите **тот же регион**, который собираетесь выбрать для развертывания приложения. Это обеспечит наилучшую производительность.

6.  Щелкните значок галочки. Теперь система создает пространство имен службы и включает его. Возможно, вам придется подождать несколько минут, пока система выделит ресурсы для вашей учетной записи.

    ![][2]

    Созданное пространство имен появится на портале управления, его активация займет некоторое время. Прежде чем продолжать, подождите, пока состояние не изменится на **Активное**.

## <span class="short-header">Получение учетных данных управления</span>Получение учетных данных управления по умолчанию для пространства имен

Для выполнения операций управления, таких как подключение ретрансляции, над новым пространством имен необходимо получить учетные данные управления для пространства имен.

1.  В левой области навигации щелкните узел **Служебная шина**, чтобы отобразить список доступных пространств имен:

    ![][0]

2.  Выберите из отображенного списка пространство имен, которое вы только что создали.

    ![][3]

3.  Нажмите кнопку **Сведения о подключении**.

    ![][4]

4.  В диалоговом окне **Сведения по доступу к подключению** найдите записи **Издатель по умолчанию** и **Ключ по умолчанию**. Запишите эти значения, так как эти сведения будут использованы ниже для выполнения операций с пространством имен.

## <span class="short-header">Получение пакета NuGet</span>Получение пакета NuGet для Service Bus

Пакет **NuGet** для Service Bus является самым простым способом получить API Service Bus и настроить для приложения все зависимости Service Bus. Расширение NuGet для Visual Studio упрощает установку и обновление библиотек и инструментов в Visual Studio и Visual Studio Express 2012 для Web. Пакет NuGet для Service Bus является самым простым способом получить API Service Bus и настроить для приложения все зависимости Service Bus.

Для установки пакета NuGet в приложении выполните следующие действия:

1.  В обозревателе решений щелкните правой кнопкой мыши **Ссылки**, затем выберите команду **Управление пакетами NuGet**.
2.  Выполните поиск «MicrosoftAzure» и выберите элемент **Azure Service Bus**. Щелкните **Установить**, чтобы выполнить установку, а затем закройте это диалоговое окно.

    ![][5]

## <span class="short-header">Предоставление и использование веб-службы SOAP</span>Как использовать Service Bus для предоставления и использования веб-служб SOAP с помощью TCP

Чтобы предоставить существующую веб-службу WCF SOAP для внешнего использования, необходимо внести изменения в привязки и адреса служб. Для этого может потребоваться изменить файл конфигурации либо внести изменения в код, в зависимости от способа установки и настройки служб WCF. Обратите внимание, что служба WCF позволяет использовать несколько сетевых конечных точек для одной службы, поэтому при добавлении конечных точек Service Bus для внешнего доступа можно одновременно сохранить существующие внутренние конечные точки.

В этом задании необходимо создать простую службу WCF и добавить для нее прослушиватель Service Bus. Это упражнение предполагает определенное знакомство с Visual Studio 2012, поэтому рассматриваются не все подробности создания проекта. Вместо этого в центре внимания оказывается код.

Прежде чем выполнять описанные ниже действия, выполните следующее, чтобы настроить свою среду:

1.  В Visual Studio создайте в рамках решения консольное приложение, содержащее два
    проекта: Client и Service.
2.  Определите в качестве целевой платформы обоих проектов платформу .NET Framework 4.
3.  Добавьте пакет **NuGet для Azure Service Bus** в оба проекта.
    Таким образом, в проект будут добавлены все необходимые ссылки на сборки.

### Как создать службу

Во-первых, создайте саму службу. Каждая служба WCF состоит как минимум из трех частей:

-   Определение контракта, в котором описывается обмен сообщениями
    и вызываемые операции.
-   Реализация указанного контракта.
-   Узел, на котором размещается эта служба и который предоставляет несколько
    конечных точек.

В примерах кода этого раздела рассматривается каждый из эти компонентов.

Контракт определяет одну операцию, **AddNumbers**, которая складывает два числа и возвращает результат. Интерфейс **IProblemSolverChannel** упрощает для клиента управление временем жизни прокси-сервера. Рекомендуется создать этот интерфейс. Стоит поместить это определение контракта в отдельный файл, чтобы на него можно было ссылаться из проектов Client и Service. Также можно скопировать код в оба проекта.

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

При наличии контракта реализация предельно проста:

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**Программная настройка узла службы**

После создания контракта и его реализации можно разместить службу. Размещение происходит внутри объекта **System.ServiceModel.ServiceHost**, который отвечает за управление экземплярами службы и в котором размещаются конечные точки, прослушивающие сообщения. Приведенный ниже код настраивает для службы обычную локальную конечную точку и конечную точку Service Bus для параллельного представления внутренних и внешних конечных точек. Замените строку \*\*namespace\*\* именем своего пространства имен, а key — ключом издателя, полученным на приведенном выше шаге настройки.

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");
    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

В приведенном примере создаются две конечные точки, предназначенные для одной и той же реализации контракта. Одна из них является локальной, а вторая проецируется с помощью Service Bus. Основная разница между ними заключается в используемых привязках: привязка **NetTcpBinding** используется для локальной конечной точки, а привязка **NetTcpRelayBinding** — для конечной точки Service Bus и адресов. Для локальной конечной точки используется локальный сетевой адрес с отдельным портом. Адрес конечной точки Service Bus включает в себя строку sb, имя пространства имен и путь solver. В результате получается следующий универсальный код ресурса (URI): .servicebus.windows.net/solver. Он определяет конечную точку службы как конечную точку TCP Service Bus с полным внешним DNS-именем. Если в функции **Main** приложения Service вместо заполнителей использовать код, как описано выше, будет создана функциональная служба. Если нужно, чтобы служба прослушивала только шину Service Bus, удалите объявление локальной конечной точки.

**Как настроить узел службы в файле App.config**

С помощью файла App.config также можно настроить узел. В этом случае код размещения службы выглядит следующим образом:

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

Определения конечных точек перемещаются в файл App.config. Обратите внимание, что пакет **NuGet** уже добавил в файл App.config ряд определений, являющихся необходимыми расширениями Service Bus. Следующий фрагмент кода, который полностью эквивалентен приведенному выше коду, должен находиться непосредственно под элементом **system.serviceModel**. В этом фрагменте предполагается, что пространство имен C# проекта имеет имя Service.
Укажите вместо заполнителей созданное вами пространство имен службы Service Bus и ключ.

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

После этих изменений служба запускается как и раньше, но с двумя работающими конечными точками: одной локальной и одной прослушивающей в облаке.

### Как создать клиент

**Программная настройка клиента**

Чтобы использовать службу, вы можете создать клиент WCF с помощью объекта **ChannelFactory**. Service Bus использует модель безопасности на основе утверждений, реализуемую с помощью службы контроля доступа (ACS). Класс **TokenProvider** представляет из себя поставщик маркеров безопасности со встроенными методами, которые возвращают ряд хорошо известных поставщиков маркеров. В приведенном ниже примере **SharedSecretTokenProvider** используется для хранения учетных данных общего секрета и обработки получения соответствующих маркеров от службы контроля доступа. Для имени и ключа используются значения, полученные с портала, как описано в предыдущем разделе.

Сначала создайте в проекте клиента ссылку на код контракта **IProblemSolver** или скопируйте ее в него.

Затем замените код в методе **Main** клиента, снова заменяя текст заполнителя на имя пространства служб и ключ Service Bus:

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));
    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Теперь вы можете скомпилировать клиент и службу, а затем запустить их (сначала следует запускать службу). После этого клиент вызовет службу и напечатает «9». Можно запустить клиент и сервер на разных компьютерах, даже в разных сетях, и связь по-прежнему будет работать. Код клиента также может работать в облаке или локально.

**Настройка клиента в файле App.config**

Клиент также можно настроить с помощью файла App.config. Для этого используется следующий код клиента:

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Определения конечных точек перемещаются в файл App.config. Следующий фрагмент кода, который полностью эквивалентен приведенному выше коду, должен находиться непосредственно под элементом **system.serviceModel**. Здесь, как и в коде выше, вместо заполнителей нужно указать созданное вами пространство имен службы Service Bus и ключ.

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

## <span class="short-header">Дальнейшие действия</span>Дальнейшие действия

Теперь, когда вы изучили основы использования службы **ретрансляции** Service Bus, воспользуйтесь следующими ссылками, чтобы получить дополнительную информацию.

-   Создание службы: [Создание службы для служебной шины][Создание службы для служебной шины].
-   Создание клиента: [Построение клиентского приложения Service Bus][Построение клиентского приложения Service Bus].
-   Примеры Service Bus можно скачать с веб-сайта [примеров Azure][примеров Azure].

  [Дальнейшие действия]: #next_steps
  [create-account-note]: ../includes/create-account-note.md
  [Основные понятия ретрансляции]: ./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png
  [портал управления Azure]: http://manage.windowsazure.com
  [0]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png
  [1]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png
  [2]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png
  [3]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png
  [4]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png
  [5]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png
  [Создание службы для служебной шины]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee173564.aspx
  [Построение клиентского приложения Service Bus]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee173543.aspx
  [примеров Azure]: http://code.msdn.microsoft.com/windowsazure
