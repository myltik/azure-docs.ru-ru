<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" urlDisplayName="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Учебник по многоуровневому веб-приложению ASP.NET c Azure - шаг 4, рабочая роль A " metaKeywords="учебник по Azure, многоуровневое приложение .NET, многоуровневая архитектура" description="Четвертый учебник в серии, в котором объясняется настройка компьютера для разработки и развертывания приложения службы электронной почты в Azure." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Создание рабочей роли A (планировщик электронной почты) для приложения службы электронной почты Azure — 4 из 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Создание рабочей роли A (планировщик электронной почты) для приложения службы электронной почты Azure — 4 из 5. 

Это четвертый из пяти учебников в серии. В нем показано, как построить и развернуть образец приложения службы электронной почты Azure.  Сведения о приложении и самой серии учебников см. в [первом учебнике серии][firsttutorial].

В этом учебнике вы узнаете следующее:

*  Как запрашивать и обновлять хранилище таблиц Azure.
*  Как добавлять рабочие элементы в очередь для обработки другой рабочей ролью.
*  Как обрабатывать запланированные завершения работы путем переопределения метода `OnStop`.
*  Как обрабатывать незапланированные завершения работы, проверяя отсутствие пропущенных и повторяющихся сообщений электронной почты.
*  Как проверить рабочую роль, использующую хранилище таблиц Azure, с помощью обозревателя хранилищ Azure.
 
Вы уже создали проект рабочей роли A при создании проекта облачной службы. Поэтому сейчас нужно лишь запрограммировать рабочую роль и настроить ее на использование вашей учетной записи хранилища Azure.




<h2><a name="addref"></a><span class="short-header">Добавление ссылки на проект</span>Добавление ссылки на веб-проект</h2>

Ссылка на веб-проект нужна, поскольку именно там определяются классы сущностей. Те же классы сущностей используются в рабочей роли B для чтения и записи данных в используемых приложением таблицах Azure.

**Примечание.** В рабочем приложении задавать ссылку на веб-проект из проекта рабочей роли не требуется, так как это приводит к ссылке на набор зависимых сборок, которые не нужны в данной рабочей роли. В обычной ситуации общие классы модели сохраняются в проекте библиотеки классов, и проекты веб-роли и рабочей роли ссылаются на этот проект библиотеки классов. Чтобы упростить структуру решения, в рамках данного учебника классы модели хранятся в веб-проекте.

4. Щелкните проект WorkerRoleA правой кнопкой мыши и выберите **Добавить ссылку**.

	![Add reference in WorkerRoleA project][mtas-worker-a-add-reference-menu]

4. В окне **Диспетчер ссылок** добавьте ссылку в проект MvcWebRole (или в проект веб-приложения при использовании веб-интерфейса на веб-сайте Azure), затем нажмите кнопку **ОК**.

	![Add reference to MvcWebRole][mtas-worker-a-reference-manager]




<h2><a name="addref2"></a><span class="short-header">Добавление ссылки на библиотеку клиента хранения 1.7</span>Добавление ссылки на сборку библиотеки клиента хранения 1.7</h2>

>[WACOM.NOTE] Пропустите этот шаг, если вы установили пакет SDK версии 2.3 и выше.

Версия 2.0 библиотеки клиента хранения (SCL) не предоставляет все необходимое для диагностики, поэтому следует добавить ссылку на одну из сборок 1.7. Это уже сделано, если вы выполнили действия, описанные в предыдущем учебнике. Однако здесь приведены соответствующие инструкции на тот случай, если этот шаг был пропущен.

4. Щелкните проект WorkerRoleA правой кнопкой мыши и выберите **Добавить ссылку**.

5. Нажмите кнопку **Обзор...** в нижней части диалогового окна.

6. Перейдите в следующую папку:

        C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. Выберите *Microsoft.WindowsAzure.StorageClient.dll* и нажмите кнопку **Добавить**.

8. В диалоговом окне **Диспетчер ссылок** нажмите кнопку **ОК**.




