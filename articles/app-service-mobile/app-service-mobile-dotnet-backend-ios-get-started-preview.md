<properties
	pageTitle="Начало работы с мобильными приложениями для iOS службы приложений Azure"
	description="Следуйте указаниям этого учебника, чтобы приступить к использованию службы приложений Azure для разработки приложений для iOS."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="ysxu"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="get-started-article"
	ms.date="02/20/2015"
	ms.author="yuaxu"/>

# <a name="getting-started"> </a>Создание приложения для iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]

В этом учебнике показано, как создать приложение для iOS с использованием мобильных приложений службы приложений Azure. Будет создана новая серверная часть мобильного приложения и простое приложение _To do list_, которое хранит данные приложения. Учебник использует .NET и Visual Studio для логики на стороне сервера.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Если вы хотите ознакомиться со службой приложений Azure до создания учетной записи, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где вы можете быстро создать краткосрочное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## <a name="create-new-service"> </a>Создание серверной части мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового приложения для iOS

После создания серверной части мобильного приложения вы можете следовать простым шагам быстрого запуска на портале предварительной версии Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к серверной части вашего мобильного приложения.

1. На портале Azure щелкните **Мобильное приложение**, а затем щелкните только что созданную серверную часть мобильного приложения.

2. В верхней части колонки нажмите кнопку «Добавить клиент» и разверните iOS.

	![][6]

	Здесь показаны шаги для создания приложения для iOS, подключенного к серверной части вашего мобильного приложения.

3. Если вы еще не сделали этого, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. Загрузите и установите [Xcode] 4.4 или более поздней версии, а также [Xamarin Studio]. Вы также можете использовать Xamarin для Visual Studio.

5. В разделе **Скачивание и публикация службы в облаке** щелкните элемент **Скачать**.

 Загрузится решение, содержащее проекты для серверной части мобильной службы и примера приложения _Список заданий_, которое подключается к серверной части вашей мобильной службы. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения для iOS

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

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
 

<!---HONumber=62-->