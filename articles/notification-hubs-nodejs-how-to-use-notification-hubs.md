<properties linkid="develop-nodejs-how-to-guides-service-bus-notification-hubs" urlDisplayName="Notification Hubs" pageTitle="Notification Hubs - Node.js Dev Center" metaKeywords="" description="Learn how to use Notification Hubs to send push notifications. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Notification Hubs" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Использование концентраторов уведомлений

В этом руководстве показывается, как использовать концентраторы уведомлений
в приложениях Node.js. Сценарии включают **отправку уведомлений в приложения Android, iOS, Windows Phone и Магазина Windows**. Дополнительные сведения о концентраторах уведомлений см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое концентраторы уведомлений?][Что такое концентраторы уведомлений?]
-   [Создание приложения Node.js][Создание приложения Node.js]
-   [Настройка приложения для использования концентратора уведомлений][Настройка приложения для использования концентратора уведомлений]
-   [Практическое руководство. Отправка уведомлений][Практическое руководство. Отправка уведомлений]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="hub"></span></a> Что такое концентраторы уведомлений?

Концентраторы уведомлений обеспечивают легкую в использовании, масштабируемую, многоплатформенную инфраструктуру для отправки push-уведомлений на мобильные устройства. Дополнительные сведения см. в разделе [Концентраторы уведомлений Azure][Концентраторы уведомлений Azure].

## <span id="create"></span></a>Создание приложения Node.js

Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание и развертывание приложения Node.js на веб-сайте Azure][Создание и развертывание приложения Node.js на веб-сайте Azure], [Облачная служба Node.js][Облачная служба Node.js] (с помощью Windows PowerShell) или [Веб-сайт с WebMatrix][Веб-сайт с WebMatrix].

## <span id="config"></span></a> Настройка приложения для использования концентратора уведомления

Чтобы воспользоваться концентратором уведомлений Azure, вам потребуется загрузить и задействовать
пакет Azure Node.js. В него входит набор удобных библиотек, которые
сообщаются со службами REST.

### Использование диспетчера пакета Node (NPM) для получения пакета

1.  Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.

2.  Введите в окне команд **npm install azure**, что должно привести к следующему результату:

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

3.  Можно вручную выполнить команду **ls** или **dir**, чтобы убедиться в создании папки **node\_modules**. В этой папке находится пакет **azure**, содержащий библиотеки, необходимые для доступа к концентратору уведомлений.

### Импорт модуля

С помощью текстового редактора добавьте следующее в начало файла **server.js** приложения:

    var azure = require('azure');

### Настройка подключения концентратора уведомлений Azure

Объект **NotificationHubService** позволяет работать с концентраторами уведомлений. Следующий код создает объект **NotificationHubService** для концентратора уведомлений с именем **hubname**. Добавьте его в начало файла **server.js** после инструкции импорта модуля azure:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Значение подключения **connectionstring** можно получить через портал управления Azure, выполнив следующие действия:

1.  На портале управления Azure выберите **Service Bus**, затем выберите пространство имен, содержащее концентратор уведомления.

2.  Выберите **КОНЦЕНТРАТОРЫ УВЕДОМЛЕНИЙ**, затем выберите концентратор, который хотите использовать.

3.  Выберите пункт **Просмотреть строку подключения** в разделе **Краткое описание** и скопируйте значение строки подключения.

<div class="dev-callout">
<strong>Примечание.</strong>
<p>Строку подключения можно также получить с помощью командлета <b>Get-AzureSbNamespace</b>, предоставляемого Azure PowerShell, или команды <b>azure sb namespace show</b> в программе командной строки Azure.</p>

</div>

## <span id="send"></span></a> Отправка уведомлений

Объект **NotificationHubService** предоставляет следующие экземпляры объекта для отправки уведомлений определенным устройствам и приложениям:

