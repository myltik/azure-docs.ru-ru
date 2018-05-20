---
title: Отправка push-уведомлений в приложения Chrome с помощью Центров уведомлений Azure | Документация Майкрософт
description: Узнайте, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Chrome.
services: notification-hubs
keywords: мобильные push-уведомления, push-уведомления, push-уведомление, push-уведомления Chrome
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 5754a537b8a0bf0a93d6d54ba0ba78e5957ac87f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Руководство. Отправка push-уведомлений в приложения Chrome с помощью Центров уведомлений Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

В этом руководстве показано пошаговое создание центра уведомлений и отправка push-уведомлений в пример приложения Google Chrome путем использования [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). Приложение Chrome выполняется в контексте браузера Google Chrome и регистрируется в центре уведомлений. 

> [!NOTE]
> push-уведомления приложения Chrome не поддерживаются во всех браузерах. Они поддерживаются в браузерах с конкретной моделью расширения (дополнительные сведения см. в статье [Обзор приложений Chrome]). Помимо классического браузера, приложения Chrome также могут работать на мобильных устройствах (с ОС Android и iOS) с помощью Apache Cordova. Дополнительные сведения см. в статье [Run Chrome Apps on Mobile Using Apache Cordova] (Запуск приложений Chrome на мобильном устройстве с помощью Apache Cordova).

При работе с этим руководством вы выполните следующие задачи:

