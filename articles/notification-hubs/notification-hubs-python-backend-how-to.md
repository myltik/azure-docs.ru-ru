<properties 
	pageTitle="Использование концентраторов уведомлений с Python" 
	description="Узнайте, как использовать центры уведомлений Azure из серверной части Python." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="python" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="yuaxu"/>

# Использование концентраторов уведомлений с Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
		
Вы можете обращаться ко всем функциям центров уведомлений из серверной части Java, PHP, Python или Ruby, используя интерфейс REST в соответствии с описанием в разделе MSDN [Интерфейсы API REST центров уведомлений](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE]Это образец эталонной реализации для отправки уведомлений в Python, который не является официально поддерживаемым пакетом SDK концентраторов уведомлений Python.

> [AZURE.NOTE]Этот образец написан с помощью Python 3.4.

В этом разделе описывается:

* Построение клиента REST для функций концентраторов уведомлений на языке Python.
* Отправка уведомлений с помощью интерфейса API REST концентратора уведомления Python. 
* Получение дампа запросов и ответов HTTP REST для отладки и образовательных целей. 

Можно воспользоваться указаниями в разделе [Приступая к работе с центрами уведомлений](notification-hubs-windows-store-dotnet-get-started.md) для выбранной мобильной платформы, которая реализует серверную часть на языке Python.

> [AZURE.NOTE]Область действия образца ограничена отправкой уведомлений. Образец не выполняет функции управления регистрацией.

## Интерфейс клиента
Интерфейс основного клиента предоставляет те же методы, что и [пакет SDK центров уведомлений для .NET](http://msdn.microsoft.com/library/jj933431.aspx). Это позволяет напрямую переводить все учебники и примеры, доступные на этом сайте в настоящий момент и пополняемые интернет-сообществом.

Полный код доступен в [примере оболочки REST Python].

Например, чтобы создать клиента, необходимо выполнить следующие действия.

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
	
Чтобы отправить всплывающее уведомление Windows:
	
	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Hello world!</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)
	
## Реализация
Если вы еще этого не делали, выполните шаги, описанные в [учебнике по началу работы], до последнего раздела, в котором вам нужно реализовать серверную часть.

Подробные сведения о реализации полноценной оболочки REST можно найти в [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). В этом разделе описана реализация основных действий на языке Python, необходимых для доступа к конечным точкам концентраторов уведомлений REST и отправки уведомлений.

1. Проанализируйте строку подключения
2. Создайте маркер проверки подлинности
3. Отправка уведомления с помощью HTTP REST API

### Проанализируйте строку подключения

Ниже показан основной класс, реализующий клиента, конструктор которого выполняет анализ строки подключения:

	class NotificationHub:
	    API_VERSION = "?api-version=2013-10"
	    DEBUG_SEND = "&test"
	
	    def __init__(self, connection_string=None, hub_name=None, debug=0):
	        self.HubName = hub_name
	        self.Debug = debug
	
	        # Parse connection string
	        parts = connection_string.split(';')
	        if len(parts) != 3:
	            raise Exception("Invalid ConnectionString.")
	
	        for part in parts:
	            if part.startswith('Endpoint'):
	                self.Endpoint = 'https' + part[11:]
	            if part.startswith('SharedAccessKeyName'):
	                self.SasKeyName = part[20:]
	            if part.startswith('SharedAccessKey'):
	                self.SasKeyValue = part[16:]


