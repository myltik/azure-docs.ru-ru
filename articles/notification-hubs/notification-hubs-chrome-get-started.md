<properties
	pageTitle="Приступая к работе с Центрами уведомлений Azure для приложений Chrome | Microsoft Azure"
	description="Из этого руководства вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложения Chrome."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-chrome"
	ms.devlang="JavaScript"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="wesmc"/>

# Приступая к работе с Центрами уведомлений для приложений Chrome

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

В этом разделе показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение Chrome.

Одно из основных преимуществ использования уведомлений Chrome заключается в том, что они отображаются в браузере Google Chrome. При этом в нем не должно быть запущено или открыто приложение Chrome (хотя сам браузер Chrome должен быть запущен). В окне уведомлений Chrome можно увидеть обобщенное представление всех уведомлений.

>[AZURE.NOTE] Это не стандартные push-уведомления браузера. Они используются только для приложений Chrome. Дополнительные сведения см. в статье [Обзор приложений Chrome]. Приложения Chrome ранее назывались "упакованными приложениями". Они отличаются от более простых "размещаемых приложений". Информацию о различиях между этими приложениями см. в статье [Устанавливаемые веб-приложения]. С помощью платформы Apache Cordova приложения Chrome могут работать также на мобильных устройствах под управлением Android и iOS. Дополнительную информацию см. в статье [Приложения Chrome на мобильных устройствах].

