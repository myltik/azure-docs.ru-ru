<properties 
	pageTitle="Подключение к учетной записи служб мультимедиа с помощью .NET" 
	description="В этом разделе показано, как подключиться к службам мультимедиа с помощью .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>


# Подключение к учетной записи служб мультимедиа с помощью пакета SDK служб мультимедиа для .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-connect_programmatically.md)
- [.NET](media-services-dotnet-connect_programmatically.md)


В этом разделе описывается установка программного подключения к службам мультимедиа Microsoft Azure при программировании с помощью пакета SDK служб мультимедиа для .NET.


## Подключение к службам мультимедиа

Для программного подключения к службам мультимедиа необходимо предварительно настроить учетную запись Azure, настроить службы мультимедиа для этой учетной записи, а затем настроить проект Visual Studio для разработки с помощью пакета SDK служб мультимедиа для .NET. Дополнительную информацию см. в разделе "Настройка для разработки с помощью пакета SDK служб мультимедиа для .NET".

В конце процесса настройки учетной записи служб мультимедиа вы получите следующие значения, необходимые для подключения. Используйте их для программного подключения к службам мультимедиа.

- Имя учетной записи служб мультимедиа.

- Ключ учетной записи служб мультимедиа.

Чтобы найти эти значения, перейдите на портал управления Azure, выберите свою учетную запись служб мультимедиа и щелкните значок **УПРАВЛЕНИЕ КЛЮЧАМИ** в нижней части окна портала. Щелкните значок рядом с каждым текстовым полем, чтобы скопировать значения в буфер обмена.


## Создание экземпляра CloudMediaContext

Чтобы начать программирование для служб мультимедиа, необходимо создать экземпляр **CloudMediaContext**, представляющий контекст сервера. **CloudMediaContext** содержит ссылки на важные коллекции, в том числе на задания, ресурсы, файлы, политики доступа и указатели.

>[AZURE.NOTE] Класс **CloudMediaContext** не является потокобезопасным. Для каждого потока или набора операций необходимо создать новый CloudMediaContext.


В CloudMediaContext предусмотрено пять перегрузок конструктора. Рекомендуется использовать конструкторы, принимающие **MediaServicesCredentials** в качестве параметра. Дополнительную информацию см. ниже в разделе **Повторное использование маркеров Access Control Service**.

В следующем примере используется открытый конструктор CloudMediaContext(учетные данные MediaServicesCredentials):

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## Повторное использование маркеров службы управления доступом

В этом разделе показано, как повторно использовать маркеры службы управления доступом с помощью конструкторов CloudMediaContext, принимающих MediaServicesCredentials в качестве параметра.


