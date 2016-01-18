## Обработка сообщений, отправляемых с устройства в облако

В этом разделе вы создадите консольное приложение Windows, которое обрабатывает сообщения, отправляемые с устройства в облако из центра IoT. Центр IoT предоставляет совместимую с [концентраторами событий] конечную точку, с помощью которой приложение считывает сообщения, отправляемые с устройства в облако. В этом руководстве для обработки этих сообщений в консольном приложении используется класс [EventProcessorHost]. Дополнительные сведения о способах обработки сообщений из концентраторов событий см. в руководстве [Приступая к работе с концентраторами событий].

Основная сложность, с которой вы сталкиваетесь при реализации надежного хранилища сообщений точки данных или перенаправления интерактивных сообщений, заключается в том, что контроль хода выполнения обработки событий концентраторов событий основан на потребителе сообщений. Кроме того, чтобы добиться высокой пропускной способности, при считывании из концентраторов событий необходимо установить конечные точки в больших пакетах. Таким образом создается возможность повторной обработки большого количества сообщений в случае возникновения сбоя и возвращения к предыдущей контрольной точке. Из этого руководства вы узнаете, как синхронизировать операции записи в службу хранилища Azure и периоды дедупликации служебной шины с помощью контрольных точек **EventProcessorHost**.

Для обеспечения надежной записи сообщений в службу хранилища Azure в примере используется функция фиксации отдельных блоков [блочных BLOB-объектов][Azure Block Blobs]. Обработчик событий накапливает сообщения в памяти, пока не настанет время установки контрольной точки, например, когда буфер накопленных сообщений превышает максимальный размер блока в 4 МБ или после окончания периода дедупликации служебной шины. Затем перед установкой контрольной точки в большом двоичном объекте с помощью кода фиксируется новый блок.

Обработчик событий использует смещения сообщений концентраторов событий в качестве идентификаторов блоков. Это позволяет обработчику выполнить проверку дедупликации перед фиксацией нового блока в хранилище, устраняя вероятность сбоя в промежутке между фиксацией блока и контрольной точкой.

> [AZURE.NOTE]В этом руководстве используется одна учетная запись хранения для записи всех сообщений, полученных из центра IoT. Чтобы определить, требуется ли в вашем решении использовать несколько учетных записей хранения Azure, см. статью [Рекомендации по обеспечению масштабируемости службы хранилища Azure].

Во избежание повторений при обработке интерактивных сообщений в приложении используется функция дедупликации служебной шины. Виртуальное устройство помечает каждое интерактивное сообщение с помощью уникального **MessageId**. Благодаря этому служебная шина предотвращает доставку получателям двух сообщений с одним и тем же **MessageId** в течение указанного периода дедупликации. Такая дедупликация совместно с семантикой завершения каждого сообщения, предоставляемой очередями служебной шины, значительно упрощают реализацию надежной обработки интерактивных сообщений.

Чтобы предотвратить повторную отправку сообщений вне периода дедупликации, код синхронизирует механизм контрольных точек **EventProcessorHost** с периодом дедупликации очереди служебной шины. Это достигается путем хотя бы однократной принудительной установки контрольной точки при каждом окончании периода дедупликации (в этом руководстве этот период составляет один час).

> [AZURE.NOTE]В данном руководстве для обработки всех интерактивных сообщений, полученных из центра IoT, используется одна секционированная очередь служебной шины. Дополнительные сведения об использовании очередей служебной шины в соответствии с требованиями масштабируемости решения см. в [документации по служебной шине].

### Подготовка учетной записи хранения Azure и очереди служебной шины
Чтобы использовать класс [EventProcessorHost], вам понадобится учетная запись службы хранилища Azure для записи сведений о контрольных точках с помощью **EventProcessorHost**. Можно использовать существующую учетную запись хранения или создать новую, следуя инструкциям в статье [О службе хранилища Azure]. Запишите строку подключения учетной записи хранения.

Требуется также реализовать надежную обработку интерактивных сообщений в очереди служебной шины. Можно программно создать очередь с периодом дедупликации в 1 час, как описано в статье [Как использовать очереди служебной шины][Service Bus Queue], или воспользоваться [порталом Azure], где нужно сделать следующее:

1. В левом нижнем углу последовательно выберите **СОЗДАТЬ**, **Службы приложений**, **Служебная шина**, **Очередь**, **Настраиваемое создание**, введите имя **d2ctutorial**, выберите регион, воспользуйтесь существующим пространством имен или создайте новое, а затем на следующей странице установите флажок **Включить обнаружение повторений** и для параметра **Временное окно журнала обнаружения повторений** установите значение один час. Щелкните значок галочки, чтобы сохранить настройки очереди.

    ![][30]

2. В списке очередей служебной шины щелкните **d2ctutorial**, а затем нажмите кнопку **Настроить**. Создайте две политики общего доступа: **send** с разрешениями **Отправка** и **listen** с разрешениями **Прослушивание**. По завершении нажмите кнопку **Сохранить** в нижней части окна.

    ![][31]

3. Выберите **Панель мониторинга** в верхней части окна, а затем щелкните **Сведения о подключении** в нижней части окна и запишите две строки подключения.

    ![][32]

### Создание обработчика событий

1. В текущем решении Visual Studio последовательно выберите **Файл**, **Добавить**, **Новый проект**, чтобы создать новый проект Visual C# Windows с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **ProcessDeviceToCloudMessages**.

    ![][10]

