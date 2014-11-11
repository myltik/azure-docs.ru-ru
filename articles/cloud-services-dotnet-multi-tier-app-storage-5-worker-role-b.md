<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Создание рабочей роли B (отправитель электронной почты) для приложения службы электронной почты Azure – 5 из 5.

Это пятый из пяти учебников в серии. В нем показано, как построить и развернуть образец приложения службы электронной почты Azure. Сведения о приложении и самой серии учебников см. в [первом учебнике серии][первом учебнике серии].

В этом учебнике вы узнаете следующее:

-   Как добавить в проект облачной службы рабочую роль.
-   Как опросить очередь и обработать рабочие элементы в очереди.
-   Как отправить электронную почту с помощью SendGrid.
-   Как обрабатывать запланированные завершения работы путем переопределения метода `OnStop`.
-   Как обрабатывать незапланированные завершения работы, проверяя отсутствие повторяющихся электронных сообщений.

## Разделы этого руководства

-   [Добавление проекта рабочей роли B в решение][Добавление проекта рабочей роли B в решение]
-   [Добавление ссылки на веб-проект][Добавление ссылки на веб-проект]
-   [Добавление в проект пакета NuGet SendGrid][Добавление в проект пакета NuGet SendGrid]
-   [Добавление параметров проекта][Добавление параметров проекта]
-   [Добавление кода, который выполняется при запуске рабочей роли][Добавление кода, который выполняется при запуске рабочей роли]
-   [Проверка рабочей роли B][Проверка рабочей роли B]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="addworkerrole"></a><span class="short-header">Добавление рабочей роли B</span>Добавление проекта рабочей роли B в решение

1.  В обозревателе решений щелкните правой кнопкой мыши проект облачной службы и выберите **Создать проект рабочей роли**.

    ![Меню нового проекта рабочей роли][Меню нового проекта рабочей роли]

2.  В диалоговом окне **Добавить новый проект роли** выберите **C#**, выберите **Рабочая роль**, назовите проект WorkerRoleB и нажмите **Добавить**.

    ![Диалоговое окно "Новый проект роли"][Диалоговое окно "Новый проект роли"]

## <a name="addreference"></a>Добавление ссылки на веб-проект

Ссылка на веб-проект нужна, поскольку именно там определяются классы сущностей. Классы сущностей используются в рабочей роли B для чтения и записи данных в используемых приложением таблицах Azure.

1.  Щелкните проект WorkerRoleB правой кнопкой мыши и выберите **Добавить — Ссылка**.

2.  В окне **Диспетчер ссылок** добавьте ссылку на проект MvcWebRole.

    ![Добавление ссылки на MvcWebRole][Добавление ссылки на MvcWebRole]

## <a name="addsendgrid"></a>Добавление в проект пакета NuGet SendGrid

Чтобы отправить сообщение эл. почты с помощью SendGrid, необходимо установить пакет SendGrid NuGet.

1.  В **обозревателе решений** щелкните правой кнопкой мыши проект WorkerRoleB и выберите **Управление пакетами NuGet**.

    ![Управление пакетами NuGet][Управление пакетами NuGet]

2.  В диалоговом окне **Управление пакетами NuGet** выберите вкладку **В сети**, введите sendgrid в поле поиска и нажмите клавишу ВВОД.

3.  Щелкните **Установить** для пакета **SendGrid**.

    ![Установка пакета Sendgrid][Установка пакета Sendgrid]

4.  Закройте диалоговое окно.

## <a name="addsettings"></a>Добавление параметров проекта

Как и рабочая роль А, рабочая роль B нуждается в учетных данных учетной записи хранения для работы с таблицами, запросами и BLOB-объектами. Кроме того, чтобы отправить эл. почту, рабочая роль должна иметь учетные данные для вставки в вызовы службы SendGrid. А для формирования ссылки отмены подписки, которая будет включаться в отправляемые сообщения эл. почты, рабочей роли должен быть известен URL-адрес приложения. Эти значения сохраняются в параметрах проекта.

Для учетных данных учетной записи хранения используется та же процедура, что и в [третьем учебнике][третьем учебнике].

