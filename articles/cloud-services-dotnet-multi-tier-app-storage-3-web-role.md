<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Шаг 3. Веб-роль" pageTitle="Учебник по многоуровневому веб-приложению ASP.NET с Azure — шаг 3, веб-роль" metaKeywords="Учебник по Azure, приложение службы электронной почты, веб-роль ASP.NET ASP 4, контроллеры MVC 4, контроллер веб-API, проект облачной службы" description="Третий учебник в серии, где объясняется, как создать и развернуть приложение службы электронной почты в облачной службе Azure." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Создание веб-роли для приложения службы электронной почты Azure - 3 из 5" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />.

# Создание веб-роли для приложения службы электронной почты Azure — 3 из 5 

Это третий из пяти учебников в серии. В нем показано, как построить и развернуть образец приложения службы электронной почты Azure.  Сведения о приложении и самой серии учебников см. в [первом учебнике серии][firsttutorial].

В этом учебнике вы узнаете следующее:

* Как создать решение, содержащее проект облачной службы с веб-ролью и рабочей ролью. 
* Как работать с таблицами, BLOB-объектами и очередями Azure в представлениях и контроллерах MVC 4.
* Как обрабатывать конфликты параллелизма при работе с таблицами Azure.
* Как настроить веб-роль или веб-проект для использования вашей учетной записи хранения Azure.

 
<h2><a name="cloudproject"></a><span class="short-header">Создание решения</span>Создание решения Visual Studio</h2>

Работа начинается с создания решения Visual Studio с помощью проекта для интерфейсного веб-компонента и проекта для одной из серверных рабочих ролей Azure. Вторую рабочую роль вы добавите позднее. 

(Если требуется запустить веб-интерфейс на веб-сайте Azure, а не в облачной службе Azure, соответствующие инструкции см. в разделе [Альтернативная архитектура][alternativearchitecture] далее в этом учебнике.)

### Создание проекта облачной службы с веб-ролью и рабочей ролью

1. Запустите Visual Studio с повышенным уровнем привилегий.

	>[WACOM.NOTE] В Visual Studio 2013 не нужно использовать повышенные привилегии, так как для новых проектов по умолчанию используется эмулятор среды выполнения приложений.
   
2. В меню **Файл** выберите **Новый проект**.

	![Меню нового проекта][mtas-file-new-project]

3. Разверните узел **C#** и выберите **Облако** в области **Установленные шаблоны**, а затем выберите **Облачная служба Azure**.  

4. Присвойте приложению имя **AzureEmailService** и нажмите кнопку **ОК**.

	![Диалоговое окно "Новый проект"][mtas-new-cloud-project]

5. В диалоговом окне **Новая облачная служба Azure** выберите **Веб-роль ASP.NET** и щелкните стрелку вправо.

	>[WACOM.NOTE] Загруженный код, используемый в этом руководстве, является MVC 4, но не может создать веб-роль MVC 4 в Visual Studio 2013 с помощью инструкций, написанных для Visual Studio 2012. В Visual Studio 2013 выполните следующие действия: (1) пропустите приведенные здесь действия по созданию веб-роли и выполните действия по созданию рабочей роли. (2) После создания рабочей роли щелкните правой кнопкой мыши решение в **обозревателе решений** и выберите **Добавить** -- **Новый проект**. На левой панели диалогового окна **Добавить новый проект** разверните пункт **Интернет** и выберите **Visual Studio 2012**.  (3) Выберите пункт **Веб-приложение ASP.NET MVC 4**, присвойте проекту имя **MvcWebRole** и нажмите кнопку **ОК**. (4) В диалоговом окне **Новый проект ASP.NET** выберите шаблон **Веб-приложение**. (5) Щелкните правой кнопкой мыши пункт **Роли** в разделе **AzureEmailService** **обозревателя решений**, затем щелкните **Добавить** - **Проект веб-роли в решении**. (6) В поле **Связать с проектом роли** выберите проект **MvcWebRole** и нажмите кнопку **ОК**.

	![Диалоговое окно нового облачного проекта Azure][mtas-new-cloud-service-dialog]

6. Наведите указатель мыши на элемент **MvcWebRole1** в правом столбце, а затем щелкните значок карандаша, чтобы изменить имя веб-роли. 

7. Введите MvcWebRole в качестве нового имени и нажмите клавишу ВВОД.

	![Диалоговое окно нового облачного проекта Azure - переименование веб-роли][mtas-new-cloud-service-dialog-rename]

8. Выполните ту же процедуру для добавления элемента **Рабочая роль**, присвойте ему имя WorkerRoleA и нажмите кнопку **ОК**.

	![Диалоговое окно нового облачного проекта Azure - добавление рабочей роли][mtas-new-cloud-service-add-worker-a]

5. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **Веб-приложение**.

6. Убедитесь, что в раскрывающемся списке **Обработчик представлений** выбрано значение **Razor**, а затем нажмите кнопку **ОК**.

	![Диалоговое окно "Новый проект"][mtas-new-mvc4-project]

### Задание верхнего колонтитула, меню и нижнего колонтитула страницы

В этом разделе вы обновите верхние и нижние колонтитулы и пункты меню, которые отображаются на каждой странице для веб-интерфейса администратора.  Приложение будет иметь три набора веб-страниц администратора: один для списков рассылки, один для подписчиков на списки рассылки и один для сообщений.

1. В **обозревателе решений** разверните папку "Views\Shared" и откройте файл &#95;Layout.cshtml.

	![_Layout.cshtml в обозревателе решений][mtas-opening-layout-cshtml]

2. В элементе **&lt;title&gt;** замените "Мое приложение ASP.NET MVC" на "Служба электронной почты Azure".

3. В элементе **&lt;p&gt;** с классом "site-title" замените "Здесь размещается ваша эмблема" на "Служба электронной почты Azure", а также замените Home на MailingList.

	![заголовок и верхний колонтитул в файле _Layout.cshtml][mtas-title-and-logo-in-layout]

4. Удалите раздел меню:

	![меню в _Layout.cshtml][mtas-menu-in-layout]

4. Вставьте новый раздел меню в то место, где находился старый раздел:

        <ul id="menu">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

4. В элементе **&lt;footer&gt;** замените "Мое приложение ASP.NET MVC" на "Служба электронной почты Azure".<br/>

	![нижний колонтитул в _Layout.cshtml][mtas-footer-in-layout]

### Локальный запуск приложения

1. Нажмите сочетание клавиш CTRL+F5 для запуска приложения.

	Главная страница приложения откроется в браузере, используемом по умолчанию.

	![домашняя страница][mtas-home-page-before-adding-controllers]

	Приложение запускается в эмуляторе среды выполнения приложений Azure.  Значок эмулятора вычислений отображается на панели задач Windows:

	![Эмулятор вычислений на панели задач][mtas-compute-emulator-icon]


<h2><a name="tracing"></a><span class="short-header">Настройка трассировки</span>Настройка трассировки</h2>

Чтобы включить сохранение данных трассировки, откройте файл *WebRole.cs* и добавьте следующий метод `ConfigureDiagnostics`. Добавьте код, который вызывает новый метод в методе `OnStart`.

>[WACOM.NOTE] В Visual Studio 2013 вместо выполнения следующих шагов по изменению кода в файле *WebRole.cs* вручную щелкните правой кнопкой мыши проектMvcWebRole, щелкните **Добавить существующий элемент** и добавьте файл *WebRole.cs* из загруженного проекта.

    private void ConfigureDiagnostics()
    {
        DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
    }

    public override bool OnStart()
    {
        ConfigureDiagnostics();
        return base.OnStart();
    }

Метод `ConfigureDiagnostics` описан во [втором учебнике][tut2].





<h2><a name="restarts"></a><span class="short-header">Перезапуски</span>Добавьте код, чтобы эффективно обрабатывать перезагрузки.</h2>

Приложения облачной службы Azure перезапускаются приблизительно два раза в месяц для обновления операционной системы. (Дополнительные сведения об обновлениях ОС см. в разделе [Перезапуск экземпляра роли из-за обновлений ОС](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).) При завершении работы веб-приложения возникает событие `OnStop`. Стандартная веб-роль, созданная в Visual Studio, не переопределяет метод `OnStop`, поэтому приложение будет иметь всего несколько секунд для завершения обработки HTTP-запросов перед завершением работы. Можно добавить код, переопределяющий метод  `OnStop` для обеспечения корректного завершения работы.

Для обработки завершений работы и перезагрузок откройте файл *WebRole.cs* и добавьте следующий метод переопределения `OnStop`.

      public override void OnStop()
      {
          Trace.TraceInformation("OnStop called from WebRole");
          var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
          while (rcCounter.NextValue() > 0)
          {
              Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
              System.Threading.Thread.Sleep(1000);
          }           
      }

Этот код требует применения дополнительного оператора `using`:

      using System.Diagnostics;

До завершения работы приложения у метода `OnStop` есть до 5 минут времени на выполнение выхода. В метод `OnStop` можно добавить бездействующий вызов длительностью 5 минут, чтобы дать приложению максимум времени на обработку текущих запросов, однако в случае правильного масштабирования приложение должно затратить на обработку оставшихся запросов гораздо меньше 5 минут. Рекомендуется как можно быстрее остановить работу, чтобы приложение могло максимально быстро выполнить перезагрузку и продолжить обработку запросов.