2. В обозревателе решений щелкните правой кнопкой мыши проект **ProcessDeviceToCloudMessages** и выберите **Управление пакетами NuGet**. Откроется диалоговое окно **Диспетчер пакетов NuGet**.

3. Найдите **WindowsAzure.ServiceBus**, щелкните **Установить** и примите условия использования. После этого будут выполнены скачивание, установка и добавление ссылки на [пакет NuGet служебной шины Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) со всеми ее зависимостями.

4. Найдите **Концентратор событий служебной шины Microsoft Azure — EventProcessorHost**, а затем щелкните **Установить** и примите условия использования. Будет скачана, установлена и добавлена ссылка на [пакет концентратора событий служебной шины Azure — EventProcessorHost NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) со всеми его зависимостями.

5. Щелкните правой кнопкой мыши проект **ProcessDeviceToCloudMessages**, выберите команду **Добавить**, а затем щелкните элемент **Класс**. Присвойте классу имя **StoreEventProcessor**, а затем нажмите кнопку **ОК**, чтобы создать класс.

6. Добавьте в начало файла StoreEventProcessor.cs следующие инструкции:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Замените следующий код текстом класса:

    ```
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
        blobContainer.CreateIfNotExists();
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
    ```

    Класс **EventProcessorHost** вызывается для обработки сообщений из центра IoT, отправляемых с устройства в облако. В этом классе код реализует логику для надежного хранения сообщений в контейнере больших двоичных объектов и перенаправления интерактивных сообщений в очередь служебной шины. Метод **OpenAsync** инициализирует переменную **currentBlockInitOffset**, отслеживающую текущее смещение первого сообщения, которое считал этот обработчик событий. Помните, что каждый процессор отвечает за один раздел.
    
    Метод **ProcessEventsAsync** получает пакет сообщений из центра IoT и обрабатывает их следующим образом: интерактивные сообщения отправляются в очередь служебной шины, а сообщения точки данных добавляются в буфер памяти под названием **toAppend**. Если буфер памяти достигает предельного размера блока в 4 МБ или заканчивается период дедупликации служебной шины с момента установки последней контрольной точки (один час в этом руководстве), активируется контрольная точка.

    Сначала метод **AppendAndCheckpoint** создает blockId для добавляемого блока. В службе хранилища Azure все идентификаторы блоков должны быть одной длины, поэтому смещение дополняется начальными нулями — `currentBlockInitOffset.ToString("0000000000000000000000000")`. Если блок с таким идентификатором уже находится в большом двоичном объекте, метод перезаписывает его текущим содержимым буфера.

    > [AZURE.NOTE]В целях упрощения кода в этом руководстве для хранения сообщений используется по одному файлу большого двоичного объекта для каждого раздела. В реальном решении реализуется смещение файлов путем создания дополнительных файлов при достижении определенного размера (помните, что размер блочного BLOB-объекта Azure не может превышать 195 ГБ) или через заданный промежуток времени.

8. В классе **Program** добавьте следующие инструкции **using** в начало:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Измените метод **Main** в классе **Program**, как показано ниже, подставив строку подключения **iothubowner** центра IoT (из руководства [Начало работы с центром IoT]), строку подключения хранилища и строку подключения служебной шины с разрешениями **Отправка** для очереди с именем **d2ctutorial**:

    ```
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
    ```
    
    > [AZURE.NOTE]В целях упрощения в данном руководстве используется один экземпляр класса [EventProcessorHost]. Дополнительные сведения см. в статье [Руководство по программированию концентраторов событий].

## Прием интерактивных сообщений
В этом разделе вам предстоит написать консольное приложение Windows, которое принимает интерактивные сообщения из очереди служебной шины. Дополнительные сведения о том, как разработать решение с использованием служебной шины, см. в разделе [Создание многоуровневых приложений с помощью служебной шины][].

1. В текущем решении Visual Studio создайте новый проект Visual C# Windows с помощью шаблона проекта **Консольное приложение**. Присвойте проекту имя **ProcessD2CInteractiveMessages**.

2. В обозревателе решений щелкните правой кнопкой мыши проект **ProcessD2CInteractiveMessages** и выберите **Управление пакетами NuGet**. Отобразится окно **Диспетчер пакетов NuGet**.

3. Найдите **WindowsAzure.Service Bus**, щелкните **Установить** и примите условия использования. После этого будут выполнены скачивание, установка и добавление ссылки на [служебную шину Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus) со всеми ее зависимостями.

4. Добавьте следующую инструкцию **using** в начало файла **Program.cs**:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Наконец, добавьте следующие строки в метод **Main**, подставив строку подключения с разрешениями **Прослушивание** для очереди с именем **d2ctutorial**:

    ```
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
    ```

<!-- Links -->
[О службе хранилища Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Приступая к работе с концентраторами событий]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Рекомендации по обеспечению масштабируемости службы хранилища Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[концентраторами событий]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Руководство по программированию концентраторов событий]: ../event-hubs/event-hubs-programming-guide.md
[Azure preview portal]: https://portal.azure.com/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[порталом Azure]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[Создание многоуровневых приложений с помощью служебной шины]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[Начало работы с центром IoT]: iot-hub-csharp-csharp-getstarted.md
[документации по служебной шине]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0107_2016-->