<h2><a name="addmodel"></a><span class="short-header">Добавление модели SendEmail</span>Добавление модели SendEmail</h2>

Рабочая роль A создает строки `SendEmail` в таблице `Message`, а рабочая роль B считывает эти строки для получения информации, необходимой для отправки сообщений электронной почты. На следующем рисунке показано подмножество свойств для двух строк `Message` и трех строк `SendEmail` в таблице `Message`.

	![message table with sendmail][mtas-sendMailTbl]

Эти строки в таблице `Message` служат для достижения нескольких целей:

* Они предоставляют всю информацию, необходимую рабочей роли B для отправки отдельного сообщения электронной почты.
* Они отслеживают факт отправки сообщения электронной почты, чтобы предотвратить отправку его дубликатов в случае перезапуска рабочей роли после сбоя.
* Они позволяют рабочей роли A определить отправку всех сообщений электронной почты для сообщения, чтобы его можно было пометить как `Завершенное`.

Для чтения и записи строк `SendEmail` требуется класс модели.  Так как он должен быть доступен как рабочей роли A, так и рабочей роли B, а все другие классы модели определены в веб-проекте, имеет смысл также определить в веб-проекте и данный класс. 

1. В **обозревателе решений** щелкните правой кнопкой мыши папку &quot;Модели&quot; в веб-проекте и выберите **Добавить существующий элемент**.

	![Add existing item to Models folder in web project][mtas-add-existing-for-sendemail-model]

3. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *SendEmail.cs* в папке &quot;Модели&quot; веб-проекта и нажмите кнопку &quot;Добавить&quot;.

4. Откройте *SendEmail.cs* и изучите код.

		public class SendEmail : TableEntity 	    { 	        public long MessageRef { get; set; }
	        public string EmailAddress { get; set; }
	        public DateTime? ScheduledDate { get; set; }
	        public String FromEmailAddress { get; set; }
	        public string SubjectLine { get; set; }
	        public bool? EmailSent { get; set; }
	        public string SubscriberGUID { get; set; }
	        public string ListName { get; set; }
	    }
	
	Приведенный здесь код аналогичен другим классам модели, за исключением отсутствия атрибутов DataAnnotations, так как этой модели не сопоставлен пользовательский интерфейс, и она не используется в контроллере MVC. 

<h2><a name="addcode"></a><span class="short-header">Добавление кода рабочей роли</span>Добавление кода, выполняемого при запуске рабочей роли</h2>

4. В проекте WorkerRoleA откройте файл *WorkerRole.cs* и изучите код.
	
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
	        { 	            // Set the maximum number of concurrent connections  	            ServicePointManager.DefaultConnectionLimit = 12; 	 	            // For information on handling configuration changes 	            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
	 	            return base.OnStart(); 	        } 	    } 	
	Это код шаблона по умолчанию для рабочей роли. Существует метод `OnStart`, в который можно поместить код инициализации, выполняемый только при запуске экземпляра рабочей роли, а также метод `Run`, вызываемый после завершения метода `OnStart`. Замените этот код собственным кодом инициализации и выполните его.

5. Удалите *WorkerRole.cs*, щелкните правой кнопкой мыши проект WorkerRoleA и выберите **Добавить существующий элемент**.

	![Add existing item to Worker Role A][mtas-worker-a-add-existing]

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл *WorkerRoleA.cs* в проекте WorkerRoleA и щелкните пункт **Добавить**.

