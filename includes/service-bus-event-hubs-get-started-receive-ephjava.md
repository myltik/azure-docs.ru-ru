## Прием сообщений через EventProcessorHost в Java

EventProcessorHost — это класс Java, который упрощает прием событий от концентраторов событий путем управления постоянными контрольными точками и параллельно принимает сообщения от этих концентраторов событий. С помощью класса EventProcessorHost можно разделить события между несколькими получателями, даже если они размещены на разных узлах. В этом примере показано, как использовать EventProcessorHost для одного получателя.

###Создайте учетную запись хранения.

Для использования класса EventProcessorHost необходимо настроить [учетную запись хранения Azure][].

1. Войдите на [классический портал Azure][] и в нижней части экрана нажмите кнопку **Создать**.

2. Выберите **Службы данных**, **Хранилище**, **Быстрое создание**, а затем введите имя для своей учетной записи хранения. Выберите нужный регион и нажмите кнопку **Создать учетную запись хранения**.

    ![][11]

3. Выберите созданную учетную запись хранения и нажмите кнопку **Управление ключами доступа**.

    ![][12]

    Скопируйте первичный ключ доступа, чтобы использовать позже в этом руководстве.

###Создание проекта Java с помощью EventProcessorHost

Клиентская библиотека Java для концентраторов событий доступна для использования в проектах Maven из [центрального репозитория Maven][Maven Package]. Ссылаться на нее можно, используя следующее объявление зависимости в файле проекта Maven:

``` XML
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs</artifactId>
	<version>{VERSION}</version>
</dependency>
<dependency>
	<groupId>com.microsoft.azure</groupId>
	<artifactId>azure-eventhubs-eph</artifactId>
	<version>{VERSION}</version>
</dependency>
```
 
Для различных типов сред сборки можно явно получить последние выпущенные JAR-файлы из [центрального репозитория Maven][Maven Package] или из [точки распространения выпусков на GitHub](https://github.com/Azure/azure-event-hubs/releases).

1. Следующий пример сначала создает новый проект Maven для приложения консоли или оболочки в избранной среде разработки Java. Класс будет называться ```ErrorNotificationHandler```.

	``` Java
	import java.util.function.Consumer;
	import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

	public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
	{
		@Override
		public void accept(ExceptionReceivedEventArgs t)
		{
			System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
		}
	}
	```

2. Создайте новый класс с именем ```EventProcessor``` с помощью следующего кода:

	```Java
	import com.microsoft.azure.eventhubs.EventData;
	import com.microsoft.azure.eventprocessorhost.CloseReason;
	import com.microsoft.azure.eventprocessorhost.IEventProcessor;
	import com.microsoft.azure.eventprocessorhost.PartitionContext;

	public class EventProcessor implements IEventProcessor
	{
		private int checkpointBatchingCount = 0;

		@Override
		public void onOpen(PartitionContext context) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
		}

		@Override
		public void onClose(PartitionContext context, CloseReason reason) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
		}
		
		@Override
		public void onError(PartitionContext context, Throwable error)
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
		}

		@Override
		public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
		{
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
			int messageCount = 0;
			for (EventData data : messages)
			{
				System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
						data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
				messageCount++;
				
				this.checkpointBatchingCount++;
				if ((checkpointBatchingCount % 5) == 0)
				{
					System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
						data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
					context.checkpoint(data);
				}
			}
			System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
		}
	}
	```

3. Создайте последний класс с именем ```EventProcessorSample``` с помощью следующего кода:

	```Java
	import com.microsoft.azure.eventprocessorhost.*;
	import com.microsoft.azure.servicebus.ConnectionStringBuilder;
	import com.microsoft.azure.eventhubs.EventData;

	public class EventProcessorSample
	{
		public static void main(String args[])
		{
			final String consumerGroupName = "$Default";
			final String namespaceName = "----ServiceBusNamespaceName-----";
			final String eventHubName = "----EventHubName-----";
			final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
			final String sasKey = "---SharedAccessSignatureKey----";

			final String storageAccountName = "---StorageAccountName----";
			final String storageAccountKey = "---StorageAccountKey----";
			final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
			
			ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
			
			EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
			
			System.out.println("Registering host named " + host.getHostName());
			EventProcessorOptions options = new EventProcessorOptions();
			options.setExceptionNotification(new ErrorNotificationHandler());
			try
			{
				host.registerEventProcessor(EventProcessor.class, options).get();
			}
			catch (Exception e)
			{
				System.out.print("Failure while registering: ");
				if (e instanceof ExecutionException)
				{
					Throwable inner = e.getCause();
					System.out.println(inner.toString());
				}
				else
				{
					System.out.println(e.toString());
				}
			}

			System.out.println("Press enter to stop");
			try
			{
				System.in.read();
				host.unregisterEventProcessor();
				
				System.out.println("Calling forceExecutorShutdown");
				EventProcessorHost.forceExecutorShutdown(120);
			}
			catch(Exception e)
			{
				System.out.println(e.toString());
				e.printStackTrace();
			}
			
			System.out.println("End of sample");
		}
	}
	```

4. Замените значения в следующих полях значениями, которые использовались при создании концентратора событий и учетной записи хранения.

	``` Java
	final String namespaceName = "----ServiceBusNamespaceName-----";
	final String eventHubName = "----EventHubName-----";

	final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
	final String sasKey = "---SharedAccessSignatureKey----";

	final String storageAccountName = "---StorageAccountName----"
	final String storageAccountKey = "---StorageAccountKey----";
	```

> [AZURE.NOTE] В данном учебнике используется один экземпляр EventProcessorHost. Чтобы увеличить пропускную способность, рекомендуется запустить несколько экземпляров EventProcessorHost. В этом случае различные экземпляры автоматически координируются друг с другом для распределения нагрузки полученных событий. Если каждый из нескольких получателей должен обрабатывать *все* события, то необходимо использовать понятие **ConsumerGroup**. При получении события от разных компьютеров может оказаться полезным указать имена экземпляров EventProcessorHost в компьютерах (или ролях), где они развернуты.

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[учетную запись хранения Azure]: ../storage/storage-create-storage-account.md
[классический портал Azure]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

<!---HONumber=AcomDC_0928_2016-->