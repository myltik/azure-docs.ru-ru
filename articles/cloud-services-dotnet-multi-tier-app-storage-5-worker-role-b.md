<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Шаг 5. Рабочая роль B" pageTitle="Многоуровневое веб-приложение ASP.NET в Azure — шаг 5. Рабочая роль B" metaKeywords="Учебник по Azure, добавление облачной службы рабочей роли C#" description="Пятый учебник в серии, который посвящен настройке компьютера для разработки приложений для среды Azure и развертывания приложения службы эл. почты" metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Создание рабочей роли B (отправитель электронной почты) для приложения службы электронной почты Azure – 5 из 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Создание рабочей роли B (отправитель электронной почты) для приложения службы электронной почты Azure – 5 из 5. 

Это пятый из пяти учебников в серии. В нем показано, как построить и развернуть образец приложения службы электронной почты Azure.  Сведения о приложении и самой серии учебников см. в [первом учебнике серии][firsttutorial].

В этом учебнике вы узнаете следующее:

* Как добавить в проект облачной службы рабочую роль.
* Как опросить очередь и обработать рабочие элементы в очереди.
* Как отправить электронную почту с помощью SendGrid.
* Как обрабатывать запланированные завершения работы путем переопределения метода `OnStop`.
* Как обрабатывать незапланированные завершения работы, проверяя отсутствие повторяющихся сообщений электронной почты.
 
<h2><a name="addworkerrole"></a><span class="short-header">Добавление рабочей роли B</span>Добавление проекта рабочей роли B в решение</h2>

1. В обозревателе решений щелкните правой кнопкой мыши проект облачной службы и выберите **Создать проект рабочей роли**.

	![Меню нового проекта рабочей роли][mtas-new-worker-role-project]

3. В диалоговом окне **Добавить новый проект роли** выберите **C#**, выберите **Рабочая роль**, назовите проект WorkerRoleB и нажмите **Добавить**. 

	![Диалоговое окно "Новый проект роли"][mtas-add-new-role-project-dialog]

<h2><a name="addreference"></a><span class="short-header">Добавление ссылки</span>Добавление ссылки на веб-проект</h2>

Ссылка на веб-проект нужна, поскольку именно там определяются классы сущностей. Классы сущностей используются в рабочей роли B для чтения и записи данных в используемых приложением таблицах Azure.

4. Щелкните проект WorkerRoleB правой кнопкой мыши и выберите **Добавить ссылку**.

	![Добавление ссылки в проект WorkerRoleB][mtas-worker-b-add-reference-menu]

4. В окне **Диспетчер ссылок** добавьте ссылку в проект MvcWebRole (или в проект веб-приложения при использовании веб-интерфейса на веб-сайте Azure).

	![Добавление ссылки на MvcWebRole][mtas-worker-b-reference-manager]



<h2><a name="sclpackage"></a><span class="short-header">Добавление пакета SCL 2.0</span>Добавление в проект пакета NuGet клиентской библиотеки хранилища 2.0</h2>

>[WACOM.NOTE] Благодаря Visual Studio 2013 этот раздел можно пропустить, поскольку текущий пакет хранилища Azure установлено в новом проекте рабочей роли.

При добавлении проекта он не получил автоматически обновленную версию пакета NuGet клиентской библиотеки хранилища. Вместо этого была получена старая версия пакета 1.7, так как именно она была включена в шаблон проекта. Теперь решение содержит две версии пакета NuGet хранилища Azure: версию 2.0 в проектах MvcWebRole и WorkerRoleA и версию 1.7 в проекте WorkerRoleB. Необходимо удалить версию 1.7 и установить версию 2.0 в проекте WorkerRoleB.

1. В меню **Сервис** выберите **Диспетчер пакетов библиотеки** и затем **Управление пакетами NuGet для решения**.

2. С выбранным на левой панели пунктом **Установленные пакеты** прокрутите вниз, пока не найдете пакет хранилища Azure.

	В списке этот пакет будет содержаться дважды: один раз для версии 1.7 и второй – для версии 2.0.

4. Выберите версию 1.7 пакета и нажмите кнопку **Управление**.

	Флажки для MvcWebRole и WorkerRoleB сняты, а флажок для WorkerRoleB установлен.

5. Снимите флажок для WorkerRoleB и нажмите кнопку **ОК**.

6. При появлении запроса не необходимость удаления зависимых пакетов нажмите кнопку **Нет**.

	После завершения удаления в диалоговом окне NuGet отображается только версия пакета 2.0.

7. Щелкните **Управление** для версии пакета 2.0.

	Флажки для MvcWebRole и WorkerRoleA выбраны, а флажок для WorkerRoleA снят.