-   **Android** — используйте объект **GcmService**, доступный в **notificationHubService.gcm**
-   **iOS** — используйте объект **ApnsService**, доступный в **notificationHubService.apns**
-   **Windows Phone** — используйте объект **MpnsService**, доступный в **notificationHubService.mpns**
-   **Приложения магазина Windows** — используйте объект **WnsService**, доступный в **notificationHubService.wns**

### Отправка уведомлений в приложение Android

Объект **GcmService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Android. Метод **Отправить** принимает следующие параметры:

-   Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
-   Payload — полезные данные о JSON или строке сообщения.
-   Callback — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе "Полезные данные" пособия [Реализация сервера GCM][Реализация сервера GCM]

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

-   Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
-   Payload — полезные данные о JSON или строке сообщения.
-   Callback — функция обратного вызова.

Дополнительные сведения о формате полезных данных см. в разделе "Полезные данные уведомления" [Руководства по программированию локальных и push-уведомлений][Руководства по программированию локальных и push-уведомлений]

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

-   Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
-   Payload — полезные данные сообщения в формате XML.
-   TargetName — 'toast' для уведомлений во всплывающем окне, 'token' для уведомлений на плитке.
-   NotificationClass — приоритет уведомления. Допустимые значения см. в разделе "Элементы заголовка HTTP" пособия [Push-уведомления от сервера][Push-уведомления от сервера] .
-   Options — необязательные заголовки запроса.
-   Callback — функция обратного вызова.

Сведения о списке допустимых TargetName NotificationClass и параметрах заголовка см. в пособии [Push-уведомления от сервера][Push-уведомления от сервера]

В следующем коде используется экземпляр **MpnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### Отправка уведомлений в приложение Магазина Windows

Объект **WnsService** предоставляет метод **send**, который может использоваться для отправки уведомлений в приложения Магазина Windows. Метод **Отправить** принимает следующие параметры:

-   Tags — идентификатор тега. Если тег отсутствует, уведомления будут отправляться всем клиентам
-   Payload — полезные XML-данные сообщения.
-   Type — тип уведомления.
-   Options — необязательные заголовки запроса.
-   Callback — функция обратного вызова.

Перечень допустимых типов и заголовков запроса см. в учебнике [Отклик службы и заголовки запроса push-уведомления][Отклик службы и заголовки запроса push-уведомления]

В следующем коде используется экземпляр **WnsService**, предоставляемый **NotificationHubService**, для отправки всплывающего оповещения:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <span id="next"></span></a> Дальнейшие действия

Теперь, когда вы узнали основные сведения об использовании концентратора уведомлений,
используйте следующие ссылки для более подробного ознакомления.

-   См. справочник MSDN: [Концентраторы уведомлений Azure][Концентраторы уведомлений Azure]
-   Посетите репозиторий [Пакет SDK хранилища Azure для Node][Пакет SDK хранилища Azure для Node] на веб-сайте GitHub.

  [Дальнейшие действия]: #next
  [Что такое концентраторы уведомлений?]: #hub
  [Создание приложения Node.js]: #create
  [Настройка приложения для использования концентратора уведомлений]: #config
  [Практическое руководство. Отправка уведомлений]: #send
  [Концентраторы уведомлений Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/jj927170.aspx
  [Создание и развертывание приложения Node.js на веб-сайте Azure]: /ru-ru/develop/nodejs/tutorials/create-a-website-(mac)/
  [Облачная служба Node.js]: /ru-ru/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Веб-сайт с WebMatrix]: /ru-ru/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Реализация сервера GCM]: http://developer.android.com/google/gcm/server.html#payload
  [Руководства по программированию локальных и push-уведомлений]: http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html
  [Push-уведомления от сервера]: http://msdn.microsoft.com/ru-ru/library/hh221551.aspx
  [Отклик службы и заголовки запроса push-уведомления]: http://msdn.microsoft.com/ru-ru/library/windows/apps/hh465435.aspx
  [Пакет SDK хранилища Azure для Node]: https://github.com/WindowsAzure/azure-sdk-for-node
