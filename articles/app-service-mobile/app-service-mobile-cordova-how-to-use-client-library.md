<properties
	pageTitle="Использование подключаемого модуля Apache Cordova для мобильных приложений Azure"
	description="Использование подключаемого модуля Apache Cordova для мобильных приложений Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="ggailey"/>

# Использование клиентской библиотеки Apache Cordova для мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В данном руководстве показано, как реализовать типичные сценарии с использованием последней версии [подключаемого модуля Apache Cordova для мобильных приложений Azure]. Если вы не знакомы с мобильными приложениями Azure, изучите статью [Быстрый запуск мобильного приложения Azure], чтобы создать серверную часть и таблицу, а также скачать предварительно собранный проект Apache Cordova. В данном руководстве мы сосредоточимся на клиентской части подключаемого модуля Apache Cordova.

##<a name="Setup"></a>Настройка и необходимые компоненты

В данном руководстве предполагается, что вы уже создали серверную часть с таблицей. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках. В этом руководстве также предполагается, что подключаемый модуль Apache Cordova уже добавлен в код. Если это не так, добавьте подключаемый модуль Apache Cordova в проект через командную строку:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Дополнительные сведения о создании [первого приложения Apache Cordova] см. в соответствующей документации.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Практическое руководство. Проверка подлинности пользователей

Служба приложений Azure поддерживает проверку подлинности и авторизацию пользователей с помощью различных внешних поставщиков идентификаторов: Facebook, Google, учетная запись Майкрософт и Twitter. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Приступая к работе с проверкой подлинности].

Если в приложении Apache Cordova используется проверка подлинности, должны быть доступны следующие подключаемые модули Cordova:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Поддерживается два потока аутентификации: серверный и клиентский. Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Практическое руководство. Настройка службы мобильных приложений для внешних URL-адресов перенаправления

Для обработки потоков пользовательского интерфейса OAuth некоторые типы приложений Apache Cordova используют функцию замыкания на себя. Это приводит к возникновению проблемы, поскольку службе проверки подлинности известно только об использовании службы по умолчанию. Примерами являются использование эмулятора Ripple, запуск службы локально или в другой службе приложений Azure, но с перенаправлением в службу приложений Azure для проверки подлинности, или Live Reload с Ionic. Чтобы добавить локальные параметры в конфигурацию, выполните приведенные ниже указания.

1. Войдите на [портал Azure].
2. Выберите **Все ресурсы** или **Службы приложений**, а затем щелкните имя своего мобильного приложения.
3. Щелкните **Средства**.
4. В меню "Наблюдение" щелкните **Обозреватель ресурсов** и нажмите кнопку**Перейти**. Откроется новое окно или вкладка.
5. В левой области навигации разверните узлы **config**, **authsettings** для сайта.
6. Щелкните **Изменить**.
7. Найдите элемент "allowedExternalRedirectUrls". Ему задано значение NULL. Измените его, как показано далее.

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Замените URL-адреса на URL-адреса службы. Примеры: "http://localhost:3000" (для примера службы Node.js) или "http://localhost:4400" (для службы Ripple). Это только примеры, так как ваша ситуация, включая упомянутые в приведенных примерах службы, может быть совершенно другой.
8. В правом верхнем углу экрана нажмите кнопку **Чтение и запись**.
9. Нажмите зеленую кнопку **РАЗМЕСТИТЬ**.

Параметры будут сохранены. Не закрывайте окно браузера, пока не завершится сохранение параметров. В параметры CORS потребуется добавить указанные URL-адреса замыкания на себя.

1. Войдите на [портал Azure].
2. Выберите **Все ресурсы** или **Службы приложений**, а затем щелкните имя своего мобильного приложения.
3. Автоматически откроется колонка "Параметры". В противном случае щелкните **Все параметры**.
4. В меню API щелкните элемент **CORS**.
5. Введите URL-адрес, который вы хотите добавить, и нажмите клавишу ВВОД.
6. При необходимости введите дополнительные URL-адреса.
7. Нажмите кнопку **Сохранить**, чтобы сохранить настройки.

Новые настройки вступят в силу приблизительно через 10-15 секунд.

##<a name="register-for-push"></a>Практическое руководство. Регистрация для получения push-уведомлений

Для обработки push-уведомлений установите [phonegap-plugin-push]. Его можно легко добавить с помощью команды `cordova plugin add` в командной строке или через установщик подключаемых модулей Git в Visual Studio. Следующий код в приложении Apache Cordova регистрирует устройство для получения push-уведомлений:

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
[портал Azure]: https://portal.azure.com
[Быстрый запуск мобильного приложения Azure]: app-service-mobile-cordova-get-started.md
[Приступая к работе с проверкой подлинности]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[подключаемого модуля Apache Cordova для мобильных приложений Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[первого приложения Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/ru-RU/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0525_2016-->