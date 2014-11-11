<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Создание рабочей роли A (планировщик электронной почты) для приложения службы электронной почты Azure — 4 из 5

Это четвертый из пяти учебников в серии. В нем показано, как построить и развернуть образец приложения службы электронной почты Azure. Сведения о приложении и самой серии учебников см. в [первом учебнике серии][первом учебнике серии].

В этом учебнике вы узнаете следующее:

-   Как запрашивать и обновлять таблицы в службе хранилища Azure.
-   Как добавлять рабочие элементы в очередь для обработки другой рабочей ролью.
-   Как обрабатывать запланированные завершения работы путем переопределения метода `OnStop`.
-   Как обрабатывать незапланированные завершения работы, проверяя отсутствие пропущенных и повторяющихся электронных сообщений.
-   Как проверить рабочую роль, использующую таблицы службы хранилища Azure, с помощью обозревателя сервера.

Вы уже создали проект рабочей роли A при создании проекта облачной службы. Все что теперь нужно сделать — это запрограммировать рабочую роль.

## Разделы этого руководства

-   [Добавление ссылки на веб-проект][Добавление ссылки на веб-проект]
-   [Добавление модели SendEmail][Добавление модели SendEmail]
-   [Добавление кода, который выполняется при запуске рабочей роли][Добавление кода, который выполняется при запуске рабочей роли]
-   [Проверка рабочей роли A][Проверка рабочей роли A]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="addref"></a><span class="short-header">Добавление ссылки на проект</span>Добавление ссылки на веб-проект

Ссылка на веб-проект нужна, поскольку именно там определяются классы сущностей. Те же классы сущностей используются в рабочей роли B для чтения и записи данных в используемых приложением таблицах Azure.

**Примечание.** В рабочем приложении задавать ссылку на веб-проект из проекта рабочей роли не требуется, так как это приводит к ссылке на набор зависимых сборок, которые не нужны в данной рабочей роли. В обычной ситуации общие классы модели сохраняются в проекте библиотеки классов, и проекты веб-роли и рабочей роли ссылаются на этот проект библиотеки классов. Чтобы упростить структуру решения, в рамках данного учебника классы модели хранятся в веб-проекте.

1.  Щелкните проект WorkerRoleA правой кнопкой мыши и выберите **Добавить**, а затем — **Ссылка**.

2.  В диалоговом окне **Диспетчер ссылок** добавьте ссылку на проект MvcWebRole и нажмите кнопку **OK**.

    ![Добавление ссылки на MvcWebRole][Добавление ссылки на MvcWebRole]

## <a name="addmodel"></a>Добавление модели SendEmail

Рабочая роль A создает строки `SendEmail` в таблице `Message`, а рабочая роль B считывает эти строки для получения информации, необходимой для отправки электронных сообщений. На следующем рисунке показано подмножество свойств для двух строк `Message` и трех строк `SendEmail` в таблице `Message`.

![Таблица message с sendmail][Таблица message с sendmail]

Эти строки в таблице `Message` служат для достижения нескольких целей:

-   Они предоставляют всю информацию, необходимую рабочей роли B для отправки отдельного электронного сообщения.
-   Они отслеживают факт отправки электронного сообщения, чтобы предотвратить отправку его дубликатов в случае перезапуска рабочей роли после сбоя.
-   Они позволяют рабочей роли A определить отправку всех электронных сообщений для сообщения, чтобы его можно было пометить как `Complete`.

Для чтения и записи строк `SendEmail` требуется класс модели. Так как он должен быть доступен как рабочей роли A, так и рабочей роли B, а все другие классы модели определены в веб-проекте, имеет смысл также определить в веб-проекте и данный класс.

1.  В папке *Models* в веб-проекте добавьте файл *SendEmail.cs* из скачанного проекта.

Приведенный здесь код аналогичен другим классам модели, за исключением отсутствия атрибутов DataAnnotations, так как этой модели не сопоставлен пользовательский интерфейс, и она не используется в контроллере MVC.

        public class SendEmail : TableEntity
        {
            public long MessageRef { get; set; }
            public string EmailAddress { get; set; }
            public DateTime? ScheduledDate { get; set; }
            public String FromEmailAddress { get; set; }
            public string SubjectLine { get; set; }
            public bool? EmailSent { get; set; }
            public string SubscriberGUID { get; set; }
            public string ListName { get; set; }
        }

