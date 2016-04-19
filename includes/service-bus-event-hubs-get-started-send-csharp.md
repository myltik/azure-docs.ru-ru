## Отправка сообщений концентраторам событий

В этом разделе создается консольное приложение Windows для отправки событий в концентратор событий.

1. Создайте в Visual Studio новый проект Visual C# для классических приложений с помощью шаблона проекта **Консольное приложение**. Назовите проект **Sender**.

   ![][7]

2. В обозревателе решений щелкните правой кнопкой мыши решение и выберите **Управление пакетами NuGet для решения**. 

3. Щелкните вкладку **Обзор** и выполните поиск `Microsoft Azure Service Bus`. Убедитесь, что имя проекта (**Sender**) указано в поле **Версии**. Щелкните **Установить**, после чего примите условия использования.

	![][8]

	После этого будут выполнены скачивание, установка и добавление ссылки на <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">пакет NuGet библиотеки служебной шины Azure</a>.

4. Добавьте следующие инструкции `using` в начало файла **Program.cs**:

	```
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

5. Добавьте следующие поля в класс **Program**, заменяя значения именем концентратора событий, созданного в предыдущем разделе, и строку подключения с правами на **отправку** (строка подключения **SendRule**). Не забудьте удалить суффикс `EntityPath` в строке подключения:

	```
	static string eventHubName = "{Event Hub name}";
	static string connectionString = "{send connection string}";
	```

6. Добавьте следующий метод в класс **Program**.

	```
	static void SendingRandomMessages()
	{
	    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
	    while (true)
	    {
	        try
	        {
	            var message = Guid.NewGuid().ToString();
	            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
	            eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
	        }
	        catch (Exception exception)
	        {
	            Console.ForegroundColor = ConsoleColor.Red;
	            Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
	            Console.ResetColor();
	        }

	        Thread.Sleep(200);
	    }
	}
	```

	Этот метод постоянно отправляет события в концентратор событий с задержкой 200 мс.

7. Наконец, добавьте следующие строки в метод **Main**:

	```
	Console.WriteLine("Press Ctrl-C to stop the sender process");
	Console.WriteLine("Press Enter to start now");
	Console.ReadLine();
	SendingRandomMessages();
	```


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png

<!---HONumber=AcomDC_0413_2016-->