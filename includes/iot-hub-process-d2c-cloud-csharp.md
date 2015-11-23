## Обработка сообщений, отправляемых с устройства в облако

В этом разделе вы создадите консольное приложение Windows, которое обрабатывает сообщения, передаваемые с устройства в облако, из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий][Event Hubs Overview] конечную точку для считывания сообщений, передаваемых с устройства в облако. В данном руководстве для обработки этих сообщений в консольном приложении используется [EventProcessorHost]. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий].

Основная сложность при реализации надежного хранилища сообщений точки данных или перенаправления интерактивных сообщений заключается в том, что контроль хода выполнения обработки событий концентраторов событий основан на потребителе сообщений. Кроме того, для повышения пропускной способности при считывании данных из концентраторов событий контрольные точки должны фиксироваться большими пакетами, создавая возможность повторной обработки большого количества сообщений. В этом руководстве показано, как синхронизировать операции записи в службу хранилища Azure и периоды дедупликации служебной шины с помощью контрольных точек узла обработчика событий.

Для обеспечения надежной записи сообщений в службу хранилища Azure мы используем функцию фиксации отдельных блоков компонента [блочных больших двоичных объектов][Azure Block Blobs]. Обработчик событий накапливает сообщения в памяти, пока не настанет время установки контрольной точки (например, когда буфер накопленных сообщений становится больше максимального размера блока в 4 МБ или после окончания периода дедупликации сервисной шины). Затем перед установкой контрольной точки в большом двоичном объекте фиксируется новый блок.

Обработчик событий использует смещения сообщений концентраторов событий в качестве идентификаторов блоков. Это позволяет выполнить проверку дедупликации перед фиксацией нового блока в хранилище, устраняя вероятность сбоя в промежутке между фиксацией блока и контрольной точкой.

> [AZURE.NOTE]В этом руководстве используется одна учетная запись хранения для записи всех сообщений, полученных из центра IoT. Ознакомьтесь с разделом [Рекомендации по обеспечению масштабируемости службы хранилища Azure], чтобы определить, требуется ли в вашем решении использовать несколько учетных записей хранения Azure.

Во избежание повторений при обработке интерактивных сообщений мы используем функцию дедупликации служебной шины. Помечая каждое интерактивное сообщение с помощью уникального `MessageId`, служебная шина предотвращает доставку получателям двух сообщений с одним и тем же `MessageId` в течение периода дедупликации. Такая дедупликация совместно с семантикой завершения каждого сообщения, предоставляемой очередями служебной шины, значительно упрощают надежную обработку интерактивных сообщений.

Чтобы убедиться в отсутствии повторной отправки сообщений вне периода дедупликации, мы синхронизируем механизм контрольных точек узла обработчика событий с периодом дедупликации очереди служебной шины. Это достигается путем хотя бы однократной принудительной установки контрольной точки при каждом окончании периода (в этом руководстве этот период составляет 1 час).

> [AZURE.NOTE]В данном руководстве для обработки всех интерактивных сообщений, полученных из центра IoT, используется одна секционированная очередь служебной шины. См. [документацию по служебной шине] для получения дополнительных сведений об использовании очередей служебной шины в зависимости от потребностей решения.

### Подготовка учетной записи хранения Azure и очереди служебной шины
Для использования класса [EventProcessorHost] необходимо настроить учетную запись хранения Azure. Можно использовать существующую учетную запись или создать новую, следуя инструкциям из раздела [Сведения о службе хранилища Azure]. Запишите строку подключения учетной записи хранения.

Потребуется также реализовать в очереди служебной шины надежную обработку интерактивных сообщений. Можно программно создать очередь с периодом дедупликации в 1 час, как описано в разделе [Использование очередей служебной шины][Service Bus Queue], или воспользоваться [порталом Azure], выполнив следующие действия:

1. Щелкните элемент **СОЗДАТЬ** в левом нижнем углу, затем элементы **Службы приложений**, **Служебная шина**, **Очередь**, **Пользовательская**, выберите имя **d2ctutorial**, а затем укажите период дедупликации величиной в 1 час.

    ![][30]

2. Щелкните имя очереди (**d2ctutorial**), элемент **Настройка** и создайте две политики общего доступа: одну **send** с разрешениями **Отправка** и одну **listen** с разрешениями **Прослушивание**. По завершении нажмите кнопку **Сохранить** в нижней части окна.

    ![][31]

3. Щелкните элемент **Панель мониторинга** в верхней части окна, затем элемент **Сведения о подключении** в нижней части окна и запишите две строки подключения.

    ![][32]

### Создание обработчика событий

1. В текущем решении Visual Studio щелкните **Файл -> Новый -> Проект**, чтобы создать новый проект классического приложения Visual C# с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **ProcessDeviceToCloudMessages**.

    ![][10]

2. В обозревателе решений щелкните правой кнопкой мыши проект и выберите пункт **Управление пакетами NuGet**.

    Откроется диалоговое окно **Управление пакетами NuGet**.