8. Установите флажок для WorkerRoleA и нажмите кнопку **ОК**.





<h2><a name="addref2"></a><span class="short-header">Добавление ссылки на библиотеку клиента хранения 1.7</span>Добавление ссылки на сборку библиотеки клиента хранения 1.7</h2>

>[WACOM.NOTE] Пропустить этот раздел, если был установлен последний пакет SDK и используется Visual Studio 2013

Версия 2.0 клиентской библиотеки хранения (SCL) не предоставляет все необходимое для диагностики, поэтому следует добавить ссылку на одну из сборок 1.7, как это было сделано ранее для двух других проектов.

4. Щелкните проект WorkerRoleB правой кнопкой мыши и выберите **Добавить ссылку**.

5. Нажмите кнопку **Обзор...** в нижней части диалогового окна.

6. Перейдите в следующую папку:

        C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\2012-10\ref

7. Выберите *Microsoft.WindowsAzure.StorageClient.dll* и нажмите кнопку **Добавить**.

8. В диалоговом окне **Диспетчер ссылок** нажмите кнопку **ОК**.





<h2><a name="addsendgrid"></a><span class="short-header">Добавление пакета SendGrid</span>Добавление в проект пакета SendGrid NuGet</h2>

Чтобы отправить сообщение эл. почты с помощью SendGrid, необходимо установить пакет SendGrid NuGet.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект WorkerRoleB и выберите **Управление пакетами NuGet**.

	![Управление пакетами NuGet][mtas-worker-b-manage-nuget]

2. В диалоговом окне **Управление пакетами NuGet** выберите вкладку **В сети**, введите sendgrid в поле поиска и нажмите клавишу ВВОД.

3. Щелкните пункт **Установить** на пакете **SendGrid**.

	![Установка пакета Sendgrid][mtas-worker-b-install-sendgrid]

4. Закройте диалоговое окно.








<h2><a name="addsettings"></a><span class="short-header">Добавление параметров проекта</span>Добавление параметров проекта</h2>

Как и рабочая роль А, рабочая роль B нуждается в учетных данных учетной записи хранения для работы с таблицами, запросами и BLOB-объектами. Кроме того, чтобы отправить эл. почту, рабочая роль должна иметь учетные данные для вставки в вызовы службы SendGrid. А для формирования ссылки отмены подписки, которая будет включаться в отправляемые сообщения эл. почты, рабочей роли должен быть известен URL-адрес приложения. Эти значения сохраняются в параметрах проекта.

Для учетных данных учетной записи хранения используется та же процедура, что и в [третьем учебнике][tut3configstorage].

1. В разделе **Роли** **обозревателя решений** щелкните правой кнопкой мыши **WorkerRoleB** и выберите пункт **Свойства**.
 
2. Перейдите на вкладку **Параметры**.

2. Убедитесь, что в раскрывающемся списке **Конфигурация службы** выбрано значение **Все конфигурации**.

2. Откройте вкладку **Параметры** и щелкните **Добавить параметр**.

3. Введите StorageConnectionString в столбце **Имя**.

4. Выберите элемент **Строка подключения** в раскрывающемся списке **Тип**.  

6. Нажмите кнопку с многоточием (**...**) в правом конце строки, чтобы открыть диалоговое окно **Строка подключения учетной записи хранения**.

7. В диалоговом окне **Создание строки подключения к хранилищу** щелкните переключатель **Ваша подписка**.

8. Выберите ту же **подписку** и **имя учетной записи**, которые были выбраны для веб-роли и рабочей роли A.

1. Выполните ту же процедуру для настройки параметров строки подключения **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**.

	Затем необходимо создать и настроить три новых параметра, которые будут использоваться только рабочей ролью B.

3. На вкладке **Параметры** окна **Свойства** щелкните пункт **Добавить параметр**, затем добавьте три новых параметра типа **Строка**:

	* **Имя**: SendGridUserName, **Значение**: имя пользователя SendGrid, заданное при работе с [вторым учебником][tut2].

	* **Имя**: SendGridPassword, **Значение**: пароль SendGrid.

	* **Имя**: AzureMailServiceURL, **Значение**: базовый URL-адрес, который будет присвоен приложению после развертывания, например: http://sampleurl.cloudapp.net.

	![Новые параметры в проекте WorkerRoleB][mtas-worker-b-settings]

### Добавьте код, который выполняется при запуске рабочей роли

4. В проекте рабочей роли WorkerRoleB удалите WorkerRole.cs.