### Создание маркера безопасности
Подробные сведения о создании токенов безопасности можно найти [здесь](http://msdn.microsoft.com/library/dn495627.aspx). Для создания маркера на основе универсального кода ресурса (URI) текущего запроса и учетных данных, извлеченных из строки подключения, необходимо добавить следующие методы в класс **NotificationHub**.

	@staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### Отправка уведомления с помощью HTTP REST API
Сначала следует определить класс, представляющий уведомление.

	class Notification:
	    def __init__(self, notification_format=None, payload=None, debug=0):
	        valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
	        if not any(x in notification_format for x in valid_formats):
	            raise Exception(
	                "Invalid Notification format. " +
	                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
	
	        self.format = notification_format
	        self.payload = payload
	
	        # array with keynames for headers
	        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
	        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
	        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
	        self.headers = None

Этот класс представляет собой контейнер для собственного текста уведомления либо набор свойств (в случае шаблонного уведомления), а также набор заголовков, содержащих свойства формата (собственная платформа или шаблон) и специальные свойства платформы (например, свойство срока действия Apple и заголовки WNS).

Обратитесь к [Документации по REST API для концентраторов уведомлений](http://msdn.microsoft.com/library/dn495827.aspx) и изучите форматы специализированных платформ уведомлений, чтобы узнать обо всех доступных параметрах.

Имея в распоряжении этот класс, можно создавать методы отправки уведомлений внутри класса **NotificationHub**.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

Указанные выше методы отправляют запрос HTTP POST в конечную точку "/messages" концентратора уведомлений, с надлежащим телом и заголовками для отправки уведомления.

### Включение подробного ведения журнала с помощью свойства отладки
Если включить свойство отладки при инициализации концентратора уведомлений, в журнал будут записаны подробные сведения о дампе запроса и ответа HTTP, а также детальные выходные данные об отправке уведомлений. Недавно мы добавили такое свойство — [свойство TestSend центров уведомлений](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx), которое возвращает подробные сведения о результатах отправки уведомлений. Чтобы его использовать, выполните инициализацию следующим образом:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

В результате к URL-адресу типа HTTP запроса на отправку для концентратора уведомлений прикрепляется строка запроса "test".

##<a name="complete-tutorial"></a>Завершение работы с учебником
Теперь вы можете завершить работу с учебником по началу работы, отправив уведомление из серверной части Python.

Инициализируйте клиент концентратора уведомлений (замените строку подключения и имя концентратора в соответствии с инструкциями в [учебнике по началу работы]):

	hub = NotificationHub("myConnectionString", "myNotificationHubName")

Затем добавьте код отправки, определяемый целевой мобильной платформой. В этом примере добавлены методы более высокого уровня, чтобы включить отправку уведомлений для определенной платформы, например send\_windows\_notification (для Windows), send\_apple\_notification (для Apple) и т. д.

### Магазин Windows и Windows Phone 8.1 (без Silverlight)

	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Test</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)

### Windows Phone 8.0 и 8.1 Silverlight

	hub.send_mpns_notification(toast)

### iOS

	alert_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_apple_notification(alert_payload)

### Android
	gcm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_gcm_notification(gcm_payload)

### Kindle Fire
	adm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_adm_notification(adm_payload)

### Baidu
	baidu_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_baidu_notification(baidu_payload)

После выполнения кода Python на целевом устройстве должно отобразиться уведомление.

## Примеры:

### Включение свойства отладки
Если включить флаг отладки во время инициализации центра уведомлений (NotificationHub), вы увидите подробный дамп HTTP-запроса и HTTP-ответа наряду с данными NotificationOutcome, которые указывают, какие именно заголовки HTTP переданы в запросе и какой HTTP-ответ был получен от центра уведомлений: ![][1]

Вы увидите подробный результат концентратора уведомлений, например сведения о том,

- когда сообщение успешно отправлено в службу push-уведомлений. 
	
		<Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Если какое-либо push-уведомление не достигло адресата, отобразится следующий ответ (указывающий, что, вероятнее всего, регистрации для доставки уведомлений не были найдены из-за несоответствующих тегов).

		'<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### Рассылка всплывающих уведомлений для Windows 

Обратите внимание на заголовки, которые отправляются при рассылке всплывающих уведомлений клиенту Windows.

	hub.send_windows_notification(wns_payload)

![][2]

### Отправка уведомления с указанием тега (или регулярного выражения)

Обратите внимание на заголовок Tags HTTP, который будет добавлен к запросу HTTP (в приведенном ниже примере мы отправляем уведомление только для регистраций с полезными данными "sports").

	hub.send_windows_notification(wns_payload, "sports")

![][3]

### Отправка уведомления с указанием нескольких тегов

Обратите внимание на изменения заголовка Tags HTTP при отправке нескольких тегов.
	
	tags = {'sports', 'politics'}
	hub.send_windows_notification(wns_payload, tags)

![][4]

### Шаблон уведомления

Обратите внимание, что происходит изменение заголовка Format HTTP и текст полезных данных отправляется в составе запроса HTTP.

**Сторона клиента — зарегистрированный шаблон**

		var template =
		                @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Серверная сторона — отправка полезных данных**
		
		template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
		hub.send_template_notification(template_payload)

![][5]


## Дальнейшие действия
В этом разделе рассмотрено создание простого клиента Python REST для концентраторов уведомлений. На данном этапе можно сделать следующее.

* Скачать полный [пример оболочки REST Python], содержащий весь используемый выше код.
* Продолжить изучение функции тегов в центрах уведомлений с помощью [учебника по передаче экстренных новостей].
* Продолжить изучение функции шаблонов центров уведомлений в учебнике [по передаче локализованных экстренных новостей].

<!-- URLs -->
[пример оболочки REST Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[примере оболочки REST Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[учебнике по началу работы]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[учебника по передаче экстренных новостей]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[по передаче локализованных экстренных новостей]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 

<!---HONumber=Oct15_HO2-->