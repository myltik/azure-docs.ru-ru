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
	ms.date="10/28/2015"
	ms.author="krisragh"/>

#Создание приложения iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение iOS с помощью серверной части мобильного приложения Azure. Мы создадим новую серверная часть мобильного приложения и простое приложение _Todo list_ для iOS, которое хранит свои данные в Azure.

Завершение этого учебника необходимо для использования других учебников по iOS, посвященных использованию функции мобильных приложений в службе приложений Azure.

## Предварительные требования

Для работы с этим учебником требуется:

* [Активная учетная запись Azure](http://azure.microsoft.com/pricing/free-trial/).

* Компьютер с [Visual Studio Community 2013] или более поздней версией.

* Компьютер Mac с Xcode v7.0 или более поздней версией.

* [Платформа для мобильных приложений IOS Azure](https://go.microsoft.com/fwLink/?LinkID=529823), которая автоматически включена как часть загруженного проекта быстрого запуска.

## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Скачивание проекта сервера

1. На компьютере откройте [портал Azure]. Последовательно выберите пункты **Просмотреть все** > **Мобильные приложения**, а затем щелкните только что созданную серверную часть мобильного приложения.

2. В колонке «Мобильное приложение» щелкните **Параметры**, а затем в разделе **Мобильное приложение** последовательно выберите пункты **Быстрый запуск** > **iOS (Objective-C)**. Если вы предпочитаете Swift, выберите вместо этого пункты **Быстрый запуск** > **iOS (Swift)**.

3. В разделе **Загрузка и запуск серверного проекта** щелкните **Загрузить**. Извлеките сжатые файлы проекта на компьютер и откройте решение в Visual Studio.

## Публикация серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## Скачивание и запуск приложения для iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[портал Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO2-->