5. Щелкните правой кнопкой мыши проект WorkerRoleB и выберите **Добавить существующий элемент**.

	![Добавление существующего элемента для рабочей роли B][mtas-worker-b-add-existing]

2. Перейдите к папке, куда вы загрузили пример приложения, выберите файл WorkerRoleB.cs в проекте WorkerRoleB и щелкните пункт **Добавить**.

>[WACOM.NOTE] В Visual Studio 2013 с последними пакетами SDK и SendGrid NuGet откройте *WorkerRoleB.cs* и внесите следующие изменения в код. (1) Удалите инструкцию `using` для `SendGridMail.Transport`. (2) Измените оба экземпляра `SendGrid.GenerateInstance` на `SendGrid.GetInstance`. (3) Измените оба экземпляра `REST.GetInstance` на `Web.GetInstance`.

3. Откройте WorkerRoleB.cs и изучите код.

	Как вы уже видели в рабочей роли A, метод `OnStart` инициализирует классы контекста, которые необходимы для работы с сущностями хранилища Azure. Это также гарантирует существование всех необходимых таблиц, очередей и контейнеров BLOB-объектов, которые будут использоваться в методе `Run`.  

	Разница по сравнению с рабочей ролью A заключается в добавлении контейнера BLOB-объектов и очереди подписок по создаваемым ресурсам, если эти компоненты еще не существуют. Для получения файлов, содержащих HTML или обычный текст для основной части сообщений эл. почты будет использован контейнер BLOB-объектов. Очередь подписки используется для отправки сообщений эл. почты с подтверждением подписки.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

	Метод `Run` обрабатывает рабочие элементы из двух очередей: очередь, используемая для сообщений, отправленных спискам эл. почты (рабочие элементы, созданные рабочей ролью А), и очередь, используемая для сообщений эл. почты подтверждения подписки (рабочие элементы, созданные методом интерфейса API подписки в проекте MvcWebRole).

       
        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    } 

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

	Этот код выполняется в бесконечном цикле, пока не будет завершена работа рабочей роли. Если рабочий элемент содержится в основной очереди, код обрабатывает его и проверяет очередь подписки. 

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

	Если ни в одной из очередей нет ожидающих элементов, код ждет 60 секунд, прежде чем продолжить работу в цикле. 

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

	Назначением времени ожидания является уменьшение затрат на транзакции Azure, как объясняется в [предыдущем учебнике][tut4] 

	Когда элемент очереди извлекается из очереди методом [GetMessage][], этот элемент очереди на 30 секунд становится невидимым для всех остальных рабочих и веб-ролей, имеющим доступ к очереди. Именно это гарантирует поступление любого сообщения из очереди на обработку только в один экземпляр рабочей роли. Можно явным образом задать это время *эксклюзивной аренды* (время, в течение которого элемент в очереди остается невидимым), передав параметр [время ожидания видимости](http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758454.aspx) в метод `GetMessage`. Если рабочая роль может потратить более 30 на обработку сообщения очереди, следует увеличить время эксклюзивной аренды для предотвращения обработки одного и того же сообщения другими экземплярами роли. 

	С другой стороны не следует задавать слишком большое значение времени эксклюзивной аренды. Например, если время эксклюзивной аренды равно 48 часам и ваша рабочая роль неожиданно завершает работу после вывода сообщения из очереди, другая рабочая роль не должна обрабатывать сообщение в течение следующих 48 часов. Максимальный срок эксклюзивной аренды составляет 7 дней.

	Метод [GetMessages](http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx)  (обратите внимание на s в конце имени) может использоваться для извлечения до 32 сообщений из очереди в рамках одного вызова. Каждый доступ к очереди имеет небольшую стоимость транзакции, и стоимость транзакции не отличается, возвращается ли 32 сообщения или ни одного сообщения. Следующий код извлекает до 32 сообщений в рамках одного вызова и обрабатывает их.

    	foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
	    {
	        ProcessQueueMessage(msg);
	        messageFound = true;
	    }

	При использовании `GetMessages` для удаления нескольких сообщений убедитесь, что тайм-аут видимости предоставляет приложению достаточно времени для обработки всех сообщений. После истечения таймаута видимости другие экземпляры ролей могут получить доступ к сообщению, после чего первый экземпляр не сможет удалить сообщения после обработки рабочего элемента.

	Метод `Run` вызывает `ProcessQueueMessage` после нахождения рабочего элемента в основной очереди:

       
        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

	Опасные сообщения — это те сообщения, которые приводят к созданию приложением исключения во время их обработки.  Если сообщение было извлечено из очереди более пяти раз, предполагается, что оно не может быть обработано, затем оно удаляется из очереди для предотвращения дальнейших попыток обработки. При работе производственных приложений следует рассмотреть возможность перемещения опасного сообщения в очередь "мертвых" сообщений для дальнейшего анализа вместо удаления этого сообщения.

	Код анализирует сообщение в очереди по ключу раздела и ключу строки, которые необходимы для получения строки SendEmail, а также по флагу перезагрузки.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

	Если обработка этого сообщения была перезапущена после неожиданного завершения работы, код проверяет таблицу `messagearchive` для определения, было ли отправлено сообщение эл. почты. Если сообщение было отправлено, код удаляет строку `SendEmail`, если она существует, и удаляет сообщение очереди.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

	Далее мы получаем строку `SendEmail` из таблицы `message`. Эта строка содержит все сведения, необходимые для отправки сообщения электронной почты, за исключением BLOB-объектов, которые содержат основной текст сообщения эл. почты в виде HTML или обычного текста.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

	Затем код отправляет сообщение электронной почты и архивирует строку `SendEmail`.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

	Перемещение строки в таблицу messagearchive не может быть выполнено в транзакции, так как воздействует на несколько таблиц.

	Наконец, если все остальное выполняется успешно, сообщение очереди удаляется.

            sendEmailQueue.DeleteMessage(msg);

	Фактическая работа по отправке эл. почты с использованием SendGrid выполняется с помощью метода `SendEmailToList`. Если необходимо использовать службу, отличную от SendGrid, все, что нужно сделать, это изменить код в этом методе.

	**Примечание.** При наличии недопустимых учетных данных в параметрах проекта вызов SendGrid завершится со сбоем, однако приложение не получит какое-либо подтверждение сбоя.  При использовании SendGrid в производственном приложении рассмотрите возможность настройки отдельных учетных данных для веб-интерфейса API, чтобы избежать незаметных сбоев при изменении администратором своего пароля учетной записи SendGrid. Дополнительные сведения см. в разделе [SendGrid MultiAuth - несколько учетных данных учетных записей](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials). Можно определить учетные данные на сайте [https://sendgrid.com/credentials](https://sendgrid.com/credentials). 

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GenerateInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = REST.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

	В методе `GetBlobText` код получает размер BLOB-объекта, а затем использует это значение для инициализации объекта `MemoryStream` в целях обеспечения производительности. Если не предоставить размер, объект `MemoryStream` выделяет 256 байтов, затем при превышении объемом загрузки этого значения выделяется еще 512 байтов и т. д. Каждый раз выделяемый объем увеличивается вдвое. Для больших BLOB-объектов этот процесс будет неэффективным по сравнению с выделением нужного объема в самом начале загрузки.

	Метод `Run` вызывает `ProcessSubscribeQueueMessage`, если находит рабочий элемент в очереди подписки:
 
        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

	Этот метод выполняет следующие задачи:

	* Если сообщение является "опасным", оно регистрируется и удаляется.
	* Получает GUID подписчика из сообщения очереди.
	* Использует GUID для получения сведений о подписчике из таблицы MailingList.
	* Отправляет сообщения эл. почты с подтверждением новому подписчику.
	* Удаляет сообщение очереди.

	Как и в случае сообщений эл. почты, отправленных в списки, фактическая отправка сообщений эл. почты выполняется в отдельном методе, что упрощает использование при необходимости другой службы эл. почты.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GenerateInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = REST.GetInstance(credentials);
            transportREST.Deliver(email);
        }