В этом руководстве мы создадим приложение Chrome, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). Ознакомившись с этим руководством, вы сможете рассылать push-уведомления всем пользователям Chrome, у которых установлено это приложение Chrome.

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Включение Google Cloud Messaging](#register)
* [Настройка концентратора уведомлений](#configure-hub)
* [Подключение приложения Chrome к концентратору уведомлений](#connect-app)
* [Отправка уведомления в приложение Chrome](#send)
* [Дальнейшие действия](#next-steps)

В этом учебнике описывается простой сценарий вещания с использованием Центров уведомлений. Так как служба [Google Cloud Messaging для Chrome] устарела и одна версия GCM теперь поддерживает как устройства Android, так и экземпляры Chrome, настройка GCM и Центров уведомлений Azure аналогична их настройке для ОС Android.

Обязательно прочитайте руководства в разделе "Дальнейшие действия", чтобы научиться использовать концентраторы уведомлений для охвата определенных пользователей и групп устройств.

>[AZURE.NOTE] Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fru-RU%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

##<a id="register"></a>Включение Google Cloud Messaging

1. Перейдите на веб-сайт [консоли Google Cloud], войдите с помощью своих учетных данных Google и нажмите кнопку **Create Project** (Создать проект). Введите соответствующее имя в поле **Project Name** (Имя проекта) и нажмите кнопку **Create** (Создать).

   	![][1]

2. Запишите номер только что созданного проекта из поля **Project Number** (Номер проекта) на странице **Projects** (Проекты). Этот номер следует использовать как **идентификатор отправителя GCM** в приложении Chrome для регистрации в GCM.

   	![][2]

3. В области слева щелкните **APIs & auth** (API и проверка подлинности), прокрутите вниз и щелкните выключатель, чтобы включить **Google Cloud Messaging для Android**. **Google Cloud Messaging для Chrome** включать не нужно.

   	![][3]

4. В левой области щелкните **Credentials** (Учетные данные) > **Create New Key** (Создать новый ключ) > **Server Key** (Ключ сервера) > **Create** (Создать).

   	![][4]

5. Запишите значение **API Key** (Ключ API) для сервера. Далее мы выполним эти настройки в концентраторе уведомлений, чтобы активировать отправку push-уведомлений в GCM.

   	![][5]

##<a id="configure-hub"></a>Настройка концентратора уведомлений

1. Войдите на [классический портал Azure] и в нижнем левом углу экрана щелкните **+Создать**.

2. Последовательно выберите **Службы приложений** > **Служебная шина** > **Концентратор уведомлений** > **Быстрое создание**. Введите имя для концентратора уведомлений, выберите нужный регион и щелкните элемент **Создать новый концентратор уведомлений**.

   	![][6]

4. Перейдите к только что созданному концентратору уведомлений. Щелкните пространство имен, в котором находится концентратор уведомлений (обычно это ***имя концентратора уведомлений*-ns**).

   	![][7]

5. В верхней части страницы выберите вкладку **Концентраторы уведомлений**.

   	![][8]

6. Затем в верхней части страницы выберите вкладку **Настройка**.

   	![][9]

7. На вкладке **Настройка** прокрутите до элемента **Параметры Google Cloud Messaging**, введите в поле **Ключ API** значение, полученное в предыдущем разделе, и щелкните **Сохранить**.

   	![][10]

8. В верхней части страницы выберите вкладку **Панель мониторинга** и щелкните **Сведения о подключении** внизу.

   	![][11]

9. Запишите значение **DefaultListenSharedAccessSignature** (оно понадобится в приложении Chrome для регистрации в концентраторе уведомлений) и **DefaultFullSharedAccessSignature** (оно понадобится для отправки уведомлений).

   	![][12]

Теперь ваш центр уведомлений настроен для работы с GCM, и у вас есть требуемые строки подключения для дальнейшей настройки.

##<a id="connect-app"></a>Подключение приложения Chrome к концентратору уведомлений

###Создание нового приложения Chrome

Пример ниже основан на [образце использования службы GCM для приложения Chrome]. В нем приведен рекомендуемый способ создания приложения Chrome. В следующих разделах описаны действия, которые можно выполнять с Центрами уведомлений Azure. Мы рекомендуем скачать источник для этого приложения Chrome со страницы [Образец использования концентратора уведомлений с приложением Chrome].

Для создания приложения Chrome используется код JavaScript, который можно создать в любом текстовом редакторе. Приложение Chrome будет выглядеть следующим образом.

   	![][15]

2. Создайте папку с именем **ChromePushApp** или любым другим именем.

3. Скачайте в эту папку **библиотеку crypto-js** из [библиотеки crypto-js]. Папка библиотеки будет содержать две вложенные папки: **components** и **rollups**.

4. Создайте файл **manifest.json**. Для всех приложений Chrome создаются резервные копии с помощью файла манифеста, в котором содержатся метаданные приложения, в частности доступные для него разрешения.

		{
		  "name": "NH-GCM Notifications",
		  "description": "Chrome platform app.",
		  "manifest_version": 2,
		  "version": "0.1",
		  "app": {
		    "background": {
		      "scripts": ["background.js"]
		    }
		  },
		  "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
		  "icons": { "128": "gcm_128.png" }
		}

	Обратите внимание на элемент **permissions**, который указывает, что это приложение Chrome сможет получать push-уведомления от GCM. Кроме того, он должен содержать универсальный код ресурса (URI) Центров уведомлений Azure, по которому приложение Chrome будет вызывать REST для регистрации. При этом используется файл значка gcm\_128.png, который можно найти в источнике, многократно используемом из исходного образца GCM. Вы можете использовать любое изображение.

5. Создайте файл с именем **background.js** и вставьте следующий код.

		// Returns a new notification ID used in the notification.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}

		function messageReceived(message) {
		  // A message is an object with a data property that
		  // consists of key-value pairs.

		  // Concatenate all key-value pairs to form a display string.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Message received: " + messageString);

		  // Pop up a notification to show the GCM message.
		  chrome.notifications.create(getNotificationId(), {
		    title: 'GCM Message',
		    iconUrl: 'gcm_128.png',
		    type: 'basic',
		    message: messageString
		  }, function() {});
		}

		var registerWindowCreated = false;

		function firstTimeRegistration() {
		  chrome.storage.local.get("registered", function(result) {

		    registerWindowCreated = true;
		    chrome.app.window.create(
		      "register.html",
		      {  width: 520,
		         height: 500,
		         frame: 'chrome'
		      },
		      function(appWin) {}
		    );
		  });
		}

		// Set up a listener for GCM message event.
		chrome.gcm.onMessage.addListener(messageReceived);

		// Set up listeners to trigger the first-time registration.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Это файл, в котором всплывает HTML-код окна приложения Chrome (**register.html**) и который определяет обработчик **messageReceived** для обработки входящего push-уведомления.

6. Создайте файл с именем **register.html**, который определяет пользовательский интерфейс приложения Chrome. Обратите внимание, что в этом образце используется *CryptoJS 3.1.2*. Если вы скачали какую-либо другую версию, исправьте путь script src.

		<html>

		<head>
		<title>GCM Registration</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>

		<body>

		Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>

		Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
		Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

		<br/>

		<button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

		<br/>
		<br/>

		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

		</body>

		</html>

7. Создайте файл с именем **register.js**, используя приведенный ниже код. Этот файл определяет сценарий, лежащий в основе **register.html**. Приложения Chrome не предусматривают встроенное выполнение, поэтому необходимо создать отдельный резервный сценарий для пользовательского интерфейса.

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

		window.onload = function() {
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH;
		   updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
		}

		function updateLog(status) {
		  currentStatus = document.getElementById("console").innerHTML;
		  if (currentStatus != "") {
		    currentStatus = currentStatus + "\n\n";
		  }

		  document.getElementById("console").innerHTML = currentStatus  + status;
		}

		function registerWithGCM() {
		  var senderId = document.getElementById("senderId").value.trim();
		  chrome.gcm.register([senderId], registerCallback);

		  // Prevent register button from being clicked again before the registration finishes.
		  document.getElementById("registerWithGCM").disabled = true;
		}

		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;

		  if (chrome.runtime.lastError) {
		    // When the registration fails, handle the error and retry the
		    // registration later.
		    updateLog("Registration failed: " + chrome.runtime.lastError.message);
		    return;
		  }

		  updateLog("Registration with GCM succeeded.");
		  document.getElementById("registerWithNH").disabled = false;

		  // Mark that the first-time registration is done.
		  chrome.storage.local.set({registered: true});
		}

		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}

		// From http://msdn.microsoft.com/library/dn495627.aspx
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  throw "Error parsing connection string";

		  parts.forEach(function(part) {
		    if (part.indexOf('Endpoint') == 0) {
		    endpoint = 'https' + part.substring(11);
		    } else if (part.indexOf('SharedAccessKeyName') == 0) {
		    sasKeyName = part.substring(20);
		    } else if (part.indexOf('SharedAccessKey') == 0) {
		    sasKeyValue = part.substring(16);
		    }
		  });

		  originalUri = endpoint + hubName;
		}

		function generateSaSToken()
		{
		  targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
		  var expiresInMins = 10; // 10 minute expiration

		  // Set expiration in seconds.
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;

		  // Using CryptoJS.
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);

		  // Construct authorization string.
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}

		function sendNHRegistrationRequest()
		{
		  var registrationPayload =
		  "<?xml version="1.0" encoding="utf-8"?>" +
		  "<entry xmlns="http://www.w3.org/2005/Atom">" +
		      "<content type="application/xml">" +
		          "<GcmRegistrationDescription xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";

		  // Update the payload with the registration ID obtained earlier.
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();

		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Notification Hub Registration succesful!");
		        updateLog(client.responseText);
		      } else {
		        updateLog("Notification Hub Registration did not succeed!");
		        updateLog("HTTP Status: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };

		  client.onerror = function () {
		        updateLog("ERROR - Notification Hub Registration did not succeed!");
		  }

		  client.open("POST", url, true);
		  client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
		  client.setRequestHeader("Authorization", sasToken);
		  client.setRequestHeader("x-ms-version", "2014-09");

		  try {
		      client.send(registrationPayload);
		  }
		  catch(err) {
		      updateLog(err.message);
		  }
		}

	Из приведенного выше сценария можно сделать следующие выводы.
	- *window.onload* определяет события, которые происходят при нажатии каждой из двух кнопок в пользовательском интерфейсе. Одна кнопка запускает регистрацию в GCM, а другая использует полученный после регистрации в GCM идентификатор для регистрации в центрах уведомлений Azure.
	- *updateLog* — это функция, которая определяет простую функцию ведения журнала.
	- *registerWithGCM* — это обработчик нажатия первой кнопки, который выполняет вызов **chrome.gcm.register**, адресованный службе GCM, чтобы зарегистрировать этот экземпляр приложения Chrome.
	- *registerCallback* — это функция обратного вызова, которая вызывается при возврате вышеупомянутого вызова регистрации в GCM.
	- *registerWithNH* — это обработчик нажатия второй кнопки, который выполняет регистрацию в центрах уведомлений. Он получает свойства **hubName** и **connectionString**, которые указал пользователь, и вызывает REST API для регистрации в центрах уведомлений.
	- *splitConnectionString* и *generateSaSToken* — это параметры создания токена SaS с помощью JavaScript. Этот токен должен отправляться при каждом вызове REST API. Дополнительные сведения см. в статье [Основные понятия](http://msdn.microsoft.com/library/dn495627.aspx).
	- *sendNHRegistrationRequest* — это функция, вызывающая HTTP REST.
	- *registrationPayload* определяет полезные XML-данные регистрации. Дополнительные сведения см. в статье [Создание REST API концентратора уведомлений для регистрации]. Здесь мы обновляем идентификатор регистрации, используя полученные из GCM данные.
	- *client* — это экземпляр **XMLHttpRequest**, который используется для выполнения запроса HTTP POST. Обратите внимание, что мы обновляем заголовок **Authorization** с помощью **sasToken**. Если этот вызов будет выполнен успешно, этот экземпляр приложения Chrome будет зарегистрирован в центре уведомлений Azure.


В итоге папка должна выглядеть так: ![][21]

###Настройка и тестирование приложения Chrome

1. Откройте браузер Chrome. Откройте окно **Chrome extensions** (Расширения Chrome) и установите флажок **Developer mode** (Режим разработчика).

   	![][16]

2. Нажмите кнопку **Load unpacked extension** (Скачать распакованное расширение) и перейдите к папке, в которой вы создали файлы. При необходимости можно использовать также **Chrome Apps & Extensions Developer Tool**. Это средство само по себе является приложением Chrome (оно устанавливается из веб-магазина Chrome), предоставляющим дополнительные возможности отладки для разработки приложений Chrome.

   	![][17]

3. Если ошибки создания отсутствуют, вы увидите свое приложение Chrome.

   	![][18]

4. Введите в поле **Sender ID** (Идентификатор отправителя) номер проекта, который вы получили ранее из **консоли Google Cloud**, а затем щелкните **Register with GCM** (Зарегистрировать в GCM). Должно появиться сообщение **Registration with GCM succeeded** (Регистрация в GCM выполнена успешно).

   	![][19]

5. Введите имя в поле **Notification Hub Name** (Имя концентратора уведомлений) и значение **DefaultListenSharedAccessSignature**, полученное ранее на портале, а затем щелкните **Register with Azure Notification Hub** (Зарегистрировать в концентраторе уведомлений Azure). Должно отобразиться сообщение **Notification Hub Registration successful!** (Регистрация в концентраторе уведомлений выполнена успешно) и данные ответа о регистрации, в которых содержится идентификатор регистрации в Центрах уведомлений Azure.

   	![][20]

##<a name="send"></a>Отправка уведомления в приложение Chrome

В этом учебнике мы будем отправлять уведомления с помощью консольного приложения .NET. Однако их можно отправлять с помощью Центров уведомлений с любого сервера через <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейс REST</a>.

Пример отправки уведомлений из интерфейса мобильных служб Azure, интегрированного с центрами уведомлений, см. в статье [Добавление push-уведомлений в приложение мобильных служб](../mobile-services/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md).
  
Пример отправки уведомлений с помощью интерфейсов REST API см. в статье "Использование Центров уведомлений из Java/PHP/Python" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md) | [Python](notification-hubs-python-backend-how-to.md)).

1. В Visual Studio откройте меню **Файл** и выберите **Создать**, а затем — **Проект**. В разделе **Visual C#** последовательно щелкните **Windows** и **Консольное приложение**, а затем нажмите кнопку **ОК**. Это создаст новый проект консольного приложения.

2. В меню **Инструменты** последовательно выберите **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Отобразится консоль диспетчера пакетов.

3. В окне консоли выполните следующую команду:

        Install-Package Microsoft.Azure.NotificationHubs

   	В результате будет добавлена ссылка на пакет Azure Service Bus SDK с помощью <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">пакета WindowsAzure.ServiceBus NuGet</a>.

4. Откройте файл **Program.cs** и добавьте такой оператор `using`:

        using Microsoft.Azure.NotificationHubs;

5. Добавьте в класс **Program** следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{"data":{"message":"Hello Chrome from Azure Notification Hubs"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Обязательно замените заполнитель *hub name* именем концентратора уведомлений, которое отображается на портале на вкладке **Концентраторы уведомлений**. Кроме того, замените заполнитель строки подключения на строку подключения с именем **DefaultFullSharedAccessSignature**, полученную в разделе "Настройка концентратора уведомлений".

	>[AZURE.NOTE] Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. У строки доступа **Прослушивание** отсутствуют разрешения для отправки уведомлений.

5. Добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Убедитесь, что браузер Chrome открыт. Для этого не нужно открывать приложение Chrome. На рабочем столе должно появиться следующее всплывающее уведомление.

   	![][13]

7. Кроме того, вы можете просмотреть все уведомления в окне уведомлений Chrome, которое можно открыть в запущенном браузере Chrome на панели задач (в ОС Windows).

   	![][14]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере рассматривается рассылка уведомлений в приложение Chrome. Дополнительные сведения о Центрах уведомлений см. в статье [Общие сведения о концентраторах уведомлений]. Сведения о том, как ориентироваться на определенных пользователей, см. в руководстве [Уведомление пользователей посредством центров уведомлений]. Если необходимо разделить пользователей по группам интересов, см. статью [Использование концентраторов уведомлений для передачи экстренных новостей].

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Образец использования концентратора уведомлений с приложением Chrome]: http://google.com
[консоли Google Cloud]: http://cloud.google.com/console
[классический портал Azure]: https://manage.windowsazure.com/
[Общие сведения о концентраторах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Обзор приложений Chrome]: https://developer.chrome.com/apps/about_apps
[образце использования службы GCM для приложения Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Устанавливаемые веб-приложения]: https://developers.google.com/chrome/apps/docs/
[Приложения Chrome на мобильных устройствах]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Создание REST API концентратора уведомлений для регистрации]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[библиотеки crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging для Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Уведомление пользователей посредством центров уведомлений]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Использование концентраторов уведомлений для передачи экстренных новостей]: notification-hubs-windows-store-dotnet-send-breaking-news.md

<!---HONumber=AcomDC_0302_2016-->