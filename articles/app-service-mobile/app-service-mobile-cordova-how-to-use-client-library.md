---
title: Использование подключаемого модуля Apache Cordova для мобильных приложений Azure
description: Использование подключаемого модуля Apache Cordova для мобильных приложений Azure
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 6fb8be96c9793e96f1f7d2ad8e212d056d7e9ba5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32152592"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Как использовать клиентскую библиотеку Apache Cordova для мобильных приложений Azure
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

В данном руководстве показано, как реализовать типичные сценарии с использованием последней версии [подключаемого модуля Apache Cordova для мобильных приложений Azure]. Если вы не знакомы с мобильными приложениями Azure, изучите статью [Быстрый запуск мобильного приложения Azure] , чтобы создать серверную часть и таблицу, а также скачать предварительно собранный проект Apache Cordova. В данном руководстве мы сосредоточимся на клиентской части подключаемого модуля Apache Cordova.

## <a name="supported-platforms"></a>Поддерживаемые платформы
Этот пакет SDK поддерживает Apache Cordova 6.0.0 и более поздних версий на устройствах iOS, Android и устройствах с Windows.  Ниже перечислены возможности, поддерживаемые платформой:

* API Android 19–24 (от KitKat до Nougat);
* iOS 8.0 и более поздних версий;
* Windows Phone 8.1;
* универсальная платформа Windows.

## <a name="Setup"></a>Настройка и необходимые компоненты
В данном руководстве предполагается, что вы уже создали серверную часть с таблицей. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках. В этом руководстве также предполагается, что подключаемый модуль Apache Cordova уже добавлен в код.  Если это не так, добавьте подключаемый модуль Apache Cordova в проект через командную строку:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Дополнительные сведения о создании [первого приложения Apache Cordova] см. в соответствующей документации.

## <a name="ionic"></a>Настройка приложения Ionic v2

Чтобы правильно настроить проект Ionic v2, создать простое приложение и добавить подключаемый модуль Cordova, сделайте следующее.

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Добавьте следующие строки в `app.component.ts`, чтобы создать клиентский объект:

```
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

Теперь можно создать и запустить проект в браузере.

```
ionic platform add browser
ionic run browser
```

Подключаемый модуль Cordova для мобильных приложений Azure поддерживает обе версии приложения Ionic — v1 и v2.  Но в приложениях Ionic v2 требуется дополнительное объявление для объекта `WindowsAzure`.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Практическое руководство. Проверка подлинности пользователей
Служба приложений Azure поддерживает аутентификацию и авторизацию пользователей с помощью различных внешних поставщиков удостоверений: Facebook, Google, учетная запись Майкрософт и Twitter. Можно задать разрешения таблиц, чтобы предоставить доступ к определенным операциям только пользователям, прошедшим проверку подлинности. Удостоверения пользователей, прошедших проверку подлинности, также можно применять для реализации правил авторизации в серверных скриптах. Дополнительные сведения см. в учебнике [Приступая к работе с проверкой подлинности].

Если в приложении Apache Cordova используется проверка подлинности, должны быть доступны следующие подключаемые модули Cordova:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Поддерживается два потока аутентификации: серверный и клиентский.  Серверный поток обеспечивает самый простой способ проверки подлинности, так как он использует веб-интерфейс проверки подлинности. Клиентский поток обеспечивает более тесную интеграцию с возможностями устройства, такими как единый вход, так как использует пакеты SDK конкретного поставщика для конкретного устройства.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Практическое руководство. Настройка службы мобильных приложений для внешних URL-адресов перенаправления
Для обработки потоков пользовательского интерфейса OAuth некоторые типы приложений Apache Cordova используют функцию замыкания на себя.  Потоки пользовательского интерфейса OAuth в localhost приводят к возникновению проблем, так как службе аутентификации известно только, как использовать вашу службу по умолчанию.  Ниже перечислены примеры проблемных потоков пользовательского интерфейса OAuth:

* эмулятор Ripple;
* динамическая перезагрузка с Ionic.
* Локальное выполнение серверной части мобильной службы
* Возможно выполнение серверной части мобильной службы в службе приложений Azure, отличной от той, которая обеспечивает аутентификацию.

Чтобы добавить локальные параметры в конфигурацию, выполните приведенные ниже указания.

1. Войдите на [портал Azure]
2. Выберите **Все ресурсы** или **Службы приложений**, а затем щелкните имя своего мобильного приложения.
3. Щелкните **Инструменты**
4. В меню "Наблюдение" щелкните **Обозреватель ресурсов**, затем щелкните **Перейти**.  Откроется новое окно или вкладка.
5. В левой области навигации разверните узлы **config**, **authsettings** для сайта.
6. Щелкните **Изменить**
7. Найдите элемент "allowedExternalRedirectUrls".  Ему может быть присвоено значение NULL или массив значений.  Измените его значение на следующее:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Замените URL-адреса на URL-адреса службы.  Примеры: http://localhost:3000 (для примера службы Node.js) или http://localhost:4400 (для службы Ripple).  Это только примеры URL-адресов. Ваша ситуация, включая упомянутые в приведенных примерах службы, может быть совершенно другой.
8. В правом верхнем углу экрана нажмите кнопку **Чтение и запись** .
9. Нажмите зеленую кнопку **PUT** .

Параметры будут сохранены.  Не закрывайте окно браузера, пока не завершится сохранение параметров.
Кроме того, добавьте указанные URL-адреса замыкания на себя в параметры CORS своей службы приложений.

1. Войдите на [портал Azure]
2. Выберите **Все ресурсы** или **Службы приложений**, а затем щелкните имя своего мобильного приложения.
3. Автоматически откроется колонка "Параметры".  В противном случае щелкните **Все параметры**.
4. В меню API щелкните **CORS** .
5. Введите URL-адрес, который вы хотите добавить, и нажмите клавишу ВВОД.
6. При необходимости введите дополнительные URL-адреса.
7. Нажмите кнопку **Сохранить** , чтобы сохранить параметры.

Новые параметры вступят в действие приблизительно через 10–15 секунд.

## <a name="register-for-push"></a>Практическое руководство. Регистрация для получения push-уведомлений
Для обработки push-уведомлений установите [phonegap-plugin-push] .  Этот подключаемый модуль можно легко добавить, введя команду `cordova plugin add` в командной строке или воспользовавшись установщиком подключаемых модулей Git в Visual Studio.  Следующий код в приложении Apache Cordova регистрирует устройство для получения push-уведомлений.

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

Для отправки push-уведомления с сервера используется пакет SDK для центров уведомлений.  Никогда не следует отправлять push-уведомления непосредственно из клиентов. Это может быть использовано для атак типа "отказ в обслуживании" на центры уведомлений или PNS.  PNS может заблокировать ваш трафик в результате таких атак.

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения об API можно найти в нашей [документация по API](http://azure.github.io/azure-mobile-apps-js-client/).

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
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
