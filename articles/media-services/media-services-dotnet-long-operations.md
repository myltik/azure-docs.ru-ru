<properties 
	pageTitle="Опрос долговременных операций" 
	description="В этом разделе содержатся сведения об опросе долговременных операций." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Доставка динамической потоковой передачи с помощью служб мультимедиа Azure

##Обзор

Службы Microsoft Azure Media Services предоставляют интерфейсы API, которые отправляют запросы в службы мультимедиа на запуск операций (например, создание, запуск, остановка или удаление канала). Эти операции являются долговременными.

Пакет SDK .NET служб мультимедиа предоставляет интерфейсы API, которые отправляют запрос и ожидают завершения операции (на внутреннем уровне API выполняют опрос о ходе операции через определенные промежутки времени). Например, при вызове channel.Start() метод возвращается после запуска канала. Кроме того, вы можете использовать асинхронную версию: await channel.StartAsync() (сведения об асинхронном шаблоне на основе задач см. в разделе [TAP](https://msdn.microsoft.com/library/hh873175(v=vs.110).aspx)). API, которые отправляют запрос на операцию, а затем запрашивают состояние до завершения операции, называются методами опроса. Такие методы (особенно асинхронная версия) рекомендуются для полнофункциональных клиентских приложений и (или) служб с отслеживанием состояния.

Существуют сценарии, когда приложение не может ждать долговременного HTTP-запроса и хочет запросить ход выполнения операции вручную. Типичным примером может служить браузер, взаимодействующий со службой без учета состояния: когда браузер запрашивает создание канала, веб-служба инициирует долговременную операцию и возвращает идентификатор операции в браузер. Затем браузер может запросить в веб-службе состояние операции на основе идентификатора Пакет SDK .NET служб мультимедиа предоставляет интерфейсы API, полезные для этого сценария. Такие API называются неопросными методами. "Неопросные методы" именуются по такому шаблону: Send*OperationName*Operation (например, SendCreateOperation). Методы Send*OperationName*Operation возвращают объект **IOperation**. Полученный объект содержит сведения, которые можно использовать для отслеживания операции. Методы Send*OperationName*OperationAsync возвращают **Task<IOperation>**.

В настоящее время неопросные методы поддерживаются такими классами: **Channel**, **StreamingEndpoint** и **Program**.

Чтобы опросить состояние операции, используйте метод **GetOperation** в классе **OperationBaseCollection**. Для проверки состояния операции используйте такие интервалы: для операций **Channel** и **StreamingEndpoint** — 30 секунд, для операций **Program** — 10 секунд.


##Пример

Пример ниже определяет класс, который называется **ChannelOperations**. Это определение класса может быть стартовой точкой для определения класса веб-службы. Для простоты в примерах ниже используются синхронные версии методов.

В примере также показано, как клиент может использовать этот класс.

###Определение класса ChannelOperations

	/// <summary> 
	/// The ChannelOperations class only implements 
	/// the Channel’s creation operation. 
	/// </summary> 
	public class ChannelOperations
	{
	    // Read values from the App.config file.
	    private static readonly string _mediaServicesAccountName =
	        ConfigurationManager.AppSettings["MediaServicesAccountName"];
	    private static readonly string _mediaServicesAccountKey =
	        ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	    // Field for service context.
	    private static CloudMediaContext _context = null;
	    private static MediaServicesCredentials _cachedCredentials = null;
	
	    public ChannelOperations()
	    {
	            _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	            _context = new CloudMediaContext(_cachedCredentials);    }
	
	    /// <summary>  
	    /// Initiates the creation of a new channel.  
	    /// </summary>  
	    /// <param name="channelName">Name to be given to the new channel</param>  
	    /// <returns>  
	    /// Operation Id for the long running operation being executed by Media Services. 
	    /// Use this operation Id to poll for the channel creation status. 
	    /// </returns> 
	    public string StartChannelCreation(string channelName)
	    {
	        var operation = _context.Channels.SendCreateOperation(
	            new ChannelCreationOptions
	            {
	                Name = channelName,
	                Input = CreateChannelInput(),
	                Preview = CreateChannelPreview(),
	                Output = CreateChannelOutput()
	            });
	
	        return operation.Id;
	    }
	
	    /// <summary> 
	    /// Checks if the operation has been completed. 
	    /// If the operation succeeded, the created channel Id is returned in the out parameter.
	    /// </summary> 
	    /// <param name="operationId">The operation Id.</param> 
	    /// <param name="channel">
	    /// If the operation succeeded, 
	    /// the created channel Id is returned in the out parameter.</param>
	    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
	    public bool IsCompleted(string operationId, out string channelId)
	    {
	        IOperation operation = _context.Operations.GetOperation(operationId);
	        bool completed = false;
	
	        channelId = null;
	
	        switch (operation.State)
	        {
	            case OperationState.Failed:
	                // Handle the failure. 
	                // For example, throw an exception. 
					// Use the following information in the exception: operationId, operation.ErrorMessage.
	                break;
	            case OperationState.Succeeded:
	                completed = true;
	                channelId = operation.TargetEntityId;
	                break;
	            case OperationState.InProgress:
	                completed = false;
	                break;
	        }
	        return completed;
	    }
	
	
	    private static ChannelInput CreateChannelInput()
	    {
	        return new ChannelInput
	        {
	            StreamingProtocol = StreamingProtocol.RTMP,
	            AccessControl = new ChannelAccessControl
	            {
	                IPAllowList = new List<IPRange>
	                {
	                    new IPRange
	                    {
	                        Name = "TestChannelInput001",
	                        Address = IPAddress.Parse("0.0.0.0"),
	                        SubnetPrefixLength = 0
	                    }
	                }
	            }
	        };
	    }
	
	    private static ChannelPreview CreateChannelPreview()
	    {
	        return new ChannelPreview
	        {
	            AccessControl = new ChannelAccessControl
	            {
	                IPAllowList = new List<IPRange>
	                {
	                    new IPRange
	                    {
	                        Name = "TestChannelPreview001",
	                        Address = IPAddress.Parse("0.0.0.0"),
	                        SubnetPrefixLength = 0
	                    }
	                }
	            }
	        };
	    }
	
	    private static ChannelOutput CreateChannelOutput()
	    {
	        return new ChannelOutput
	        {
	            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
	        };
	    }
	}

###Код клиента

	ChannelOperations channelOperations = new ChannelOperations();
	string opId = channelOperations.StartChannelCreation("MyChannel001");
	
	string channelId = null;
	bool isCompleted = false;
	
	while (isCompleted == false)
	{
	    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
	    isCompleted = channelOperations.IsCompleted(opId, out channelId);
	}
	
	// If we got here, we should have the newly created channel id.
	Console.WriteLine(channelId);
 


##Схемы обучения работе со службами мультимедиа

Схемы обучения AMS можно просмотреть здесь:

- [Рабочий процесс для потоковой передачи в реальном времени в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Рабочий процесс для потоковой передачи по запросу в службах AMS](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Sept15_HO2-->