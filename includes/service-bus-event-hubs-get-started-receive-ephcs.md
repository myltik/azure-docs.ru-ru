## Прием сообщений через EventProcessorHost

**EventProcessorHost** представляет собой класс .NET, который упрощает прием событий от концентраторов событий путем управления постоянными контрольными точками и одновременно принимает сообщения от этих концентраторов событий в параллельном режиме. С помощью класса **EventProcessorHost** можно разделить события между несколькими получателями даже в том случае, если они размещены в разных узлах. В этом примере показано, как использовать **EventProcessorHost** для одного получателя. В [примере обработки масштабируемого события] показано, как использовать **EventProcessorHost** для нескольких получателей.

Дополнительные сведения о шаблонах получения концентраторов событий см. в разделе [Руководство разработчика по концентраторам событий].

[EventProcessorHost] представляет собой класс .NET, который упрощает прием событий от концентраторов событий путем управления постоянными контрольными точками и одновременно принимает сообщения от этих концентраторов событий в параллельном режиме. С помощью класса [EventProcessorHost] можно разделить события между несколькими получателями даже в том случае, если они размещены в разных узлах. В этом примере показано, как использовать [EventProcessorHost] для одного получателя. В [примере обработки масштабируемого события] показано, как использовать [EventProcessorHost] для нескольких получателей.

Для использования класса [EventProcessorHost] необходимо настроить [учетную запись хранения Azure]:

1. Войдите на [портал управления Azure] и щелкните пункт **СОЗДАТЬ** в нижней части экрана.

2. Выберите **Службы данных**, **Хранилище**, **Быстрое создание**, а затем введите имя для своей учетной записи хранения. Выберите нужный регион и нажмите кнопку **Создать учетную запись хранения**.

   	![][11]

3. Выберите созданную учетную запись хранения и нажмите кнопку **Управление ключами доступа**:

   	![][12]

	Скопируйте ключ доступа для последующего использования.

4. Создайте в Visual Studio новый проект приложения C# для настольных ПК с помощью шаблона **Консольное приложение**. Присвойте проекту имя **Получатель**.

   	![][14]

5. В обозревателе решений щелкните правой кнопкой мыши проект и выберитеы **Управление пакетами NuGet**. 

	Откроется диалоговое окно **Управление пакетами NuGet**.

6. Выполните поиск `Microsoft Azure Service Bus Event Hub - EventProcessorHost` и щелкните **Установить**, после чего примите условия использования. 

	![][13]

	Будет скачана, установлена и добавлена ссылка на <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">пакет концентратора событий служебной шины Azure - EventProcessorHost NuGet</a> со всеми его зависимостями.

7. Создайте новый класс с именем **SimpleEventProcessor**, добавьте следующие инструкции в начало файла:

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	Вставьте следующий код в текст класса:

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	        
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine(string.Format("Processor Shuting Down.  Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason.ToString()));
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine(string.Format("SimpleEventProcessor initialize.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset));
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	                
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5)) 
                { 
                    await context.CheckpointAsync(); 
                    this.checkpointStopWatch.Restart(); 
                } 
	        }
	    }

	Этот класс будет вызываться из **EventProcessorHost** для обработки событий, полученных от концентратора событий. Обратите внимание, что в классе `SimpleEventProcessor` используется контрольный таймер для периодического вызова метода контрольных точек в контексте **EventProcessorHost**. Это гарантирует, что в случае перезагрузки получателя будет потеряно не более пяти минут обработки.

8. В классе **Program** добавьте следующие инструкции `using` в начало:

		using Microsoft.ServiceBus.Messaging;
		using System.Threading.Tasks;
	
	Затем добавьте следующий код в метод **Main**, подставив имя концентратора событий и строку подключения, учетную запись хранилища и ключ, который был скопирован в предыдущих разделах:

		string eventHubConnectionString = "{event hub connection string}";
        string eventHubName = "{event hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                    storageAccountName, storageAccountKey);

        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();
            
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

> [AZURE.NOTE] В данном учебнике используется один экземпляр [EventProcessorHost]. Для увеличения пропускной способности рекомендуется создать несколько экземпляров [EventProcessorHost], как показано в [примере обработки масштабируемого события]. В этом случае различные экземпляры автоматически координируются друг с другом для распределения нагрузки полученных событий. Если каждый из нескольких получателей должен обрабатывать *all* события, то необходимо использовать понятие **ConsumerGroup**. При получении события от разных компьютеров может оказаться полезным указать имена экземпляров [EventProcessorHost] в компьютерах (или ролях), где они развернуты. Дополнительные сведения об этих темах см. в разделах [Общие сведения о концентраторах событий] и [Руководство по программированию концентраторов событий].

<!-- Links -->
[Общие сведения о концентраторах событий]: http://msdn.microsoft.com/ru-ru/library/azure/dn821413.aspx
[Пример обработки масштабируемых событий]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Учетная запись хранения Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/ru-ru/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[Руководство разработчика по концентраторам событий]: http://msdn.microsoft.com/ru-ru/library/azure/dn789972.aspx
<!--HONumber=42-->
