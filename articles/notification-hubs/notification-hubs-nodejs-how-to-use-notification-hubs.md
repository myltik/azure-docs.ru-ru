<properties
	pageTitle="Отправка push-уведомлений с помощью Центров уведомлений Azure и Node.js"
	description="Узнайте, как использовать концентраторы уведомлений для отправки push-уведомлений из приложения Node.js."
    keywords="push-уведомление, push-уведомления, push-уведомления node.js, push-уведомления ios"
	services="notification-hubs"
	documentationCenter="nodejs"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/08/2015"
	ms.author="wesmc"/>

# Отправка push-уведомлений с помощью Центров уведомлений Azure и Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##Обзор

> [AZURE.IMPORTANT] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

В этом руководстве показано, как отправлять push-уведомления с помощью Центров уведомлений Azure непосредственно из приложения Node.js.

Описанные сценарии включают отправку push-уведомлений в приложения на следующих платформах:

* Android
* iOS
* Windows Phone
* Универсальные приложения Windows 

Дополнительные сведения о концентраторах уведомлений см. в разделе [Дальнейшие действия](#next).

##Что такое концентраторы уведомлений

Центры уведомлений Azure — это простая в использовании масштабируемая многоплатформенная инфраструктура для отправки push-уведомлений на мобильные устройства. Подробные сведения об инфраструктуре служб приведены на странице [Центры уведомлений Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Создание приложения Node.js

Первый шаг этого руководства представляет собой создание пустого приложения Node.js. Указания по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure][nodejswebsite], [Облачная служба Node.js][Node.js Cloud Service] (с использованием Windows PowerShell) или [Веб-сайт с WebMatrix].

##Настройка приложения для использования центров уведомлений

Для использования центров уведомлений Azure необходимо загрузить и использовать [пакет Azure](https://www.npmjs.com/package/azure) для Node.js, который включает встроенный набор вспомогательных библиотек, взаимодействующих со службами push-уведомлений REST.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  В командной строке, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Linux) перейдите к папке, в которой вы создали пустое приложение.

2.  Введите в командной строке **npm install azure**, после этого вы должны получить результат, подобный следующему (номера версий могут отличаться):

        azure@0.7.0 node_modules\azure
		|-- dateformat@1.0.2-1.2.3
		|-- xmlbuilder@0.4.2
		|-- node-uuid@1.2.0
		|-- mime@1.2.9
		|-- underscore@1.4.4
		|-- validator@0.4.28
		|-- tunnel@0.0.2
		|-- wns@0.5.3
		|-- xml2js@0.2.6 (sax@0.4.2)
		|-- request@2.16.6 (forever-agent@0.2.0, aws-sign@0.2.0, tunnel-agent@0.2.0, oauth-sign@0.2.0, json-stringify-safe@3.0.0, cookie-jar@0.2.0, node-uuid@1.4.0, qs@0.5.5, hawk@0.10.2, form-data@0.0.7)

3.  Вы можете выполнить команду **ls** или **dir** вручную, чтобы проверить создание папки **node\_modules**. В этой папке найдите пакет **azure**, который содержит библиотеки для доступа к центрам уведомлений.

>[AZURE.NOTE] Дополнительные сведения об установке NPM доступны в официальном [блоге NPM](http://blog.npmjs.org/post/85484771375/how-to-install-npm).

### Импорт модуля

С помощью текстового редактора добавьте в начало файла **server.js** приложения следующее:

    var azure = require('azure');

### Настройка подключения центра уведомлений Azure

Объект **NotificationHubService** позволяет работать с концентраторами уведомлений. Следующий код создает объект **NotificationHubService** для центра уведомлений с именем **hubname**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Значение строки подключения **connectionstring** можно получить с помощью [портала Azure], выполнив следующие действия:

1. На панели навигации слева щелкните **Обзор**.

2. Выберите **Центры уведомлений**, затем центр, который хотите использовать. Если вам нужна помощью в создании центра уведомлений, обратитесь к руководству [Приступая к работе с Магазином Windows](notification-hubs-windows-store-dotnet-get-started.md).

3. Выберите элемент **Параметры**.

4. Щелкните **Политики доступа**. Вы увидите строки подключения как для общего, так и для полного доступа.

![Портал Azure — центры уведомлений](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Строку подключения можно также получить с помощью командлета **Get-AzureSbNamespace**, предоставляемого [Azure PowerShell](../powershell-install-configure.md), или команды **azure sb namespace show** в [интерфейсе командной строки Azure](../xplat-cli-install.md).

##Общая архитектура

Объект **NotificationHubService** предоставляет следующие экземпляры объекта для отправки push-уведомлений определенным устройствам и приложениям:

* **Android** — используйте объект **GcmService**, доступный в **notificationHubService.gcm**
* **iOS** — используйте объект **ApnsService**, доступный в **notificationHubService.apns**
* **Windows Phone** — используйте объект **MpnsService**, доступный в **notificationHubService.mpns**
* **Универсальные приложения Windows** — используйте объект **WnsService**, который доступен в **notificationHubService.wns**

### Практическое руководство. Отправка push-уведомлений в приложения Android

Объект **GcmService** предоставляет метод **send**, который может использоваться для отправки push-уведомлений в приложения Android. Метод **Отправить** принимает следующие параметры:

* **Tags** — идентификатор тега. Если тег отсутствует, уведомление будет отправляться всем клиентам.
* **Payload** — полезные данные из JSON сообщения или необработанной строки.
* **Callback** — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе **Полезные данные** документа [Реализация сервера GCM](http://developer.android.com/google/gcm/server.html#payload).

В следующем коде используется экземпляр **GcmService**, предоставляемый **NotificationHubService**, для отправки push-уведомления всем зарегистрированным клиентам.

	var payload = {
	  data: {
	    msg: 'Hello!'
	  }
	};
	notificationHubService.gcm.send(null, payload, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Практическое руководство. Отправка push-уведомлений в приложения iOS

Как и с описанными выше приложениями Android, объект **ApnsService** предоставляет метод **send**, который может использоваться для отправки push-уведомлений в приложения iOS. Метод **Отправить** принимает следующие параметры:

* **Tags** — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам.
* **Payload** — полезные данные из JSON сообщения или строки.
* **Callback** — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе **Полезные данные уведомления** [Руководства по программированию локальных и push-уведомлений](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

В следующем коде используется экземпляр **ApnsService**, предоставляемый **NotificationHubService**, для отправки оповещений всем клиентам:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Практическое руководство. Отправка push-уведомлений в приложения Windows Phone

Объект **MpnsService** предоставляет метод **send**, который может использоваться для отправки push-уведомлений в приложения Windows Phone. Метод **Отправить** принимает следующие параметры:

* **Tags** — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам.
* **Payload** — полезные XML-данные сообщения.
* **TargetName** — `toast` для всплывающих уведомлений и `token` для уведомлений на элементах.
* **NotificationClass** — приоритет уведомления. Допустимые значения см. в разделе **Элементы заголовка HTTP** документа [Push-уведомления от сервера](http://msdn.microsoft.com/library/hh221551.aspx).
* **Options** — необязательные заголовки запроса.
* **Callback** — функция обратного вызова.

Сведения о списке допустимых параметрах **TargetName**, **NotificationClass** и параметрах заголовка см. в на странице [Push-уведомления от сервера](http://msdn.microsoft.com/library/hh221551.aspx).

В следующем примере кода используется экземпляр **MpnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего push-уведомления:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Практическое руководство. Отправка push-уведомлений в приложения универсальной платформы Windows (UWP)

Объект **WnsService** предоставляет метод **send**, который может использоваться для отправки push-уведомлений в приложения универсальной платформы Windows. Метод **Отправить** принимает следующие параметры:

* **Tags** — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем зарегистрированным клиентам.
* **Payload** — полезные XML-данные сообщения.
* **Type** — тип уведомления.
* **Options** — необязательные заголовки запроса.
* **Callback** — функция обратного вызова.

Перечень допустимых типов и заголовков запроса см. в учебнике [Заголовки запросов и ответов службы push-уведомлений](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

В следующем примере кода используется экземпляр **WnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего push-уведомления приложению UWP:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Дальнейшие действия

Примеры фрагментов выше позволяют легко создать инфраструктуру службы для отправки push-уведомлений на широкий спектр устройств. Теперь, когда вы познакомились с основами использования центров уведомлений с Node.js, используйте следующие ссылки для получения дополнительных сведений о том, как можно дальше расширить эти возможности.

-   См. раздел [Центры уведомлений Azure](https://msdn.microsoft.com/library/azure/jj927170.aspx) в справочнике MSDN.
-   Дополнительные примеры и сведения о реализации доступны в репозитории [Пакет Azure SDK для Node] в GitHub.

  [Пакет Azure SDK для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Веб-сайт с WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [портала Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0309_2016-->