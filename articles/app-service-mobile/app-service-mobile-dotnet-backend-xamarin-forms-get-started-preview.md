<properties
	pageTitle="Приступая к работе с мобильными приложениями с помощью Xamarin.Forms"
	description="Этот учебник поможет приступить к использованию мобильных приложений Azure в разработке для Xamarin.Forms."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="normesta"/>

#Создание приложения Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение Xamarin.Forms с помощью серверной части мобильного приложения Azure. Вы создадите новую серверную часть мобильного приложения и простое приложение Xamarin.Forms _Todo list_, в котором в Azure хранятся данные приложения.

Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными мобильным приложениям для приложений Xamarin.Forms.

##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] или более поздняя версия. В случае установки Visual Studio Community 2013 устанавливайте [Xamarin] отдельно. При установке Visual Studio 2015 можно установить инструменты Xamarin.

* Компьютер Mac установленным ПО [Xcode] 7.0 или более поздней версии и [Xamarin Studio].
 
     >[AZURE.NOTE]Если вы планируете создать приложение на компьютере Windows с помощью Visual Studio, для этого все равно нужен компьютер Mac с доступом к сети.
 
>[AZURE.NOTE]Если вы хотите ознакомиться со службой приложений Azure до создания учетной записи, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где вы можете быстро создать краткосрочное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Скачивание проекта сервера

1. На компьютере откройте [портал Azure]. Последовательно выберите пункты **Просмотреть все** > **Мобильные приложения**, а затем только что созданную серверную часть мобильного приложения.

2. В колонке мобильного приложения щелкните **Параметры**, а затем в разделе **Мобильное приложение** последовательно выберите **Быстрый запуск** > **Xamarin.Forms**.
 
3. В разделе **Загрузка и запуск серверного проекта** щелкните **Загрузить**. Извлеките сжатые файлы проекта на компьютер и откройте решение в Visual Studio.
 
## Локальное тестирование серверного проекта

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##Скачивание и запуск решения Xamarin.Forms

У вас несколько вариантов на выбор. Можно скачать решение на компьютер Mac и открыть его в Xamarin Studio или скачать его на компьютер Windows и открыть в Visual Studio. Можно также использовать обе эти среды и переключаться между ними. Рассмотрим эти варианты:

* Проще всего запустить проект iOS вашего решения на компьютере Mac. Можно использовать и Visual Studio на компьютере Windows, если хотите, но это немного сложнее, так как необходимо подключиться к компьютеру Mac с доступом к сети. Если вам это интересно, см. раздел [Установка Xamarin.iOS в Windows].
* Проект Android можно запустить на компьютере Windows или Mac.
* Проекты Windows можно запускать только с помощью Visual Studio на компьютере Windows.

Помня все это, продолжим.

 1. На компьютере Mac или Windows откройте [портал Azure] в окне браузера.
 2. В разделе **Загрузка и запуск проекта Xamarin.Forms** нажмите кнопку **Загрузить**.

    Будет скачан проект, содержащий клиентское приложение, подключенное к вашему мобильному приложению. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

 3. Извлеките скачанный проект и откройте его в Xamarin Studio или Visual Studio.

	![][9]

	![][8]

###Запуск проекта iOS

####В Xamarin Studio

1. Щелкните правой кнопкой мыши проект iOS и выберите **Назначить запускаемым проектом**.
2. В меню **Выполнить** щелкните **Начать отладку**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе iPhone.

####В Visual Studio
1. Щелкните правой кнопкой мыши проект iOS и выберите **Назначить запускаемым проектом**.
2. В меню **Сборка** выберите **Диспетчер конфигурации**. 
3. В диалоговом коне **Диспетчер конфигурации** установите флажки **Построить** и **Развернуть** для проекта iOS.
4. Нажмите клавишу **F5**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе iPhone.

В приложении введите содержательный текст, например _Изучение Xamarin_, и нажмите кнопку **+**.

![][10]

Отправится запрос POST к серверной части новой мобильной службы, размещенной в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются внутренним сервером мобильного приложения, а данные отображаются в списке.

> [AZURE.NOTE]Код, который обращается к серверной части мобильного приложения, можно найти в файле C# ToDoActivity.cs проекта переносимой библиотеки классов решения.

###Запуск проекта Android

####В Xamarin Studio

1. Щелкните правой кнопкой мыши проект Android и выберите **Назначить запускаемым проектом**.
2. В меню **Выполнить** щелкните **Начать отладку**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе Android.

####В Visual Studio
1. Щелкните правой кнопкой мыши проект Android и выберите **Назначить запускаемым проектом**.
4. В меню **Сборка** выберите **Диспетчер конфигурации**. 
5. В диалоговом коне **Диспетчер конфигурации** установите флажки **Построить** и **Развернуть** для проекта Android.
6. Нажмите клавишу **F5**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе Android.

В приложении введите содержательный текст, например _Изучение Xamarin_, и нажмите кнопку **+**.

![][11]

Отправится запрос POST к серверной части новой мобильной службы, размещенной в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются внутренним сервером мобильного приложения, а данные отображаются в списке.

> [AZURE.NOTE]Код, который обращается к серверной части мобильного приложения, можно найти в файле C# ToDoActivity.cs проекта переносимой библиотеки классов решения.


###Запуск проекта Windows

####В Visual Studio
1. Щелкните правой кнопкой мыши любой проект Windows и выберите **Назначить запускаемым проектом**.
4. В меню **Сборка** выберите **Диспетчер конфигурации**. 
5. В диалоговом коне **Диспетчер конфигурации** установите флажки **Построить** и **Развернуть** для выбранного проекта Windows.
6. Нажмите клавишу **F5**, чтобы выполнить сборку проекта и запустить приложение в эмуляторе Windows.

В приложении введите содержательный текст, например _Изучение Xamarin_, и нажмите кнопку **+**.
	
Отправится запрос POST к серверной части новой мобильной службы, размещенной в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются внутренним сервером мобильного приложения, а данные отображаются в списке.

![][12]
	
> [AZURE.NOTE]Код, который обращается к серверной части мобильного приложения, можно найти в файле C# ToDoActivity.cs проекта переносимой библиотеки классов решения.

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-dotnet-backend-xamarin-forms-get-started-preview/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Get started with offline data sync]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Get started with authentication]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Get started with push notifications]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[портал Azure]: https://portal.azure.com/
[JavaScript backend version]: ../partner-xamarin-mobile-services-ios-get-started.md
[Get started with data in app services using Visual Studio 2012]: ../app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Troubleshoot a mobile app .NET backend]: ../app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
[Установка Xamarin.iOS в Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
 

<!----HONumber=Oct15_HO3-->