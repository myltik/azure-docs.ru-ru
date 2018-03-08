---
title: "Отправка push-уведомлений в приложения Chrome с помощью Центров уведомлений Azure | Документация Майкрософт"
description: "Узнайте, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Chrome."
services: notification-hubs
keywords: "мобильные push-уведомления, push-уведомления, push-уведомление, push-уведомления Chrome"
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: 33ef17f1556822c78783cc56b8ea7867eef2ec71
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
---
# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Отправка push-уведомлений в приложения Chrome с помощью Центров уведомлений Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

В этой статье показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Chrome, которое запущено в браузере Google Chrome. В этом руководстве вы создаете приложение Chrome, которое получает push-уведомления с помощью [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). 

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).
> 
> 

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

* [Включение Google Cloud Messaging](#register)
* [Настройка концентратора уведомлений](#configure-hub)
* [Подключение приложения Chrome к концентратору уведомлений](#connect-app)
* [Отправка push-уведомления в приложение Chrome](#send)
* [Дополнительные функции и возможности](#next-steps)

> [!NOTE]
> push-уведомления приложения Chrome не поддерживаются во всех браузерах. Они поддерживаются в браузерах с конкретной моделью расширения (дополнительные сведения см. в статье [Обзор приложений Chrome]). Помимо классического браузера, приложения Chrome также могут работать на мобильных устройствах (с ОС Android и iOS) с помощью Apache Cordova. Дополнительные сведения см. в статье [Run Chrome Apps on Mobile Using Apache Cordova] (Запуск приложений Chrome на мобильном устройстве с помощью Apache Cordova).
> 
> 

Так как служба [Google Cloud Messaging для Chrome] устарела и одна версия GCM теперь поддерживает как устройства Android, так и экземпляры Chrome, настройка GCM и Центров уведомлений Azure аналогична их настройке для ОС Android.

## <a id="register"></a>Включение Google Cloud Messaging
1. Перейдите на веб-сайт [консоли Google Cloud] , войдите с помощью своих учетных данных Google и нажмите кнопку **Create Project** (Создать проект). Введите соответствующее **имя проекта** и нажмите кнопку **Create** (Создать).
   
    ![Консоль Google Cloud: кнопка Create Project (Создать проект)][1]
2. Запишите номер созданного проекта из поля **Project Number** (Номер проекта) на странице **Projects** (Проекты). Используйте этот номер в качестве **идентификатора отправителя GCM** в приложении Chrome для регистрации в GCM.
   
    ![Консоль Google Cloud: поле Project Number (Номер проекта)][2]
3. В области слева щелкните **APIs & auth** (API и проверка подлинности), прокрутите вниз и переведите переключатель **Google Cloud Messaging for Android** (Google Cloud Messaging для Android) в положение "Вкл.". **Google Cloud Messaging для Chrome**включать не нужно.
   
    ![Консоль Google Cloud: кнопка Server Key (Ключ сервера)][3]
4. В области слева щелкните **Credentials** (Учетные данные) > **Create New Key** (Создать ключ) > **Server Key** (Ключ сервера) > **Create** (Создать).
   
    ![Консоль Google Cloud: раздел Credentials (Учетные данные)][4]
5. Запишите значение **API Key**(Ключ API) для сервера. Используйте это значение для настройки центра уведомлений в следующем разделе, чтобы он мог отправлять push-уведомления в GCM.
   
    ![Консоль Google Cloud: поле API Key (Ключ API)][5]

## <a id="configure-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6.   На странице **Settings** (Параметры) щелкните **Notification Services** (Службы уведомлений) и **Google (GCM)**. Введите ключ API и сохраните изменения.

        ![Центры уведомлений Azure — Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

## <a id="connect-app"></a>Подключение приложения Chrome к концентратору уведомлений
Теперь центр уведомлений настроен для работы с GCM, а у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения и отправки push-уведомлений.

### <a name="create-a-new-chrome-app"></a>Создание нового приложения Chrome
Следующий пример основан на [образце использования службы GCM для приложения Chrome]. В нем приведен рекомендуемый способ создания приложения Chrome. В этом разделе описаны шаги, необходимые для работы с Центрами уведомлений Azure. 

> [!NOTE]
> Мы рекомендуем скачать источник для этого приложения Chrome со страницы [Образец использования концентратора уведомлений с приложением Chrome]. 

Для создания приложения Chrome используется код JavaScript, который можно создать в любом текстовом редакторе. На следующем рисунке показано, каким образом выглядит приложение Chrome.

![Приложение Google Chrome][15]

1. Создайте папку с именем `ChromePushApp`. Вы можете присвоить ей другое имя, но в таком случае необходимо изменить путь в соответствующих сегментах кода.
2. Скачайте [библиотеку crypto-js] в папку, созданную на втором шаге. В этой папке библиотеки содержатся две вложенные папки: `components` и `rollups`.
3. Создайте файл `manifest.json` . Все приложения Chrome сохраняются с помощью файла манифеста, в котором содержатся метаданные приложения и, самое главное, все предоставленные для него разрешения при установке.
   
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
   
    Обратите внимание на элемент `permissions`, который указывает, что это приложение Chrome может получать push-уведомления от GCM. Кроме того, он должен содержать универсальный код ресурса Центров уведомлений Azure, по которому приложение Chrome вызывает REST для регистрации.
    В примере приложения также используется файл значка `gcm_128.png`, который можно найти в источнике, многократно используемом из исходного образца GCM. Его можно заменить на любое другое изображение, которое соответствует [критериям значка](https://developer.chrome.com/apps/manifest/icons).
4. Создайте файл с именем `background.js` с помощью указанного ниже кода.
   
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
   
    Этот файл извлекает HTML-код окна приложения Chrome (**register.html**) и определяет обработчик **messageReceived** для обработки входящего push-уведомления.
5. Создайте файл с именем `register.html`, который определяет пользовательский интерфейс приложения Chrome. 
   
   > [!NOTE]
   > В этом образце используется **CryptoJS 3.1.2**. Если вы скачали другую версию библиотеки, измените версию в пути `src` .
   > 
   > 
   
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
6. Создайте файл `register.js`, используя код, указанный на этом шаге. Этот файл определяет скрипт, лежащий в основе `register.html`. Приложения Chrome не предусматривают встроенное выполнение, поэтому необходимо создать отдельный резервный сценарий для пользовательского интерфейса.
   
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
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
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
   
    В скрипте используются следующие параметры.
   
   * **window.onload** определяет события, которые происходят при нажатии каждой из двух кнопок в пользовательском интерфейсе. Первый обработчик событий нажатия кнопки запускает регистрацию в GCM, а второй использует полученный после регистрации в GCM идентификатор для регистрации в Центрах уведомлений Azure.
   * **updateLog** — это функция, позволяющая записывать данные журнала кода. 
   * **registerWithGCM** — это обработчик нажатия первой кнопки, выполняющий вызов `chrome.gcm.register` в GCM, чтобы зарегистрировать текущий экземпляр приложения Chrome.
   * **registerCallback** — это функция обратного вызова, которая вызывается при возврате вызова регистрации в GCM.
   * **registerWithNH** — это обработчик нажатия второй кнопки, который выполняет регистрацию в Центрах уведомлений. Он получает значения `hubName` и `connectionString` (которые указал пользователь) и вызывает REST API для регистрации в Центре уведомлений.
   * **splitConnectionString** и **generateSaSToken** — это вспомогательные приложения для создания маркера SaS с помощью JavaScript. Этот маркер должен использоваться при каждом вызове REST API. Дополнительные сведения см. в статье [Common Concepts](http://msdn.microsoft.com/library/dn495627.aspx) (Основные понятия).
   * **sendNHRegistrationRequest** — это функция, вызывающая HTTP REST в Центрах уведомлений Azure.
   * **registrationPayload** определяет полезные XML-данные регистрации. Дополнительные сведения см. в статье [Create Registration NH REST API]. (Создание REST API Центра уведомлений для регистрации). Необходимо обновить идентификатор регистрации, используя полученное из GCM значение.
   * **client** — это экземпляр **XMLHttpRequest**, используемый приложением для выполнения запроса HTTP POST. Обновите заголовок `Authorization` с помощью `sasToken`. Если этот вызов выполнен успешно, экземпляр приложения Chrome будет зарегистрирован в центре уведомлений Azure.

Общая структура папок для этого проекта должна выглядеть следующим образом. ![Приложение Google Chrome — структура папок][21]

### <a name="set-up-and-test-your-chrome-app"></a>Настройка и тестирование приложения Chrome
1. Откройте браузер Chrome. Откройте окно **Расширения** и установите флажок **Режим разработчика**.
   
    ![Google Chrome: включение режима разработчика][16]
2. Нажмите кнопку **Load unpacked extension** (Скачать распакованное расширение) и перейдите к папке, в которой вы создали файлы. При необходимости можно также использовать средство **Chrome Apps & Extensions Developer Tool**. Это средство само по себе является приложением Chrome (оно устанавливается из веб-магазина Chrome), предоставляющим дополнительные возможности отладки для разработки приложений Chrome.
   
    ![Google Chrome: кнопка Load Unpacked Extension (Загрузить распакованное расширение)][17]
3. Если ошибки создания отсутствуют, вы увидите свое приложение Chrome.
   
    ![Google Chrome: отображение приложения Chrome][18]
4. Введите в поле Sender ID (Идентификатор отправителя) **номер проекта**, полученный ранее из **консоли Google Cloud**, а затем щелкните **Register with GCM** (Зарегистрировать в GCM). Должно появиться сообщение **Registration with GCM succeeded**
   
    ![Google Chrome: настройка приложения Chrome][19]
5. Введите имя в поле **Notification Hub Name** (Имя Центра уведомлений) и значение **DefaultListenSharedAccessSignature**, полученное ранее на портале, а затем щелкните **Register with Azure Notification Hub** (Зарегистрировать в Центре уведомлений Azure). Должно появиться сообщение **Notification Hub Registration successful!** (Регистрация в концентраторе уведомлений выполнена успешно) и данные ответа о регистрации, в которых содержится идентификатор регистрации в Центрах уведомлений Azure.
   
    ![Google Chrome: указание сведений о Центре уведомлений][20]  

## <a name="send"></a>Отправка уведомления в приложение Chrome
Отправьте для тестирования push-уведомления Chrome с помощью консольного приложения .NET. 

> [!NOTE]
> Push-уведомления можно отправлять с помощью Центров уведомлений с любого сервера через общедоступный <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">интерфейс REST</a>. Больше примеров, включающих использование разных платформ, можно найти на [портале документации](https://azure.microsoft.com/documentation/services/notification-hubs/).
> 
> 

1. В Visual Studio откройте меню **Файл** и выберите **Создать**, а затем — **Проект**. В разделе **Visual C#** щелкните **Windows** и **Консольное приложение**, а затем нажмите кнопку **ОК**.  На этом шаге создается проект консольного приложения.
2. В меню **Инструменты** щелкните **Диспетчер пакетов NuGet**, а затем щелкните **Консоль диспетчера пакетов**. Внизу окна откроется консоль диспетчера пакетов.
3. В окне консоли выполните следующую команду:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
       This adds a reference to the Azure Service Bus SDK with the <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>.
4. Откройте файл `Program.cs` и добавьте следующую инструкцию `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Добавьте в класс `Program` следующий метод:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
       Make sure to replace the `<hub name>` placeholder with the name of the notification hub that appears in the [portal](https://portal.azure.com) in your Notification Hub blade. Also, replace the connection string placeholder with the connection string called `DefaultFullSharedAccessSignature` that you obtained in the notification hub configuration section.
   
   > [!NOTE]
   > Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. Строка подключения с доступом к **прослушиванию** не предоставляет разрешения для отправки push-уведомлений.
   > 
   > 
6. Добавьте в метод `Main` следующие вызовы:
   
         SendNotificationAsync();
         Console.ReadLine();
7. Убедитесь, что браузер Chrome запущен, и запустите консольное приложение.
8. На рабочем столе должно появиться следующее всплывающее уведомление.
   
    ![Google Chrome: уведомление][13]
9. Кроме того, вы можете просмотреть все уведомления в окне уведомлений Chrome, которое можно открыть в запущенном браузере Chrome на панели задач (в ОС Windows).
   
    ![Google Chrome: список уведомлений](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> При этом в нем не должно быть запущено или открыто приложение Chrome (хотя сам браузер Chrome должен быть запущен). В окне уведомлений Chrome можно увидеть обобщенное представление всех уведомлений.
> 
> 

## <a name="next-steps"> </a>Дальнейшие действия
Дополнительные сведения о Центрах уведомлений см. в статье [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET].

Дополнительные сведения о том, как ориентироваться на определенных пользователей, см. в статье [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. 

Дополнительные сведения о том, как разделить пользователей по группам интересов, см. в статье [Использование Центров уведомлений Azure для передачи экстренных новостей].

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
[Образец использования концентратора уведомлений с приложением Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[консоли Google Cloud]: http://cloud.google.com/console
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: notification-hubs-push-notification-overview.md
[Обзор приложений Chrome]: https://developer.chrome.com/apps/about_apps
[образце использования службы GCM для приложения Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Run Chrome Apps on Mobile Using Apache Cordova]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Create Registration NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[библиотеку crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging для Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Использование Центров уведомлений Azure для передачи экстренных новостей]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