> [!div class="checklist"]
> * [Включение Google Cloud Messaging](#register)
> * [Настройка концентратора уведомлений](#configure-hub)
> * [Подключение приложения Chrome к концентратору уведомлений](#connect-app)
> * [Отправка push-уведомления в приложение Chrome](#send)

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a id="register"></a>Включение Google Cloud Messaging
1. Перейдите к веб-сайту [консоли Google Cloud](https://console.cloud.google.com/cloud-resource-manager) и выполните вход с помощью учетной записи Google.
2. Выберите **Создать проект** на панели инструментов. 

    ![Кнопка "Создать проект"](media/notification-hubs-chrome-get-started/create-project-button.png)
1. Введите соответствующее **имя проекта** и нажмите кнопку **Create** (Создать).
2. Щелкните значок уведомления (колокольчик) на панели инструментов и выберите сообщение **Создание проекта**. 

    ![Уведомление о создании проекта](media/notification-hubs-chrome-get-started/project-creation-notification.png)
1. Запишите номер созданного проекта из поля **Project Number** (Номер проекта) на странице **Projects** (Проекты). Используйте этот номер в качестве **идентификатора отправителя GCM** в приложении Chrome для регистрации в GCM.
   
    ![Консоль Google Cloud: поле Project Number (Номер проекта)](media/notification-hubs-chrome-get-started/gcm-project-number.png)
3. На панели мониторинга выберите **Перейти к обзору API**. 

    ![Кнопка "Перейти к обзору API"](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
1. На странице "API и сервисы" выберите **Включить API и сервисы**. 

    ![Включение API и сервисов](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
1. Выполните поиск по списку, введя ключевое слово **cloud messaging**. Выберите **Google Cloud Messaging** в отфильтрованном списке. 

    ![API Google Cloud Messaging](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
1. На странице **Google Cloud Messaging** нажмите кнопку **Включить**.

    ![Включение GCM](media/notification-hubs-chrome-get-started/enable-gcm.png)
1. На странице **API и сервисы** переключитесь на вкладку **Учетные данные**. Нажмите кнопку **Создать учетные данные** и выберите **Ключ API**. 

    ![Кнопка создания ключа API](media/notification-hubs-chrome-get-started/create-api-key-button.png)
1. В диалоговом окне **Ключ API создан** нажмите кнопку копирования, чтобы скопировать ключ в буфер обмена. Сохраните его где-либо. Используйте это значение для настройки центра уведомлений в следующем разделе, чтобы он мог отправлять push-уведомления в GCM.

    ![Страница API](media/notification-hubs-chrome-get-started/api-created-page.png)
12. Выберите свой ключ API в списке **Ключи API**. На странице ключа API выберите **IP-адреса (веб-серверы, задания cron и т. п.)**, введите **0.0.0.0/0** для IP-адреса и нажмите кнопку "Сохранить". 

    ![Включение IP-адресов](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>Создание центра уведомлений
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6. Выберите **Google (GCM)** в категории **Параметры уведомления**, введите **ключ API** для проекта GCM и щелкните **Сохранить**.

      ![Центры уведомлений Azure — Google (GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>Подключение приложения Chrome к концентратору уведомлений
Теперь центр уведомлений настроен для работы с GCM, а у вас есть строки подключения, с помощью которых вы можете зарегистрировать приложение для получения и отправки push-уведомлений.

### <a name="create-a-new-chrome-app"></a>Создание нового приложения Chrome
Следующий пример основан на [образце использования службы GCM для приложения Chrome]. В нем приведен рекомендуемый способ создания приложения Chrome. В этом разделе описаны шаги, необходимые для работы с Центрами уведомлений Azure. 

> [!NOTE]
> Мы рекомендуем скачать источник для этого приложения Chrome со страницы [Образец использования концентратора уведомлений с приложением Chrome]. 

Для создания приложения Chrome используется код JavaScript, который можно создать в любом текстовом редакторе. На следующем рисунке показано, каким образом выглядит приложение Chrome:

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
   
    Обратите внимание на элемент `permissions`, который указывает, что это приложение Chrome может получать push-уведомления от GCM. В примере приложения также используется файл значка `gcm_128.png`, который можно найти в источнике, многократно используемом из исходного образца GCM. Его можно заменить на любое другое изображение, которое соответствует [критериям значка](https://developer.chrome.com/apps/manifest/icons).
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
   * **registrationPayload** определяет полезные XML-данные регистрации. Дополнительные сведения см. в статье [Create Registration NH REST API]. (Создание REST API Центра уведомлений для регистрации). Обновите идентификатор регистрации, используя полученное из GCM значение.
   * **client** — это экземпляр **XMLHttpRequest**, используемый приложением для выполнения запроса HTTP POST. Обновите заголовок `Authorization` с помощью `sasToken`. Если этот вызов выполнен успешно, экземпляр приложения Chrome будет зарегистрирован в центре уведомлений Azure.

        Общая структура папок для этого проекта должна выглядеть следующим образом: ![Приложение Google Chrome — структура папок][21]

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
2. В меню **Сервис** щелкните **Диспетчер пакетов NuGet**, а затем щелкните **Консоль диспетчера пакетов**. Внизу окна откроется консоль диспетчера пакетов.
3. В окне консоли выполните следующую команду:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
   Ссылка на пакет SDK служебной шины Azure с <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">пакетом NuGet WindowsAzure.ServiceBus автоматически добавляется в проект</a>.
4. Откройте файл `Program.cs` и добавьте следующую инструкцию `using`:
   
        using Microsoft.Azure.NotificationHubs;
5. Добавьте в класс `Program` следующий метод:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
    Обязательно замените заполнитель `<hub name>` именем центра уведомлений, которое отображается на [портале](https://portal.azure.com) на странице "Центры уведомлений". Кроме того, замените заполнитель строки подключения строкой подключения с именем `DefaultFullSharedAccessSignature` , полученной в разделе настройки центра уведомлений.
   
    > [!NOTE]
    > Обязательно используйте строку подключения с **полным** доступом, а не доступом к **прослушиванию**. Строка подключения с доступом к **прослушиванию** не предоставляет разрешения для отправки push-уведомлений.
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
В рамках этого руководства вы отправили широковещательные уведомления на клиенты, зарегистрированные в серверной части. Чтобы узнать, как отправлять push-уведомления на конкретные устройства, перейдите к следующему руководству: 

> [!div class="nextstepaction"]
>[Руководство по отправке push-уведомлений на конкретные устройства Android с помощью Центров уведомлений Azure и Google Cloud Messaging](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
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
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Обзор приложений Chrome]: https://developer.chrome.com/apps/about_apps
[образце использования службы GCM для приложения Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Run Chrome Apps on Mobile Using Apache Cordova]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Create Registration NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[библиотеку crypto-js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