1.  В разделе **Роли** **обозревателя решений** щелкните правой кнопкой мыши **WorkerRoleB** и выберите пункт **Свойства**.

2.  Выберите вкладку **Параметры**.

3.  Убедитесь, что в раскрывающемся списке **Конфигурации службы** выбрано значение **Все конфигурации**.

4.  Откройте вкладку **Параметры** и нажмите кнопку **Добавить параметр**.

5.  Введите "StorageConnectionString" в столбце **Имя**.

6.  Выберите элемент **Строка подключения** в раскрывающемся списке **Тип**.

7.  Нажмите кнопку с многоточием (**...**) в правом конце строки, чтобы открыть диалоговое окно **Строка подключения учетной записи хранения**.

8.  В диалоговом окне **Создать строку подсоединения хранилища** щелкните переключатель **Эмулятор хранения Azure** и нажмите кнопку **ОК**.

Затем необходимо создать и настроить три новых параметра, которые будут использоваться только рабочей ролью B.

1.  На вкладке **Параметры** окна **Свойства** щелкните пункт **Добавить параметр**, затем добавьте три новых параметра типа **String**:

    -   **Имя**: SendGridUserName. **Значение**: имя пользователя SendGrid, заданное при работе со [вторым учебником][вторым учебником].

    -   **Имя**: SendGridPassword. **Значение**: пароль SendGrid.

    -   **Имя**: AzureMailServiceURL. **Значение**: базовый URL-адрес, который будет присвоен приложению после развертывания, например: <http://sampleurl.cloudapp.net>.

    ![Новые параметры в проекте WorkerRoleB][Новые параметры в проекте WorkerRoleB]

## <a name="addcode"></a> Добавьте код, который выполняется при запуске рабочей роли

1.  В проекте рабочей роли WorkerRoleB удалите WorkerRole.cs.

2.  Добавьте в проект WorkerRoleB файл WorkerRoleB.cs из скачанного проекта.

3.  Выполните сборку решения.

    При возникновении ошибки создания у вас может оказаться несколько версий пакета NuGet службы хранилища Azure. В этом случае для решения проблемы перейдите к диспетчеру пакетов NuGet для решения, выберите «Установленные пакеты» и прокрутите вниз, чтобы посмотреть, нет ли двух экземпляров пакета службы хранилища Azure. Выберите запись для службы хранилища Azure 4.0.0 и удалите ее из проектов, в которых она установлена. Затем выберите запись для службы хранилища Azure 3.0.x и установите ее в проектах, где ее недостает. Закройте и перезапустите Visual Studio, а затем снова выполните сборку решения.

4.  Убедитесь, что проект облачной службы все еще является автоматически запускаемым пакетом решения.

### Метод Onstart

Как вы уже видели в рабочей роли A, метод `OnStart` инициализирует классы контекста, которые необходимы для работы с сущностями службы хранилища Azure. Это также гарантирует существование всех необходимых таблиц, очередей и контейнеров больших двоичных объектов, которые будут использоваться в методе `Run`.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

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

Разница по сравнению с рабочей ролью A заключается в добавлении контейнера BLOB-объектов и очереди подписок по создаваемым ресурсам, если эти компоненты еще не существуют. Для получения файлов, содержащих HTML или обычный текст для основной части сообщений эл. почты будет использован контейнер BLOB-объектов. Очередь подписки используется для отправки сообщений эл. почты с подтверждением подписки.

### Метод Run

Метод `Run` обрабатывает рабочие элементы из двух очередей: очереди, используемой для сообщений, отправленных по спискам (рабочие элементы, созданные рабочей ролью А), и очереди, используемой для сообщений подтверждения подписки (рабочие элементы, созданные методом API подписки в проекте MvcWebRole).

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

Назначением времени ожидания является уменьшение затрат на транзакции Azure, как объясняется в [предыдущем учебнике][предыдущем учебнике]

