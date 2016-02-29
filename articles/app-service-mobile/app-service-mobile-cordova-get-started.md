<properties
    pageTitle="Создание мобильного приложения Apache Cordova в службе мобильных приложений Azure | Microsoft Azure"
    description="Изучите этот учебник, чтобы начать работу с серверными частями мобильных приложений Azure для разработки программ Apache Cordova."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="ggailey777"
    editor=""
    tags=""
    keywords="cordova, javascript, мобильный, клиент" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Создание приложения Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение для платформы Apache Cordova с помощью серверной части мобильного приложения Azure. Мы создадим новую серверную часть мобильного приложения и простое приложение _списка дел_ для Apache Cordova, которое хранит свои данные в Azure.

Выполнение инструкций из этого учебника необходимо для работы с другими учебниками по Apache Cordova, посвященными использованию функции мобильных приложений в службе приложений Azure.

## Предварительные требования

Для работы с этим учебником требуется:

* Компьютер с [Visual Studio Community 2015] или более поздней версии.
* [Средства Visual Studio для Apache Cordova].
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).

Можно также обойтись без Visual Studio и использовать командную строку Apache Cordova напрямую. Это пригодится, если вы работаете с данным учебником на компьютере Mac. Компиляция клиентских приложений Apache Cordova с помощью командной строки в этом учебнике не рассматривается.

## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Настройка серверного проекта

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Загрузка и запуск приложения Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Дальнейшие действия

Закончив работу с этим кратким учебником, переходите к одному из следующих руководств:

* [Добавление проверки подлинности] в приложение Apache Cordova.
* [Добавление push-уведомлений] в приложение Apache Cordova.

Дополнительные сведения об основных понятиях, связанных со службой приложений Azure.

* [Аутентификация]
* [Push-уведомления]

Подробнее об использовании пакетов SDK.

* [Серверный пакет SDK для ASP.NET]
* [Серверный пакет SDK для Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Средства Visual Studio для Apache Cordova]: https://www.visualstudio.com/ru-RU/features/cordova-vs.aspx
[Добавление проверки подлинности]: app-service-mobile-cordova-get-started-users.md
[Добавление push-уведомлений]: app-service-mobile-cordova-get-started-push.md
[Аутентификация]: app-service-mobile-auth.md
[Push-уведомления]: ../notification-hubs/notification-hubs-overview.md
[Серверный пакет SDK для ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Серверный пакет SDK для Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0218_2016-->