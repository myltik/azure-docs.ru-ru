<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Концентраторы уведомлений" pageTitle="Концентраторы уведомлений Service Bus — Центр разработчиков Node.js" metaKeywords="" description="Использование концентраторов уведомлений Service Bus для отправки push-уведомлений. Примеры кода написаны для приложений Node.js." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="Использование концентраторов уведомлений Service Bus" authors="larryfr" solutions="" manager="" editor="" />





# Использование концентраторов уведомлений Service Bus

В этом руководстве показывается, как использовать концентраторы уведомлений Service Bus в приложениях Node.js. Сценарии включают **отправку уведомлений в приложения Android, iOS, Windows Phone и Магазина Windows**. Дополнительные сведения о концентраторах уведомлений см. в разделе [Дальнейшие действия](#next).

## Оглавление

-   [Что такое концентраторы уведомлений Service Bus?](#hub)
-   [Создание приложения Node.js](#create)
-   [Настройка приложения для использования Service Bus](#config)
-   [Практическое руководство. Рассылка уведомлений](#send)
-   [Следующие шаги](#next)

##<a id="hub"></a> Что такое концентраторы уведомлений Service Bus? 

Концентраторы уведомлений Service Bus системы Azure обеспечивают легкую в использовании, масштабируемую, многоплатформенную инфраструктуру для отправки push-уведомлений на мобильные устройства. Дополнительные сведения см. в разделе [Концентраторы уведомлений Service Bus в системе Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927170.aspx).

##<a id="create"></a> Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix].

##<a id="config"></a> Настройка приложения для использования Service Bus

Чтобы использовать Azure Service Bus, необходимо загрузить и использовать пакет azure Node.js. Он содержит набор удобных библиотек, взаимодействующих со службами REST Service Bus.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  Введите в командной строке **npm install azure**, что должно привести к следующему результату:

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

3.  Можно вручную выполнить команду **ls** или **dir**, чтобы убедиться в создании папки
    **node\_modules**. В этой папке находится пакет
    **azure**, содержащий библиотеки, необходимые для доступа
    к концентраторам уведомлений Service Bus.

### Импорт модуля

С помощью текстового редактора добавьте следующее в начало файла **server.js** приложения:

    var azure = require('azure');

### Настройка подключения к Service Bus Azure

Объект **NotificationHubService** позволяет работать с концентраторами уведомлений. Следующий
код создает объект **NotificationHubService** для концентратора уведомлений с именем **hubname**. Добавьте его в начало файла **server.js** после инструкции импорта модуля azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Значение подключения **connectionstring** можно получить через портал управления Azure, выполнив следующие действия:

1. В портале управления Azure выберите **Service Bus**, затем выберите пространство имен, содержащее концентратор уведомления.

2. Выберите **КОНЦЕНТРАТОРЫ УВЕДОМЛЕНИЙ** и затем выберите концентратор, который хотите использовать.

3. Выберите пункт **Просмотреть строку подключения** в разделе **Краткое описание** и скопируйте значение строки подключения.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Строку подключения можно также получить с помощью командлета <b>Get-AzureSbNamespace</b>, предоставляемого Azure PowerShell, или команды <b>azure sb namespace show</b> в программе командной строки Azure.</p>

</div>

##<a id="send"></a> Отправка уведомлений

Объект **NotificationHubService** предоставляет следующие экземпляры объекта для отправки уведомлений определенным устройствам и приложениям:

* **Android** -  использует объект **GcmService**, который доступен в **notificationHubService.gcm**.
* **iOS** -  использует объект **ApnsService**, который доступен в **notificationHubService.apns**.
* **Windows Phone** -  использует объект **MpnsService**, который доступен в **notificationHubService.mpns**.
* **Приложения Магазина Windows** -  используют объект **WnsService**, который доступен в **notificationHubService.wns**.

### Отправка уведомлений в приложение Android

Объект **GcmService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Android. Метод **Отправить** принимает следующие параметры:

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные данные о JSON или строке сообщения
* Callback - функция обратного вызова

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

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные данные о JSON или строке сообщения
* Callback - функция обратного вызова

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

* Tags - идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
* Payload - полезные XML-данные сообщения
* TargetName - 'toast' для уведомлений во всплывающем окне. 'token' для уведомлений на плитке.
* NotificationClass - приоритет уведомления. Допустимые значения см. в разделе "Элементы заголовка HTTP" пособия [Push-уведомления от сервера](http://msdn.microsoft.com/ru-ru/library/hh221551.aspx) .
* Options - необязательные заголовки запроса
* Callback - функция обратного вызова

Сведения о списке допустимых TargetName NotificationClass и параметрах заголовка см. в пособии [Push-уведомления от сервера](http://msdn.microsoft.com/ru-ru/library/hh221551.aspx)

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
* Payload - полезные XML-данные сообщения
* Type - тип уведомления
* Options - необязательные заголовки запроса
* Callback - функция обратного вызова

Перечень допустимых типов и заголовков запроса см. в учебнике [Отклик службы и заголовки запроса push-уведомления](http://msdn.microsoft.com/ru-ru/library/windows/apps/hh465435.aspx)

В следующем коде используется экземпляр **WnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

	var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
	notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
	  if(!error){
 	    // notification sent
	  }
	});

##<a id="next"></a> Дальнейшие действия

Вы узнали основные сведения о разделах Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   Справочник MSDN: [Концентраторы уведомлений Service Bus в системе Azure][]
-   Посетите репозиторий [Azure SDK для Node] на веб-сайте GitHub.

  [Azure SDK для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Следующие шаги]: #nextsteps
  [Что такое разделы и подписки Service Bus?]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения Node.js]: #Create_a_Nodejs_Application
  [Настройка приложения для использования Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Практическое руководство. Создание раздела]: #How_to_Create_a_Topic
  [Практическое руководство. Создание подписок]: #How_to_Create_Subscriptions
  [Практическое руководство. Отправка сообщений в раздел]: #How_to_Send_Messages_to_a_Topic
  [Практическое руководство. Получение сообщений от подписки]: #How_to_Receive_Messages_from_a_Subscription
  [Практическое руководство. Обработка сбоев приложения и нечитаемых сообщений]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Практическое руководство. Удаление разделов и подписок]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Основные понятия раздела]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Портал управления Azure]: http://manage.windowsazure.com
  [образ]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Концентраторы уведомлений Service Bus в системе Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Веб-сайт с WebMatrix]: /ru-ru/develop/nodejs/tutorials/web-site-with-webmatrix/
  : /ru-ru/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
[Предыдущая версия портала управления]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js с использованием хранилища]: /ru-ru/develop/nodejs/tutorials/web-app-with-storage/
  [Веб-приложение Node.js с использованием хранилища]: /ru-ru/develop/nodejs/tutorials/web-site-with-storage/