После завершения работы роли системой Azure подсистема балансировки нагрузки прекращает отправку запросов для экземпляра этой роли, после чего вызывается метод `OnStop`. Если другой экземпляр роли отсутствует, обработка запросов прекращается до окончания завершения работы и перезапуска роли (что обычно занимает несколько минут). Это одна из причин, по которой в соглашении об уровне обслуживания Azure требуется наличие по крайней мере двух экземпляров каждой роли, чтобы воспользоваться преимуществами гарантии времени бесперебойной работы.

В коде для метода `OnStop` создается счетчик производительности ASP.NET для `Текущих запросов`. Значение счетчика `Текущих запросов` содержит текущее число запросов, включая находящиеся в очереди, выполняемые в данный момент или ожидающие записи в клиент. Значение `Текущих запросов` проверяется каждую секунду, и как только оно становится равным нулю, возвращается метод `OnStop`. После возврата `OnStop` работа роли завершается.

Данные трассировки не сохраняются при вызове из метода `OnStop` без выполнения [передачи по требованию](http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433075.aspx). Сведения трассировки `OnStop` можно просмотреть в режиме реального времени с помощью служебной программы [dbgview](http://technet.microsoft.com/ru-ru/sysinternals/bb896647.aspx) из подключения к удаленному рабочему столу.

<h2><a name="updatescl"></a><span class="short-header">Обновление клиентской библиотеки хранения</span>Обновление пакета NuGet клиентской библиотеки хранения</h2>

>[WACOM.NOTE] Этот шаг может быть ненужным. Если пакет NuGet хранилища Azure не отображается в списке обновлений, установленная версия является текущей.

Платформа API, предназначенная для работы с таблицами, очередями и BLOB-объектами хранилища Azure, называется клиентской библиотекой хранения (SCL). Этот интерфейс API включен в пакет NuGet в шаблоне проекта облачной службы. Однако на момент написания данного учебника шаблоны проекта содержат библиотеку SCL версии 1.7, а не актуальной версии 2.0. Таким образом, прежде чем приступить к написанию кода, следует обновить пакет NuGet.

1. В меню **Сервис** Visual Studio наведите указатель на элемент **Диспетчер пакетов библиотеки** и затем выберите **Управление пакетами NuGet для решения**.

	![Управление пакетами NuGet для решения в меню][mtas-manage-nuget-for-solution]

2. В левой части диалогового окна **Управление пакетами NuGet** выберите **Обновления**, затем выполните прокрутку вниз до пакета **Хранилище Azure** и щелкните элемент **Обновить**.

	![Пакет хранилища Azure в диалоговом окне "Управление пакетами NuGet"][mtas-update-storage-nuget-pkg]

3. Убедитесь, что в диалоговом окне **Выбор проектов** выбраны оба проекта, и нажмите кнопку **ОК**.

	![Выбор обоих проектов в диалоговом окне "Выбор проектов"][mtas-nuget-select-projects]
 
4. Примите условия лицензионного соглашения для завершения установки пакета, а затем закройте диалоговое окно **Управление пакетами NuGet**.

5. При наличии в файле *WorkerRole.cs* проекта WorkerRoleA инструкции `using` удалите ее, так как она больше не нужна.

		using Microsoft.WindowsAzure.StorageClient;


Версия 1.7 библиотеки SCL включает в себя поставщик LINQ, который упрощает написание кода для запросов таблиц. На момент написания данного учебника у слоя служб таблиц (TSL) 2.0 еще нет поставщика LINQ. Если вы хотите использовать LINQ, следует по-прежнему обращаться к поставщику LINQ 1.7 в пространстве имен [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.storage.table.dataservices.aspx). Версия 2.0 TSL была разработана для повышения производительности, а поставщик LINQ 1.7 не позволяет использовать преимущества всех этих улучшений. В примере приложения применяется версия 2.0 TSL, поэтому LINQ для запросов не используется. Дополнительные сведения о SCL и TSL 2.0 см. в ресурсах, приведенных в конце [последнего учебника данной серии][tut5].

>[WACOM.NOTE] В клиентскую библиотеку хранилища 2.1 добавлена обратная поддержка LINQ, однако в этом учебнике LINQ не используется для хранения табличных запросов. Текущий SCL также поддерживает асинхронное программирование, однако в этом учебнике не приводится асинхронный код. Дополнительные сведения об асинхронном программировании и пример кода, в котором используется это программирование вместе с Azure SCL см. в следующей главе электронной книге и в прилагаемом к ней доступном для загрузки проекте: [Использование поддержки асинхронного режима .NET 4.5â€ ™ во избежание блокировки вызовов](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).


<h2><a name="addref2"></a><span class="short-header">Добавление ссылки на библиотеку клиента хранения 1.7</span>Добавление ссылки на сборку библиотеки клиента хранения 1.7</h2>

>[WACOM.NOTE] Если вы используете SCL 2.1 или более поздней версии (Visual Studio 2013 с последним пакетом SDK), пропустите этот раздел.

Версия 2.0 библиотеки клиента хранения (SCL) не предоставляет все необходимое для диагностики, поэтому следует добавить ссылку на сборку 1.7.

4. Щелкните проект MvcWebRole правой кнопкой мыши и выберите **Добавить ссылку**.

5. Нажмите кнопку **Обзор...** в нижней части диалогового окна.

6. Перейдите в следующую папку:

        C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. Выберите *Microsoft.WindowsAzure.StorageClient.dll* и нажмите кнопку **Добавить**.

8. В диалоговом окне **Диспетчер ссылок** нажмите кнопку **ОК**.

1. Повторите эту процедуру для проекта WorkerRoleA.



<h2><a name="createifnotexists"></a><span class="short-header">Код App_Start</span>Добавление кода для создания таблиц, очередей и контейнеров BLOB-объектов в методе Application_Start</h2>

Веб-приложение будет использовать таблицу `MailingList`, таблицу `Message`, очередь `azuremailsubscribequeue` и контейнер BLOB-объектов `azuremailblobcontainer`. Можно создать их вручную с помощью такого средства, как обозреватель хранилищ Azure, но затем потребуется делать это вручную каждый раз, когда вы начинаете использовать приложение с новой учетной записью хранения. В этом разделе вы добавите код, который выполняется при запуске приложения, проверяет наличие необходимых таблиц, очередей и контейнеров и создает их в случае отсутствия. 

Можно добавить этот код однократного запуска в метод `OnStart` в файле *WebRole.cs* или в файл *Global.asax*. В рамках этого учебника вы инициализируете хранилище Azure в файле *Global.asax*, так как это работает для веб-сайтов Azure, а также для веб-ролей облачных служб Azure.

1. В **обозревателе решений** разверните *Global.asax* и откройте *Global.asax.cs*.

2. Добавьте новый метод `CreateTablesQueuesBlobContainers` после метода `Application_Start` и затем вызовите новый метод из метода `Application_Start`, как показано в следующем примере:

        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            WebApiConfig.Register(GlobalConfiguration.Configuration);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
            AuthConfig.RegisterAuth();
            // Verify that all of the tables, queues, and blob containers used in this application
            // exist, and create any that don't already exist.
            CreateTablesQueuesBlobContainers();
        }

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
            //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();
            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }
	
3. Щелкните правой кнопкой мыши синюю волнистую линию под элементом `RoleEnvironment`, выберите пункт **Разрешить** и **using Microsoft.WindowsAzure.ServiceRuntime**. 

	![rightClick][mtas-4]

1. Щелкните правой кнопкой мыши синюю волнистую линию под элементом `CloudStorageAccount`, выберите пункт **Разрешить** и **using Microsoft.WindowsAzure.Storage**.  

1. Кроме того, можно вручную добавить следующие операторы using:

	    using Microsoft.WindowsAzure.ServiceRuntime;
	    using Microsoft.WindowsAzure.Storage;
	
1. Создайте приложение, которое сохраняет файл и проверяет отсутствие ошибок компиляции.

В следующих разделах создаются компоненты веб-приложения и их можно будет протестировать с хранилищем разработки или вашей учетной записью хранения без необходимости вручную создавать таблицы, очереди или контейнер BLOB-объектов.



<h2><a name="mailinglist"></a><span class="short-header">Список рассылки</span>Создание и тестирование контроллера и представлений списка рассылки</h2>

Веб-интерфейс **Список рассылки** используется администраторами для создания, редактирования и отображения списков рассылки, таких как "Объявления отдела истории университета Contoso" и "Открытые инженерные вакансии Fabrikam".

### Добавление класса сущностей MailingList в папку моделей

Класс сущностей `MailingList` используется для строк таблицы `MailingList`, содержащих сведения о списке, такие как его описание и адрес электронной почты "От" для сообщений электронной почты, отправленных в этот список.  

1. В **обозревателе решений** щелкните правой кнопкой мыши папку `Модели` в проекте MVC и выберите **Добавить существующий элемент**.

	![Добавление существующего элемента в папку моделей][mtas-add-existing-item-to-models]

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *MailingList.cs* в папке `Модели` и нажмите кнопку **Добавить**.

