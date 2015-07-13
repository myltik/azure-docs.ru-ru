

В своем серверном приложении вам необходимо переключиться на отправку уведомлений шаблона вместо собственных полезных данных. Это упростит серверный код, так как будет не нужно отправлять несколько наборов полезных данных для разных платформ.

При отправке уведомлений шаблона необходимо предоставить набор свойств, в нашем случае мы отправим набор свойств, содержащий локализованную версию текущих новостей, например:

	{
		"News_English": "World News in English!",
    	"News_French": "World News in French!",
    	"News_Mandarin": "World News in Mandarin!"
	}


В этом разделе показано, как отправлять уведомления двумя способами:

- — используя консольное приложение
- — используя скрипт мобильных служб

Включенный код выполняет широковещательную рассылку как для магазина Windows, так и для устройств iOS, поскольку серверный компонент может выполнять широковещательную рассылку на любое из поддерживаемых устройств.



## Для отправки уведомлений с помощью консольного приложения C# ##

Мы просто изменим ваш метод *SendNotificationAsync* путем отправки одного уведомления шаблона.

	var hub = NotificationHubClient.CreateClientFromConnectionString("<connection string>", "<hub name>");
    var notification = new Dictionary<string, string>() {
							{"News_English", "World News in English!"},
                            {"News_French", "World News in French!"},
                            {"News_Mandarin", "World News in Mandarin!"}};
    await hub.SendTemplateNotificationAsync(notification, "World");

Обратите внимание, что этот простой вызов будет доставлять правильно локализованную часть новостей на **все** ваши устройства, независимо от платформы, так как концентратор уведомлений создает и доставляет правильные собственные полезные данные на все устройств, подписанные на определенный тег.

### Мобильные службы

В планировщике мобильных служб замените ваш скрипт следующими данными:

	var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
			"News_English": "World News in English!",
			"News_French": "World News in French!",
			"News_Mandarin", "World News in Mandarin!"
	}
	notificationHubService.send('World', notification, function(error) {
		if (!error) {
			console.warn("Notification successful");
		}
	});
	
Обратите внимание, что в данном случае нет необходимости отправлять несколько уведомлений для различных языков и платформ.

<!---HONumber=July15_HO1-->