Когда элемент очереди извлекается из очереди методом [GetMessage][GetMessage], этот элемент очереди на 30 секунд становится невидимым для всех остальных рабочих и веб-ролей, имеющим доступ к очереди. Именно это гарантирует поступление любого сообщения из очереди на обработку только в один экземпляр рабочей роли. Можно явным образом задать это время *эксклюзивной аренды* (время, в течение которого элемент в очереди остается невидимым), передав параметр [времени ожидания видимости][времени ожидания видимости] в метод `GetMessage`. Если рабочая роль может потратить более 30 на обработку сообщения очереди, следует увеличить время эксклюзивной аренды для предотвращения обработки одного и того же сообщения другими экземплярами роли.

С другой стороны не следует задавать слишком большое значение времени эксклюзивной аренды. Например, если время эксклюзивной аренды равно 48 часам и ваша рабочая роль неожиданно завершает работу после вывода сообщения из очереди, другая рабочая роль не должна обрабатывать сообщение в течение следующих 48 часов. Максимальный срок эксклюзивной аренды составляет 7 дней.

Метод [GetMessages][GetMessages] (обратите внимание на s в конце имени) может использоваться для извлечения до 32 сообщений из очереди в рамках одного вызова. Каждый доступ к очереди имеет небольшую стоимость транзакции, и стоимость транзакции не отличается, возвращается ли 32 сообщения или ни одного сообщения. Следующий код извлекает до 32 сообщений в рамках одного вызова и обрабатывает их.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

При использовании `GetMessages` для удаления нескольких сообщений убедитесь, что время ожидания видимости предоставляет приложению достаточно времени для обработки всех сообщений. После истечения таймаута видимости другие экземпляры ролей могут получить доступ к сообщению, после чего первый экземпляр не сможет удалить сообщения после обработки рабочего элемента.

### Метод ProcessQueueMessage

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

Опасные сообщения — это те сообщения, которые приводят к созданию приложением исключения во время их обработки. Если сообщение было извлечено из очереди более пяти раз, предполагается, что оно не может быть обработано, затем оно удаляется из очереди для предотвращения дальнейших попыток обработки. При работе производственных приложений следует рассмотреть возможность перемещения опасного сообщения в очередь "мертвых" сообщений для дальнейшего анализа вместо удаления этого сообщения.

Код анализирует сообщение в очереди по ключу раздела и ключу строки, которые необходимы для получения строки SendEmail, а также по флагу перезагрузки.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

Если обработка этого сообщения была перезапущена после неожиданного завершения работы, код проверяет таблицу `messagearchive`, чтобы определить, было ли отправлено сообщение. Если сообщение было отправлено, код удаляет строку `SendEmail`, если она существует, и удаляет сообщение очереди.

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

### Метод SendEmailToList

Фактическая работа по отправке электронного сообщения с использованием SendGrid выполняется с помощью метода `SendEmailToList`. Если необходимо использовать службу, отличную от SendGrid, все, что нужно сделать, это изменить код в этом методе.

**Примечание.** При наличии недопустимых учетных данных в параметрах проекта вызов SendGrid завершится со сбоем, однако приложение не получит какого-либо подтверждения сбоя. При использовании SendGrid в производственном приложении рассмотрите возможность настройки отдельных учетных данных для веб-интерфейса API, чтобы избежать незаметных сбоев при изменении администратором своего пароля учетной записи SendGrid. Дополнительную информацию см. в разделе [SendGrid MultiAuth - Multiple Account Credentials][SendGrid MultiAuth - Multiple Account Credentials] (SendGrid MultiAuth: учетные данные нескольких учетных записей). Можно определить учетные данные на сайте [][]<https://sendgrid.com/credentials></a>.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
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

В методе `GetBlobText` код получает размер больших двоичных объектов, а затем использует это значение для инициализации объекта `MemoryStream`. Это повышает производительность. Если не предоставить размер, объект `MemoryStream` выделяет 256 байтов, затем при превышении объемов загрузки этого значения выделяется еще 512 байтов и т. д. Каждый раз выделяемый объем увеличивается вдвое. Для больших BLOB-объектов этот процесс будет неэффективным по сравнению с выделением нужного объема в самом начале загрузки.

### Метод ProcessSubscribeQueueMessage

Метод `Run` вызывает `ProcessSubscribeQueueMessage` после нахождения рабочего элемента в очереди подписки:

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