[Azure Active Directory Access Control](https://msdn.microsoft.com/library/hh147631.aspx) (Access Control Service или ACS) — это облачная служба, с помощью которой можно легко провести аутентификацию и авторизацию пользователей для предоставления им доступа к их веб-приложениям. Службы мультимедиа Microsoft Azure управляют доступом к своим службам по протоколу OAuth, для которого требуется маркер ACS. Службы мультимедиа получают маркеры ACS с сервера авторизации.

При разработке с помощью пакета SDK служб мультимедиа при желании можно отказаться от использования маркеров, так как код пакета SDK обрабатывает их автоматически. Тем не менее, когда пакет SDK полностью управляет маркерами ACS, это приводит к созданию лишних запросов маркеров. На обработку запросов маркеров тратится время и ресурсы клиента и сервера. Кроме того, если их слишком много сервер ACS регулирует поток запросов. Предельное значение — 30 запросов в секунду. Дополнительную информацию см. в статье [Ограничения службы ACS](https://msdn.microsoft.com/library/gg185909.aspx).

Начиная с пакета SDK служб мультимедиа версии 3.0.0.0, маркеры ACS можно использовать повторно. Конструкторы **CloudMediaContext**, принимающие **MediaServicesCredentials** в качестве параметра, обеспечивают возможность совместного использования токенов службы ACS в нескольких контекстах. Класс MediaServicesCredentials инкапсулирует учетные данные служб мультимедиа. Если маркер ACS доступен и известен срок его действия, можно создать новый экземпляр MediaServicesCredentials с маркером и передать его в конструктор CloudMediaContext. Обратите внимание, что пакет SDK служб мультимедиа позволяет автоматически обновлять маркеры при истечении срока их действия. Как показано в примерах ниже, маркеры ACS можно использовать повторно двумя способами.

- Объект **MediaServicesCredentials** можно кэшировать в памяти (например, в переменной статического класса). Затем кэшированный объект передается конструктору CloudMediaContext. Объект MediaServicesCredentials содержит маркер ACS, который можно использовать, если он все еще действителен. Если маркер недействителен, он будет обновлен пакетом SDK для служб мультимедиа с использованием учетных данных, предоставленных конструктору MediaServicesCredentials.

	Обратите внимание, что объект **MediaServicesCredentials** получает действительный токен после вызова RefreshToken. **CloudMediaContext** вызывает метод **RefreshToken** в конструкторе. Если вы планируете сохранить значения маркера во внешнем хранилище, перед сохранением данных маркера обязательно убедитесь, что значение TokenExpiration действительно. Если оно недействительно, вызовите RefreshToken перед кэшированием.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Кроме того, можно кэшировать строку AccessToken и значения TokenExpiration. Значения можно будет использовать позже для создания нового объекта MediaServicesCredentials с кэшированными данными маркера. Это особенно удобно в сценариях, в которых маркер может безопасно использоваться несколькими процессами или компьютерами.

	В следующих фрагментах кода вызываются методы SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage и UpdateTokenDataInExternalStorageIfNeeded, которые не определены в этом примере. Эти методы можно определить для хранения, извлечения и обновления маркеров данных во внешнем хранилище.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	Используйте сохраненные значения токена для создания MediaServicesCredentials.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	Обновите копию в случае, если токен был обновлен пакетом SDK служб мультимедиа.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- При наличии несколько учетных записей служб мультимедиа (например, для распределения нагрузки или географического распределения) объекты MediaServicesCredentials можно кэшировать с помощью коллекции System.Collections.Concurrent.ConcurrentDictionary (ConcurrentDictionary — это потокобезопасная коллекция пар "ключ-значение", к которой могут обращаться несколько потоков одновременно). Затем можно использовать метод GetOrAdd для получения кэшированных учетных данных.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## Подключение к учетной записи служб мультимедиа, расположенной в северном регионе Китая

Если ваша учетная запись находится в северном регионе Китая, используйте следующий конструктор:

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Например:


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## Хранение в конфигурации значений для подключения

Настоятельно рекомендуем хранить значения для подключения, особенно конфиденциальные, например имя учетной записи и пароль, в конфигурации. Кроме того, рекомендуется шифровать конфиденциальные данные конфигурации. С помощью шифрованной файловой системы (EFS) Windows можно зашифровать весь файл конфигурации. Чтобы включить файловую систему EFS для файла, щелкните правой кнопкой мыши файл, выберите **Свойства** и включите шифрование на вкладке параметров **Дополнительно**. Кроме того, с помощью защищенной конфигурации можно создать пользовательское решение для шифрования выбранных частей файла конфигурации. См. раздел [Шифрование сведений о конфигурации с помощью защищенной конфигурации](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Следующий файл App.config содержит необходимые значения для подключения. Значения в элементе <appSettings> — это обязательные значения, получаемые в процессе настройки учетной записи служб мультимедиа.

	<configuration>
	  <appSettings>
	    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
	    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
	  </appSettings>
	</configuration>


Чтобы получить значения для подключения из конфигурации, можно использовать класс **ConfigurationManager**, а затем присвоить значения полям в коде:
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0211_2016-->