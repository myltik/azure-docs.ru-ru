<properties 
	pageTitle="Центры уведомлений - Центр разработчиков Node.js" 
	description="Узнайте, как использовать центры уведомлений для отправки push-уведомлений. Примеры кода написаны для приложений Node.js." 
	services="notification-hubs" 
	documentationCenter="nodejs" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="glenga"/>






# Использование центров уведомлений

В этом руководстве показывается, как использовать центры уведомлений в приложениях Node.js. Сценарии включают **отправку уведомлений в приложения Android, iOS, Windows Phone и Магазина Windows**. Дополнительную информацию о центрах уведомлений см. в разделе [Дальнейшие действия](#next) .

## Оглавление

-   [Что такое центры уведомлений?](#hub)
-   [Создание приложения Node.js](#create)
-   [Настройка приложения для использования центра уведомлений](#config)
-   [Практическое руководство. Отправка уведомлений](#send)
-   [Дальнейшие действия](#next)

##<a id="hub"></a> Что такое центры уведомлений?

Центры уведомлений обеспечивают легкую в использовании, масштабируемую, многоплатформенную инфраструктуру для отправки push-уведомлений на мобильные устройства. Дополнительную информацию см. в разделе [Центры уведомлений Azure](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx).

##<a id="create"></a> Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure][nodejswebsite], [Облачная служба Node.js][Node.js Cloud Service] (с использованием Windows PowerShell) или [Веб-сайт с WebMatrix].

##<a id="config"></a> Настройка приложения для использования центра уведомлений

Чтобы использовать центр уведомлений Azure, необходимо скачать и применить
Пакет Azure Node.js. В него входит набор удобных библиотек, которые сообщаются со службами REST.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите к папке, в которой вы создали пример приложения.

2.  В окне команд введите **npm install azure**, в результате чего
    на выходе должно получиться следующее:

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

3.  Можно вручную выполнить команду **ls** или **dir**, чтобы убедиться в создании папки **node\_modules**. В этой папке находится пакет **azure**, содержащий библиотеки, необходимые для доступа к центру уведомлений.

### Импорт модуля

С помощью текстового редактора добавьте следующее в начало файла **server.js** приложения:

    var azure = require('azure');

### Настройка подключения центра уведомлений Azure

Объект **NotificationHubService** позволяет работать с центрами уведомлений. Следующий код создает объект **NotificationHubService** для центра уведомлений с именем **hubname**. Добавьте его в начало файла **server.js** после инструкции импорта модуля azure.

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Значение подключения **connectionstring** можно получить через портал управления Azure, выполнив следующие действия:

1. На портале управления Azure выберите **Service Bus**, затем выберите пространство имен, содержащее центр уведомления.

2. Выберите **ЦЕНТРЫ УВЕДОМЛЕНИЙ** и затем выберите центр, который хотите использовать.

3. Выберите пункт **Просмотреть строку подключения** в разделе **Краткое описание** и скопируйте значение строки подключения.

> [AZURE.NOTE] Строку подключения можно также получить с помощью командлета **Get-AzureSbNamespace**, предоставляемого Azure PowerShell, или команды **azure sb namespace show** в программе командной строки Azure.

</div>

##<a id="send"></a> Отправка уведомления

Объект **NotificationHubService** предоставляет следующие экземпляры объекта для отправки уведомлений определенным устройствам и приложениям:

* **Android** - используйте объект **GcmService**, доступный в **notificationHubService.gcm**
* **iOS** - используйте объект **ApnsService**, доступный в **notificationHubService.apns**
* **Windows Phone** - используйте объект **MpnsService**, доступный в **notificationHubService.mpns**
* **Приложения Магазина Windows** - используйте объект **WnsService**, доступный в **notificationHubService.wns**

### Отправка уведомлений в приложение Android

Объект **GcmService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Android. Метод **Отправить** принимает следующие параметры:

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные данные о JSON или строке сообщения.
* Callback - функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе "Полезные данные" пособия [Реализация сервера GCM](http://developer.android.com/google/gcm/server.html#payload).

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

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные данные о JSON или строке сообщения.
* Callback - функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе "Полезные данные уведомления" [Руководства по программированию локальных и push-уведомлений](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html).

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

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные данные сообщения в формате XML.
* TargetName -  'toast' для всплывающих уведомлений и  'token' для уведомлений на плитке.
* NotificationClass - приоритет уведомления. Допустимые значения см. в разделе "Элементы заголовка HTTP" пособия [Push-уведомления от сервера](http://msdn.microsoft.com/library/hh221551.aspx).
* Options - необязательные заголовки запроса.
* Callback - функция обратного вызова.

Сведения о списке допустимых TargetName NotificationClass и параметрах заголовка см. в пособии [Push-уведомления от сервера](http://msdn.microsoft.com/library/hh221551.aspx).

В следующем коде используется экземпляр **MpnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

	var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
	notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
	  if(!error){
	    //notification sent
	  }
	});

### Отправка уведомлений в приложение Магазина Windows

Объект **WnsService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Магазина Windows.  Метод **Отправить** принимает следующие параметры:

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные XML-данные сообщения.
* Type - тип уведомления.
* Options - необязательные заголовки запроса.
* Callback - функция обратного вызова.

Перечень допустимых типов и заголовков запроса см. в учебнике [Отклик службы и заголовки запроса push-уведомления](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

В следующем коде используется экземпляр **WnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

##<a id="next"></a> Дальнейшие действия

Теперь, когда вы узнали основные сведения об использовании центра уведомлений,используйте следующие ссылки для более подробного ознакомления.

-   См. справочник MSDN: [Центры уведомлений Azure][].
-   Посетите репозиторий [пакета SDK Azure для Node] на портале GitHub.

  [SDK Azure для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Дальнейшие действия]: #nextsteps
  [Информация о разделах и подписках Service Bus]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения Node.js]: #Create_a_Nodejs_Application
  [Настройка приложения для использования Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Практическое руководство. Создание разделов]: #How_to_Create_a_Topic
  [Практическое руководство. Создание подписок]: #How_to_Create_Subscriptions
  [Практическое руководство. Отправка сообщений в раздел]: #How_to_Send_Messages_to_a_Topic
  [Практическое руководство. Получение сообщений из подписки]: #How_to_Receive_Messages_from_a_Subscription
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Практическое руководство. Удаление разделов и подписок]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Основные понятия раздела]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Портал управления Azure]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Центры уведомлений Service Bus в системе Azure]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Веб-сайт с WebMatrix]: /ru-ru/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
[Предыдущая версия портала управления]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js с хранилищем]: /ru-ru/develop/nodejs/tutorials/web-app-with-storage/
  [Веб-приложение Node.js с хранилищем]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=45--> 