3. Откройте *WorkerRoleA.cs* и изучите код.

	Метод `OnStart` инициализирует объекты контекста, которые необходимы для работы с сущностями хранилища Azure. Это также гарантирует существование всех таблиц, очередей и контейнеров BLOB-объектов, которые будут использоваться в методе `Run`. Код, выполняющий эти задачи, аналогичен рассмотренному ранее коду в конструкторах контроллеров MVC. Настройка строки подключения, используемой этим методом, будет осуществлена позднее.

   
        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

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
   
	Метод `ConfigureDiagnostics`, вызываемый методом `OnStart`, задает трассировку, так что вы можете просматривать результат работы методов `Trace.Information` и `Trace.Error`.  Этот метод описан во [втором учебнике][tut2].

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

	* Azure требует перезагрузить виртуальную машину (экземпляр веб-роли или рабочей роли) или физический компьютер, на котором размещается виртуальная машина.
	* Вы остановили облачную службу, нажав кнопку **Прекратить** на портале управления Azure.
	* Вы развернули обновление для своего проекта облачной службы.

	Метод `Run` отслеживает переменную `onStopCalled` и прекращает получение новых рабочих элементов для обработки, когда значение этой переменной изменяется на `true`. Такая скоординированность работы методов `OnStop` и `Run` обеспечивает корректное завершение работы рабочего процесса.

	Azure периодически устанавливает обновления операционной системы, чтобы обеспечить безопасность, надежность и эффективность платформы. Обычно эти обновления требуют завершения работы и перезагрузки компьютеров, на которых размещаются облачные службы. Дополнительные сведения см. в разделе [Перезапуск экземпляра роли из-за обновлений ОС](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

	Метод `Run` выполняет две задачи:

	* Просматривает таблицу `message` и ищет сообщения, запланированные к передаче сегодня или раньше, для которых рабочие элементы очереди еще не создавались.

	* Просматривает таблицу `message` и ищет сообщения со статусом, указывающим на то, что были созданы все рабочие элементы очереди, однако отправлены еще не все сообщения электронной почты. Если он находит такое сообщение, то проверяет его строки `SendEmail`, чтобы определить, все ли сообщения электронной почты были отправлены. Если это так, он обновляет состояние до значения `Completed` и архивирует соответствующую строку `message`.

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
                    }                     // Retrieve all messages that are scheduled for tomorrow or earlier                     // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),                         TableOperators.And,                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();                     TableOperation replaceOperation;                     // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)                     {                         string restartFlag = "0";                         // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";                         }

                        // If the message is in Pending status, change                         // it to Queuing.
                        if (messageToProcess.Status == "Pending")                         {                             messageToProcess.Status = "Queuing";                             replaceOperation = TableOperation.Replace(messageToProcess);                             messageTable.Execute(replaceOperation);                         }

                        // If the message is in Queuing status,                          // process it and change it to Processing status;                         // otherwise it's already in processing status, and                          // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")                         {                             ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";                             replaceOperation = TableOperation.Replace(messageToProcess);                             messageTable.Execute(replaceOperation);                         }                         else                         {                             CheckAndArchiveIfComplete(messageToProcess);                         }                     }

                    // Sleep for one minute to minimize query costs.                      System.Threading.Thread.Sleep(1000 * 60);                 }                 catch (Exception ex)                 {                     string err = ex.Message;                     if (ex.InnerException != null)                     {                         err += " Inner Exception: " + ex.InnerException.Message;                     }                     Trace.TraceError(err);                     // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);                 }             }         }


	Обратите внимание, что вся работа выполняется в бесконечном цикле в блоке `while`, а весь код в блоке `while` заключен в блок `try`-`catch`, чтобы избежать возникновения необработанного исключения. Если возникает необработанное исключение, Azure генерирует событие [UnhandledException](http://msdn.microsoft.com/ru-ru/library/system.appdomain.unhandledexception.aspx), рабочий процесс прерывается, после чего роль переводится в автономный режим. Рабочая роль будет перезапущена Azure, но для этого требуется несколько минут. Блок `try` вызывает `TraceError` для регистрации ошибки, а затем бездействует в течение 60 секунд, чтобы в случае повторения ошибки сообщение о ней не повторялось слишком много раз.  В рабочем приложении в блоке `try` можно отправить сообщение электронной почты администратору.

	Метод `Run` обрабатывает запрос для строк `message` в таблице `message`, для которых запланирована дата до завтрашнего дня:

                    // Retrieve all messages that are scheduled for tomorrow or earlier                     // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(                         TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),                         TableOperators.And,                         TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));                     var query = (new TableQuery<Message>().Where(typeAndDateFilter));                     var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

	**Примечание.** Одно из преимуществ перемещения строк message в таблицу `messagearchive` после их обработки заключается в том, что данный запрос должен указывать только условия поиска `PartitionKey` и `RowKey`. Если бы обработанные строки не архивировались, запросу нужно было бы также указывать неключевое поле (`Состояние`) и выполнять поиск по большему числу строк.  Размер таблицы увеличивался бы, на выполнение запроса требовалось бы больше времени, и он мог бы начать получать маркеры продолжения.

	Если сообщение находится в состоянии `Ожидает`, обработка еще не началась, если же оно находится в состоянии `Очередь`, обработка уже начиналась, но была прервана до создания всех сообщений очереди. В этом случае при отправке каждого сообщения электронной почты в рабочей роли B требуется выполнять дополнительную проверку, чтобы убедиться, что данное сообщение электронной почты не было отправлено ранее. Именно для этого и служит переменная `restartFlag`.

                        string restartFlag = "0";                         if (messageToProcess.Status == "Queuing")                         {                             restartFlag = "1";                         }

	Затем код переводит строки `message` с состоянием `Ожидает` в состояние `Очередь`.  После чего для этих и всех других строк, которые уже были в состоянии `Очередь`, он вызывает метод `ProcessMessage`, чтобы создать рабочие элементы очереди для отправки сообщений электронной почты для данного сообщения. 

                        if (messageToProcess.Status == "Pending")                         {                             messageToProcess.Status = "Queuing";                             replaceOperation = TableOperation.Replace(messageToProcess);                             messageTable.Execute(replaceOperation);                         }

                        if (messageToProcess.Status == "Queuing")                         {                             ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else                         {                             CheckAndArchiveIfComplete(messageToProcess);
                        }


	После обработки сообщения в состоянии `Очередь` код устанавливает для строки `Message` состояние `Обработка`. Строки в таблице `message`, которым не назначено состояние `Ожидание` или `Очередь`, уже находятся в состоянии `Обработка`, и для этих строк код вызывает метод, который проверяет, все ли сообщения электронной почты были отправлены для данного сообщения.  Если были отправлены все сообщения электронной почты, строка `message` архивируется.

	После обработки всех полученных по запросу записей код бездействует в течение одной минуты.  

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

	Существуют минимальные затраты на выполнение каждого запроса хранилища Azure, даже если запрос не возвращает никаких данных, поэтому непрерывное повторное сканирование увеличивает ваши расходы на Azure. На момент написания данного учебника стоимость миллиона транзакций (запрос подсчитывается как транзакция) составляет 0,1 долл. США, поэтому время бездействия может составлять значительно меньше минуты, и при этом затраты на сканирование таблиц для поиска отправляемых сообщений по-прежнему будут минимальными. Дополнительную информацию о ценах см. в [первом учебнике][firsttutorial].

	**Примечание по применению потоков и оптимальному использованию ЦП.** В методе `Run` существует две задачи (помещение сообщений электронной почты в очередь и проверка выполненных сообщений), которые последовательно выполняются в одном потоке. Малая виртуальная машина (ВМ) имеет 1,75 ГБ ОЗУ и только один ЦП, чего вполне достаточно для последовательного выполнения этих задач в одном потоке. Предположим, что для эффективной работы вашему приложению требуется больше памяти, чем может предоставить малая виртуальная машина. Средняя виртуальная машина предоставляет 3,5 ГБ ОЗУ и 2 ЦП, однако приложение будет использовать только один ЦП, так как оно ориентировано на однопотоковое выполнение. Чтобы воспользоваться преимуществами всех ЦП, потребовалось бы создать для каждого из них рабочий поток. Кроме того, один поток не позволяет полностью использовать возможности даже одного ЦП. Когда поток выполняет вызовы сети или ввода-вывода, поток должен ожидать завершения этого вызова, при этом он не выполняет никакой полезной работы. Если бы метод `Run` был реализован на базе двух потоков, то во время ожидания сетевой операции или операции ввода-вывода одним потоком другой поток мог бы выполнять полезную работу.

	Метод `ProcessMessage` получает все адреса электронной почты для списка получателей электронной почты и создает рабочий элемент очереди для каждого адреса электронной почты. При создании рабочих элементов очереди он также создает строки `SendEmail` в таблице `Message`. Эти строки содержат сведения, необходимые рабочей роли B для отправки сообщений электронной почты, а также свойство `EmailSent`, позволяющее отслеживать отправку каждого сообщения электронной почты.
	
        private void ProcessMessage(Message messageToProcess, string restartFlag)         {             // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");             var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;             }             // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),                TableOperators.And,                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));             var query = new TableQuery<Subscriber>().Where(filter);             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)             {                 // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)                 {                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");                     continue;                 }

                // Create a SendEmail entity for this email.                               var sendEmailRow = new SendEmail                 {                     PartitionKey = messageToProcess.PartitionKey,                     RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,                     EmailAddress = subscriber.EmailAddress,                     EmailSent = false,                     MessageRef = messageToProcess.MessageRef,                     ScheduledDate = messageToProcess.ScheduledDate,                     FromEmailAddress = mailingList.FromEmailAddress,                     SubjectLine = messageToProcess.SubjectLine,                     SubscriberGUID = subscriber.SubscriberGUID,                     ListName = mailingList.ListName                 };

                // When we try to add the entity to the SendEmail table,                  // an exception might happen if this worker role went                  // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try                 {                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);                     messageTable.Execute(upsertOperation);                 }                 catch (Exception ex)                 {                     string err = "Error creating SendEmail row:  " + ex.Message;                     if (ex.InnerException != null)                     {                         err += " Inner Exception: " + ex.InnerException;                     }                     Trace.TraceError(err);                 }

                // Create the queue message.
                string queueMessageString =                     sendEmailRow.PartitionKey + "," +                     sendEmailRow.RowKey + "," +                     restartFlag;                 var queueMessage = new CloudQueueMessage(queueMessageString);                 sendEmailQueue.AddMessage(queueMessage);             }

            Trace.TraceInformation("ProcessMessage end PK: "                 + messageToProcess.PartitionKey);         }

	Сначала код получает строку списка рассылки из таблицы `mailinglist` для списка получателей рассылки.  Эта строка содержит адрес электронной почты &quot;От&quot;, который необходим рабочей роли B для отправки сообщений электронной почты.

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");             var retrievedResult = mailingListTable.Execute(retrieveOperation);             var mailingList = retrievedResult.Result as MailingList;             if (mailingList == null)             {                 Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);                 return;             }

	Затем он запрашивает из таблицы `mailinglist` все строки подписчика для списка получателей рассылки.

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),                TableOperators.And,                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));             var query = new TableQuery<Subscriber>().Where(filter);             var subscribers = mailingListTable.ExecuteQuery(query).ToList();

	В цикле обработки результатов запроса код начинает работу с проверки подтверждения адреса электронной почты подписчика, если подтверждение не выполнено, сообщение электронной почты в очередь не помещается.

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)                 {                     Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");                     continue;
                }

	Затем код создает строку `SendEmail` в таблице `message`. Эта строка содержит информацию, используемую рабочей ролью B для отправки сообщения электронной почты. Создается строка со свойством `EmailSent`, имеющим значение `false`.

                // Create a SendEmail entity for this email.                               var sendEmailRow = new SendEmail                 {                     PartitionKey = messageToProcess.PartitionKey,                     RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,                     EmailAddress = subscriber.EmailAddress,                     EmailSent = false,                     MessageRef = messageToProcess.MessageRef,                     ScheduledDate = messageToProcess.ScheduledDate,                     FromEmailAddress = mailingList.FromEmailAddress,                     SubjectLine = messageToProcess.SubjectLine,                     SubscriberGUID = subscriber.SubscriberGUID,                     ListName = mailingList.ListName                 };                 try                 {                     var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);                     messageTable.Execute(upsertOperation);
                }                 catch (Exception ex)                 {                     string err = "Error creating SendEmail row:  " + ex.Message;                     if (ex.InnerException != null)                     {                         err += " Inner Exception: " + ex.InnerException;                     }                     Trace.TraceError(err);                 }

	В коде используется операция upsert, поскольку строка может уже существовать, если рабочая роль A перезапускается после сбоя.

	Последняя задача, которую требуется выполнить для каждого адреса электронной почты, заключается в создании рабочего элемента очереди, который будет активировать рабочую роль B для отправки сообщения электронной почты. Рабочий элемент очереди содержит ключ раздела и значение ключа строки `SendEmail`, созданной ранее, а также флаг перезапуска, который был установлен ранее. Строка `SendEmail` содержит всю информацию, нужную рабочей роли B для отправки сообщения электронной почты.

                // Create the queue message.
                string queueMessageString =                     sendEmailRow.PartitionKey + "," +                     sendEmailRow.RowKey + "," +                     restartFlag;                 var queueMessage = new CloudQueueMessage(queueMessageString);                 sendEmailQueue.AddMessage(queueMessage);

	Метод `CheckAndUpdateStatusIfComplete` проверяет сообщения, имеющие состояние &quot;Обработка&quot;, чтобы определить, все ли сообщения электронной почты были отправлены. При обнаружении неотправленных сообщений электронной почты он обновляет состояние строки до значения `Completed` и архивирует строку. 

        private void CheckAndArchiveIfComplete(Message messageToCheck)         {             // Get the list of emails to be sent for this message: all SendEmail rows             // for this message.               string pkrkFilter = TableQuery.CombineFilters(                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),                 TableOperators.And,                 TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));             var query = new TableQuery<SendEmail>().Where(pkrkFilter);             var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

            if (emailToBeSent != null)
            {
                return;             }

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table             var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };             messageToCheck.Status = "Complete";             var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);             messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);             messageTable.Execute(deleteOperation);         }





