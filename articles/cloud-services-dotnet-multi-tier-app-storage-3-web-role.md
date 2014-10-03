<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Создание веб-роли для приложения службы электронной почты Azure — 3 из 5

Это третий из пяти учебников в серии. В нем показано, как построить и развернуть образец приложения службы электронной почты Azure. Сведения о приложении и самой серии учебников см. в [первом учебнике серии][].

В этом учебнике вы узнаете следующее:

-   Как создать решение, содержащее проект облачной службы с веб-ролью и рабочей ролью.
-   Как работать с таблицами, BLOB-объектами и очередями Azure в представлениях и контроллерах MVC 5.
-   Как обрабатывать конфликты параллелизма при работе с таблицами Azure.

## Разделы этого руководства

-   [Создание решения Visual Studio][]
-   [Обновления пакета клиентской библиотеки хранилища NuGet][]
-   [Настройка проектов для использования эмулятора хранения][]
-   [Настройка трассировки и перезапусков указателей][]
-   [Добавление кода для создания таблиц, очередей и контейнеров BLOB-объектов в методе запуска приложения][]
-   [Создание и тестирование списка адресов][]
-   [Создание и тестирование контроллера и представлений подписчика][]
-   [Создание и тестирование контроллера и представлений сообщения][]
-   [Создание и тестирование контроллера и представления отмены подписки][]
-   [Дальнейшие действия][]

## <a name="cloudproject"></a>Создание решения Visual Studio

Работа начинается с создания решения Visual Studio с помощью проекта для интерфейсного веб-компонента и проекта для одной из серверных рабочих ролей Azure. Вторую рабочую роль вы добавите позднее.

### Создание проекта облачной службы с веб-ролью и рабочей ролью

1.  Запустите Visual Studio.

2.  В меню **Файл** выберите **Новый проект**.

    ![Меню нового проекта][]

3.  Разверните узел **C\#** и выберите **Облако** в области **Установленные шаблоны**, а затем выберите **Облачная служба Azure**.

4.  Присвойте приложению имя **AzureEmailService** и нажмите кнопку **ОК**.

    ![Диалоговое окно "Новый проект"][]

5.  В диалоговом окне **Новая облачная служба Azure** выберите **Веб-роль ASP.NET** и щелкните стрелку вправо.

    ![Диалоговое окно нового облачного проекта Azure][]

6.  Наведите указатель мыши на элемент **WebRole1** в правом столбце, а затем щелкните значок карандаша, чтобы изменить имя веб-роли.

7.  Введите MvcWebRole в качестве нового имени и нажмите клавишу ВВОД.

    ![Диалоговое окно нового облачного проекта Azure — переименование веб-роли][]

8.  Выполните ту же процедуру для добавления элемента **Рабочая роль**, присвойте ему имя WorkerRoleA и нажмите кнопку **ОК**.

    ![Диалоговое окно нового облачного проекта Azure — добавление рабочей роли][]

9.  В диалоговом окне **Новый проект ASP.NET** выберите шаблон **MVC**, установите флажок **Веб-интерфейс API**, а затем щелкните **Изменить проверку подлинности**.

    ![Диалоговое окно "Новый проект"][1]

10. В диалоговом окне **Изменить аутентификацию** щелкните **Без аутентификации** и нажмите кнопку **ОК**.

    ![Нет проверки подлинности][]

11. В диалоговом окне **Новый проект ASP.NET** нажмите кнопку **ОК**.

### Задание верхнего колонтитула, меню и нижнего колонтитула страницы

В этом разделе вы обновите верхние и нижние колонтитулы и пункты меню, которые отображаются на каждой странице для веб-интерфейса администратора. Приложение будет иметь три набора веб-страниц администратора: один для списков рассылки, один для подписчиков на списки рассылки и один для сообщений.

1.  Если [завершенное решение][] еще не загружено, сделайте это, прежде чем перейти к следующему шагу.

    В оставшейся части руководства, если нужно добавить код, копируйте файлы из загруженного проекта в новый проект вместо копирования и вставки частей кода. Это руководство покажет и пояснит важные части кода, который копируется.

    Для добавления файла из загруженного проекта щелкните правой кнопкой мыши проект, в который нужно добавить файл, или папку, куда его нужно добавить, и выберите **Добавить — существующий элемент** из контекстного меню. Затем перейдите в место загрузки завершенного проекта, выберите нужные файлы и щелкните **Добавить**. Если появится диалоговое окно **Файл назначения существует**, щелкните **Да**.

2.  В проекте MvcWebRole добавьте файл *Views\\Shared\_Layout.cshtml* из загруженного проекта (щелкните правой кнопкой мыши папку *Общие* под *Представления* для добавления файла).

    Записи для заголовка, нижнего колонтитула и меню для страниц списка рассылки, сообщения и подписчика:

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### Локальный запуск приложения

1.  Для запуска приложения нажмите сочетание клавиш CTRL+F5.

    При использовании веб-проектов запуска, которые не являются проектами облачной службы Azure, нужно учесть, что они запускаются дольше обычного, прежде чем в браузере появится домашняя страница.

    ![домашняя страница][]

    Задержка вызвана запуском Visual Studio эмулятора вычислений Azure и эмулятора хранения Azure. Значок эмулятора вычислений отображается на панели задач Windows:

    ![Эмулятор вычислений на панели задач][]

2.  Закройте браузер.

## <a name="updatescl"></a>Обновления пакета клиентской библиотеки хранилища NuGet

Платформа API, предназначенная для работы с таблицами, очередями и BLOB-объектами хранилища Azure, называется клиентской библиотекой хранения (SCL). Этот интерфейс API включен в пакет NuGet в шаблоне проекта облачной службы. Однако обновления SCL часто выпускаются после того, как были созданы шаблоны проекта, и всегда предпочтительно проверить наличие обновления для пакета SCL NuGet.

1.  В меню **Сервис** Visual Studio наведите указатель на элемент **Диспетчер пакетов библиотеки** и затем выберите **Управление пакетами NuGet для решения**.

    ![Управление пакетами NuGet для решения в меню][]

