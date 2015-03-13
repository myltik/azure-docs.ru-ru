<properties 
	pageTitle="Приступая к работе с центрами уведомлений Azure" 
	description="Узнайте, как использовать центры уведомлений Azure для отправки push-уведомлений пользователям." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="piyushjo" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="01/10/2015" 
	ms.author="piyushjo"/>
# Приступая к работе с центрами уведомлений

<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/ru-ru/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ru-ru/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ru-ru/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ru-ru/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ru-ru/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ru-ru/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a><a href="/ru-ru/documentation/articles/notification-hubs-chrome-get-started/" title="Chrome" class="current">Chrome</a></div>

В этом разделе показано, как использовать центры уведомлений Azure для отправки push-уведомлений в приложение Chrome.

Одно из основных преимуществ использования уведомлений приложения Chrome состоит в том, что они отображаются в браузере Google Chrome, и при этом в нем не должно быть запущено или открыто приложение Chrome (хотя сам браузер Chrome должен быть запущен). В окне уведомлений Chrome можно увидеть обобщенное представление всех уведомлений. 

>[AZURE.NOTE] Это не общее push-уведомление, используемое в браузере. Оно используется только для приложений Chrome. Дополнительную информацию см. в статье [Обзор приложений Chrome]. Приложения Chrome ранее назывались "упакованными приложениями". Они отличаются от более простых "размещаемых приложений". Информацию о различиях между этими приложениями см. в статье [Устанавливаемые веб-приложения]. Приложения Chrome также можно использовать на мобильных устройствах (под управлением Android и iOS), используя Apache Cordova. Дополнительную информацию см. в статье [Приложения Chrome на мобильных устройствах]. 

