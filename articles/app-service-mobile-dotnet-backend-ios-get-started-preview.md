<properties
	pageTitle="Начало работы с мобильными приложениями iOS службы приложений Azure"
	description="Пройдите этот учебник, чтобы начать использовать службу приложений Azure для разработки приложений iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="yuaxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Создание приложения iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

В этом учебнике показано, как создать приложение iOS с помощью мобильных приложений службы приложений Azure. Вы создадите внутренний сервер мобильного приложения и добавите простое приложение _Список задач_, которое хранит данные приложения. В учебнике используется .NET и Visual Studio для серверной логики.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure до регистрации учетной записи Azure, перейдите в раздел [Пробная версия службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где можно сразу создать кратковременное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## <a name="create-new-service"> </a>Создание внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового приложения для iOS

После создания внутреннего сервера мобильного приложения вы можете следовать простым шагам быстрого запуска на портале предварительной версии Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к внутреннему серверу мобильных служб.

1. На портале Azure щелкните **Мобильное приложение**, а затем выберите только что созданный внутренний сервер мобильного приложения.

2. В верхней части колонки щелкните "Добавить клиент" и разверните iOS.

	![][6]

	Будут показаны шаги для создания приложения iOS, подключенного к внутреннему серверу мобильного приложения.

3. Если вы этого еще не сделали, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальный компьютер или виртуальную машину. 

4. Загрузите и установите [Xcode] 4.4 или более поздней версии, а также [Xamarin Studio]. Вы также можете использовать Xamarin для Visual Studio.

5. В разделе **Загрузка и публикация службы в облаке** щелкните **Скачать**.

 При этом скачивается решение, содержащее проекты для внутреннего сервера мобильного приложения и примера приложения _Список задач_, которое подключается к внутреннему серверу мобильного приложения. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app-preview](../includes/app-service-mobile-ios-run-app-preview.md)]



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/app-service-mobile-dotnet-backend-ios-get-started-preview/ios-quickstart.png

[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png

[Портал управления]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532

<!--HONumber=49-->