2.  В левой части диалогового окна **Управление пакетами NuGet** выберите **Обновления**, затем выполните прокрутку вниз до пакета **Хранилище Azure** и щелкните элемент **Обновить**.

    ![Пакет хранилища Azure в диалоговом окне "Управление пакетами NuGet"][]

3.  Убедитесь, что в диалоговом окне **Выбор проектов** выбраны оба проекта, и нажмите кнопку **ОК**.

    ![Выбор обоих проектов в диалоговом окне "Выбор проектов"][]

4.  Примите условия лицензионного соглашения для завершения установки пакета, а затем закройте диалоговое окно **Управление пакетами NuGet**.

## <a name="configurestorage"></a>Настройка проектов для использования эмулятора хранения

Код веб-роли и рабочей роли, которые добавляются позже, использует строку подключения StorageConnectionString для соединения с хранилищем Azure. В этом разделе будет добавлен параметр для свойств роли и проведена его настройка для использования эмулятора хранения. Второе руководство в серии покажет, как настроить строку подключения для использования учетной записи хранилища Azure.

1.  В **обозревателе решений** щелкните правой кнопкой мыши элемент **MvcWebRole** в области **Роли** облачного проекта **AzureEmailService**, а затем выберите пункт **Свойства**.

    ![Свойства веб-роли][]

2.  Убедитесь, что в раскрывающемся списке **Конфигурации службы** выбрано значение **Все конфигурации**.

3.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.

4.  Введите "StorageConnectionString" в столбце **Имя**.

5.  Выберите элемент **Строка подключения** в раскрывающемся списке **Тип**.

6.  Нажмите кнопку с многоточием (**...**) в правом конце строки, чтобы открыть диалоговое окно **Строка подключения учетной записи хранения**.

    ![Щелкните элемент "Свойства" правой кнопкой мыши][]

7.  В диалоговом окне **Создать строку подсоединения хранилища** щелкните переключатель **Эмулятор хранения Azure** и нажмите кнопку **ОК**.

    Строка подключения `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` по умолчанию настраивается на эмулятор хранения, поэтому менять ее не нужно.

8.  Выполните ту же процедуру, которую использовали для роли MvcWebRole, чтобы добавить строку подключения для роли WorkerRoleA.

При добавлении нового параметра с помощью кнопки **Добавить параметры** он был добавлен в XML-код файла *ServiceDefinition.csdf*, а также в каждый из двух файлов конфигурации *CSCFG*. Visual Studio добавляет в файл *ServiceDefinition.csdf* следующий XML-код.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

В каждый файл конфигурации *CSCFG* добавляется следующий XML-код.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

Можно вручную добавить параметры в файл *ServiceDefinition.csdf* и два файла конфигурации *CSCFG*, однако применение редактора свойств имеет следующие преимущества для строк подключения:

-   Можно добавить новый параметр только в одно место, после чего правильный XML-код добавляется во все три файла.
-   Правильный XML-код создается для трех файлов параметров. Файл *ServiceDefinition.csdf* определяет параметры, которые должны присутствовать в каждом файле конфигурации *CSCFG*. Если параметры в файле *ServiceDefinition.csdf* и двух файлах конфигурации *.CSCFG* не согласованы между собой, Visual Studio может выдать следующее сообщение об ошибке: *"Текущая модель службы рассинхронизирована. Убедитесь, что конфигурация службы и файлы определений являются допустимыми".*

    ![Ошибка, вызванная недопустимыми файлами определений и конфигурацией службы][]

При возникновении этой ошибки редактор свойств не будет работать до устранения проблемы несоответствия путем ручного изменения файлов.

## <a name="tracing"></a>Настройка трассировки и перезапусков указателей

1.  В проекте MvcWebRole добавьте файл *WebRole.cs* из загруженного проекта.

Добавляется метод, который настраивает ведение журналов и вызывает его из метода `OnStart`, который выполняется, когда запускается веб-роль. Код в новом методе `ConfigureDiagnostics` описан во [втором учебнике][].

Также добавляется код, который будет запущен, когда веб-роль уведомляется, что она будет выключена. Приложения облачной службы Azure перезапускаются приблизительно два раза в месяц для обновления операционной системы. (Дополнительные сведения об обновлениях ОС см. в разделе [Перезапуск экземпляра роли из-за обновлений ОС][].) При завершении работы веб-приложения возникает событие `OnStop`. Шаблон веб-роли, создаваемый Visual Studio, не перезаписывает метод `OnStop`, поэтому приложение имеет только несколько секунд для завершения обработки запросов HTTP, прежде чем его работа будет завершена. Можно добавить код для переопределения метода `OnStop`, чтобы обеспечить безопасное завершение работы.

Только что добавленный файл содержит следующее переопределение метода `OnStop`.

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

До завершения работы приложения у метода `OnStop` есть до 5 минут времени на выход. Можно добавить бездействующий вызов на 5 минут в метод `OnStop`, чтобы дать приложению максимум времени на обработку текущих запросов, однако в случае правильного масштабирования приложение должно затратить на обработку оставшихся запросов гораздо меньше 5 минут. Рекомендуется как можно быстрее остановить работу, чтобы приложение могло максимально быстро выполнить перезагрузку и продолжить обработку запросов.

После завершения работы роли системой Azure подсистема балансировки нагрузки прекращает отправку запросов для экземпляра этой роли, после чего вызывается метод `OnStop`. Если другой экземпляр роли отсутствует, обработка запросов прекращается до окончания завершения работы и перезапуска роли (что обычно занимает несколько минут). Это одна из причин, по которой в соглашении об уровне обслуживания Azure требуется наличие по крайней мере двух экземпляров каждой роли, чтобы воспользоваться преимуществами гарантии времени бесперебойной работы.

В коде, показанном для метода `OnStop`, создается счетчик производительности ASP.NET для `Requests Current`. Значение счетчика `Requests Current` содержит текущее число запросов, включая находящиеся в очереди, выполняемые в данный момент или ожидающие записи на клиенте. Значение `Requests Current` проверяется каждую секунду, и как только оно становится равным нулю, возвращается метод `OnStop`. После возврата `OnStop` работа роли завершается.