-   Если сообщение является «подозрительным», метод регистрирует и удаляет его.
-   Получает GUID подписчика из сообщения очереди.
-   Использует GUID для получения информации о подписчике из таблицы MailingList.
-   Отправляет сообщение с подтверждением новому подписчику.
-   Удаляет сообщение очереди.

Как и в случае сообщений эл. почты, отправленных в списки, фактическая отправка сообщений эл. почты выполняется в отдельном методе, что упрощает использование при необходимости другой службы эл. почты.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
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
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>Проверка рабочей роли B

1.  Запустите приложение, нажав клавишу F5.

2.  Перейдите на страницу **Сообщения**, чтобы просмотреть сообщение, созданное для тестирования рабочей роли А. Примерно через минуты обновите веб-страницу. Вы увидите, что строка исчезла из списка, так как была заархивирована.

3.  Проверьте папку входящих сообщений, куда должно прийти сообщение. Обратите внимание, что возможны задержки при отправке сообщений электронной почты с помощью SendGrid или при доставке сообщения клиенту эл. почты, поэтому до прихода сообщения эл. почты может пройти некоторое время. Кроме того, необходимо проверить папку нежелательной почты.

## <a name="nextsteps"></a>Дальнейшие действия

Итак, вы создали приложение службы эл. почты Azure "с нуля", которое не отличается от завершенного проекта, который вы загрузили ранее. Для развертывания его в облаке, тестирования в облаке и вывода в производственную среду можно использовать те же процедуры, с которыми вы работали во [втором учебнике][вторым учебником]

Пример, в котором показано, как использовать LINQ, см. в разделе [PhluffyFotos][PhluffyFotos].

Дополнительные сведения о хранилище Azure см. на следующем ресурсе:

-   [Essential Knowledge for Microsoft Azure Storage][Essential Knowledge for Microsoft Azure Storage] (Базовые сведения о службе хранилища Microsoft Azure) (блог Бруно Теркали)

Дополнительные сведения о службе таблицы Azure см. на следующих ресурсах:

-   [Essential Knowledge for Azure Table Storage][Essential Knowledge for Azure Table Storage] (Базовые сведения о хранилище таблиц Azure) (блог Бруно Теркали)
-   [How to get the most out of Microsoft Azure Tables][How to get the most out of Microsoft Azure Tables] (Как эффективно использовать таблицы Microsoft Azure) (блог группы, занимающейся службой хранилища Azure)
-   [How to use the Table Storage Service in .NET][How to use the Table Storage Service in .NET] (Как использовать службу хранилища таблиц в .NET)
-   [Microsoft Azure Storage Client Library 2.0 Tables Deep Dive][Microsoft Azure Storage Client Library 2.0 Tables Deep Dive] (Подробный обзор таблиц клиентской библиотеки службы хранилища Microsoft Azure 2.0) (блог группы, занимающейся службой хранилища Azure)
-   [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage][Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage] (Реальный мир: разработка стратегии масштабируемого секционирования для хранилища таблиц Azure)

Дополнительные сведения о службе очередей Azure и очередях Service Bus Azure см. на следующих ресурсах:

-   [Шаблон работы на основе очередей (создание реальных облачных приложений в Windows Azure)][Шаблон работы на основе очередей (создание реальных облачных приложений в Windows Azure)]
-   [Azure Queues and Azure Service Bus Queues - Compared and Contrasted][Azure Queues and Azure Service Bus Queues - Compared and Contrasted] (Очереди Azure и очереди Service Bus Azure — сравнение и противопоставление)
-   [Использование службы хранилища очередей в .NET][Использование службы хранилища очередей в .NET]

Дополнительные сведения о службе BLOB-объектов Azure см. на следующих ресурсах:

-   [Неструктурированное хранилище BLOB-объектов (создание реальных облачных приложений в Windows Azure)][Неструктурированное хранилище BLOB-объектов (создание реальных облачных приложений в Windows Azure)]
-   [Использование службы хранения BLOB-объектов Azure в .NET][Использование службы хранения BLOB-объектов Azure в .NET]