3. Выполните поиск `WindowsAzure.ServiceBus` и щелкните **Установить**, после чего примите условия использования.

    После этого будут выполнены скачивание, установка и добавление ссылки на [пакет NuGet служебной шины Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) со всеми ее зависимостями.

4. Выполните поиск `Microsoft Azure Service Bus Event Hub - EventProcessorHost` и щелкните **Установить**, после чего примите условия использования.

    Будет скачана, установлена и добавлена ссылка на [пакет NuGet EventProcessorHost в концентраторе событий служебной шины Azure](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) со всеми его зависимостями.

5. Щелкните правой кнопкой мыши проект **ProcessDeviceToCloudMessages**, выберите команду **Добавить**, а затем щелкните элемент **Класс**. Присвойте классу имя **StoreEventProcessor**, а затем нажмите кнопку **ОК**, чтобы создать класс.

6. Добавьте в начало файла StoreEventProcessor.cs следующие инструкции:

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    Затем замените следующий код текстом класса:

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }

                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    Этот класс будет вызываться **EventProcessorHost** для обработки сообщений, передаваемых с устройства в облако, которые были получены из центра IoT, он реализует основную логику для надежного хранения сообщений в контейнере больших двоичных объектов и пересылки интерактивной сообщений в очередь служебной шины. Метод `OpenAsync()` инициализировал переменную `currentBlockInitOffset`, которая отслеживает текущее смещение первого сообщения, считываемого этим обработчиком событий (помните, что каждый обработчик отвечает за один раздел).

    Метод `ProcessEventsAsync()` принимает пакет сообщений из центра IoT, которые обрабатываются следующим образом. Интерактивные сообщения отправляются в очередь служебной шины, а сообщения точек данных добавляются к буферу памяти с именем `toAppend`. Если буфер памяти достигает предельного размера блока (т. е. 4 МБ) или заканчивается период дедупликации служебной шины с момента установки последней контрольной точки (1 час в этом руководстве), активируется контрольная точка.

    Сначала метод `AppendAndCheckpoint()` создает blockId для добавляемого блока. Поскольку служба хранилища Azure требует, чтобы все идентификаторы блоков имели одинаковую длину, смещение дополняется начальными нулями (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Если блок с таким идентификатором уже находится в большом двоичном объекте, метод перезаписывает его текущим блоком.

> [AZURE.NOTE]В целях упрощения кода в этом руководстве для хранения сообщений используется по одному файлу большого двоичного объекта для каждого раздела. В реальном решении реализуется смещение файлов путем создания дополнительных файлов при достижении определенного размера (помните, что размер большого двоичного объекта Azure не может превышать 195 ГБ) или через заданный промежуток времени (например, `fileName_{partitionId}_{date-time}`).

7. В классе **Program** добавьте следующие инструкции `using` в начало.

        using Microsoft.ServiceBus.Messaging;

    Измените метод **Main** на класс **Program**, как показано ниже, подставив строку подключения **iothubowner** центра IoT (из руководства [Приступая к работе с центром IoT]), строку подключения хранилища и строку подключения служебной шины с разрешениями **Отправка** для очереди с именем **d2ctutorial**:

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]В целях упрощения в данном руководстве используется один экземпляр [EventProcessorHost]. Дополнительные сведения об обработке сообщений, передаваемых с устройства в облако, см. в [руководстве по программированию концентраторов событий] и руководстве [Обработка сообщений, передаваемых с устройства в облако].

## Прием интерактивных сообщений
В этом разделе вам предстоит написать консольное приложение Windows, которое принимает интерактивные сообщения из очереди служебной шины. Дополнительные сведения о том, как разработать решение с использованием служебной шины, см. в разделе [Создание многоуровневых приложений с помощью служебной шины][].

1. В текущем решении Visual Studio создайте новый проект классического приложения Visual C# с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **ProcessD2cInteractiveMessages**.

2. В обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Управление пакетами NuGet для решения...**.

    Откроется окно «Управление пакетами NuGet».

3. Выполните поиск `WindowsAzure.Service Bus` и щелкните **Установить**, после чего примите условия использования.

    После этого будут выполнены скачивание, установка и добавление ссылки на [служебную шину Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) со всеми ее зависимостями.

4. Добавьте следующий оператор `using` в начало файла **Program.cs**.

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Наконец, добавьте следующие строки в метод **Main**, подставив строку подключения с разрешениями **Прослушивание** для очереди с именем **d2ctutorial**:

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[Сведения о службе хранилища Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Приступая к работе с концентраторами событий]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Рекомендации по обеспечению масштабируемости службы хранилища Azure]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[документацию по служебной шине]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[руководстве по программированию концентраторов событий]: ../event-hubs/event-hubs-programming-guide.md

[Azure preview portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[порталом Azure]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Создание многоуровневых приложений с помощью служебной шины]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md


<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=Nov15_HO3-->