<properties
	pageTitle="Начало работы с мобильными приложениями в Xamarin iOS"
	description="Начало работы с Xamarin iOS для выполнения сборки мобильного приложения Azure со службой приложений Azure."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Создание приложения Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

В этом учебнике показано, как добавить облачную внутреннюю службу в приложение Xamarin iOS с помощью мобильного приложения Azure. В этом учебнике вы создадите новую службу .NET и простое приложение _Список задач_, в котором хранятся данные приложения на внутреннем сервере .NET.

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас нет учетной записи, вы можете зарегистрироваться для получения пробной версии Azure и получить до 10 бесплатных мобильных приложений, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure до регистрации учетной записи Azure, перейдите в раздел [Пробная версия службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), где можно сразу создать кратковременное начальное мобильное приложение в службе приложений. Никаких кредитных карт и обязательств.

## Создание внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Создание нового приложения Xamarin iOS

После создания внутреннего сервера мобильного приложения вы можете следовать простым шагам краткого руководства на портале Azure, чтобы создать новое приложение или изменить существующее и подключить его к внутреннему серверу мобильного приложения.

В этом разделе вы скачаете новое приложение Xamarin iOS и проект службы для внутреннего сервера мобильного приложения.

1. На портале Azure щелкните **Мобильное приложение**, а затем выберите только что созданный внутренний сервер мобильного приложения.

2. В верхней части колонки щелкните **Добавить клиент** и разверните **Xamarin iOS**.

	![][6]

	Отобразятся три простых шага для создания приложения Xamarin iOS, подключенного к внутреннему серверу мобильного приложения.

3. Если вы еще этого не сделали, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.  

4. Загрузите и установите [Xcode] 4.4 или более поздней версии, а также [Xamarin Studio]. Вы также можете использовать Xamarin для Visual Studio.

5. В разделе **Загрузка и публикация службы в облаке** щелкните **Скачать**.

 При этом скачивается решение, содержащее проекты для внутреннего сервера мобильного приложения и примера приложения _Список задач_, которое подключается к внутреннему серверу мобильного приложения. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Публикация внутреннего сервера мобильного приложения

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Запуск приложения Xamarin iOS

Последний раздел учебника - построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении внутреннего сервера мобильного приложения в Visual Studio или Xamarin Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запустить**, чтобы создать клиентский проект и запустить приложение в эмуляторе iPhone.

3. В приложении введите содержательный текст, например _Завершение работы с учебником_, и щелкните значок "плюс" (**+**).

	![][10]

	При этом на новый внутренний сервер мобильного приложения, размещенный в Azure, отправляется запрос POST. Данные из запроса вставляются в таблицу TodoItem. Элементы, сохраненные в таблице, возвращаются внутренним сервером мобильного приложения, а данные отображаются в списке.

>[AZURE.NOTE]Вы можете просмотреть код, который получает доступ к внутреннему серверу мобильного приложения для запроса и вставки данных в файл C# QSTodoService.cs.


<!-- Anchors. -->
[Начало работы с внутренними серверами мобильных приложений]:#getting-started
[Создание внутреннего сервера мобильного приложения]:#create-new-service
[Дальнейшие действия]:#next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Начало работы с автономной синхронизацией данных]: app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Начало работы с проверкой подлинности]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-users.md
[Начало работы с push-уведомлениями]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Пакет SDK для мобильных приложений]: http://go.microsoft.com/fwlink/?LinkId=257545
[Портал Azure]: https://portal.azure.com/
[Серверная версия JavaScript]: partner-xamarin-mobile-services-ios-get-started.md
[Начало работы с данными в службах приложений, использующих Visual Studio 2012]: app-service-mobile-windows-store-dotnet-get-started-data-vs2012-preview.md
[Устранение неполадок с внутренним сервером .NET мобильного приложения]: app-service-mobile-dotnet-backend-how-to-troubleshoot-preview.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin для Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!--HONumber=49-->