## <a name="addcode"></a><span class="short-header">Добавление кода рабочей роли</span>Добавление кода, выполняемого при запуске рабочей роли

В проекте WorkerRoleA шаблон проекта создал файл *WorkerRole.cs* со следующим кодом:

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");

                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }

            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;

                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

                return base.OnStart();
            }
        }

Это код шаблона по умолчанию для рабочей роли. Существует метод `OnStart`, в который можно поместить код инициализации, выполняемый только при запуске экземпляра рабочей роли, а также метод `Run`, вызываемый после завершения метода `OnStart`. Замените этот код собственным кодом инициализации и выполните его.

1.  Удалите файл *WorkerRole.cs* в проекте WorkerRoleA, а затем добавьте файл *WorkerRoleA.cs* из скачанного проекта.

### Метод OnStart

Метод `OnStart` инициализирует объекты контекста, которые необходимы для работы с сущностями службы хранилища Azure. Это также гарантирует существование всех таблиц, очередей и контейнеров больших двоичных объектов, которые будут использоваться в методе `Run`. Код, выполняющий эти задачи, аналогичен рассмотренному ранее коду в конструкторах контроллеров MVC. Настройка строки подключения, используемой этим методом, будет осуществлена позднее.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 

            return base.OnStart();
        }

Вы могли просматривать предыдущую версию документации по работе с хранилищем Azure, где приведен код инициализации в цикле, проверяющий наличие ошибок транспорта. Это больше не требуется, поскольку теперь API имеет встроенный механизм повтора, который позволяет преодолевать временные сбои сети, предпринимая до 3 дополнительных попыток.

Метод `ConfigureDiagnostics`, вызываемый методом `OnStart`, настраивает трассировку таким образом, чтобы вы могли просматривать выходные данные методов `Trace.Information` и `Trace.Error`. Этот метод описан во [втором учебнике][втором учебнике].

Параметр [ServicePointManager.DefaultConnectionLimit][ServicePointManager.DefaultConnectionLimit] указывает максимальное количество одновременных подключений TCP, которое может быть открыто в .NET. В документации класса [ServicePointManager][ServicePointManager] объясняется, что каждый уникальный узел, к которому подключается экземпляр рабочей роли, использует отдельное подключение. Например, у этой рабочей роли три одновременных подключения: одно для таблиц, одно для больших двоичных объектов и одно для очередей. Для некоторых подключений, например к серверу SQL, клиентское ПО создает пул соединений, который может уменьшить количество одновременных подключений, управляемых диспетчером `ServicePointManager`. Лучшее значение параметра `DefaultConnectionLimit` зависит частично от того, к какой серверной службе выполняется подключение. Для некоторых служб достаточно иметь 500 открытых подключений, а другая служба может быть перегружена даже пятью подключениями. Предел в 12 подключений на каждый процессор — это общая рекомендация, применимая ко многим сценариям.

### Метод OnStop

Метод `OnStop` задает для глобальной переменной `onStopCalled` значение true, а затем ожидает, пока метод `Run` установит значение true для глобальной переменной `returnedFromRunMethod`, что сигнализирует о готовности к завершению работы без ошибок.

        public override void OnStop()
        {
            onStopCalled = true;
            while (returnedFromRunMethod == false)
            {
                System.Threading.Thread.Sleep(1000);
            }
        }

Метод `OnStop` вызывается, когда рабочая роль завершает работу по одной из следующих причин:

-   Azure требует перезагрузить виртуальную машину (экземпляр веб-роли или рабочей роли) или физический компьютер, на котором размещается виртуальная машина.
-   Вы остановили облачную службу, нажав кнопку **Остановить** на портале управления Azure.
-   Вы развернули обновление для своего проекта облачной службы.

Метод `Run` отслеживает переменную `onStopCalled` и прекращает получение новых рабочих элементов для обработки, когда значение этой переменной меняется на `true`. Такая скоординированность работы методов `OnStop` и `Run` обеспечивает корректное завершение работы рабочего процесса.

