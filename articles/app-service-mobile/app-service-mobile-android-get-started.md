<properties
    pageTitle="Создание мобильного приложения для Android в службе мобильных приложений Azure | Microsoft Azure"
    description="Изучите этот учебник, чтобы начать работу с серверными частями мобильных приложений Azure для разработки программ на платформе Android."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="yuaxu"/>

#Создание приложения Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение для платформы Android с помощью серверной части мобильного приложения Azure. Мы создадим новую серверную часть мобильного приложения и простое приложение _списка дел_ для Android, которое хранит свои данные в Azure.

Выполнение инструкций из этого учебника необходимо для работы с другими учебниками по Android, посвященными использованию функции мобильных приложений в службе приложений Azure.

## Предварительные требования

Для работы с этим учебником требуется:

* [Средства разработчика Android](https://developer.android.com/sdk/index.html), которые включают интегрированную среду разработки Android Studio и новейшую платформу Android.
* Пакет SDK для мобильных приложений Android в Azure, который автоматически включается как часть скачиваемого вами ознакомительного проекта.
* ПК с [Visual Studio Community 2013] или более поздней версией не требуется для серверной части Node.js.
* [Активная учетная запись Azure](http://azure.microsoft.com/pricing/free-trial/).

## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Настройка серверного проекта

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Скачивание и запуск приложения для Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1210_2015-->