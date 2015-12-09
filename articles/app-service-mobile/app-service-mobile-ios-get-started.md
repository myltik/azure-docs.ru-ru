<properties
	pageTitle="Создание мобильного приложения для iOS в службе мобильных приложений Azure | Microsoft Azure"
	description="Изучите этот учебник, чтобы начать работу с серверным частями мобильных приложений Azure для разработки приложений iOS на Objective-C или Swift"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="krisragh"/>

#Создание приложения iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Обзор

В этом руководстве показано, как использовать облачную серверную службу ([мобильные приложения Azure](app-service-mobile-value-prop.md)) для использования с мобильными приложениями iOS. Вы создадите новую серверную часть мобильного приложения и простое приложение iOS _Todo list_, в котором хранятся данные приложения в Azure.

## Предварительные требования

Для работы с этим учебником требуется:

* [Активная учетная запись Azure](http://azure.microsoft.com/pricing/free-trial/).

* Компьютер с [Visual Studio Community 2013] или более поздней версии.

* Компьютер Mac с Xcode v7.0 или более поздней версией.

* [Платформа для мобильных приложений iOS Azure](https://go.microsoft.com/fwLink/?LinkID=529823), которая автоматически включена как часть загруженного проекта быстрого запуска.

## Создание серверной части мобильного приложения Azure

Чтобы создать серверную часть мобильного приложения, выполните указанные ниже действия.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Итак, вы подготовили серверную часть мобильного Azure, которая может использоваться мобильными клиентскими приложениями. Теперь скачайте серверный проект со списком простых задач и опубликуйте его в Azure.

## Скачивание проекта сервера

1. На компьютере откройте [портал Azure]. Последовательно выберите элементы **Просмотреть все** > **Мобильные приложения**, а затем щелкните только что созданную серверную часть мобильного приложения.

2. В колонке «Мобильное приложение» щелкните **Параметры**, а затем в разделе **Мобильное приложение** последовательно выберите **Быстрый запуск** > **iOS (Objective-C)**. Если вы предпочитаете Swift, выберите вместо этого элементы **Быстрый запуск** > **iOS (Swift)**.

## Настройка серверного проекта

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]


## Скачивание и запуск приложения для iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[портал Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1203_2015--->