В этом учебнике создается пустое приложение Chrome, которое получает push-уведомления с помощью Google Cloud Messaging (GCM). По завершении вы сможете рассылать push-уведомления всем пользователям Chrome, у которых установлено это приложение Chrome. 

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Включение Google Cloud Messaging (GCM)](#register)
* [Настройка центра уведомлений](#configure-hub)
* [Подключение приложения Chrome к центру уведомлений](#connect-app)
* [Отправка уведомлений для приложения Chrome](#send)
* [Дальнейшие действия](#next-steps)

В этом учебнике описывается простой сценарий вещания с использованием центров уведомлений. С тех пор, как [Google Cloud Messaging for Chrome] устарела и одна версия GCM поддерживает и устройства Android, и экземпляры Chrome, настройка GCM и центра уведомлений Azure аналогична их настройке для ОС Android. 

Рекомендуем изучить следующий учебник в разделе "Дальнейшие действия", чтобы узнать об использовании центров уведомлений для охвата определенных пользователей и групп устройств. 

>[AZURE.NOTE] Для работы с этим учебником требуется активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http://azure.microsoft.com/ documentation/articles/notification-hubs-chrome-get-started/" target="_blank">Бесплатная пробная версия Azure</a>.

##<a id="register"></a>Включение Google Cloud Messaging (GCM)

1. Перейдите на веб-сайт [консоли Google Cloud] и войдите с помощью учетной записи Google. Нажмите кнопку **Создать проект**. Введите соответствующее имя в поле **Имя проекта** и нажмите кнопку **Создать**. 

   	![][1]

2. Запишите номер только что созданного проекта из поля **Номер проекта** на странице проектов. Этот номер будет использоваться как **идентификатор отправителя GCM** в приложении Chrome для регистрации в GCM.

   	![][2]

3. В области справа щелкните элемент **API и авторизация**, выполните прокрутку вниз и щелкните переключатель, чтобы включить **Google Cloud Messaging for Android**. Вам не требуется включать  *Google Cloud Messaging for Chrome*. Возможно, в будущем имя изменится на просто  *Google Cloud Messaging*. 

   	![][3]

4. В левой области щелкните **Учетные данные** -> **Создать новый ключ** -> **Ключ сервера** -> **Создать**.

   	![][4]

5. Запишите значение **Ключ API**. Вы выполните эти настройки в центре уведомлений после его включения, чтобы получить возможность отправлять push-уведомления в GCM. 

   	![][5]

##<a id="configure-hub"></a>Настройка центра уведомлений

1. Войдите на **[Портал управления Azure]**, а затем нажмите кнопку **+СОЗДАТЬ** в левой нижней части экрана.

2. Последовательно щелкните элементы **Службы приложений**, **Service Bus**, **Центр уведомлений** и **Быстрое создание**. Введите имя для центра уведомлений, выберите нужный регион и щелкните элемент **Создать новый центр уведомлений**.

   	![][6]

4. Перейдите к только что созданному центру уведомлений. Щелкните пространство имен, содержащее имя центра уведомлений (обычно это ***имя центра уведомлений*-ns**).   

   	![][7]

5. В верхней части страницы выберите вкладку **Центры уведомлений**.

   	![][8]

6. Затем в верхней части страницы выберите вкладку **Настройка**. 

   	![][9]

7. На вкладке **Настройка** перейдите к настройкам **google cloud messaging**, введите значение **Ключ API**, полученное в предыдущем разделе, а затем нажмите кнопку **Сохранить**.

   	![][10]

8. Откройте расположенную сверху вкладку **Панель мониторинга** и щелкните элемент **Информация о подключении** внизу. 

   	![][11]
 
9. Запишите значение **DefaultListenSharedAccessSignature** (которое понадобится в приложении Chrome для регистрации в центре уведомлений) и **DefaultFullSharedAccessSignature** (которое понадобится для отправки уведомлений). 

   	![][12]

Теперь ваш центр уведомлений настроен для работы с GCM, и у вас есть требуемые строки подключения для дальнейшей настройки.

##<a id="connect-app"></a>Подключение приложения Chrome к центру уведомлений

###Создание нового приложения Chrome
Пример ниже основан на [образце использования службы GCM для приложения Chrome]. В нем используется рекомендуемый способ создания приложения Chrome. В следующих разделах описаны действия, которые необходимо выполнять с центром уведомлений Azure. Рекомендуется скачать источник для этого приложения Chrome со страницы [Образец использования центра уведомлений с приложением Chrome].

Для создания приложения Chrome используется код JavaScript, который можно создать в любом текстовом редакторе. 

1. Приложение Chrome будет выглядеть следующим образом. 

   	![][15]

2. Создайте папку с именем **ChromePushApp**. На самом деле ее можно назвать как угодно. 

3. Скачайте  *cryto-js library* из [библиотеки crypto-js] в этой папке. Эта папка библиотеки будет содержать две вложенные папки -  *components* и  *rollups*. 

4. Создайте файл manifest.json. Все приложения Chrome сохраняются с помощью файла манифеста, в котором содержатся метаданные приложения, в частности доступные для него разрешения.

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
	
	Обратите внимание на элемент  *permissions*, который указывает, что это приложение Chrome сможет получать push-уведомления от GCM. Он также должен содержать универсальный код ресурса центра уведомлений Azure, по которому приложение Chrome будет осуществлять вызов REST для регистрации.
	При этом используется файл значка gcm_128.png, который можно найти в источнике, многократно используемом из исходного образца GCM. Вы можете использовать любое изображение. 
 
5. Создайте файл с именем background.js и вставьте следующий код:

		// Возвращает новый идентификатор уведомления, используемый в уведомлении.
		function getNotificationId() {
		  var id = Math.floor(Math.random() * 9007199254740992) + 1;
		  return id.toString();
		}
		
		function messageReceived(message) {
		  // Сообщение - это объект со свойством данных,
		  // который состоит из пар "ключ-значение".
		
		  // Объединение всех пар "ключ-значение" для создания отображаемой строки.
		  var messageString = "";
		  for (var key in message.data) {
		    if (messageString != "")
		      messageString += ", "
		    messageString += key + ":" + message.data[key];
		  }
		  console.log("Получено сообщение: " + messageString);
		
		  // Открытие уведомления для отображения сообщения GCM.
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
		
		// Настройка прослушивателя для события сообщения GCM.
		chrome.gcm.onMessage.addListener(messageReceived);
		
		// Настройка прослушивателей для запуска первой регистрации.
		chrome.runtime.onInstalled.addListener(firstTimeRegistration);
		chrome.runtime.onStartup.addListener(firstTimeRegistration);

	Это файл, в котором всплывает HTML-код окна приложения Chrome (* register.html*) и который определяет обработчик  *messageReceived* для обработки входящего push-уведомления. 

6. Создайте файл с именем  *register.html*, который определяет пользовательский интерфейс приложения Chrome. Обратите внимание, что в этом примере используется  *CryptoJS v3.1.2*. Если вы скачали какую-либо другую версию, исправьте путь src сценария. 

		<html>
		
		<head>
		<title>Регистрация в GCM</title>
		<script src="register.js"></script>
		<script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
		<script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
		</head>
		
		<body>
		
		Идентификатор отправителя:<br/><input id="senderId" type="TEXT" size="20"><br/>
		<button id="registerWithGCM">Register with GCM</button>
		<br/>
		<br/>
		<br/>
		
		Имя центра уведомлений:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/> 
		Строка подключения:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea> 
		
		<br/>
		
		<button id="registerWithNH" disabled="true">Регистрация в центре уведомлений Azure</button>
		
		<br/>
		<br/>
		
		<textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea> 
		
		</body>
		
		</html>

7. Создайте файл  *register.js*, используя код ниже. Этот файл определяет сценарий, лежащий в основе  *register.html*. Приложения Chrome не предусматривают встроенное выполнение, поэтому необходимо создать отдельный резервный сценарий для пользовательского интерфейса. 

		var registrationId = "";
		var hubName        = "", connectionString = "";
		var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
		
		window.onload = function() { 
		   document.getElementById("registerWithGCM").onclick = registerWithGCM;  
		   document.getElementById("registerWithNH").onclick = registerWithNH; 
		   updateLog("Вы еще не зарегистрированы. Укажите идентификатор отправителя и зарегистрируйтесь в GCM, а затем в центре уведомлений."); 
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
		
		  // Не допустите повторное нажатие кнопки регистрации до завершения регистрации
		  document.getElementById("registerWithGCM").disabled = true;
		}
		
		function registerCallback(regId) {
		  registrationId = regId;
		  document.getElementById("registerWithGCM").disabled = false;
		
		  if (chrome.runtime.lastError) {
		    // В случае сбоя регистрации обработайте ошибку и повторите попытку
		    // позже.
		    updateLog("Сбой регистрации: " + chrome.runtime.lastError.message);
		    return;
		  }
		
		  updateLog("Регистрация в GCM успешно завершена.");
		  document.getElementById("registerWithNH").disabled = false;
		
		  // Обратите внимание, что первая регистрация выполнена.
		  chrome.storage.local.set({registered: true});
		}
		
		function registerWithNH() {
		  hubName = document.getElementById("hubName").value.trim();
		  connectionString = document.getElementById("connectionString").value.trim();
		  splitConnectionString();
		  generateSaSToken();
		  sendNHRegistrationRequest();
		}
		
		// По адресу http://msdn.microsoft.com/library/dn495627.aspx 
		function splitConnectionString()
		{
		  var parts = connectionString.split(';');
		  if (parts.length != 3)
		  вызывает ошибку "Ошибка анализа строки подключения";
		
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
		  var expiresInMins = 10; // срок действия - 10 минут
		
		  // Установка срока действия в секундах
		  var expireOnDate = new Date();
		  expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
		  var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
		    .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
		    .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
		    .getUTCSeconds()) / 1000;
		  var tosign = targetUri + '\n' + expires;
		
		  // с помощью CryptoJS
		  var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
		  var base64signature = signature.toString(CryptoJS.enc.Base64);
		  var base64UriEncoded = encodeURIComponent(base64signature);
		
		  // создание строки авторизации
		  sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
		                  + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
		}
		
		function sendNHRegistrationRequest()
		{
		  var registrationPayload = 
		  "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
		  "<entry xmlns=\"http://www.w3.org/2005/Atom\">" + 
		      "<content type=\"application/xml\">" + 
		          "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
		              "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
		          "</GcmRegistrationDescription>" +
		      "</content>" +
		  "</entry>";
		
		  // Обновление полезных данных с использованием идентификатора регистрации, полученного ранее
		  registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
		
		  var url = originalUri + "/registrations/?api-version=2014-09";
		  var client = new XMLHttpRequest();
		
		  client.onload = function () {
		    if (client.readyState == 4) {
		      if (client.status == 200) {
		        updateLog("Регистрация центра уведомлений успешно завершена!");
		        updateLog(client.responseText);
		      } else {
		        updateLog ("Не удалось выполнить регистрацию центра уведомлений!").
		        updateLog ("Состояние HTTP: " + client.status + " : " + client.statusText);
		        updateLog("HTTP Response: " + "\n" + client.responseText);
		      }
		    }
		  };
		
		  client.onerror = function () {
		        updateLog("Ошибка. Регистрация центра уведомлений не удалась.");
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
	- *window.onload* определяет события нажатия кнопки для двух кнопок пользовательского интерфейса - одна кнопка выполняет регистрацию в GCM, а другая кнопка использует идентификатор регистрации, возвращенный после регистрации в GCM, чтобы выполнить регистрацию в центре уведомлений Azure. 
	- Функция *updateLog* определяет обычную функцию ведения журнала. 
	- *registerWithGCM* - это обработчик нажатия первой кнопки, который выполняет вызов  *chrome.gcm.register* GCM, чтобы зарегистрировать этот экземпляр приложения Chrome. 
	- *registerCallback* - это функция обратного вызова, которая получает вызов при возврате вышеприведенного вызова регистрации в GCM. 
	- *registerWithNH* - это обработчик нажатия второй кнопки, который выполняет регистрацию в центре уведомлений. Он получает значения  *hubName* и  *connectionString*, которые указал пользователь, и вызывает REST API для регистрации в центре уведомлений. 
	- *splitConnectionString* и  *generateSaSToken* - это параметры создания маркера SaS с помощью JavaScript. Этот маркер должен отправляться при каждом вызове REST API. Дополнительную информацию см. здесь: http://msdn.microsoft.com/library/dn495627.aspx. 
	- *sendNHRegistrationRequest* - это функция, осуществляющая вызов HTTP REST. 
	- *registrationPayload* определяет полезные XML-данные регистрации. Дополнительную информацию см. здесь: [Создание регистрации]. Здесь мы обновляем идентификатор регистрации на основе данных, полученных из GCM. 
	- *client* - это экземпляр  *XMLHttpRequest*, который мы используем, чтобы выполнить запрос HTTP POST. Обратите внимание, что мы обновляем заголовок  *Authorization* с помощью sasToken. Если этот вызов будет выполнен успешно, этот экземпляр приложения Chrome будет зарегистрирован в центре уведомлений Azure. 
	

8. В конечном результате папка должна выглядеть следующим образом:
   	![][21]

###Настройка и тестирование приложения Chrome

1. Откройте браузер Chrome. Откройте окно с **расширениями Chrome** и установите флажок **Режим разработчика**. 

   	![][16]

2. Нажмите кнопку **Скачать распакованное расширение** и перейдите к папке, в которой вы создали файлы. Дополнительно вы можете использовать **Chrome App and Extensions Developer Tool**, который сам по себе является приложением Chrome (его необходимо будет установить из интернет-магазина Chrome) и предоставляет дополнительные возможности отладки для разработки приложений Chrome. 

   	![][17]

3. Затем при отсутствии ошибок создания вы увидите приложение Chrome. 

   	![][18]

4. Введите **номер проекта**, который вы получили ранее из **консоли Google Cloud**, и идентификатор отправителя, а затем щелкните **Зарегистрировать в GCM**. Вы должны увидеть сообщение  *Registration with GCM succeeded.*.

   	![][19]

5. Введите имя в поле **Имя центра уведомлений** и значение **DefaultListenSharedAccessSignature**, полученное ранее на портале управления Azure, а затем нажмите кнопку **Зарегистрировать в центре уведомлений Azure**. Вы должны увидеть сообщение  *Notification Hub Registration succesful!* и данные ответа о регистрации, в которых содержится идентификатор регистрации центра уведомлений Azure. 

   	![][20]  

##<a name="send"></a>Отправка уведомлений для приложения Chrome

В этом учебнике показано, как отправлять уведомления с помощью консольного приложения .NET. Тем не менее, уведомления можно отправлять с помощью центра уведомлений из любой серверной части, используя <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейс REST</a>. 

Пример отправки уведомлений из серверной части мобильных служб Azure, интегрированной с помощью центра уведомлений, см. в статье **Приступая к работе с push-уведомлениями в мобильных службах** ([Серверная часть .NET](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/) | [Серверная часть JavaScript](/ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)).  
Пример отправки уведомлений с помощью REST API см. в статьях **Использование центра уведомлений из Java, PHP или Python** ([Java](/ru-ru/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/ru-ru/documentation/articles/notification-hubs-php-backend-how-to/) | [Python](/ru-ru/documentation/articles/notification-hubs-python-backend-how-to/)).

1. В Visual Studio в меню **Файл** последовательно выберите **Создать** и **Проект...**, затем в области **Visual C#** нажмите **Windows** и **Консольные приложения**, затем нажмите кнопку **ОК**.  Будет создан новый проект консольного приложения.

2. В меню **Инструменты** последовательно выберите **Диспетчер пакетов библиотеки** и **Консоль диспетчера пакетов**. Отобразится консоль диспетчера пакетов.

3. В окне консоли выполните следующую команду:

        Install-Package WindowsAzure.ServiceBus
    
   	После этого будет добавлена ссылка на пакет SDK для Azure Service Bus с помощью пакета NuGet <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus</a>. 

4. Откройте файл Program.cs и добавьте следующий оператор `using`:

        с помощью Microsoft.ServiceBus.Notifications;

5. В классе **Program** добавьте следующий метод:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<строка подключения с полным доступом>", "<имя центра>");
            String message = "{\"data\":{\"message\":\"Добро пожаловать Chrome из центра уведомлений Azure\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

   	Обязательно замените заполнитель "имя центра" именем центра уведомлений, отображаемым на вкладке **Центры уведомлений** портала: Замените заполнитель строки подключения строкой с именем **DefaultFullSharedAccessSignature**, полученной в разделе "Настройка центра уведомлений". 

	>[AZURE.NOTE] Обязательно используйте строку подключения с **полным** доступом, а не с доступом для **прослушивания**. У строки с доступом к прослушиванию нет прав на отправку уведомлений.

5. Затем добавьте следующие строки в метод **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

6. Убедитесь, что браузер Chrome открыт. Для этого не нужно открывать приложение Chrome. На рабочем столе вы должны увидеть следующее всплывающее уведомление. 

   	![][13]

7. Вы также можете просмотреть все уведомления, используя окно уведомлений Chrome, которое можно открыть в запущенном браузере Chrome на панели задач (в ОС Windows). 

   	![][14]

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере осуществляется рассылка уведомлений на приложения Chrome. 
Дополнительную информацию об использовании центров уведомлений см. в [Обзоре центров уведомлений].
Для охвата определенных пользователей см. учебник [Пользователи push-уведомлений центров уведомлений для отправки Azure], если же требуется разделить пользователей по группам интересов, см. [Экстренные новости центров уведомлений Azure]. 

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
[Образец использования центра уведомлений с приложением Chrome]: http://google.com
[Консоль Google Cloud]: http://cloud.google.com/console
[Портал управления Azure]: https://manage.windowsazure.com/
[Общая информация о центрах уведомлений Azure]: http://msdn.microsoft.com/library/jj927170.aspx
[Обзор приложений Chrome]: https://developer.chrome.com/apps/about_apps
[Образец использования службы GCM для приложения Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Устанавливаемые веб-приложения]: https://developers.google.com/chrome/apps/docs/
[Приложения Chrome на мобильных устройствах]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Создание регистрации]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[Библиотека crypto-js]: http://code.google.com/p/crypto-js/
[Использование GCM с приложениями Chrome]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Уведомление пользователей посредством центров уведомлений]: http://azure.microsoft.com/ documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
[Использование центров уведомлений Azure для передачи экстренных новостей]: http://azure.microsoft.com/ documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/

<!--HONumber=45--> 