<h2><a name="testing"></a><span class="short-header">Тестирование</span>Тестирование рабочей роли B</h2>

1. Запустите приложение нажатием клавиши F5.

2. Перейдите на страницу **Сообщения** для просмотра сообщения, созданного для тестирования рабочей роли А. Примерно через минуты обновите веб-страницу. Вы увидите, что строка исчезла из списка, так как была заархивирована.

4. Проверьте папку входящих сообщений эл. почты, куда должно прийти сообщение. Обратите внимание, что возможны задержки при отправке сообщений электронной почты с помощью SendGrid или при доставке сообщения клиенту эл. почты, поэтому до прихода сообщения эл. почты может пройти некоторое время. Кроме того, необходимо проверить папку нежелательной почты.




<h2><a name="nextsteps"></a><span class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Итак, вы создали приложение службы эл. почты Azure "с нуля", которое не отличается от завершенного проекта, который вы загрузили ранее.  Для развертывания его в облаке, тестирования в облаке и вывода в производственную среду можно использовать те же процедуры, с которыми вы работали во [втором учебнике][tut2]  Если вы решили создать альтернативную архитектуру, дополнительные сведения о развертывании проекта MVC на веб-сайте Azure см. в [учебнике по началу работы с веб-сайтами Azure][getstartedtutorial].