Azure периодически устанавливает обновления операционной системы, чтобы обеспечить безопасность, надежность и эффективность платформы. Обычно эти обновления требуют завершения работы и перезагрузки компьютеров, на которых размещаются облачные службы. Дополнительные сведения см. в разделе [Перезапуск экземпляра роли из-за обновлений ОС][Перезапуск экземпляра роли из-за обновлений ОС].

### Метод Run

Метод `Run` выполняет две задачи:

-   Просматривает таблицу `message` и ищет сообщения, запланированные к передаче сегодня или раньше, для которых рабочие элементы очереди еще не создавались.

-   Просматривает таблицу `message` и ищет сообщения с состоянием, указывающим на то, что были созданы все рабочие элементы очереди, однако отправлены еще не все электронные сообщения. Если он находит такое сообщение, то проверяет его строки `SendEmail`, чтобы определить, все ли электронные сообщения были отправлены. Если это так, он обновляет состояние до значения `Completed` и архивирует соответствующую строку `message`.

Этот метод также проверяет глобальную переменную `onStopCalled`. Если переменная имеет значение `true`, метод прекращает получение новых рабочих элементов для обработки и возвращается к ним после выполнения уже запущенных задач.

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }
                    }

                    // Sleep for one minute to minimize query costs. 
                    System.Threading.Thread.Sleep(1000 * 60);
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Обратите внимание, что вся работа выполняется в бесконечном цикле в блоке `while`, а весь код в блоке `while` заключен в блок `try`-`catch`, чтобы избежать возникновения необработанного исключения. Если возникает необработанное исключение, Azure генерирует событие [UnhandledException][UnhandledException], рабочий процесс прерывается, после чего роль переводится в автономный режим. Рабочая роль будет перезапущена Azure, но для этого требуется несколько минут. Блок `try` вызывает `TraceError` для регистрации ошибки, а затем бездействует в течение 60 секунд, чтобы в случае повторения ошибки сообщение о ней не повторялось слишком много раз. В рабочем приложении в блоке `try` можно отправить электронное сообщение администратору.

Метод `Run` обрабатывает запрос для строк `message` в таблице `message`, для которых запланирована дата до завтрашнего дня:

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**Примечание.** Одно из преимуществ перемещения строк message в таблицу `messagearchive` после их обработки заключается в том, что данный запрос должен указывать только условия поиска `PartitionKey` и `RowKey`. Если бы обработанные строки не архивировались, запросу нужно было бы также указывать неключевое поле (`Status`) и выполнять поиск по большему числу строк. Размер таблицы увеличивался бы, на выполнение запроса требовалось бы больше времени, и он мог бы начать получать маркеры продолжения.

Если сообщение находится в состоянии `Pending`, обработка еще не началась, если же оно находится в состоянии `Queuing`, обработка уже начиналась, но была прервана до создания всех сообщений очереди. В этом случае при отправке каждого сообщения электронной почты в рабочей роли B требуется выполнять дополнительную проверку, чтобы убедиться, что данное сообщение электронной почты не было отправлено ранее. Именно для этого и служит переменная `restartFlag`.

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

Затем код устанавливает для строк `message` в состоянии `Pending` значение `Queuing`. После чего для этих и всех других строк, которые уже были в состоянии `Queuing`, он вызывает метод `ProcessMessage`, чтобы создать рабочие элементы очереди для отправки электронных сообщений для данного сообщения.

                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }

После обработки сообщения в состоянии `Queuing` код устанавливает для строки `Message` состояние `Processing`. Строки в таблице `message`, которым не назначено состояние `Pending` или `Queuing`, уже находятся в состоянии `Processing`, и для этих строк код вызывает метод, который проверяет, все ли электронные сообщения были отправлены для данного сообщения. Если были отправлены все электронные сообщения, строка `message` архивируется.

После обработки всех полученных по запросу записей код бездействует в течение одной минуты.

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

