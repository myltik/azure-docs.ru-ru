<properties
	pageTitle="Как использовать пакет SDK JavaScript для мобильных приложений Azure"
	description="Как использовать v для мобильных приложений Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="adrianha;ricksal"/>

# Как использовать клиентскую библиотеку JavaScript для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В данном руководстве показано, как реализовать типичные сценарии с использованием последней версии [пакета SDK JavaScript для мобильных приложений Azure]. Если вы не знакомы с мобильными приложениями Azure, сначала изучите статью [Быстрый запуск мобильного приложения Azure], чтобы создать серверную часть и таблицу. В этом руководстве мы рассмотрим использование мобильного внутреннего сервера в веб-приложениях HTML и JavaScript.

##<a name="Setup"></a>Настройка и необходимые компоненты

В данном руководстве предполагается, что вы уже создали серверную часть с таблицей. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках.

Установить пакет SDK JavaScript для мобильных приложений Azure можно с помощью команды `npm`.

```
npm install azure-mobile-apps-client --save
```

После установки библиотека находится в `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`. Скопируйте этот файл в свою область Интернета.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

Библиотеку также можно использовать как модуль ES2015 в средах CommonJS, таких как Browserify и Webpack, а также как библиотеку AMD. Например:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Практическое руководство. Проверка подлинности пользователей

Служба приложений Azure поддерживает проверку подлинности и авторизацию пользователей с помощью различных внешних поставщиков идентификаторов: Facebook, Google, учетная запись Майкрософт и Twitter. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Приступая к работе с проверкой подлинности].

Поддерживается два потока аутентификации: серверный и клиентский. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Практическое руководство. Регистрация для получения push-уведомлений

Для обработки push-уведомлений установите [phonegap-plugin-push]. Его можно легко добавить с помощью команды `cordova plugin add` в командной строке или установщика подключаемых модулей Git в Visual Studio. Следующий код в приложении Apache Cordova регистрирует устройство для получения push-уведомлений:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Для отправки push-уведомления с сервера используется пакет SDK для центров уведомлений. Не следует отправлять push-уведомление непосредственно из клиентов, так как это может использоваться для проведения атак типа "отказ в обслуживании" на центры уведомлений или PNS.

<!-- URLs. -->
[Быстрый запуск мобильного приложения Azure]: app-service-mobile-cordova-get-started.md
[Приступая к работе с проверкой подлинности]: app-service-mobile-cordova-get-started-users.md
[Добавление проверки подлинности в приложение]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[документации по объектам запросов]: https://msdn.microsoft.com/ru-RU/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0504_2016-->