<h2><a name="configure"></a><span class="short-header">Настройка хранилища</span>Настройка строки подключения хранилища</h2>

Если вы не настроили учетные данные учетной записи хранилища для рабочей роли A одновременно с настройкой веб-роли, сделайте это сейчас. 

1. В обозревателе решений щелкните правой кнопкой мыши элемент **WorkerRoleA** в области **Роли** облачного проекта **AzureEmailService**, затем выберите пункт **Свойства**.

2. Убедитесь, что в раскрывающемся списке **Конфигурация службы** выбрано значение **Все конфигурации**.

2. Откройте вкладку **Параметры** и щелкните **Добавить параметр**.

3. Введите *StorageConnectionString* в столбце **Name**.

4. Выберите элемент **Строка подключения** в раскрывающемся списке **Тип**.  
  
5. Щелкните многоточие (**...**) в правом конце строки, чтобы создать новую строку подключения.

6. В диалоговом окне **Строка подключения к учетной записи хранилища** щелкните элемент **Ваша подписка**.

7. Выберите правильные значения в полях **Подписка** и **Имя учетной записи** и нажмите кнопку **ОК**.

8. Задайте строку подключения диагностики. Для строки подключения диагностики можно использовать ту же учетную запись хранилища, однако для трассировки (диагностической) информации рекомендуется использовать другую учетную запись хранилища.