Существуют минимальные затраты на выполнение каждого запроса хранилища Azure, даже если запрос не возвращает никаких данных, поэтому непрерывное повторное сканирование увеличивает ваши расходы на Azure. На момент написания данного учебника стоимость миллиона транзакций (запрос подсчитывается как транзакция) составляет 0,1 долл. США, поэтому время бездействия может составлять значительно меньше минуты, и при этом затраты на сканирование таблиц для поиска отправляемых сообщений по-прежнему будут минимальными. Дополнительную информацию о ценах см. в [первом учебнике][первом учебнике серии].

**Примечание по применению потоков и оптимальному использованию ЦП.** В методе `Run` существует две задачи (помещение электронных сообщений в очередь и проверка выполненных сообщений), которые последовательно выполняются в одном потоке. Малая виртуальная машина (ВМ) имеет 1,75 ГБ ОЗУ и только один ЦП, чего вполне достаточно для последовательного выполнения этих задач в одном потоке. Предположим, что для эффективной работы вашему приложению требуется больше памяти, чем может предоставить малая виртуальная машина. Средняя виртуальная машина предоставляет 3,5 ГБ ОЗУ и 2 ЦП, однако приложение будет использовать только один ЦП, так как оно ориентировано на однопотоковое выполнение. Чтобы воспользоваться преимуществами всех ЦП, потребовалось бы создать для каждого из них рабочий поток. Кроме того, один поток не позволяет полностью использовать возможности даже одного ЦП. Когда поток выполняет вызовы сети или ввода-вывода, поток должен ожидать завершения этого вызова, при этом он не выполняет никакой полезной работы. Если бы метод `Run` был реализован на базе двух потоков, то во время ожидания сетевой операции или операции ввода-вывода одним потоком другой поток мог бы выполнять полезную работу.

### Метод ProcessMessage

Метод `ProcessMessage` получает все электронные адреса для целевого списка и создает рабочий элемент очереди для каждого электронного адреса. При создании рабочих элементов очереди он также создает строки `SendEmail` в таблице `Message`. Эти строки содержат информацию, необходимую рабочей роли B для отправки электронных сообщений, а также свойство `EmailSent`, позволяющее отслеживать отправку каждого электронного сообщения.

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };

                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);
            }

            Trace.TraceInformation("ProcessMessage end PK: "
                + messageToProcess.PartitionKey);
        }

Сначала код получает строку списка рассылки из таблицы `mailinglist` для списка получателей рассылки. Эта строка содержит адрес электронной почты "От", который необходим рабочей роли B для отправки сообщений электронной почты.

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

Затем он запрашивает из таблицы `mailinglist` все строки подписчика для списка получателей рассылки.

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

В цикле обработки результатов запроса код начинает работу с проверки подтверждения адреса электронной почты подписчика, если подтверждение не выполнено, сообщение электронной почты в очередь не помещается.

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

Затем код создает строку `SendEmail` в таблице `message`. Эта строка содержит информацию, используемую рабочей ролью B для отправки сообщения электронной почты. Создается строка со свойством `EmailSent`, имеющим значение `false`.

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

В коде используется операция upsert, поскольку строка может уже существовать, если рабочая роль A перезапускается после сбоя.

Последняя задача, которую требуется выполнить для каждого адреса электронной почты, заключается в создании рабочего элемента очереди, который будет активировать рабочую роль B для отправки сообщения электронной почты. Рабочий элемент очереди содержит ключ раздела и значение ключа строки `SendEmail`, созданной ранее, а также флаг перезапуска, который был установлен ранее. Строка `SendEmail` содержит всю информацию, нужную рабочей роли B для отправки электронного сообщения.

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### Метод CheckAndUpdateStatusIfComplete

Метод `CheckAndUpdateStatusIfComplete` проверяет сообщения, имеющие состояние Processing, чтобы определить, все ли электронные сообщения были отправлены. При обнаружении неотправленных электронных сообщений он обновляет состояние строки до значения `Completed` и архивирует строку.

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
            string pkrkFilter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
            var query = new TableQuery<SendEmail>().Where(pkrkFilter);
            var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

            if (emailToBeSent != null)
            {
                return;
            }

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>Проверка рабочей роли A

1.  Запустите приложение, нажав клавишу F5.

