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
	ms.date="08/11/2015"
	ms.author="krisragh"/>

#Создание приложения iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение iOS с помощью серверной части мобильного приложения Azure. Мы создадим новую серверная часть мобильного приложения и простое приложение _Todo list_ для iOS, которое хранит свои данные в Azure.

Завершение этого учебника необходимо для использования других учебников по iOS, посвященных использованию функции мобильных приложений в службе приложений Azure.

##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] или более поздняя версия.

* Компьютер Mac с Xcode версии 7.0 или более поздней.

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до регистрации для получения учетной записи Azure, перейдите на страницу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). Там вы сможете немедленно создать кратковременное начальное мобильное приложение в службе приложений. Для этого не потребуется ни кредитная карта, ни какие-либо обязательства.

## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Скачивание проекта сервера

1. Откройте [портал Azure] на вашем компьютере. Последовательно выберите пункты **Просмотреть все** > **Мобильные приложения**, а затем только что созданную серверную часть мобильного приложения.

2. В колонке "Мобильное приложение" щелкните **Параметры**, а затем в разделе **Мобильное приложение** последовательно выберите пункты **Быстрый запуск** > **iOS (Objective-C)**. Если вы предпочитаете Swift, выберите вместо этого пункты **Быстрый запуск** > **iOS (Swift)**.

3. В разделе **загрузить и запустить серверный проект** нажмите **Загрузить**. Извлеките сжатые файлы проекта на компьютер и откройте решение в Visual Studio.

## Публикация серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Скачивание и запуск приложения для iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../../includes/app-service-mobile-ios-run-app-preview.md)]


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[портал Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Oct15_HO3-->