<h2><a name="testing"></a><span class="short-header">Тестирование</span>Тестирование рабочей роли A</h2>

1. Запустите приложение нажатием клавиши F5.

>[WACOM.NOTE] При использовании Visual Studio 2013 и последнего пакета SDK может возникнуть ошибка &quot;неоднозначная ссылка&quot;, связанная с ссылкой на `LogLevel`. Щелкните правой кнопкой мыши пункт `LogLevel`, щелкните &quot;Разрешить&quot; и выберите пункт `Microsoft.WindowsAzure.Diagnostics.LogLevel`.

2. Используйте веб-страницы администратора для создания списка рассылки и помещения в него подписчиков. Присвойте значение `true` свойству `Проверено` по крайней мере для одного из подписчиков и установите в качестве адреса электронной почты такой адрес, на который вы можете получать почту.

	Сообщения электронной почты не отправляются до реализации рабочей роли B, но те же тестовые данные будут использоваться для тестирования рабочей роли B.

3. Создайте сообщение для отправки в созданный список рассылки и запланируйте его на текущий день или на уже прошедший день.

	![New message in pending status][mtas-worker-a-test-pending]

4. Подождав немного дольше минуты (так как период бездействия метода Run составляет одну минуту), обновите веб-страницу &quot;Сообщения&quot; и убедитесь, что состояние изменено на &quot;Обработка&quot;. (Возможно, сначала состояние изменится на &quot;Очередь&quot;, но скорее всего, переход от состояния &quot;Очередь&quot; к состоянию &quot;Обработка&quot; произойдет настолько быстро, что вы его не заметите.)

	![New message in processing status][mtas-worker-a-test-processing]

