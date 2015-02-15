#####Создание очереди
Объект **CloudQueueClient** позволяет ссылаться на объекты очередей. Следующий код создает объект **CloudQueueClient**. Для всего кода в этой статье используется строка подключения к хранилищу, сохраненная в конфигурации службы приложения Azure. Существуют также другие способы создания объекта **CloudStorageAccount**. Дополнительные сведения см. в документации по [CloudStorageAccount](http://msdn.microsoft.com/ru-ru/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount").

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

С помощью объекта **queueClient** получите ссылку на очередь, которую необходимо использовать. Код ссылается на очередь под названием myqueue. Если очередь с таким именем отсутствует, она будет создана.

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	queue.CreateIfNotExists();

**ПРИМЕЧАНИЕ.** Используйте этот блок кода перед кодом из следующих разделов.

#####Вставка сообщения в очередь
Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект **CloudQueueMessage**. Затем вызовите метод addMessage(). Для создания объекта **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив байтов. Ниже приведен код, который создает очередь (если она не существует) и вставляет сообщение 'Hello, World'.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	queue.AddMessage(message);

#####Просмотр следующего сообщения
Можно просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод PeekMessage().

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	Console.WriteLine(peekedMessage.AsString);

#####Удаление следующего сообщения
Ваш код может удалить сообщение из очереди в два этапа. 


1. Вызовите метод GetMessage(), чтобы получить следующее сообщение в очереди. Сообщение, возвращаемое методом GetMessage(), становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. 
2.	Чтобы завершить удаление сообщения из очереди, необходимо вызвать метод DeleteMessage(). 

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод DeleteMessage() сразу после обработки сообщения.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = queue.GetMessage();

	// Process the message in less than 30 seconds, and then delete the message.
	queue.DeleteMessage(retrievedMessage);

[Дополнительные сведения о службе хранилища Azure](http://azure.microsoft.com/documentation/services/storage/)
См. также раздел [Общие сведения о ресурсах службы хранилища в обозревателе серверов](http://msdn.microsoft.com/ru-ru/library/azure/ff683677.aspx).


<!--HONumber=42-->