Чтобы получить дополнительные сведения об автомасштабировании ролей облачных служб Azure, изучите следующие ресурсы:

-   [Практическое руководство по использованию блока приложения автомасштабирования][Практическое руководство по использованию блока приложения автомасштабирования]
-   [Автоматическое масштабирование и Azure][Автоматическое масштабирование и Azure]
-   [Building Elastic, Autoscalable Solutions with Azure][Building Elastic, Autoscalable Solutions with Azure] (Создание эластичных, автомасштабируемых решений в Azure) (видео канала 9 MSDN)

## <a name="Acknowledgments"></a><span class="short-header">Благодарности</span>Благодарности

Эти учебники и примерное приложение были написаны [Риком Андерсоном][Риком Андерсоном] и Томом Дайкстрой. Мы хотели бы выразить благодарность за помощь следующим людям:

-   Барри Дорранс (Twitter [@blowdart][@blowdart])
-   [Кори Фаулер][Кори Фаулер] (Twitter [@SyntaxC4][@SyntaxC4] )
-   [Джо Джиардино][Джо Джиардино]
-   Дон Гловер
-   Яи Харидас
-   [Скотт Хантер][Скотт Хантер] (Twitter: [@coolcsh][@coolcsh])
-   [Брайан Свон][Брайан Свон]
-   [Даниэл Уэнг][Даниэл Уэнг]
-   Члены консультационного совета разработчиков, предоставившие свои комментарии:
     -   Дамир Ар
     -   Жан-Люк Бушо
     -   Карлос дос Сантос
     -   Майк Дуглас
     -   Роберт Файт
     -   Джанни Роза Галлина
     -   Фабьен Лавокат
     -   Карл Отс
     -   Карлос-Алехандро Перес
     -   Сунао Томита
     -   Перес Джонс Циса
     -   Микьель ван Отегем

  [первом учебнике серии]: /ru-ru/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Добавление проекта рабочей роли B в решение]: #addworkerrole
  [Добавление ссылки на веб-проект]: #addreference
  [Добавление в проект пакета NuGet SendGrid]: #addsendgrid
  [Добавление параметров проекта]: #addsettings
  [Добавление кода, который выполняется при запуске рабочей роли]: #addcode
  [Проверка рабочей роли B]: #testing
  [Дальнейшие действия]: #nextsteps
  [Меню нового проекта рабочей роли]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [Диалоговое окно "Новый проект роли"]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Добавление ссылки на MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [Управление пакетами NuGet]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Установка пакета Sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [третьем учебнике]: /ru-ru/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [вторым учебником]: /ru-ru/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Новые параметры в проекте WorkerRoleB]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [предыдущем учебнике]: /ru-ru/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee741827.aspx
  [времени ожидания видимости]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth - Multiple Account Credentials]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Essential Knowledge for Microsoft Azure Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Essential Knowledge for Azure Table Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [How to get the most out of Microsoft Azure Tables]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [How to use the Table Storage Service in .NET]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/table-services/
  [Microsoft Azure Storage Client Library 2.0 Tables Deep Dive]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Real World: Designing a Scalable Partitioning Strategy for Azure Table Storage]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh508997.aspx
  [Azure Queues and Azure Service Bus Queues - Compared and Contrasted]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh767287.aspx
  [Использование службы хранилища очередей в .NET]: /ru-ru/develop/net/how-to-guides/queue-service/
  [Использование службы хранения BLOB-объектов Azure в .NET]: /ru-ru/develop/net/how-to-guides/blob-storage/
  [Практическое руководство по использованию блока приложения автомасштабирования]: /ru-ru/develop/net/how-to-guides/autoscaling/
  [Автоматическое масштабирование и Azure]: http://msdn.microsoft.com/ru-ru/library/hh680945(v=PandP.50).aspx
  [Building Elastic, Autoscalable Solutions with Azure]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Риком Андерсоном]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Кори Фаулер]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Джо Джиардино]: http://blogs.msdn.com/b/windowsazurestorage/
  [Скотт Хантер]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Брайан Свон]: http://blogs.msdn.com/b/brian_swan/
  [Даниэл Уэнг]: http://blogs.msdn.com/b/daniwang/