5. Откройте обозреватель хранилища Azure и выберите свою тестовую учетную запись хранения.

6. В области **Тип хранилища** обозревателя хранилищ Azure, выберите **Запросы**, затем **azuremailqueue**.

	Для каждого проверенного подписчика в списке получателей электронной почты отображается одно сообщение очереди.

	![Queue message in ASE][mtas-worker-a-tst-ase-queue]

7. Дважды щелкните сообщение очереди, а затем выберите вкладку **Сообщение** в диалоговом окне **Сведения о сообщении**.

	Отображается содержимое сообщения очереди: ключ раздела (от 2012-12-14), ключ строки (значение MessageRef и адрес электронной почты) и флаг перезапуска, разделенные запятой.

	![Queue message contents in ASE][mtas-worker-a-tst-ase-queue-detail]

8. Закройте диалоговое окно **Сведения о сообщении**.

9. В разделе **Тип хранилища** щелкните элемент **Таблицы** и выберите таблицу **Message**.

10. Щелкните элемент **Запрос** для просмотра всех строк в таблице.

	Отображается запланированное сообщение со значением &quot;Message&quot; в ключе строки, а затем отображается строка для каждого проверенного подписчика с адресом электронной почты в ключе строки.

	![Message table rows in ASE][mtas-worker-a-test-ase-message-table]

