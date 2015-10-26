<properties
	pageTitle="Использование концентраторов уведомлений с Node.js"
	description="Узнайте, как использовать концентраторы уведомлений для отправки push-уведомлений из приложения Node.js."
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
	ms.date="09/24/2015"
	ms.author="wesmc"/>

# Использование концентраторов уведомлений с Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##Обзор

В этом руководстве показывается, как использовать концентраторы уведомлений в приложениях Node.js. Сценарии включают **отправку уведомлений в приложения Android, iOS, Windows Phone и Магазина Windows**. Дополнительные сведения о концентраторах уведомлений см. в разделе [Дальнейшие действия](#next).

##Что такое концентраторы уведомлений

Концентраторы уведомлений Azure — это простая в использовании масштабируемая многоплатформенная инфраструктура для отправки push-уведомлений на мобильные устройства. Дополнительные сведения см. в разделе [Концентраторы уведомлений Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure][nodejswebsite], [Облачная служба Node.js][Node.js Cloud Service] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix].

##Настройка приложения для использования центра уведомлений

Чтобы использовать концентратор уведомлений Azure, вам потребуется загрузить и задействовать пакет Azure для Node.js. В него входит набор удобных библиотек, которые сообщаются со службами REST.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  В командной строке введите команду **npm install azure**. Должен получиться вот такой результат:

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

3.  Вы можете выполнить команду **ls** или **dir** вручную, чтобы проверить создание папки **node\_modules**. В этой папке найдите пакет **azure**, который содержит библиотеки для доступа к концентратору уведомлений.

### Импорт модуля

С помощью текстового редактора добавьте в начало файла **server.js** приложения следующее:

    var azure = require('azure');

### Настройка подключения центра уведомлений Azure

Объект **NotificationHubService** позволяет работать с концентраторами уведомлений. Следующий код создает объект **NotificationHubService** для центра уведомлений с именем **hubname**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Значение подключения **connectionstring** можно получить через портал управления Azure, выполнив следующие действия:

1. На портале управления Azure выберите **Service Bus**, затем выберите пространство имен, содержащее концентратор уведомления.

2. Выберите **КОНЦЕНТРАТОРЫ УВЕДОМЛЕНИЙ**, затем выберите концентратор, который хотите использовать.

3. Выберите пункт **Просмотреть строку подключения** в разделе **Краткое описание** и скопируйте значение строки подключения.

> [AZURE.NOTE]Строку подключения можно также получить с помощью командлета **Get-AzureSbNamespace**, предоставляемого Azure PowerShell, или команды **azure sb namespace show** в интерфейсе командной строки Azure (Azure CLI).

</div>

##Отправка уведомления

Объект **NotificationHubService** предоставляет следующие экземпляры объекта для отправки уведомлений определенным устройствам и приложениям:

* **Android** — используйте объект **GcmService**, доступный в **notificationHubService.gcm**
* **iOS** — используйте объект **ApnsService**, доступный в **notificationHubService.apns**
* **Windows Phone** — используйте объект **MpnsService**, доступный в **notificationHubService.mpns**
* **Приложения магазина Windows** — используйте объект **WnsService**, доступный в **notificationHubService.wns**

### Отправка уведомлений в приложение Android

Объект **GcmService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Android. Метод **Отправить** принимает следующие параметры:

* Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload — полезные данные о JSON или строке сообщения.
* Callback — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе "Полезные данные" пособия [Реализация сервера GCM](http://developer.android.com/google/gcm/server.html#payload)

В следующем коде используется экземпляр **GcmService**, предоставляемый **NotificationHubService**, для отправки сообщения всем клиентам.

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

### Отправка уведомлений в приложение iOS

Объект **ApnsService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения iOS. Метод **Отправить** принимает следующие параметры:

* Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload — полезные данные о JSON или строке сообщения.
* Callback — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе "Полезные данные уведомления" [Руководства по программированию локальных и push-уведомлений](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)

В следующем коде используется экземпляр **ApnsService**, предоставляемый **NotificationHubService**, для отправки оповещений всем клиентам:

	var payload={
	    alert: 'Hello!'
	  };
	notificationHubService.apns.send(null, payload, function(error){
	  if(!error){
 	    // notification sent
      }
	});

### Инструкции по отправке уведомлений для Windows Phone

Объект **MpnsService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Windows Phone. Метод **Отправить** принимает следующие параметры:

* Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Полезные данные — полезные XML-данные сообщения.
* TargetName – "toast" для всплывающих уведомлений и "token" для уведомлений на плитке.
* NotificationClass — приоритет уведомления. Допустимые значения см. в разделе "Элементы заголовка HTTP" пособия [Push-уведомления от сервера](http://msdn.microsoft.com/library/hh221551.aspx) .
* Options — необязательные заголовки запроса.
* Callback — функция обратного вызова.

Сведения о списке допустимых TargetName NotificationClass и параметрах заголовка см. в пособии [Push-уведомления от сервера](http://msdn.microsoft.com/library/hh221551.aspx)

В следующем коде используется экземпляр **MpnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Отправка уведомлений в приложение Магазина Windows

Объект **WnsService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Магазина Windows. Метод **Отправить** принимает следующие параметры:

* Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload — полезные XML-данные сообщения.
* Type — тип уведомления.
* Options — необязательные заголовки запроса.
* Callback — функция обратного вызова.

Перечень допустимых типов и заголовков запроса см. в учебнике [Отклик службы и заголовки запроса push-уведомления](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx)

В следующем коде используется экземпляр **WnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

## Дальнейшие действия

Теперь, когда вы узнали основные сведения об использовании центра уведомлений,используйте следующие ссылки для более подробного ознакомления.

-   См. справочник MSDN: [Центры уведомлений Azure][].
-   Посетите репозиторий [Пакет SDK хранилища Azure для Node] на веб-сайте GitHub.

  [Пакет SDK хранилища Azure для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
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
  [Azure Management Portal]: http://manage.windowsazure.com
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

<!---HONumber=Oct15_HO3-->