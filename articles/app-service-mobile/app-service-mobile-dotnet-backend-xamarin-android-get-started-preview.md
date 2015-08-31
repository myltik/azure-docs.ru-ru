<properties
	pageTitle="Приступая к работе с мобильными приложениями Azure для приложений Xamarin.Android | Microsoft Azure"
	description="Этот учебник поможет приступить к использованию мобильных приложений Azure для разработки приложений Xamarin Android"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="normesta"
	manager="dwrede"
	editor="" />

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta" />

#Создание приложения Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]
 
##Обзор

В этом учебнике показано, как добавить облачную серверную службу в приложение Xamarin.Android с помощью серверной части мобильного приложения Azure. Вы создадите новую серверную часть мобильного приложения и простое приложение Xamarin.Andorid _Todo list_, в котором в Azure хранятся данные приложения.

Снимок экрана завершенного приложения приведен ниже:

![][0]

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными мобильным приложениям для приложений Xamarin.Android.
 
##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] или более поздняя версия. В случае установки Visual Studio Community 2013 устанавливайте [Xamarin] отдельно. При установке Visual Studio 2015 можно установить инструменты Xamarin.
 
>[AZURE.NOTE]Если вы хотите ознакомиться со службой приложений Azure до создания учетной записи, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где вы можете быстро создать краткосрочное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.


## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Скачивание проекта сервера

1. На компьютере откройте [портал Azure]. Последовательно выберите пункты **Просмотреть все** > **Мобильные приложения**, а затем только что созданную серверную часть мобильного приложения.

2. В колонке мобильного приложения щелкните **Параметры**, а затем в разделе **Мобильное приложение** последовательно выберите **Быстрый запуск** > **Xamarin.Android**.
 
3. В разделе **Загрузка и запуск серверного проекта** щелкните **Загрузить**. Извлеките сжатые файлы проекта на компьютер и откройте решение в Visual Studio.
 
## Локальное тестирование серверного проекта

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Скачивание и запуск приложения Xamarin.Android

1. В разделе **Загрузка и запуск проекта Xamarin.Android** нажмите кнопку **Загрузить**.

  	Будет скачан проект, содержащий клиентское приложение, подключенное к вашему мобильному приложению. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

	![][8]

	![][9]

2. Нажмите клавишу **F5** для выполнения сборки проекта, после чего запустите приложение.

3. В приложении введите осмысленный текст, например _Работа с учебником_, и нажмите кнопку **Добавить**.

	![][10]

	Отправится запрос POST к серверной части новой мобильной службы, размещенной в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются серверной частью мобильного приложения, а данные отображаются в списке.

	> [AZURE.NOTE]Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных, которые находятся в файле C# ToDoActivity.cs.

##Дальнейшие действия

* [Добавление аутентификации в приложение](app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md) <br/>Узнайте, как аутентифицировать пользователей приложения с помощью поставщика удостоверений.


<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[портал Azure]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=August15_HO8-->