Дополнительные сведения о хранилище Azure см. на следующем ресурсе:

* [Базовые сведения о хранилище Windows Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (блог Бруно Теркали)

Дополнительные сведения о службе таблицы Azure см. на следующих ресурсах:

* [Базовые сведения о хранилище таблицы Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (блог Бруно Теркали)
* [Практическое руководство по эффективному использованию таблиц Windows Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (Блог группы, занимающейся хранилищем Azure)
* [Практическое руководство по службе хранилище таблиц в .NET](http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/table-services/) 
* [Подробный обзор таблиц клиентской библиотеки хранения 2.0 Windows Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (Блог группы, занимающейся хранилищем Azure)
* [Реальный мир: разработка масштабируемой стратегии секционирования для хранилища таблиц Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/hh508997.aspx)

Дополнительные сведения о службе очередей Azure и очередях Service Bus Azure см. на следующих ресурсах:

* [Шаблон работы на основе очередей (создание реальных облачных приложений в Windows Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
* [Очереди Azure и очереди Service Bus Azure - сравнение и противопоставление][sbqueuecomparison]
* [Использование службы хранилища очередей в .NET][queuehowto]

Дополнительные сведения о службе BLOB-объектов Azure см. на следующих ресурсах:

* [Неструктурированное хранилище BLOB-объектов (создание реальных облачных приложений в Windows Azure) ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
* [Использование службы хранения BLOB-объектов Azure в .NET][blobhowto]

Чтобы получить дополнительные сведения об автомасштабировании ролей облачных служб Azure, изучите следующие ресурсы:

* [Практическое руководство по использованию блока приложения автомасштабирования][autoscalingappblock]
* [Автоматическое масштабирование и Azure][autoscaling-and-windows-azure]
* [Создание эластичных, автомасштабируемых решений в Azure](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (видео канала 9 MSDN)


<h2><a name="Acknowledgments"></a><span class="short-header">Подтверждения</span>Подтверждения</h2>

Эти учебники и примерное приложение были написаны [Риком Андерсоном](http://blogs.msdn.com/b/rickandy/) и Томом Дайкстрой. Мы хотели бы выразить благодарность за помощь следующим людям:

* Барри Dorrans (Твиттер [@blowdart](https://twitter.com/blowdart)) 
* [Кори Фаулер](http://blog.syntaxc4.net/) (Твиттер [@SyntaxC4](https://twitter.com/SyntaxC4)) 
* [Джо Джиардино](http://blogs.msdn.com/b/windowsazurestorage/)
* Дон Гловер 
* Яи Харидас
* [Скотт Хантер](http://blogs.msdn.com/b/scothu/) (Твиттер: [@coolcsh](http://twitter.com/coolcsh))
* [Брайан Свон](http://blogs.msdn.com/b/brian_swan/)
* [Даниэл Уэнг](http://blogs.msdn.com/b/daniwang/)
* Члены Консультативного совета разработчиков, которые предоставляли свои отзывы:
   * Дамир Ар
   * Жан-Люк Бушо
   * Карлос дос Сантос
   * Майк Дуглас
   * Роберт Файт
   * Джианни Роза Галлина
   * Фабьен Лавокат
   * Карл Отс
   * Карлос-Алехандро Перез
   * Сунао Томита
   * Перез Джоунс Цисах
   * Майкл ван Отегем

 







[firsttutorial]: /ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/
[tut2]: /ru-ru/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3configstorage]: /ru-ru/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
[tut4]: /ru-ru/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[queuehowto]: /ru-ru/develop/net/how-to-guides/queue-service/

[blobhowto]: /ru-ru/develop/net/how-to-guides/blob-storage/
[GetMessage]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee741827.aspx
[getstartedtutorial]: /ru-ru/develop/net/tutorials/get-started
[sbqueuecomparison]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh767287.aspx
[autoscalingappblock]: /ru-ru/develop/net/how-to-guides/autoscaling/


[mtas-new-worker-role-project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
[mtas-add-new-role-project-dialog]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
[mtas-worker-b-add-existing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png
[mtas-worker-b-add-reference-menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png
[mtas-worker-b-reference-manager]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
[mtas-worker-b-manage-nuget]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
[mtas-worker-b-install-sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png


[mtas-worker-b-settings]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
[autoscaling-and-windows-azure]: http://msdn.microsoft.com/ru-ru/library/hh680945(v=PandP.50).aspx