11. Дважды щелкните строку, которая содержит адрес электронной почты в ключе строки, чтобы просмотреть содержимое строки `SendEmail`, созданной рабочей ролью A.

	![SendEmail row in Message table][mtas-worker-a-test-ase-sendemail-row]

<h2><a name="nextsteps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы создали рабочую роль A и проверили, что она создает сообщения очереди и строки таблицы, необходимые рабочей роли B для отправки сообщений электронной почты. В [следующем учебнике][tut5] вы создадите и протестируете рабочую роль B.

Ссылки на дополнительные ресурсы по работе с таблицами, очередями и BLOB-объектами хранилища Azure приведены в конце [последнего учебника данной серии][tut5].

<div><a href="/ru-ru/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Учебник 5</a></div>








[firsttutorial]: /ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut5]: /ru-ru/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[tut2]: /ru-ru/develop/net/tutorials/multi-tier-web-site/2-download-and-run/




[mtas-worker-a-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-existing.png
[mtas-worker-a-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-add-reference-menu.png
[mtas-worker-a-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
[mtas-add-existing-for-sendemail-model]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-add-existing-for-sendemail-model.png
[mtas-worker-a-test-processing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
[mtas-worker-a-test-pending]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
[mtas-worker-a-tst-ase-queue]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
[mtas-worker-a-tst-ase-queue-detail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
[mtas-worker-a-test-ase-message-table]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
[mtas-worker-a-test-ase-sendemail-row]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
[mtas-sendMailTbl]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