2.  Используйте веб-страницы администратора для создания списка рассылки и помещения в него подписчиков. Присвойте свойству `Verified` значение `true` по крайней мере для одного из подписчиков и установите в качестве электронного адреса такой адрес, на который вы можете получать почту.

    Сообщения электронной почты не отправляются до реализации рабочей роли B, но те же тестовые данные будут использоваться для тестирования рабочей роли B.

3.  Создайте сообщение для отправки в созданный список рассылки и запланируйте его на текущий день или на уже прошедший день.

    ![Новое сообщение в состоянии ожидания][Новое сообщение в состоянии ожидания]

4.  Подождав немного дольше минуты (так как период бездействия метода Run составляет одну минуту), обновите веб-страницу "Сообщения " и убедитесь в изменении состояния на "Обработка". (Возможно, сначала состояние изменится на "Очередь", но скорее всего, переход от состояния "Очередь" к состоянию "Обработка" произойдет настолько быстро, что вы его не заметите.)

    ![Новое сообщение в состоянии обработки][Новое сообщение в состоянии обработки]

5.  Откройте **обозреватель сервера** и откройте узел хранилища разработки.

6.  Разверните пункт **Очереди**, правой клавишей щелкните **azuremailqueue** и затем **Просмотр очереди**.

    Для каждого проверенного подписчика в списке получателей электронной почты отображается одно сообщение очереди.

    ![Сообщение очереди в ASE][Сообщение очереди в ASE]

7.  Дважды щелкните сообщение очереди.

    Отображается содержимое сообщения очереди: ключ раздела (дата), ключ строки (значение MessageRef и электронный адрес) и флаг перезапуска, разделенные запятой.

    ![Содержимое сообщения очереди в ASE][Содержимое сообщения очереди в ASE]

8.  Закройте диалоговое окно **Просмотр сообщения**.

9.  Откройте **Таблицы**, щелкните правой кнопкой мыши таблицу **Message** и выберите **Просмотр таблицы**.

    Отображается запланированное сообщение со значением «message» в ключе строки, а затем отображается строка для каждого проверенного подписчика с адресом электронной почты в ключе строки.

10. Дважды щелкните строку, которая содержит адрес электронной почты в ключе строки, чтобы просмотреть содержимое строки «message», созданной этой рабочей ролью.

    ![Строка «message»][Строка «message»]

11. Дважды щелкните строку, которая содержит адрес электронной почты в ключе строки, чтобы просмотреть содержимое строки `SendEmail`, созданной этой рабочей ролью А.

    ![Строка SendEmail в таблице Message][Строка SendEmail в таблице Message]

## <a name="nextsteps"></a>Дальнейшие действия

Вы создали рабочую роль A и проверили, что она создает сообщения очереди и строки таблицы, необходимые рабочей роли B для отправки сообщений электронной почты. В [следующем учебнике][следующем учебнике] вы создадите и протестируете рабочую роль B.

Ссылки на дополнительные ресурсы по работе с таблицами, очередями и BLOB-объектами хранилища Azure приведены [в конце последнего учебника данной серии][следующем учебнике].

<div><a href="/ru-ru/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Учебник 5</a></div>

  [первом учебнике серии]: /ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Добавление ссылки на веб-проект]: #addref
  [Добавление модели SendEmail]: #addmodel
  [Добавление кода, который выполняется при запуске рабочей роли]: #addcode
  [Проверка рабочей роли A]: #testing
  [Дальнейшие действия]: #nextsteps
  [Добавление ссылки на MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [Таблица message с sendmail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [втором учебнике]: /ru-ru/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/ru-ru/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/ru-ru/library/system.net.servicepointmanager.aspx
  [Перезапуск экземпляра роли из-за обновлений ОС]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [UnhandledException]: http://msdn.microsoft.com/ru-ru/library/system.appdomain.unhandledexception.aspx
  [Новое сообщение в состоянии ожидания]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [Новое сообщение в состоянии обработки]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [Сообщение очереди в ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [Содержимое сообщения очереди в ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [Строка «message»]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [Строка SendEmail в таблице Message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [следующем учебнике]: /ru-ru/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