3. Откройте файл *MailingList.cs* и изучите код.

	    public class MailingList : TableEntity
	    {
	        public MailingList()
	        {
	            this.RowKey = "mailinglist";
	        }
	
	        [Required]
	        [RegularExpression(@"[\w]+",
	         ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
	        [Display(Name = "List Name")]
	        public string ListName
	        {
	            get
	            {
	                return this.PartitionKey;
	            }
	            set
	            {
	                this.PartitionKey = value;
	            }
	        }
	
	        [Required]
	        [Display(Name = "'From' Email Address")]
	        public string FromEmailAddress { get; set; }
	
	        public string Description { get; set; }
	    }
			

	API TSL 2.0 хранилища Azure требует, чтобы классы сущностей, используемые для работы с таблицами, были производными от [TableEntity][]. Этот класс определяет поля `PartitionKey`, `RowKey`, `TimeStamp` и `ETag`. Свойства `TimeStamp` и `ETag` используются системой. Вы увидите, как свойство `ETag` используется для обработки параллелизма, позже в этом учебнике. 

	(Имеется также класс[DynamicTableEntity], позволяющий работать со строками таблицы как с коллекциями словарей пар "ключ/значение" вместо использования предопределенных классов моделей. Дополнительные сведения см. в разделе [Подробный обзор таблиц клиентской библиотеки хранения 2.0 Azure][deepdive].)

	Ключ раздела таблицы `mailinglist` — это имя списка. В этом классе сущностей доступ к значению ключа раздела может осуществляться с помощью свойства `PartitionKey` (определяется в классе `TableEntity`) или свойства `ListName` (определяется в классе `MailingList`).  Свойство `ListName` использует `PartitionKey` в качестве резервной переменной. Определение свойства `ListName` позволяет использовать более содержательное имя переменной в коде и упрощает программирование веб-интерфейса, поскольку атрибуты форматирования и проверки DataAnnotations можно добавить в свойство `ListName`, но нельзя добавить непосредственно в свойство `PartitionKey`.

	Атрибут `RegularExpression` свойства `ListName` вынуждает MVC проверять вводимые пользователем данные, чтобы гарантировать, что введенное имя списка содержит только алфавитно-цифровые символы или знаки подчеркивания. Это ограничение было реализовано для упрощения имен списков, чтобы их было удобно использовать в строках запроса в URL-адресах. 

	**Примечание.** Если вы хотите использовать менее строгий формат имен списков, можно разрешить использование других символов и URL-кодирования для имен списков при их использовании в строках запроса. Однако некоторые символы в ключах строк и разделов таблиц Azure использовать нельзя, поэтому необходимо исключить хотя бы их. Сведения о символах, использование которых в полях ключей разделов или строк запрещено или вызывает проблемы, см. в разделах [Общие сведения о модели данных службы таблиц][tabledatamodel] и [Символ % в PartitionKey или RowKey][percentinkeyfields].

	Класс `MailingList` определяет конструктор по умолчанию, который устанавливает для параметра `RowKey` жестко запрограммированную строку "mailinglist", поскольку все строки списков рассылки в этой таблице используют это значение в качестве ключа строки. (Объяснение структуры таблицы см. в [первом учебнике серии][firsttutorial].) Для этой цели можно выбрать любую константу при условии, что она никогда не совпадает с адресом электронной почты, который является ключом строки для строк подписчика в этой таблице.

	При создании новой сущности `MailingList` всегда вводится имя списка и адрес электронной почты "От", поэтому они имеют атрибуты `Required`.

	Атрибуты `Display` указывают заголовок по умолчанию, используемый для поля в пользовательском интерфейсе MVC. 

### Добавление контроллера MVC MailingList

1. В **обозревателе решений** щелкните правой кнопкой мыши папку "Контроллеры" в проекте MVC и выберите **Добавить существующий элемент**.

	![Add existing item to Controllers folder][mtas-add-existing-item-to-controllers]

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл*MailingListController.cs* в папке `Контроллеры` и нажмите кнопку **Добавить**.

3. Откройте файл *MailingListController.cs* и изучите код.

	Конструктор по умолчанию создает объект `CloudTable`, используемый для работы с таблицей `mailinglist`.

	    public class MailingListController : Controller
	    {
	        private CloudTable mailingListTable;
	
	        public MailingListController()
	        {
	            var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
	            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
	            //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
	
	            var tableClient = storageAccount.CreateCloudTableClient();
	            mailingListTable = tableClient.GetTableReference("mailinglist");
	        }
		
	Код получает учетные данные для вашей учетной записи хранения Azure из файла параметров проекта облачной службы, чтобы подключиться к этой учетной записи хранения. (Вы настроите эти параметры позднее — перед тестированием контроллера.) Если вы собираетесь запустить проект MVC на веб-сайте Azure, можно получить строку подключения из файла Web.config.

	Далее идет метод `FindRow`, который вызывается каждый раз, когда контроллеру требуется найти конкретную запись списка рассылки в таблице `MailingList`, например, чтобы изменить запись списка рассылки. Этот код извлекает одну сущность `MailingList` с помощью переданных в него значений ключа раздела и ключа строки. Этот контроллер редактирует те строки, которые имеют ключ строки "MailingList", поэтому значение "MailingList" можно было бы жестко задать для строки ключа, однако указание как ключа раздела, так и ключа строки представляет собой шаблон, используемый для методов `FindRow` на всех контроллерах.

        private MailingList FindRow(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }

            return mailingList;
        }

	Полезно сравнить метод `FindRow` в контроллере `MailingList`, возвращающий строку списка рассылки, с методом `FindRow` в контроллере `Subscriber`, возвращающим строку подписчика из той же самой таблицы `mailinglist`.

        private Subscriber FindRow(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

	Единственное различие между этими двумя запросами заключается в типе модели, которую они передают в метод [TableOperation.Retrieve](http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx). Тип модели задает схему (свойства) строки или строк, которые предполагается получить в результате выполнения запроса. Одна таблица может иметь разные схемы в разных строках. Обычно при чтении строки указывается тот же тип модели, который использовался для создания это строки.
        
	Страница **Индекс** отображает все строки списка рассылки, поэтому запрос в методе `Index` возвращает все сущности `MailingList`, имеющие ключ строки "mailinglist" (другие строки в таблице имеют адрес электронной почты, совпадающий с ключом строки, и содержат сведения о подписчике).

                var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

	Метод `Index` дополняет этот запрос с кодом, предназначенным для обработки условий времени ожидания. 

        public ActionResult Index()
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            List<MailingList> lists;
            try
            {
                var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
            }
            catch (StorageException se)
            {
                ViewBag.errorMessage = "Timeout error, try again. ";
                Trace.TraceError(se.Message);
                return View("Error");
            }

            return View(lists);
        }

	Если не указать параметры времени ожидания, API автоматически предпринимает три повторных попытки, каждый раз экспоненциально увеличивая пределы времени ожидания. Для веб-интерфейса с пользователем, ожидающим отображения страницы, это может привести к недопустимому увеличению времени ожидания. Таким образом, этот код определяет линейные попытки (то есть без увеличения предела времени ожидания) и разумный предел времени ожидания для пользователя. 

	Когда пользователь нажимает кнопку **Создать** на странице **Создание**, связыватель модели MVC создает сущность `MailingList` из входных данных, введенных в представлении, а метод `HttpPost Create` добавляет эту сущность в таблицу.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                mailingListTable.Execute(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

 Для страницы**Правка** метод `HttpGet Edit` ищет строку, а метод `HttpPost` обновляет ее.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Concurrency error
                        var currentMailingList = FindRow(partitionKey, rowKey);
                        if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                        {
                            ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                        }
                        if (currentMailingList.Description != editedMailingList.Description)
                        {
                            ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                        }
                        ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                            + "was modified by another user after you got the original value. The "
                            + "edit operation was canceled and the current values in the database "
                            + "have been displayed. If you still want to edit this record, click "
                            + "the Save button again. Otherwise click the Back to List hyperlink.");
                         ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
                    }
                    else
                    {
                        throw; 
                    }
                }
            }
            return View(editedMailingList);
        }

	Блок try-catch обрабатывает ошибки параллелизма. Исключение параллелизма возникает в том случае, если пользователь выбирает список рассылки для редактирования, а затем во время отображении страницы **Правка** в браузере другой пользователь изменяет тот же самый список рассылки. Когда это происходит, код отображает предупреждение и указывает, какие поля были изменены другим пользователем.  API TSL использует `ETag` для выполнения проверки на наличие конфликтов параллелизма. Каждый раз при обновлении строки таблицы значение `ETag` изменяется.  При получении строки для редактирования вы сохраняете значение `ETag`, а при выполнении операции обновления или удаления передаете это сохраненное значение `ETag`. (Представление `Правка ` имеет скрытое поле для значения ETag.) Если операция обновления обнаруживает, что значение `ETag` для обновляемой записи отличается от значения `ETag`, переданного вами в эту операцию, она вызывает исключение параллелизма. Если вас не интересуют конфликты параллелизма, можно указать звездочку ("*") для поля ETag в сущности, передаваемой в операцию обновления, после чего такие конфликты игнорируются. 

	Примечание. Сообщение об ошибке HTTP 412 относится не только к ошибкам параллелизма. Оно может быть инициировано API SCL для других ошибок.

	Для страницы **Удаление** метод `HttpGet Delete` ищет строку для отображения ее содержимого, а метод `HttpPost` удаляет строку `MailingList`, а также все связанные с ней строки `Subscriber` в таблице `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

	В случае удаления большого количества подписчиков код удаляет записи пакетами. Стоимость транзакции для удаления одной строки равна стоимости удаления 100 строк в пакетном режиме. Максимальное количество операций, которые можно выполнить в рамках одного пакета, составляет 100. 

	Хотя этот цикл обрабатывает как строки `MailingList`, так и строки `Subscriber`, он считывает их все в класс сущностей `MailingList`, так как для операции `Delete` нужны только поля `ETag`, `RowKey` и `PartitionKey`.

### Добавление представлений MVC MailingList

2. Создайте новую папку в папке *Представления* **обозревателя решений** в проекте MVC и назовите ее *MailingList*.

1. Щелкните правой кнопкой мыши новую папку *Представления\MailingList* и выберите пункт **Добавить существующий элемент**.

	![Добавление существующего элемента в папку представлений][mtas-add-existing-item-to-views]

2. Перейдите к папке, куда вы загрузили пример приложения, выберите все четыре файла CSHTML в папке *Представления\MailingList* и нажмите кнопку **Добавить**.

3. Откройте файл *Edit.cshtml* и изучите код.

		@model MvcWebRole.Models.MailingList
				@{
		    ViewBag.Title = "Edit Mailing List";
		}
				<h2>Edit Mailing List</h2>
              @using (Html.BeginForm()) {
              @Html.AntiForgeryToken()
              @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
		        <legend>MailingList</legend>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ListName)
		        </div>
		        <div class="editor-field">
		            @Html.DisplayFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Description)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Description)
		            @Html.ValidationMessageFor(model => model.Description)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.FromEmailAddress)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.FromEmailAddress)
		            @Html.ValidationMessageFor(model => model.FromEmailAddress)
		        </div>
		        <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
				
	Этот код является типичным для представлений MVC.  Обратите внимание на наличие скрытого поля для сохранения значения `ETag`, которое используется для обработки конфликтов параллелизма. Обратите внимание и на то, что поле `ListName` имеет вспомогательный объект `DisplayFor` вместо `EditorFor`. Мы не разрешали странице **Правка** изменять имя списка, так как это потребовало бы включения сложного кода в контроллер: методу `HttpPost Edit` пришлось бы удалить существующую строку списка рассылки и все соответствующие строки подписчиков и повторно вставить их с новым значением ключа. В рабочем приложении такое усложнение может иметь смысл. Как вы увидите позже, контроллер `Subscriber` не позволяет изменять имя списка, поскольку одновременно работа осуществляется только с одной строкой. 

	Код *Create.cshtml* и *Delete.cshtml* аналогичен коду *Edit.cshtml*.

4. Откройте *Index.cshtml* и изучите код.

	    @model IEnumerable<MvcWebRole.Models.MailingList>
	    @{
	        ViewBag.Title = "Mailing Lists";
	    }
	    <h2>Mailing Lists</h2>
	    <p>
	        @Html.ActionLink("Create New", "Create")
	    </p>
	    <table>
	        <tr>
	            <th>
	                @Html.DisplayNameFor(model => model.ListName)
	            </th>
	            <th>
	                @Html.DisplayNameFor(model => model.Description)
	            </th>
	            <th>
	                @Html.DisplayNameFor(model => model.FromEmailAddress)
	            </th>
	            <th></th>
	        </tr>
	    @foreach (var item in Model) {
	        <tr>
	            <td>
	                @Html.DisplayFor(modelItem => item.ListName)
	            </td>
	            <td>
	                @Html.DisplayFor(modelItem => item.Description)
	            </td>
	            <td>
	                @Html.DisplayFor(modelItem => item.FromEmailAddress)
	            </td>
	            <td>
	                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
	                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
	            </td>
	        </tr>
	    }
	    </table>
	
	Этот код также является типичным для представлений MVC. Гиперссылки **Изменить** и **Удалить** указывают параметры строки ключа раздела и ключа строки, чтобы определить конкретную строку.  В действительности для сущностей `MailingList` требуется только ключ раздела, поскольку ключ строки всегда имеет значение "MailingList", однако сохраняются оба ключа, чтобы обеспечить согласованность кода представления MVC для всех контроллеров и представлений.

### Назначение MailingList в качестве контроллера по умолчанию

1. Откройте *Route.config.cs* в папке *App_Start*.

2. В строке с параметрами по умолчанию измените контроллер по умолчанию с "Home" на "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }





<h2><a name="configurestorage"></a><span class="short-header">Настройка хранилища</span>Настройка веб-роли на использование тестовой учетной записи хранения Azure</h2>

Вам предстоит ввести параметры для своей тестовой учетной записи хранения, которая будет использоваться при локальном запуске проекта.  Чтобы добавить новый параметр, необходимо добавить его как для облачной, так и для локальной среды, однако значение для облака можно изменить позже. Позднее вы будете добавлять те же параметры для рабочей роли A.

(Если требуется запустить веб-интерфейс на веб-сайте Azure, а не в облачной службе Azure, соответствующие инструкции см. в разделе [Альтернативная архитектура][alternativearchitecture] далее в этом учебнике.)

1. В **обозревателе решений** щелкните правой кнопкой мыши элемент **MvcWebRole** в области **Роли** облачного проекта **AzureEmailService**, а затем выберите пункт **Свойства**.

	![Свойства веб-роли][mtas-mvcwebrole-properties-menu]

2. Убедитесь, что в раскрывающемся списке **Конфигурация службы** выбрано значение **Все конфигурации**.

2. Откройте вкладку **Параметры** и щелкните **Добавить параметр**.

3. Введите StorageConnectionString в столбце **Имя**.

4. Выберите элемент **Строка подключения** в раскрывающемся списке **Тип**.  

6. Нажмите кнопку с многоточием (**...**) в правом конце строки, чтобы открыть диалоговое окно **Строка подключения учетной записи хранения**.

	![Щелкните элемент "Свойства" правой кнопкой мыши][mtas-elip]<br/>

7. В диалоговом окне **Создание строки подключения хранилища** выберите переключатель **Ваша подписка** и ссылку **Загрузить параметры публикации**. 

	>[WACOM.NOTE] Благодаря последней версии пакета SDK нет необходимости загружать что-либо; необходимо выбрать одну из доступных учетных записей хранения в раскрывающемся списке.

	**Примечание.** Если вы настроили параметры из учебника 2 на том же компьютере, где работаете с данным учебником, вам не нужно повторно загружать параметры, достаточно щелкнуть **Ваша подписка**, а затем выбрать правильное значение для параметров **Подписка** и **Имя учетной записи**.

	![Щелкните элемент "Свойства" правой кнопкой мыши][mtas-enter]<br/>

	При выборе ссылки **Загрузить параметры публикации** Visual Studio запускает новый экземпляр обозревателя по умолчанию с URL-адресом страницы портала управления Azure, на которой можно загрузить параметры публикации. Если вы не выполнили вход на портал, отображается соответствующий запрос. После входа браузер предлагает сохранить параметры публикации. Запомните место сохранения параметров.

	![Параметры публикации][mtas-3]

1. В диалоговом окне **Создание строки подключения хранилища** щелкните элемент **Импорт** и затем перейдите к файлу параметров публикации, созданному на предыдущем шаге.

1. Выберите нужную подписку и учетную запись хранения и нажмите кнопку **ОК**.

	![Выберите учетную запись хранения][mtas-5]

1. Для задания строки подключения `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` выполните ту же саму процедуру, которая использовалась для строки подключения `StorageConnectionString`.

	Загружать файл параметров публикации повторно не требуется. При нажатии кнопки с многоточием для строки подключения `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` вы обнаружите, что диалоговое окно **Создание строки подключения хранилища** запоминает сведения о вашей подписке. При щелчке переключателя **Ваша подписка** нужно просто выбрать те же значения **Подписка** и **Имя учетной записи**, которые вы выбрали ранее, и нажать кнопку **ОК**. 

2. Выполните ту же процедуру, которую использовали для двух строк подключения, для роли MvcWebRole, чтобы задать строки подключения для роли WorkerRoleA.

При добавлении нового параметра с помощью кнопки **Добавить параметры** он был добавлен в XML-код файла *ServiceDefinition.csdf*, а также в каждый из двух файлов конфигурации *CSCFG*. Visual Studio добавляет в файл *ServiceDefinition.csdf* следующий XML-код.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

В каждый файл конфигурации *CSCFG* добавляется следующий XML-код.

	   <Setting name="StorageConnectionString"
	   value="DefaultEndpointsProtocol=https;
	   AccountName=azuremailstorage;
	   AccountKey=[your account key]" />

Можно вручную добавить параметры в файл *ServiceDefinition.csdf* и два файла конфигурации *CSCFG*, однако применение редактора свойств имеет следующие преимущества для строк подключения:

- Можно добавить новый параметр только в одно местоположение, после чего правильный XML-код добавляется во все три файла.
- Правильный XML-код создается для трех файлов параметров. Файл *ServiceDefinition.csdf* определяет параметры, которые должны присутствовать в каждом файле конфигурации *CSCFG*. Если параметры в файле *ServiceDefinition.csdf* и двух файлах конфигурации *CSCFG* не согласованы между собой, Visual Studio может выдать следующее сообщение об ошибке: "Текущая модель службы рассинхронизирована. Убедитесь, что конфигурация службы и файлы определений являются допустимыми".

	![Ошибка, вызванная недопустимыми файлами определений и конфигурацией службы][mtas-er1]

При возникновении этой ошибки редактор свойств не будет работать до устранения проблемы несоответствия.

### Тестирование приложения

1. Запустите проект, нажав сочетание клавиш CTRL+F5.

	![Пустая страница индексов MailingList][mtas-mailing-list-empty-index-page]

2. Используйте функцию **Создать**, чтобы добавить списки рассылки, и попробуйте использовать функции **Изменить** и **Удалить**, чтобы убедиться, что они работают.

	![Страница индексов MailingList со строками][mtas-mailing-list-index-page]



<h2><a name="subscriber"></a><span class="short-header">Подписчик</span>Создание и тестирование контроллера и представлений подписчика</h2>

Веб-интерфейс **Subscriber** используется администраторами для добавления новых подписчиков в список рассылки, а также для редактирования, отображения и удаления существующих подписчиков. 

### Добавление класса сущностей Subscriber в папку моделей

Класс сущностей `Subscriber` используется для строк в таблице `MailingList`, содержащих сведения о подписчиках для списка. Эти строки содержат такие сведения, как адрес электронной почты пользователя и состояние его проверки.  

1. В **обозревателе решений** щелкните правой кнопкой мыши папку *Модели* в проекте MVC и выберите **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *Subscriber.cs* в папке *Модели* и нажмите кнопку **Добавить**.

3. Откройте *Subscriber.cs* и изучите код.

		    public class Subscriber : TableEntity
		    {
		        [Required]
		        public string ListName
		        {
		            get
		            {
		                return this.PartitionKey;
		            }
		            set
		            {
		                this.PartitionKey = value;
		            }
		        }
		
		        [Required]
		        [Display(Name = "Email Address")]
		        public string EmailAddress
		        {
		            get
		            {
		                return this.RowKey;
		            }
		            set
		            {
		                this.RowKey = value;
		            }
		        }
		
		        public string SubscriberGUID { get; set; }
		
                public bool? Verified { get; set; }
		    }
		

	Как и класс сущностей `MailingList`, класс сущностей `Subscriber` используется для чтения и записи строк в таблице `mailinglist`. Вместо константы "mailinglist" строки `Subscriber` используют для ключа строки адрес электронной почты.  (Объяснение структуры таблицы см. в [первом учебнике серии][firsttutorial].) Поэтому определяется свойство `EmailAddress`, использующее свойство `RowKey` в качестве своего резервного поля, точно так же, как `ListName` использует `PartitionKey` в качестве резервного поля. Как упоминалось ранее, это позволяет поместить в свойства атрибуты форматирования и проверки DataAnnotations.

	Значение `SubscriberGUID` создается при создании сущности `Subscriber`. Оно используется в ссылках на подписку и отказ от нее, чтобы оформить подписку адреса электронной почты и отменить ее могли только авторизованные лица.
	При первоначальном создании строки для нового подписчика свойство `Проверено` имеет значение `false`. Значение свойства `Проверено` изменяется на `true` только после того, как новый подписчик щелкает гиперссылку **Подтвердить** в приветственном сообщении. Если сообщение отправляется в список, пока значение `Проверено` для подписчика равно `false`, сообщение электронной почты этому подписчику не отправляется.

	Свойство `Проверено` в сущности `Subscriber` определяется как допускающее значение NULL. Когда вы указываете, что запрос должен возвращать сущности `Subscriber`, некоторые из извлеченных строк могут не иметь свойство `Проверено`. Поэтому сущность `Subscriber` определяет свойство `Проверено` как допускающее значение NULL, что позволяет ему точнее отражать фактическое содержимое строки, если запрос возвращает строки таблицы, не имеющие свойства *Проверено*. Возможно, вы привыкли работать с таблицами SQL Server, в которых каждая строка таблицы имеет одинаковую схему. Каждая строка таблицы хранилища Azure представляет собой коллекцию свойств, а каждая строка может иметь различный набор свойств. Например, в примере приложения службы электронной почты Azure строки с ключом строки "MailingList" не имеют свойства `Проверено`.  Если запрос возвращает строки таблицы, которые не имеют свойства `Проверено`, то при создании экземпляра класса сущностей `Subscriber` свойство `Проверено` в объекте сущности будет равно NULL.  Если бы свойство не допускало значения NULL, вы бы получили одинаковое значение `false` для строк со свойством `Проверено`, равным `false`, и для строк, вообще не имеющих свойства `Проверено`. Таким образом, для работы с таблицами Azure рекомендуется сделать каждое свойство класса сущностей допускающим значение NULL, чтобы точно считывать строки, которые были созданы с помощью других классов сущностей или других версий текущего класса сущностей. 

### Добавление контроллера MVC Subscriber

1. В **обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* в проекте MVC и выберите **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *SubscriberController.cs* в папке *Контроллеры* и нажмите кнопку **Добавить**. (Убедитесь в том, что вы используете файл *Subscriber.cs*, а не *Subscribe.cs*; файл *Subscribe.cs* будет добавлен позже.)

3. Откройте *SubscriberController.cs* и изучите код.

	Основная часть кода в данном контроллере аналогична коду контроллера `MailingList`. Совпадает даже имя таблицы, так как сведения о подписчиках хранятся в таблице `MailingList`. После метода `FindRow` стоит метод `GetListNames`. Этот метод получает данные для раскрывающегося списка на страницах **Создание** и **Правка**, в котором можно выбрать список рассылки, на который требуется подписать адрес электронной почты.

        private List<MailingList> GetListNames()
        {
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            var lists = mailingListTable.ExecuteQuery(query).ToList();
            return lists;
        }

	Это тот же запрос, который вы видели в контроллере `MailingList`. Для раскрывающегося списка требуется, чтобы строки содержали сведения о списках рассылки, так вы сможете выбрать только те, у которых значение RowKey равно "mailinglist".

	Для метода, извлекающего данные для страницы **Индекс**, требуются строки со сведениями о подписчике, чтобы можно было выбрать все строки, у которых значение RowKey не равно "MailingList".

        public ActionResult Index()
        {
            var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();
            return View(subscribers);
        }

	Обратите внимание, что запрос указывает на чтение данных из объектов `Subscriber` (путем указания `<Subscriber>`), однако данные будут считываться из таблицы `mailinglist`.

	**Примечание.** Количество подписчиков может возрасти настолько, что их обработка в одном запросе станет невозможна. В следующем выпуске этого учебника мы надеемся реализовать разбиение по страницам и показать, как следует обрабатывать маркеры продолжения. Вам необходимо обрабатывать маркеры продолжения при выполнении запросов, возвращающих более 1000 строк: Azure возвращает 1000 строк, а маркер продолжения позволяет выполнить другой запрос, начинающий работу с места завершения предыдущего запроса. (Обозреватель хранилищ Azure не обрабатывает маркеры продолжения, поэтому его запросы не возвращают более 1000 строк.) Дополнительные сведения о больших наборах результатов и маркерах продолжения см. в разделах [Эффективное использование таблицы Azure][howtogetthemost] и [Таблицы Azure: маркеры продолжения обязательно появятся](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously). 

	В методе `HttpGet Create` вы настраиваете данные для раскрывающегося списка; а в методе `HttpPost` можно задать значения по умолчанию перед сохранением новой сущности.

        public ActionResult Create()
        {
            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }

                var insertOperation = TableOperation.Insert(subscriber);
                mailingListTable.Execute(insertOperation);
                return RedirectToAction("Index");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

            return View(subscriber);
        }

	Страница `HttpPost Edit` сложнее контроллера `MailingList`, так как на странице `MailingList` можно изменять имя списка или адрес электронной почты, которые являются полями ключей. Если пользователь изменяет одно из этих полей, необходимо удалить существующую запись и добавить новую, а не обновлять существующую запись. В следующем коде показана часть метода edit, который обрабатывает различные процедуры изменений, как связанных, так и не связанных с ключами:
     
            if (ModelState.IsValid)
            {
                try
                {
                    UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                    if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                    {
                        //Keys didn't change -- Update the row
                        var replaceOperation = TableOperation.Replace(editedSubscriber);
                        mailingListTable.Execute(replaceOperation);
                    }
                    else
                    {
                        // Keys changed, delete the old record and insert the new one.
                        if (editedSubscriber.PartitionKey != partitionKey)
                        {
                            // PartitionKey changed, can't do delete/insert in a batch.
                            var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                            mailingListTable.Execute(deleteOperation);
                            var insertOperation = TableOperation.Insert(editedSubscriber);
                            mailingListTable.Execute(insertOperation);
                        }
                        else
                        {
                            // RowKey changed, do delete/insert in a batch.
                            var batchOperation = new TableBatchOperation();
                            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                            batchOperation.Insert(editedSubscriber);
                            mailingListTable.ExecuteBatch(batchOperation);
                        }
                    }
                    return RedirectToAction("Index");

	Параметры, которые связыватель модели MVC передает в метод `Edit`, включают в себя исходные значения имени списка и адреса электронной почты (в параметрах `partitionKey` и `rowKey`), а также значения, введенные пользователем (в параметрах `listName` и `emailAddress`): 

        public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

	Параметры, передаваемые в метод `UpdateModel`, исключают свойства `PartitionKey` и `RowKey` из привязки модели:

            var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            
	Это вызвано тем, что свойства `ListName` и `EmailAddress` используют `PartitionKey` и `RowKey` в качестве резервных свойств, а пользователь мог изменить одно из этих значений. Когда связыватель модели обновляет модель, задав свойство `ListName`, свойство `PartitionKey` автоматически обновляется. Если бы связыватель модели после обновления свойства `ListName` обновил свойство `PartitionKey` с исходным значением, он бы перезаписал новое значение, установленное свойством `ListName`. Свойство `EmailAddress` аналогичным образом автоматически обновляет свойство `RowKey`.  

	После обновления объекта модели `editedSubscriber` код определяет, был ли изменен ключ раздела или строки. Если значение любого ключа изменилось, следует удалить существующую строку подписчика и вставить новую. Если изменился только ключ строки, удаление и вставку можно реализовать в виде атомарной пакетной транзакции.

	Обратите внимание, что код создает новую сущность для передачи в операцию `Delete`.

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            mailingListTable.ExecuteBatch(batchOperation);

	Объекты, передаваемые в операции в виде пакета, должны быть отдельными сущностями. Например, невозможно создать сущность `Subscriber`, передать ее в операцию `Delete`, а затем изменить значение в той же самой сущности `Subscriber` и передать ее в операцию `Insert`. Если вы сделаете так, состояние сущности после изменения свойства будет действительным как для операции удаления, так и для операции вставки.

	**Примечание.** Операции в пакете должны находиться в одном разделе. Поскольку изменение имени списка приводит к изменению ключа раздела, его нельзя выполнить в рамках транзакции.



### Добавление представлений MVC Subscriber

2. Создайте новую папку в папке *Представления* **обозревателя решений** в проекте MVC и назовите ее *Unsubscribe*.

1. Щелкните правой кнопкой мыши новую папку *Представления\Подписчик* и выберите пункт **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите все пять файлов CSHTML в папке *Представления\Подписчик* и нажмите кнопку **Добавить**.

3. Откройте файл *Edit.cshtml* и изучите код.

		@model MvcWebRole.Models.Subscriber
		
		@{
		    ViewBag.Title = "Edit Subscriber";
		}
		
		<h2>Edit Subscriber</h2>
		
		@using (Html.BeginForm()) {
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
		     @Html.HiddenFor(model => model.SubscriberGUID)
             @Html.HiddenFor(model => model.ETag)
		     <fieldset>
		        <legend>Subscriber</legend>
		        <div class="display-label">
		             @Html.DisplayNameFor(model => model.ListName)
		        </div>
		        <div class="editor-field">
		            @Html.DropDownList("ListName", String.Empty)
		            @Html.ValidationMessageFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.EmailAddress)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.EmailAddress)
		            @Html.ValidationMessageFor(model => model.EmailAddress)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.Verified)
		        </div>
		        <div class="display-field">
		            @Html.EditorFor(model => model.Verified)
		        </div>
		        <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
						
	Этот код похож на то, что вы уже видели раньше для представления `Редактирование` **MailingList**. Значение `SubscriberGUID` не показано, поэтому это значение не предоставляется автоматически в поле формы для метода контроллера `HttpPost`. Таким образом, скрытое поле служит для сохранения этого значения.

	Другие представления содержат код, который похож на то, что вы уже видели для контроллера `MailingList`.

### Тестирование приложения

1. Запустите проект, нажав сочетание клавиш CTRL+F5, а затем щелкните элемент **Подписчики**.

	![Пустая страница индексов подписчиков][mtas-subscribers-empty-index-page]

2. Используйте функцию **Создать**, чтобы добавить списки рассылки, и попробуйте использовать функции **Изменить** и **Удалить**, чтобы убедиться, что они работают.

	![Страница индексов подписчиков со строками][mtas-subscribers-index-page]



<h2><a name="message"></a><span class="short-header">Сообщение</span>Создание и тестирование контроллера и представлений сообщения</h2>

Веб-интерфейс **Message** используется администраторами для создания, редактирования и отображения сведений о сообщениях, запланированных для отправки в списки рассылки.

### Добавление класса сущностей Message в папку моделей

Класс сущностей `Message` используется для строк в таблице `Message` со сведениями о сообщении, которое запланировано для отправки в список. Эти строки содержат такие сведения, как строка темы, список, в который отправляется сообщение, а также запланированная дата отправки. 

1. В **обозревателе решений** щелкните правой кнопкой мыши папку *Модели* в проекте MVC и выберите **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *Message.cs* в папке "Модели" и нажмите кнопку **Добавить**.

3. Откройте *Message.cs* и изучите код.

	    public class Message : TableEntity
	    {
	        private DateTime? _scheduledDate;
	        private long _messageRef;
	
	        public Message()
	        {
	            this.MessageRef = DateTime.Now.Ticks;
	            this.Status = "Pending";
	        }
	
	        [Required]
	        [Display(Name = "Scheduled Date")]
	        // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
	        [DataType(DataType.Date)]
	        public DateTime? ScheduledDate 
	        {
	            get
	            {
	                return _scheduledDate;
	            }
	            set
	            {
	                _scheduledDate = value;
	                this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
	            }
	        }
	        
	        public long MessageRef 
	        {
	            get
	            {
	                return _messageRef;
	            }
	            set
	            {
	                _messageRef = value;
	                this.RowKey = "message" + value.ToString();
	            }
	        }
	
	        [Required]
	        [Display(Name = "List Name")]
	        public string ListName { get; set; }
	
	        [Required]
	        [Display(Name = "Subject Line")]
	        public string SubjectLine { get; set; }
	
	        // Pending, Queuing, Processing, Complete
	        public string Status { get; set; }
	    }
		
	Класс `Message` определяет конструктор по умолчанию, который назначает свойству `MessageRef` уникальное значение для сообщения. Поскольку это значение является частью ключа строки, метод задания для свойства `MessageRef` автоматически задает также и свойство `RowKey`. Метод задания свойства `MessageRef` объединяет литерал "message" и значение `MessageRef` и помещает результат в свойство `RowKey`.

	Значение `MessageRef` создается путем получения значения `Ticks` из `DateTime.Now`. Это гарантирует, что по умолчанию при отображении сообщений в веб-интерфейсе они появляются в том порядке, в котором были созданы для конкретной запланированной даты (`ScheduledDate` является ключом раздела). Идентификатор GUID можно было бы использовать, чтобы сделать строки "message" уникальными, однако в этом случае порядок извлечения по умолчанию будет случайным.

	Конструктор по умолчанию также устанавливает состояние "Ожидает" по умолчанию для новых строк `message`.

	Дополнительные сведения о структуре таблицы `Message` см. в [первом учебнике серии][firsttutorial].

### Добавление контроллера MVC Message

1. В **обозревателе решений** щелкните правой кнопкой мыши папку "Контроллеры" в проекте MVC и выберите **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл  *MessageController.cs* в папке *Контроллеры* и нажмите кнопку **Добавить**.

3. Откройте *MessageController.cs* и изучите код.

	Основная часть кода в данном контроллере аналогична коду контроллера `Subscriber`. Новым здесь является код для работы с BLOB-объектами. Для каждого сообщения содержимое сообщения электронной почты в формате HTML или обычного текста отправляется в виде файлов HTM и TXT и сохраняется в BLOB-объектах.

	BLOB-объекты хранятся в контейнерах BLOB-объектов. Приложение службы электронной почты Azure сохраняет все свои BLOB-объекты в одном контейнере с именем "azuremailblobcontainer", а код конструктора контроллера получает ссылку на этот контейнер BLOB-объектов:

	    public class MessageController : Controller
	    {
	        private TableServiceContext serviceContext;
	        private static CloudBlobContainer blobContainer;

		      public MessageController()
	        {
	            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
	            // If this is running in an Azure Web Site (not a Cloud Service) use the Web.config file:
	            //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
	
	            // Get context object for working with tables and a reference to the blob container.
	            var tableClient = storageAccount.CreateCloudTableClient();
            serviceContext = tableClient.GetTableServiceContext();
	            var blobClient = storageAccount.CreateCloudBlobClient();
	            blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
	        }
	
	Для каждого файла, который пользователь выбирает для отправки, представление MVC предоставляет объект `HttpPostedFile` со сведениями об этом файле. Когда пользователь создает новое сообщение, объект `HttpPostedFile` используется для сохранения файла в BLOB-объекте. Когда пользователь редактирует сообщение, он может выбрать отправить файл с заменой или оставить BLOB-объект без изменений.

	Контроллер включает в себя метод, вызываемый методами `HttpPost Create` и `HttpPost Edit` для сохранения BLOB-объекта:

        private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob. 
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                blob.UploadFromStream(fileStream);
            }
        }

	Метод `HttpPost Create` сохраняет два BLOB-объекта, а затем добавляет строку таблицы `Message`. Имя BLOB-объектов формируется путем добавления значения `MessageRef` к расширению HTM или TXT. 

        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }

            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }

            if (ModelState.IsValid)
            {
                SaveBlob(message.MessageRef + ".htm", file);
                SaveBlob(message.MessageRef + ".txt", txtFile);

                var insertOperation = TableOperation.Insert(message);
                messageTable.Execute(insertOperation);

                return RedirectToAction("Index");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

	Метод `HttpGet Edit` проверяет, что полученное сообщение находится в состоянии `Ожидает`, чтобы пользователь не мог изменить сообщение после начала его обработки рабочей ролью B.  Подобный код находится в методе `HttpPost Edit`, а также в методах `Delete` и `DeleteConfirmed`.

        public ActionResult Edit(string partitionKey, string rowKey)
        {
            var message = FindRow(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be edited because it isn't in Pending status.");
            }

            var lists = GetListNames();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
            return View(message);
        }

	В методе `HttpPost Edit` код сохраняет новый BLOB-объект только в том случае, если пользователь решает отправить новый файл. В следующем коде отсутствует часть метода, отвечающая за обработку параллелизма, так как она аналогична приведенному ранее коду для контроллера `MailingList`. 
 
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
            DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
        {
            if (ModelState.IsValid)
            {
                var excludePropLst = new List<string>();
                excludePropLst.Add("PartitionKey");
                excludePropLst.Add("RowKey");

                if (httpFile == null)
                {
                    // They didn't enter a path or navigate to a file, so don't update the file.
                    excludePropLst.Add("HtmlPath");
                }
                else
                {
                    // They DID enter a path or navigate to a file, assume it's changed.
                    SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                }

                if (txtFile == null)
                {
                    excludePropLst.Add("TextPath");
                }
                else
                {
                    SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                }

                string[] excludeProperties = excludePropLst.ToArray();

                try
                {
                    UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                    if (editedMsg.PartitionKey == partitionKey)
                    {
                        // Keys didn't change -- update the row.
                        var replaceOperation = TableOperation.Replace(editedMsg);
                        messageTable.Execute(replaceOperation);
                    }
                    else
                    {
                        // Partition key changed -- delete and insert the row.
                        // (Partition key has scheduled date which may be changed;
                        // row key has MessageRef which does not change.)
                        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                        messageTable.Execute(deleteOperation);
                        var insertOperation = TableOperation.Insert(editedMsg);
                        messageTable.Execute(insertOperation);
                    }
                    return RedirectToAction("Index");
                }

	Если запланированная дата меняется, изменяется ключ раздела и требуется удалить и вставить строку. Это невозможно сделать в рамках транзакции, так как операция затрагивает несколько разделов.

	Метод `HttpPost Delete` удаляет BLOB-объекты, когда он удаляет строку в таблице:

        [HttpPost, ActionName("Delete")]
        public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = FindRow(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }

            DeleteBlob(message.MessageRef + ".htm");
            DeleteBlob(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }

        private void DeleteBlob(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            blob.Delete();
        }

### Добавление представлений MVC Message

2. Создайте новую папку в папке *Представления* **обозревателя решений** в проекте MVC и назовите ее `Message`.

1. Щелкните правой кнопкой мыши новую папку *Представления\Сообщение* и выберите пункт **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите все пять файлов CSHTML в папке *Представления\Сообщение* и нажмите кнопку **Добавить**.

3. Откройте файл *Edit.cshtml* и изучите код.

		@model MvcWebRole.Models.Message
		
		@{
		    ViewBag.Title = "Edit Message";
		}
		
		<h2>Edit Message</h2>
		
		@using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
		{
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
		    <fieldset>
		        <legend>Message</legend>
		        @Html.HiddenFor(model => model.MessageRef)
		        @Html.HiddenFor(model => model.PartitionKey)
		        @Html.HiddenFor(model => model.RowKey)
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ListName, "MailingList")
		        </div>
		        <div class="editor-field">
		            @Html.DropDownList("ListName", String.Empty)
		            @Html.ValidationMessageFor(model => model.ListName)
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.SubjectLine)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.SubjectLine)
		            @Html.ValidationMessageFor(model => model.SubjectLine)
		        </div>
		        <div class="editor-label">
		            HTML Path: Leave blank to keep current HTML File.
		        </div>
		        <div class="editor-field">
		            <input type="file" name="file" />
		        </div>
		        <div class="editor-label">
		            Text Path: Leave blank to keep current Text File.
		        </div>
		        <div class="editor-field">
		            <input type="file" name="TxtFile" />
		        </div>
		        <div class="editor-label">
		            @Html.LabelFor(model => model.ScheduledDate)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.ScheduledDate)
		            @Html.ValidationMessageFor(model => model.ScheduledDate)
		        </div>
		        <div class="display-label">
		            @Html.DisplayNameFor(model => model.Status)
		        </div>
		        <div class="editor-field">
		            @Html.EditorFor(model => model.Status)
		        </div>
		       <p>
		            <input type="submit" value="Save" />
		        </p>
		    </fieldset>
		}
		
		<div>
		    @Html.ActionLink("Back to List", "Index")
		</div>
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
				
	Методу `HttpPost Edit` требуются ключ раздела и ключ строки, поэтому код выдает эти значения в скрытых полях. Скрытые поля не нужны в контроллере `Subscriber`, потому что (а) свойства `ListName` и `EmailAddress` в модели `Subscriber` обновляют свойства `PartitionKey` и `RowKey`; (б) свойства `ListName` и `EmailAddress` были включены во вспомогательные объекты `EditorFor` в представлении Edit. Когда связыватель модели MVC для модели `Subscriber` обновляет свойство `ListName`, свойство `PartitionKey` обновляется автоматически, а когда связыватель модели MVC обновляет свойство `EmailAddress` в модели `Subscriber`, автоматически обновляется свойство `RowKey`. В модели `Message` поля, которые сопоставляются с ключом раздела и ключом строки, недоступны для редактирования, что предотвращает их изменение подобным образом.

	Скрытое поле также имеется для свойства `MessageRef`. Это значение совпадает с ключом раздела, однако служит для повышения удобочитаемости кода в методе `HttpPost Edit`. Наличие скрытого поля `HttpPost Edit` позволяет коду в методе `HttpPost Edit` ссылаться на значение `MessageRef` по этому имени, когда он создает имена файлов для BLOB-объектов. 
   
3. Откройте файл *Index.cshtml* и изучите код.

		@model IEnumerable<MvcWebRole.Models.Message>
		
		@{
		    ViewBag.Title = "Messages";
		}
		
		<h2>Messages</h2>
		
		<p>
		    @Html.ActionLink("Create New", "Create")
		</p>
		<table>
		    <tr>
		        <th>
		            @Html.DisplayNameFor(model => model.ListName)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.SubjectLine)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.ScheduledDate)
		        </th>
		        <th>
		            @Html.DisplayNameFor(model => model.Status)
		        </th>
		        <th></th>
		    </tr>
		    @foreach (var item in Model)
		    {
		        <tr>
		            <td>
		                @Html.DisplayFor(modelItem => item.ListName)
		            </td>
		            <td>
		                @Html.DisplayFor(modelItem => item.SubjectLine)
		            </td>
		            <td>
		                @Html.DisplayFor(modelItem => item.ScheduledDate)
		            </td>
		            <td>
		                @item.Status
		            </td>
		            <td>
		                @if (item.Status == "Pending")
		                {
		                    @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
		                    @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
		                }
		                @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
		            </td>
		        </tr>
		    }
		
		</table>
				
	Отличие от других представлений **Index** заключается в том, что ссылки **Изменить** и **Удалить** отображаются только для сообщений, которые находятся в состоянии `Ожидает`:

        @if (item.Status == "Pending")
        {
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
        }

	Это не позволяет пользователям вносить изменения в сообщение после начала его обработки рабочей ролью A.

	Другие представления содержат код, который похож на представление **Edit** или другие представления, которые вы уже рассматривали для других контроллеров.

### Тестирование приложения

1. Запустите проект, нажав сочетание клавиш CTRL+F5, а затем щелкните элемент **Сообщения**.

	![Пустая страница индексов сообщений][mtas-message-empty-index-page]

2. Используйте функцию **Создать**, чтобы добавить списки рассылки, и попробуйте использовать функции **Изменить** и **Удалить**, чтобы убедиться, что они работают.

	![Страница индексов подписчиков со строками][mtas-message-index-page]




<h2><a name="unsubscribe"></a><span class="short-header">Отмена подписки</span>Создание и тестирование контроллера и представления отмены подписки</h2>

Далее вы реализуете пользовательский интерфейс для процедуры отказа от подписки.

**Примечание.** В этом учебнике создается контроллер только для процесса отказа от подписки, при этом процесс подписки не затрагивается. Как было описано в [первом учебнике][firsttutorial], пользовательский интерфейс и метод обслуживания для процесса подписки были исключены до реализации достаточного уровня безопасности для метода обслуживания. До тех пор вы можете использовать для подписки адресов электронной почты на списки страницы администратора **Подписчик**.

### Добавление модели представления отмены подписки в папку моделей

Модель представления `UnsubscribeVM` используется для передачи данных между контроллером `Unsubscribe` и его представлением.  

1. В **обозревателе решений** щелкните правой кнопкой мыши папку `Модели` в проекте MVC и выберите **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл `UnsubscribeVM.cs` в папке "Модели" и нажмите кнопку **Добавить**.

3. Откройте файл `UnsubscribeVM.cs` и изучите код.


	    public class UnsubscribeVM
	    {
	        public string EmailAddress { get; set; }
	        public string ListName { get; set; }
	        public string ListDescription { get; set; }
	        public string SubscriberGUID { get; set; }
	        public bool? Confirmed { get; set; }
	    }

	Ссылки отказа от подписки содержат `SubscriberGUID`. Это значение используется для получения адреса электронной почты, имени и описания списка из таблицы `MailingList`. В представлении отображается адрес электронной почты и описание списка, подписка на который отменяется, а также выводится кнопка **Подтвердить**, которую пользователь должен нажать для завершения процесса отказа от подписки.

### Добавление контроллера Unsubscribe

1. В **обозревателе решений** щелкните правой кнопкой мыши папку `Контроллеры` в проекте MVC и выберите **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *UnsubscribeController.cs* в папке *Контроллеры* и нажмите кнопку **Добавить**.

3. Откройте файл *UnsubscribeController.cs* и изучите код.

	Этот контроллер имеет метод `HttpGet Index`, который отображает начальную страницу отказа от подписки, и метод `HttpPost Index`, который обрабатывает использование кнопки **Подтвердить** или **Отмена**.

	Метод `HttpGet Index` использует GUID и имя списка в строке запроса, чтобы получить строку таблицы `MailingList` для данного подписчика. Затем он помещает все необходимые представлению сведения в модель представления и отображает страницу **Отказ от подписки**. Он задает для свойства `Подтверждено` значение NULL, чтобы указать представлению на необходимость отображения исходной версии страницы **Отказ от подписки**.
 
	     public ActionResult Index(string id, string listName)
	     {
	         if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
	         {
	             ViewBag.errorMessage = "Empty subscriber ID or list name.";
	             return View("Error");
	         }
	         string filter = TableQuery.CombineFilters(
	             TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
	             TableOperators.And,
	             TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
	         var query = new TableQuery<Subscriber>().Where(filter);
	         var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
	         if (subscriber == null)
	         {
	             ViewBag.Message = "You are already unsubscribed";
	             return View("Message");
	         }
	         var unsubscribeVM = new UnsubscribeVM();
	         unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
	         unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
	         unsubscribeVM.SubscriberGUID = id;
	         unsubscribeVM.Confirmed = null;
	         return View(unsubscribeVM);
	     }

	Примечание. SubscriberGUID не является ключом раздела или строки, поэтому производительность запроса снижается по мере увеличения размера раздела (количества адресов электронной почты в списке рассылки).  Дополнительные сведения об альтернативных способах повышения масштабируемости запроса см. в [первом учебнике серии][firsttutorial].

	Метод `HttpPost Index` снова использует GUID и имя списка для получения сведений о подписчике и заполняет свойства модели представления. Затем в случае нажатия кнопки **Подтвердить** он удаляет строку подписчика в таблице `MailingList`. Если была нажата кнопка **Подтвердить**, он также устанавливает для свойства `Подтверждение` значение `true`, в противном случае он устанавливает для этого свойства `Подтверждение` значение `false`. Значение свойства `Подтверждение` сообщает представлению, что необходимо отобразить подтвержденную или отмененную версию страницы **Отказ от подписки**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public ActionResult Index(string subscriberGUID, string listName, string action)
        {
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;

            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }

            return View(unsubscribeVM);
        }

### Создание представлений MVC

2. Создайте новую папку в папке *Представления* **обозревателя решений** в проекте MVC и назовите ее *Unsubscribe*.

1. Щелкните правой кнопкой мыши новую папку *Представления\Unsubscribe* и выберите пункт **Добавить существующий элемент**.

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *Index.cshtml* в папке *Представления\Unsubscribe* и нажмите кнопку **Добавить**.

3. Откройте файл *Index.cshtml* и изучите код.
		
		@model MvcWebRole.Models.UnsubscribeVM
		
		@{
		    ViewBag.Title = "Unsubscribe";
		    Layout = null;
		}
		
		<h2>Email List Subscription Service</h2>
		
		@using (Html.BeginForm()) {
		    @Html.AntiForgeryToken()
		    @Html.ValidationSummary(true)
		    <fieldset>
		        <legend>Unsubscribe from Mailing List</legend>
		        @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
		        @if (Model.Confirmed == null) {
		            <p>
		                Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
		           </p>
		            <br />
		            <p>
		                <input type="submit" value="Confirm" name="action"/> 
		                &nbsp; &nbsp;
		                <input type="submit" value="Cancel" name="action"/>
		            </p>
		        }
		        @if (Model.Confirmed == false) {
		            <p>
		                @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
		            </p>
		        }
		        @if (Model.Confirmed == true) {
		            <p>
		                @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
		            </p>
		        }
		    </fieldset>
		}
		
		@section Scripts {
		    @Scripts.Render("~/bundles/jqueryval")
		}
						
	Строка `Layout = null` указывает, что файл _Layout.cshtml не должен использоваться для отображения этой страницы. Страница **Отказ от подписки** отображает очень простой пользовательский интерфейс без колонтитулов, применяемых на страницах администратора.

	Свойство `Подтверждено` в основной части страницы определяет, что будет отображаться на странице: кнопки **Подтвердить** и **Отмена**, если свойство имеет значение NULL, сообщение с подтверждением отказа от подписки, если свойство имеет значение true, сообщение об отмене процедуры отказа от подписки, если свойство имеет значение false.

### Тестирование приложения

1. Запустите проект, нажав сочетание клавиш CTRL+F5, а затем щелкните элемент **Подписчики**.

2. Нажмите кнопку **Создать** и создайте нового подписчика для любого списка рассылки, созданного в рамках предыдущих тестирований.

	Оставьте страницу **Индекс** **подписчиков** открытой в окне браузера.

3. Откройте обозреватель хранилищ Azure и выберите свою тестовую учетную запись хранения.

4. Щелкните элемент **Таблицы** в области **Тип хранилища**, выберите таблицу **MailingList** и щелкните **Запрос**.

5. Дважды щелкните строку добавленного подписчика.

	![Обозреватель хранилищ Azure][mtas-ase-unsubscribe]

6. В диалоговом окно **Изменить сущность** выберите и скопируйте значение `SubscriberGUID`.

	![Обозреватель хранилищ Azure][mtas-ase-edit-entity-unsubscribe]

7. Переключитесь обратно в окно браузера.  В адресной строке браузера измените слово "Subscriber" в URL-адресе на строку "unsubscribe?ID=[guidvalue]&listName=[listname]", где [guidvalue] — это GUID, скопированный из обозревателя хранилищ Azure, а [listname] — имя списка рассылки.  Например:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

	Отображается версия страницы **Отказ от подписки**, которая запрашивает подтверждение:

	![Страница отказа от подписки][mtas-unsubscribe-page]

2. Нажмите кнопку **Подтвердить**, после чего отображается подтверждение о том, что подписка для данного адреса электронной почты была отменена.

	![Страница с подтверждением отмены подписки][mtas-unsubscribe-confirmed-page]

3. Вернитесь на страницу **Индекс** **подписчиков**, чтобы убедиться в отсутствии строки подписчика.




<h2><a name="alternativearchitecture"></a><span class="short-header">Альтернативная архитектура</span>(Необязательно) Построение альтернативной архитектуры</h2>

Указанные ниже изменения в инструкциях применяются в том случае, если вы хотите создать альтернативную архитектуру, - то есть выполнять веб-интерфейс на веб-сайте Azure вместо рабочей роли облачной службы Azure.

* При создании решения проекта сначала создайте проект **Веб-приложение ASP.NET MVC 4**, а затем добавьте в решение проект **Облачные службы Azure** с рабочей ролью.

* Сохраните строку подключения хранилища Azure в файле Web.config вместо файла параметров облачной службы. (Это работает только для веб-сайтов Azure. При попытке использовать файл Web.config для хранения строки подключения в веб-роли облачной службы Azure вы получите сообщение об ошибке HTTP 500.) 

Добавьте новую строку подключения с именем `StorageConnectionString` в файл *Web.config*, как показано в следующем примере:

	       <connectionStrings>
	          <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
	          <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
	       </connectionStrings>
	
Получите значения для строки подключения с [портала управления Azure][managementportal]: выберите вкладку **Хранилище** и свою учетную запись хранения, а затем щелкните элемент **Управление ключами** в нижней части страницы.

* Замените все строки `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` в коде на строки `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`.



<h2><a name="nextsteps"></a><span class="short
-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Как отмечено в [первом учебнике серии][firsttutorial], мы приведем подробное описание построения процесса подписки только после реализации общего секрета для защиты метода обслуживания веб-API ASP.NET. Однако ограничение по IP-адресам также защищает метод обслуживания, и вы можете добавить возможность подписки, скопировав следующие файлы из загруженного проекта.

Для метода обслуживания веб-API ASP.NET:

* Controllers\SubscribeAPI.cs

Для веб-страницы, которую получают подписчики при выборе ссылки **Подтвердить** в сообщении электронной почты, созданном методом обслуживания:

* Models\SubscribeVM.cs
* Controllers\SubscribeController.cs
* Views\Subscribe\Index.cshtml

В [следующем учебнике][nexttutorial] вам предстоит настраивать и программировать рабочую роль A, которая отвечает за планирование сообщений электронной почты.

Ссылки на дополнительные ресурсы по работе с таблицами, очередями и BLOB-объектами хранилища Azure приведены в конце [последнего учебника данной серии][tut5].

<div><a href="/ru-ru/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Учебник 4</a></div>



[alternativearchitecture]: #alternativearchitecture


[tut5]: /ru-ru/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[tut2]: /ru-ru/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[firsttutorial]: /ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/
[nexttutorial]: /ru-ru/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/

[TableEntity]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
[DynamicTableEntity]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[managementportal]: http://manage.windowsazure.com

[percentinkeyfields]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
[tabledatamodel]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179338.aspx 
[deepdive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
[howtogetthemost]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx

[mtas-home-page-before-adding-controllers]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
[mtas-menu-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png
[mtas-footer-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png
[mtas-title-and-logo-in-layout]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png
[mtas-new-cloud-service-dialog-rename]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
[mtas-new-mvc4-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
[mtas-new-cloud-service-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
[mtas-new-cloud-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
[mtas-new-cloud-service-add-worker-a]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
[mtas-mailing-list-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
[mtas-file-new-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
[mtas-opening-layout-cshtml]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png

[mtas-add-existing-item-to-models]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png
[mtas-add-existing-item-to-controllers]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png
[mtas-add-existing-item-to-views]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png
[mtas-mvcwebrole-properties-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png




[mtas-subscribers-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
[mtas-message-empty-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
[mtas-ase-edit-entity-unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
[mtas-ase-unsubscribe]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png
[mtas-unsubscribe-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
[mtas-unsubscribe-confirmed-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
[mtas-er1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
[mtas-4]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png
[mtas-3]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png
[mtas-5]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png
[mtas-enter]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png
[mtas-elip]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
[mtas-manage-nuget-for-solution]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
[mtas-update-storage-nuget-pkg]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
[mtas-nuget-select-projects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
[mtas-compute-emulator-icon]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png