Данные трассировки не сохраняются, когда вызываются из метода `OnStop` без выполнения [Передачи по запросу][]. Сведения трассировки `OnStop` в режиме реального времени можно просмотреть с помощью служебной программы [dbgview][] через подключение к удаленному рабочему столу.

## <a name="createifnotexists"></a>Добавление кода для создания таблиц, очередей и контейнеров BLOB-объектов в методе запуска приложения

Веб-приложение использует таблицу `MailingList`, таблицу `Message`, очередь `azuremailsubscribequeue` и контейнер больших двоичных объектов `azuremailblobcontainer`. Можно создать их вручную с помощью такого средства, как обозреватель хранилищ Azure, но затем потребуется делать это вручную каждый раз, когда вы начинаете использовать приложение с новой учетной записью хранения. В этом разделе вы добавите код, который выполняется при запуске приложения, проверяет наличие необходимых таблиц, очередей и контейнеров и создает их в случае отсутствия.

Можно добавить одноразовый код загрузки в метод `OnStart` в файле *WebRole.cs* или в файл *Global.asax*. В этом руководстве инициализируется "хранилище Azure" в файле *Global.asax*.

1.  В**обозревателе решений** щелкните правой кнопкой мыши проект MvcWebRole и добавьте файл *Global.asax.cs* из загруженного проекта.

Добавлен новый метод, который вызывается из метода `Application_Start`.

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

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

В следующих разделах создаются компоненты веб-приложения и их можно будет протестировать с хранилищем разработки или вашей учетной записью хранения без необходимости вручную создавать таблицы, очереди или контейнер BLOB-объектов.

## <a name="mailinglist"></a>Создание и тестирование контроллера и представлений списка рассылки

Веб-интерфейс **Список рассылки** используется администраторами для создания, редактирования и отображения списков рассылки, таких как "Объявления отдела истории университета Contoso" и "Открытые инженерные вакансии Fabrikam".

### Добавление класса сущностей MailingList в папку моделей

Класс сущности `MailingList` используется для строк в таблице `MailingList`, содержащей сведения о списке, такие как его описание и адрес электронной почты "От" для сообщений электронной почты, отправленных в этот список.

1.  В папке `Models` в проекте MVC добавьте файл *MailingList.cs* из скачанного проекта.

    Используйте класс сущностей `MailingList` для чтения и записи строк списка рассылки в таблице mailinglist.

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

    API хранилища Azure требует, чтобы классы сущностей, используемые для работы с таблицами, были производными от [TableEntity][]. `TableEntity` определяет поля `PartitionKey`, `RowKey`, `TimeStamp` и `ETag`. Свойства `TimeStamp` и `ETag` используется системой. Способы использования свойства `ETag` для обработки параллелизма см. далее в этом учебнике.

    (Имеется также класс [DynamicTableEntity][], позволяющий работать со строками таблицы как с коллекциями-словарями пар "ключ/значение" вместо использования предопределенных классов моделей. Дополнительные сведения см. в разделе [Подробный обзор таблиц клиентской библиотеки хранения 2.0 Azure][].)

    Ключ раздела таблицы `mailinglist` — это имя списка. В этом классе сущностей значение ключа раздела может быть получено либо через свойство `PartitionKey` (определенное в классе `TableEntity`) или свойство `ListName` (определенное в классе `MailingList`). Свойство `ListName` использует `PartitionKey` как свою резервную переменную. Определение свойства `ListName` позволяет использовать более содержательное имя переменной в коде и упрощает программирование веб-интерфейса, поскольку атрибуты форматирования и проверки DataAnnotations можно добавить свойство `ListName`, но невозможно добавить непосредственно в свойство `PartitionKey`.

    Атрибут `RegularExpression` свойства `ListName` вынуждает MVC проверять вводимые пользователем данные, чтобы гарантировать, что введенное имя списка содержит только алфавитно-цифровые символы или знаки подчеркивания. Это ограничение было реализовано для упрощения имен списков, чтобы их было удобно использовать в строках запроса в URL-адресах.

    > [WACOM.NOTE] Если вы хотите использовать менее строгий формат имен списков, можно разрешить использование других символов и URL-кодирования для имен списков при их использовании в строках запроса. Однако некоторые символы в ключах строк и разделов таблиц Azure использовать нельзя, поэтому необходимо исключить хотя бы их. Сведения о символах, использование которых в полях ключей разделов или строк запрещено или вызывает проблемы, см. в разделах [Общие сведения о модели данных службы таблиц][] и [Символ % в PartitionKey или RowKey][].

    Класс `MailingList` определяет конструктор по умолчанию, который устанавливает `RowKey` в жестко заданную строку "mailinglist", поскольку все строки списков рассылки в этой таблице используют это значение в качестве ключа строки. (Объяснение структуры таблицы см. в [первом учебнике серии][].) Для этой цели можно выбрать любую константу при условии, что она никогда не совпадает с адресом электронной почты, который является ключом строки для строк подписчика в этой таблице.

    Имя списка и адреса электронной почты из поля "От" должны быть введены, когда создается новая сущность `MailingList`, поэтому они имеют атрибуты `Required`.

    Атрибуты `Display` указывают заголовок по умолчанию, используемый для поля в пользовательском интерфейсе MVC.

### Добавление контроллера MVC MailingList

1.  В папке *Controllers* в проекте MvcWebRole добавьте файл *MailingListController.cs* из загруженного проекта.

    Конструктор контроллера по умолчанию создает объект `CloudTable`, используемый для работы с таблицей `mailinglist`.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    Код получает учетные данные для вашей учетной записи хранения Azure из файла параметров проекта облачной службы, чтобы подключиться к этой учетной записи хранения. (Вы настроите эти параметры позднее — перед тестированием контроллера.)

    Следующим является метод `FindRowAsync`, который вызывается, как только контроллеру нужно проверить конкретную запись списка рассылки таблицы `MailingList`, например, для редактирования записи списка рассылки. Код возвращает одну сущность `MailingList` с помощью переданных в него значений ключа раздела и ключа строки. Этот контроллер редактирует те строки, которые имеют ключ строки "MailingList", поэтому значение "MailingList" можно было бы жестко задать для ключа строки, однако указание как ключа раздела, так и ключа строки представляет собой шаблон, используемый для методов `FindRow` во всех контроллерах.

    > [WACOM.NOTE] Это приложение использует асинхронный код ASP.NET 4.5 для операций ввода-вывода в веб-роли, чтобы использовать ресурсы сервера эффективно. Сведения о асинхронном коде в веб-приложении см. в разделе [Использование асинхронной поддержки .NET 4.5 для недопущения блокирующих вызовов][].

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    Код в этом методе `FindRow` возвращает строку списка рассылки. Код в соответствующем методе `FindRow` в контроллере `Subscriber` возвращает строку подписчика для той же таблицы `mailinglist`. Код в двух этих методах идентичен за исключением для типа модели, используемого с методом [TableOperation.Retrieve][].

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    Объект `TableOperation`, возвращаемый методом `TableOperation.Retrieve`, задает схему (свойства) строки или строк, которые предполагается получить в результате выполнения запроса. Одна таблица может иметь разные схемы в разных строках. Обычно при чтении строки указывается тот же тип модели, который использовался для создания это строки.

    На странице **Индекс** отображаются все строки списка рассылки, поэтому запрос в метод `Index` возвращает все сущности `MailingList`, которые имеют "mailinglist" в качестве ключа строки (другие строки в таблице содержат адреса электронной почты как и ключи строки, и они содержат информацию о подписчике)

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    Метод `ExecuteQuerySegmentedAsync` разбивает большие результирующие наборы на разделы. Он возвращает до 1 000 строк. Когда выполняется запрос, который получает более 1 000 строк, передается 1 000 строк и маркер продолжения. Можно использовать маркер продолжения для выполнения другого запроса, который запускается с того места, где остановился первый. Показанный код упрощен для примера приложения: он собирает все разделы в один список. Для рабочего приложения следует реализовать код разбиения на страницы. Дополнительные сведения о больших наборах результатов и маркерах продолжения см. в разделах [Эффективное использование таблицы Azure][] и [Таблицы Azure: маркеры продолжения обязательно появятся][].

    При создании объекта `OperationContext` можно установить значение свойства `ClientID` для получения уникального идентификатора, который будет включен в журналы, записываемые хранилищем Azure. Можно добавить этот идентификатор в журналах трассировки операций сохранения в коде, который вызывает активность службы хранилища. Сведения о журнале хранилища Azure см. в разделе [Журналы хранилища Azure: Использование журналов для трассировки запросов в хранилище][].

    С SCL 2.1 и новыми API можно также использовать LINQ для запросов таблиц. Пример, в котором показано, как использовать LINQ, см. в разделе [PhluffyFotos][].

    Если не указать политику повторения, API автоматически предпринимает три повторных попытки, каждый раз экспоненциально увеличивая пределы времени ожидания. Для веб-интерфейса с пользователем, ожидающим отображения страницы, это может привести к недопустимому увеличению времени ожидания. Таким образом, этот код определяет линейные попытки (то есть без увеличения предела времени ожидания) и разумный предел времени ожидания для пользователя. Политика повторения указывается в объекте `webUIRetryPolicy`, который передается в метод `ExecuteQuerySegmentedAsync`. Объект `webUIRetryPolicy` определяется в конструкторе контроллера:

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    Метод `Index` включает блок try-catch, который рассчитан на обработку условий времени ожидания.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    Когда пользователь нажимает кнопку **Создать** на странице **Создание** модуль привязки модели MVC создает сущность `MailingList` из входных данных, введенных в представление, и метод `HttpPost Create` добавляет сущность в таблицу.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    Для страницы **Правка** метод `HttpPost Edit` обновляет строку.

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    В методе `HttpPost Edit` блок catch обрабатывает текущие ошибки.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
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

    Исключение параллелизма возникает в том случае, если пользователь выбирает список рассылки для редактирования, а затем во время отображении страницы **Правка** в браузере другой пользователь изменяет тот же самый список рассылки. Когда это происходит, код отображает предупреждение и указывает, какие поля были изменены другим пользователем. API TSL использует `ETag` для проверки конфликтов при совместной работе. Каждый раз при обновлении строки таблицы значение `ETag` меняется. При получении строки для редактирования, сохраните значение `ETag` и при выполнении операции обновления или удаления передайте сохраненное значение `ETag`. (Представление `Edit` имеет скрытое поле для значения ETag.) Если операция обновления сталкивается с тем, что значение `ETag` в обновляемой записи отличается от значения `ETag`, переданного операции обновления, она вызывает исключение совместного доступа. Если вас не интересуют конфликты параллелизма, можно указать звездочку ("") для поля ETag в сущности, передаваемой в операцию обновления, после чего такие конфликты игнорируются.

    Примечание. Сообщение об ошибке HTTP 412 относится не только к ошибкам параллелизма. Оно может быть инициировано API SCL для других ошибок.

    Для страницы **Удаление** метод `HttpPost Delete` удаляет строку `MailingList` вместе с любыми строками `Subscriber`, связанными с ней в таблице `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    В случае удаления большого количества подписчиков код удаляет записи пакетами. Стоимость транзакции для удаления одной строки равна стоимости удаления 100 строк в пакетном режиме. Максимальное количество операций, которые можно выполнить в рамках одного пакета, составляет 100.

    Хотя цикл обрабатывает как строки `MailingList`, так и строки `Subscriber`, он считывает их все в класс сущности `MailingList`, поскольку единственными полями, нужными для операции `Delete` являются поля `PartitionKey`, `RowKey` и `ETag`.

### Добавление представлений MVC MailingList

1.  В папке *Views* в проекте MVC создайте новую папку и дайте ей имя *MailingList*.

2.  В новую папку *Views\\MailingList* добавьте все четыре файла *.cshtml* из загруженного проекта.

В файле *Edit.cshtml* обратите внимание на скрытое поле, которое включено для предотвращения изменения значения `ETag`, которое используется для обработки ошибок совместного доступа.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

Обратите внимание также, что поле `ListName` имеет вспомогательный объект `DisplayFor` вместо вспомогательного объекта `EditorFor`.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

На странице **Редактировать** нельзя изменить имя списка, потому что для этого требуется сложный код в контроллере: Методу `HttpPost Edit` пришлось бы удалить строку существующего списка рассылки и все связанные строки подписчиков, и вновь вставить их все с новым значением ключа. В рабочем приложении такое усложнение может иметь смысл. Как вы увидите далее, контроллер `Subscriber` позволяет изменять имя списка, поскольку одновременно работа осуществляется только с одной строкой.

В файле *Index.cshtml* гиперссылки **Изменить** и **Удалить** указывают параметры строки ключа раздела и ключа строки, чтобы определить конкретную строку. Для сущностей `MailingList` требуется только ключ раздела, поскольку ключ строки всегда имеет значение "MailingList", однако сохраняются оба ключа, чтобы обеспечить согласованность кода представления MVC для всех контроллеров и представлений.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### Назначение MailingList в качестве контроллера по умолчанию

1.  Откройте *Route.config.cs* в папке *App\_Start*.

2.  В строке с параметрами по умолчанию измените контроллер по умолчанию с "Home" на "MailingList".

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### Тестирование приложения

1.  Запустите проект, нажав сочетание клавиш CTRL+F5.

    ![Пустая страница индексов MailingList][]

2.  Используйте функцию **Создать**, чтобы добавить списки рассылки, и попробуйте использовать функции **Изменить** и **Удалить**, чтобы убедиться, что они работают.

    ![Страница индексов MailingList со строками][]

## <a name="subscriber"></a><span class="short-header">Подписчик</span>Создание и тестирование контроллера и представлений подписчика

Веб-интерфейс **Subscriber** используется администраторами для добавления новых подписчиков в список рассылки, а также для редактирования, отображения и удаления существующих подписчиков.

### Добавление класса сущностей Subscriber в папку моделей

Класс сущности `Subscriber` используется для строк в таблице `MailingList`, которая содержит информацию о подписчиках в списке. Эти строки содержат такие сведения, как адрес электронной почты пользователя и состояние его проверки.

1.  В папку *Models* в проекте MVC добавьте файл*MSubscriber.cs* из загруженного проекта.

Как и класс сущности `MailingList`, класс сущности `Subscriber` используется для чтения и записи строк в таблице `mailinglist`.

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
        

Вместо константы "mailinglist" строки `Subscriber` используют для ключа строки адрес электронной почты. (Объяснение структуры таблицы см. в [первом учебнике серии][].) Следовательно, определяется свойство `EmailAddress`, которое использует свойство `RowKey` как резервное поле аналогично тому, как `ListName` использует `PartitionKey` как резервное поле. Как упоминалось ранее, это позволяет поместить в свойства атрибуты форматирования и проверки DataAnnotations.

Значение `SubscriberGUID` создается при создании сущности `Subscriber`. Оно используется в ссылках на подписку и отказ от нее, чтобы оформить подписку адреса электронной почты и отменить ее могли только авторизованные лица.

При первоначальном создании строки для нового подписчика значение `Verified` является `false`. Значение `Verified` изменяется на `true` после того, как новый подписчик щелкает гиперссылку **Подтвердить** в приветственном сообщении. Если сообщение отправлено в список рассылки, пока параметр `Verified` = `false`, сообщение подписчику не посылается.

Свойство `Verified` в сущности `Subscriber` определяется как допускающее значение NULL. Когда указывается, что запрос должен вернуть сущности `Subscriber`, возможно, что некоторые из возвращаемых строк уже не имеют свойства `Verified`. Следовательно сущность `Subscriber` определяет свойство `Verified` как допускающее значение NULL, что позволяет ему точнее отражать фактическое содержимое строки, если запрос возвращает строки таблицы, не имеющие свойства *Verified*. Возможно, вы привыкли работать с таблицами SQL Server, в которых каждая строка таблицы имеет одинаковую схему. Каждая строка таблицы хранилища Azure представляет собой коллекцию свойств, а каждая строка может иметь различный набор свойств. Например, в примере приложения службы электронной почты Azure строки с ключом строки "MailingList" не имеют свойства `Verified`. Если запрос возвращает строку таблицы, которая не имеет свойства `Verified` во время создания экземпляра класса сущности `Subscriber`, свойство `Verified` в объекте сущности будет равно NULL. Если свойство не допускает значение NULL, будет получено то же значение `false` для строк, где `Verified` = `false` и для строк, у которых вообще нет свойства `Verified`. Таким образом, для работы с таблицами Azure рекомендуется сделать каждое свойство класса сущностей допускающим значение NULL, чтобы точно считывать строки, которые были созданы с помощью других классов сущностей или других версий текущего класса сущностей.

### Добавление контроллера MVC Subscriber

1.  В **обозревателе решений** щелкните правой кнопкой мыши папку *Контроллеры* в проекте MVC и выберите **Добавить существующий элемент**.

2.  Перейдите к папке, куда вы загрузили пример приложения, выберите файл *SubscriberController.cs* в папке *Контроллеры* и нажмите кнопку **Добавить**. (Убедитесь в том, что вы используете файл *Subscriber.cs*, а не *Subscribe.cs*; файл *Subscribe.cs* будет добавлен позже.)

Основная часть кода в данном контроллере аналогична коду в контроллере `MailingList`. Совпадает даже имя таблицы, так как сведения о подписчиках хранятся в таблице `MailingList`.

Кроме метода `FindRowAsync` существует метод `FindRow`, поскольку нужно вызывать его из блока catch, но асинхронный метод из блока catch вызвать невозможно.

После методов `FindRow` появится метод `GetListNamesAsync`. Этот метод получает данные для раскрывающегося списка на страницах **Создание** и **Правка**, в котором можно выбрать список рассылки, на который требуется подписать адрес электронной почты.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

Это тот же запрос, который вы видели в контроллере `MailingList`. Для раскрывающегося списка требуется, чтобы строки содержали сведения о списках рассылки, так вы сможете выбрать только те, у которых значение RowKey равно "mailinglist".

Для метода, извлекающего данные для страницы **Индекс**, требуются строки со сведениями о подписчике, чтобы можно было выбрать все строки, у которых значение RowKey не равно "MailingList".

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

Обратите внимание: запрос указывает, что данные будут считаны в объекты `Subscriber` (указанием `<Subscriber>`), но данные будут считаны из таблицы `mailinglist`.

Количество подписчиков может возрасти настолько, что их обработка в одном запросе станет невозможна. Как отмечено ранее, рабочее приложение должно реализовать разбиение на страницы с использованием маркера продолжения.

В методе `HttpGet Create` задайте данные для раскрывающегося списка, а в методе `HttpPost` задайте значения по умолчанию, прежде чем сохранить новую сущность.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

Страница `HttpPost Edit` сложнее, чем та, которую вы видели в контроллере `MailingList`, поскольку страница `Subscriber` позволяет изменять имя списка или адрес электронной почты, и оба они являются полями ключей. Если пользователь изменяет одно из этих полей, необходимо удалить существующую запись и добавить новую, а не обновлять существующую запись. В следующем коде показана часть метода edit, который обрабатывает различные процедуры изменений, как связанных, так и не связанных с ключами:

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

Параметры, которые модуль привязки модели MVC передает в метод `Edit`, включают значения первоначального имени списка и адресов электронной почты (в параметрах `partitionKey` и `rowKey`) и значения введенные пользователем (в параметрах `listName` и `emailAddress`):

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

Параметры, переданные в метод `UpdateModel`, не включают свойства `PartitionKey` и `RowKey` из привязки модели:

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

Причина этого в том, что свойства `ListName` и `EmailAddress` используют свойства `PartitionKey` и `RowKey` в качестве резервных, и пользователь, возможно, изменил одно из этих значений Когда модуль привязки модели обновляет модель с помощью указания свойства `ListName`, свойство `PartitionKey` обновляется автоматически. Если модулю привязки модели пришлось изменить свойство `PartitionKey` на первоначальное значение свойства после обновления свойства `ListName`, он перезапишет новое значение, которое было установлено свойством `ListName`. Свойство `EmailAddress` автоматически обновляет свойство `RowKey` аналогичным образом

После обновления объекта модели `editedSubscriber` код определяет, был ли изменен ключ раздела или строки. Если значение любого ключа изменилось, следует удалить существующую строку подписчика и вставить новую. Если изменился только ключ строки, удаление и вставку можно реализовать в виде атомарной пакетной транзакции.

Обратите внимание, что код создает новую сущность для передачи операции `Delete`:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

Объекты, передаваемые в операции в виде пакета, должны быть отдельными сущностями. Например, вы не можете создать сущность `Subscriber`, передать ее операции `Delete`, затем изменить значение в той же сущности `Subscriber` и передать его операции `Insert`. Если вы сделаете так, состояние сущности после изменения свойства будет действительным как для операции удаления, так и для операции вставки.

Операции в пакете должны находиться в одном разделе. Поскольку изменение имени списка приводит к изменению ключа раздела, его нельзя выполнить в рамках транзакции.

### Добавление представлений MVC Subscriber

1.  Создайте новую папку в папке *Представления* **обозревателя решений** в проекте MVC и назовите ее *Subscriber*.

2.  Щелкните правой кнопкой мыши новую папку *Views\\Subscriber* и выберите пункт **Добавить существующий элемент**.

3.  Перейдите к папке, куда вы загрузили пример приложения, выберите все пять файлов CSHTML в папке *Views\\Subscriber* и нажмите кнопку **Добавить**.

В файле *Edit.cshtml* скрытое поле включено для значения `SubscriberGUID`, поскольку это поле не отображается и, следовательно, не предоставляется автоматически в поле формы.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### Тестирование приложения

1.  Запустите проект, нажав сочетание клавиш CTRL+F5, а затем щелкните элемент **Подписчики**.

    ![Пустая страница индексов подписчиков][]

2.  Используйте функцию **Создать**, чтобы добавить списки рассылки, и попробуйте использовать функции **Изменить** и **Удалить**, чтобы убедиться, что они работают.

    ![Страница индексов подписчиков со строками][]

## <a name="message"></a>Создание и тестирование контроллера и представлений сообщения

Веб-интерфейс **Message** используется администраторами для создания, редактирования и отображения сведений о сообщениях, запланированных для отправки в списки рассылки.

### Добавление класса сущностей Message в папку моделей

1.  В папку *Models* в проекте MVC добавьте файл *Message.cs* из загруженного проекта.

Класс сущности `Message` используется для строк в таблице `Message`, которая содержит информацию о сообщении, запланированном для отправки в список. Эти строки содержат такие сведения, как строка темы, список, в который отправляется сообщение, а также запланированная дата отправки.

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
        

Класс `Message` определяет конструктор по умолчанию, который задает свойству `MessageRef` уникальное значение для сообщения. Поскольку это значение — часть ключа строки, метод задания для свойства `MessageRef` также автоматически задает свойство `RowKey`. Метод задания свойства `MessageRef` объединяет литерал "message" со значением `MessageRef` и отправляет их в свойство `RowKey`.

Значение `MessageRef` создается путем получения значения `Ticks` из `DateTime.Now`. Это гарантирует, что по умолчанию при отображении сообщений в веб-интерфейсе они появляются в том порядке, в котором были созданы для конкретной запланированной даты (`ScheduledDate` является ключом раздела). Идентификатор GUID можно было бы использовать, чтобы сделать строки "message" уникальными, однако в этом случае порядок извлечения по умолчанию будет случайным.

Конструктор по умолчанию также устанавливает состояние "Ожидает" по умолчанию для новых строк `message`.

Дополнительные сведения о структуре таблицы `Message` см. в [первом учебнике серии][].

### Добавление контроллера MVC Message

1.  В папке *Controllers* в проекте MVC добавьте файл*MessageController.cs* из загруженного проекта.

Основная часть кода в данном контроллере аналогична коду в контроллере `Subscriber`. Новым здесь является код для работы с BLOB-объектами. Для каждого сообщения содержимое сообщения электронной почты в формате HTML или обычного текста отправляется в виде файлов HTM и TXT и сохраняется в BLOB-объектах.

BLOB-объекты хранятся в контейнерах BLOB-объектов. Приложение службы электронной почты Azure сохраняет все свои BLOB-объекты в одном контейнере с именем "azuremailblobcontainer", а код конструктора контроллера получает ссылку на этот контейнер BLOB-объектов:

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

Для каждого файла, который выбирает пользователь для загрузки, представление MVC предоставляет объект `HttpPostedFile` со сведениями о файле. Когда пользователь создает новое сообщение, объект `HttpPostedFile` используется для сохранения файла в большой двоичный объект. Когда пользователь редактирует сообщение, он может выбрать отправить файл с заменой или оставить BLOB-объект без изменений.

Контроллер включает метод, который вызывают методы `HttpPost Create` и `HttpPost Edit` для сохранения большого двоичного объекта:

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

Метод `HttpPost Create` сохраняет два больших двоичных объекта и затем добавляет строку таблицы `Message`. Имена больших двоичных объектов создаются путем объединения значения `MessageRef` с расширением имени файла ".htm" или ".txt"

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
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
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

Метод `HttpGet Edit` проверяет, что состояние полученного сообщения — `Pending` и пользователь не может изменить сообщение после начала его обработки рабочей ролью В. Аналогичный код используется в методе `HttpPost Edit` и методах `Delete` и `DeleteConfirmed`.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

В методе `HttpPost Edit` код сохраняет новый большой двоичный объект только в том случае, если пользователь решает отправить новый файл.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

Если запланированная дата меняется, изменяется ключ раздела и требуется удалить и вставить строку. Это невозможно сделать в рамках транзакции, так как операция затрагивает несколько разделов.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

Метод `HttpPost Delete` удаляет большие двоичные объекты, когда он удаляет строку в таблице:

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Добавление представлений MVC Message

1.  В проекте MVC создайте новую папку в папке *Views* и дайте ей имя `Message`.

2.  В новую папку *Views\\Message* добавьте все пять файлов *.cshtml* из загруженного проекта.

Методу `HttpPost Edit` требуются ключ раздела и ключ строки, поэтому код в файле *Edit.cshtml* предоставляет эти значения в скрытых полях.

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
                

Скрытые поля были не нужны в контроллере `Subscriber`, так как a) свойства `ListName` и `EmailAddress` в модели `Subscriber` обновляют свойства `PartitionKey` и `RowKey`; b) свойства `ListName` и `EmailAddress` были включены в представление "Правка" со вспомогательными объектами `EditorFor`. Когда модуль привязки модели MVC для модели `Subscriber` обновляет свойство `ListName`, свойство `PartitionKey` автоматически обновляется, и когда модуль привязки модели MVC обновляет свойство `EmailAddress` в модели `Subscriber`, свойство `RowKey` автоматически обновляется. В модели `Message` поля, которые сопоставляются с ключом раздела и ключом строки, недоступны для редактирования, что предотвращает их изменение подобным образом.

Скрытое поле также имеется для свойства `MessageRef`. Это значение совпадает с ключом раздела, однако служит для повышения удобочитаемости кода в методе `HttpPost Edit`. Включение скрытого поля `MessageRef` позволяет коду в методе `HttpPost Edit` ссылаться на значение `MessageRef` по имени, когда он создает имена файлов для больших двоичных объектов.

Представление **Индекс** сообщения в файле *Index.cshtml* отличается от других представлений **Индекс**, в которых ссылки **Редактировать** и **Удалить** выведены только для сообщений, которые находятся в состоянии `Pending`:

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

Это не позволяет пользователям вносить изменения в сообщение после начала его обработки рабочей ролью A.

### Тестирование приложения

1.  Запустите проект, нажав сочетание клавиш CTRL+F5, а затем щелкните элемент **Сообщения**.

    ![Пустая страница индексов сообщений][]

2.  Используйте функцию **Создать**, чтобы добавить списки рассылки, и попробуйте использовать функции **Изменить** и **Удалить**, чтобы убедиться, что они работают.

    ![Страница индексов подписчиков со строками][2]

## <a name="unsubscribe"></a>Создание и тестирование контроллера и представления отмены подписки

Далее вы реализуете пользовательский интерфейс для процедуры отказа от подписки.

В этом руководстве создается контроллер только для процесса отказа от подписки, при этом процесс подписки не затрагивается. Как объясняет [первое руководство][первом учебнике серии], графический интерфейс и метод службы для процесса подписки включены в загружаемый файл, но не описываются в руководстве, так как пример приложения не реализует защиту для метода службы. Для целей тестирования можно использовать для подписки адресов электронной почты на списки страницы администратора **Подписчик**.

### Добавление модели представления отмены подписки в папку моделей

1.  В папке `Models` в проекте MVC добавьте файл `UnsubscribeVM.cs` из скачанного проекта.

Модель представления `UnsubscribeVM` используется для передачи данных между контроллером `Unsubscribe` и его представлением.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

Ссылки отмены подписки содержат `SubscriberGUID`. Это значение используется для получения адреса электронной почты, имени и описания списка из таблицы `MailingList`. В представлении отображается адрес электронной почты и описание списка, подписка на который отменяется, а также выводится кнопка **Подтвердить**, которую пользователь должен нажать для завершения процесса отказа от подписки.

### Добавление контроллера Unsubscribe

1.  В папке `Controllers` в проекте MVC добавьте файл *UnsubscribeController.cs* из скачанного проекта.

Этот контроллер имеет метод `HttpGet Index`, который выводит начальную страницу отмены подписки, и метод `HttpPost Index`, который обрабатывает кнопки **Подтвердить** или **Отмена**.

Метод `HttpGet Index` использует GUID и имя списка за строке запроса для получения строки таблицы `MailingList` для подписчика. Затем он помещает все необходимые представлению сведения в модель представления и отображает страницу **Отказ от подписки**. Он устанавливает для свойства `Confirmed` значение NULL, чтобы указать представлению на необходимость отображения исходной версии страницы **Отмена подписки**.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

Примечание. SubscriberGUID не является ключом раздела или строки, поэтому производительность запроса снижается по мере увеличения размера раздела (количества адресов электронной почты в списке рассылки). Дополнительные сведения об альтернативных способах повышения масштабируемости запроса см. [в первом учебнике серии][первом учебнике серии].

Метод `HttpPost Index` снова использует GUID и имя списка для получения сведений о подписчике и заполняет свойства модели представления. Затем в случае нажатия кнопки **Подтвердить** он удаляет строку подписчика из таблицы `MailingList`. Если нажать кнопку **Подтвердить**, он также задает свойству `Confirm` значение `true`, в противном случае для свойства `Confirm` устанавливается значение `false`. Значение свойства `Confirm` сообщает представлению, что необходимо отобразить подтвержденную или отмененную версию страницы **Отмена подписки**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
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

1.  В папке *Views* в проекте MVC создайте новую папку и дайте ей имя *Unsubscribe*.

2.  В новую папку *Views\\Unsubscribe* добавьте файл *Index.cshtml* из загруженного проекта.

В файле *Index.cshtml* строка `Layout = null` указывает, что файл \_Layout.cshtml не должен использоваться для отображения этой страницы. Страница **Отказ от подписки** отображает очень простой пользовательский интерфейс без колонтитулов, применяемых на страницах администратора.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

В тексте страницы свойство `Confirmed` определяет, что будет выводится на страницу: кнопки **Подтвердить** и **Отменить**, если свойство имеет значение null, сообщение unsubscribe-confirmed, если свойство истинно, сообщение unsubscribe-canceled, если ложно.

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

### Тестирование приложения

1.  Запустите проект, нажав сочетание клавиш CTRL+F5, а затем щелкните элемент **Подписчики**.

2.  Нажмите кнопку **Создать** и создайте нового подписчика для любого списка рассылки, созданного в рамках предыдущих тестирований.

    Оставьте страницу **Индекс** **подписчиков** открытой в окне браузера.

3.  Откройте **обозреватель сервера** и выберите узел **Azure / Хранилище / (Разработка)**.

4.  Откройте **Таблицы**, щелкните правой кнопкой мыши таблицу **MailingList** и выберите **Представление таблицы.**

5.  Дважды щелкните строку добавленного подписчика.

6.  В диалоговом окне **Изменение сущности** выберите и скопируйте значение `SubscriberGUID`.

    ![Обозреватель хранилищ Azure][]

7.  Переключитесь обратно в окно браузера. В адресной строке браузера измените слово "Subscriber" в URL-адресе на строку "unsubscribe?ID=guidvalue&listName=listname", где guidvalue — это GUID, скопированный из обозревателя хранилищ Azure, а listname — имя списка рассылки. Например:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    Отображается версия страницы **Отказ от подписки**, которая запрашивает подтверждение:

    ![Страница отказа от подписки][]

8.  Нажмите кнопку **Подтвердить**, после чего отображается подтверждение о том, что подписка для данного адреса электронной почты была отменена.

    ![Страница с подтверждением отмены подписки][]

9.  Вернитесь на страницу **Индекс** **подписчиков**, чтобы убедиться в отсутствии строки подписчика.

## <a name="nextsteps"></a>Дальнейшие действия

Как объяснено в [первом руководстве серии][первом учебнике серии], это руководство не показывает процесс подписки, потому что модель службы ASP.NET Web API не реализует защиту с общим секретом. Однако ограничение по IP-адресам, которое описывалось во втором руководстве, также защищает метод службы, и вы можете добавить возможность подписки, скопировав следующие файлы из загруженного проекта.

Для метода обслуживания веб-API ASP.NET:

-   Controllers\\SubscribeAPI.cs

Для веб-страницы, которую получают подписчики при выборе ссылки **Подтвердить** в сообщении электронной почты, созданном методом обслуживания:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

В [следующем учебнике][] вам предстоит настраивать и программировать рабочую роль A, которая отвечает за планирование сообщений электронной почты.

Ссылки на дополнительные ресурсы по работе с таблицами, очередями и BLOB-объектами хранилища Azure приведены [в конце последнего учебника данной серии][].

<div>

[Учебник 4][следующем учебнике]

</div>

  [первом учебнике серии]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Создание решения Visual Studio]: #cloudproject
  [Обновления пакета клиентской библиотеки хранилища NuGet]: #updatescl
  [Настройка проектов для использования эмулятора хранения]: #configurestorage
  [Настройка трассировки и перезапусков указателей]: #tracing
  [Добавление кода для создания таблиц, очередей и контейнеров BLOB-объектов в методе запуска приложения]: #createifnotexists
  [Создание и тестирование списка адресов]: #mailinglist
  [Создание и тестирование контроллера и представлений подписчика]: #subscriber
  [Создание и тестирование контроллера и представлений сообщения]: #message
  [Создание и тестирование контроллера и представления отмены подписки]: #unsubscribe
  [Дальнейшие действия]: #nextsteps
  [Меню нового проекта]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [Диалоговое окно "Новый проект"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [Диалоговое окно нового облачного проекта Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [Диалоговое окно нового облачного проекта Azure — переименование веб-роли]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [Диалоговое окно нового облачного проекта Azure — добавление рабочей роли]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [Нет проверки подлинности]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [завершенное решение]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [домашняя страница]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Эмулятор вычислений на панели задач]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Управление пакетами NuGet для решения в меню]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Пакет хранилища Azure в диалоговом окне "Управление пакетами NuGet"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Выбор обоих проектов в диалоговом окне "Выбор проектов"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Свойства веб-роли]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Щелкните элемент "Свойства" правой кнопкой мыши]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Ошибка, вызванная недопустимыми файлами определений и конфигурацией службы]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [втором учебнике]: /en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Перезапуск экземпляра роли из-за обновлений ОС]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [Передачи по запросу]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Подробный обзор таблиц клиентской библиотеки хранения 2.0 Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Общие сведения о модели данных службы таблиц]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179338.aspx
  [Символ % в PartitionKey или RowKey]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [Использование асинхронной поддержки .NET 4.5 для недопущения блокирующих вызовов]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [Эффективное использование таблицы Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Таблицы Azure: маркеры продолжения обязательно появятся]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Журналы хранилища Azure: Использование журналов для трассировки запросов в хранилище]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Пустая страница индексов MailingList]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [Страница индексов MailingList со строками]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Пустая страница индексов подписчиков]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Страница индексов подписчиков со строками]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Пустая страница индексов сообщений]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Обозреватель хранилищ Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Страница отказа от подписки]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Страница с подтверждением отмены подписки]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [следующем учебнике]: /en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [в конце последнего учебника данной серии]: /en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
