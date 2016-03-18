<properties
	pageTitle="Приступая к работе с мобильными службами для приложений Xamarin для iOS | Microsoft Azure"
	description="Этот учебник поможет приступить к использованию мобильных служб Azure для разработки приложений Xamarin для iOS"
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="donnam"/>

# <a name="getting-started"> </a>Приступая к работе с мобильными службами

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

>[AZURE.TIP] Если вы не знакомы с разработкой приложений для мобильных устройств с помощью Microsoft Azure, [начните работу с мобильными приложениями Azure](app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md), а не с мобильными службами Azure. Мобильные приложения предоставляют вам [дополнительные преимущества](app-service-mobile-value-prop-migration-from-mobile-services-preview.md).

В данном учебнике описывается добавление облачных служб внутреннего сервера в приложение Xamarin iOS с помощью мобильных служб Azure. В этом учебнике вы создадите новую мобильную службу и простое приложение _To do list_, хранящее данные приложения в новой мобильной службе. В создаваемой мобильной службе с помощью Visual Studio используются поддерживаемые языки .NET для серверной бизнес-логики, а также для управления мобильной службой. Сведения о создании мобильной службы, которая позволит создавать в JavaScript серверную бизнес-логику, см. в подразделе [Серверная версия JavaScript] этого раздела.

>[AZURE.NOTE]В этом разделе показано, как создать новый проект мобильной службы с помощью классического портала Azure. Используя Visual Studio 2013 с обновлением 2, можно также добавить новый проект мобильной службы в существующее решение Visual Studio. Дополнительные сведения см. в разделе [Быстрый запуск: добавление мобильной службы (серверная версия .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Снимок экрана завершенного приложения приведен ниже:

![][0]


Завершение изучения этого учебника является необходимым условием для работы со всеми другими учебниками, посвященными приложениям Xamarin iOS.

>[AZURE.NOTE]Для работы с этим учебником требуется учетная запись Azure. Если у вас нет учетной записи, можно зарегистрироваться для получения бесплатной пробной версии Azure и получить до 10 бесплатных мобильных служб, которые можно использовать и после окончания пробного периода. Дополнительные сведения см. в разделе <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fru-RU%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Бесплатная пробная версия Azure</a>. <br />В этом учебнике требуется наличие <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Доступна бесплатная пробная версия.

## Создание новой мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Создание нового приложения Xamarin iOS

После создания мобильной службы вы можете следовать простым шагам быстрого запуска на классическом портале Azure, чтобы создать новое приложение или изменить существующее приложение и подключить его к своей мобильной службе.

В этом разделе предстоит скачать новое приложение Xamarin iOS и проект службы для мобильной службы.

1. На [классическом портале Azure] щелкните **Мобильные службы** и выберите только что созданную мобильную службу.

2. На вкладке быстрого запуска нажмите кнопку **Xamarin** в разделе **Выбор платформы**, а затем разверните раздел **Создание нового приложения Xamarin**.

   	![][6]

   	Здесь показаны три простых шага для создания приложения на Xamarin iOS, подключенного к вашей мобильной службе.

  	![][7]

3. Если вы еще не сделали этого, скачайте и установите <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> на локальном компьютере или виртуальной машине.

4. Скачайте и установите [Xcode] v4.4 или более позднюю версию и [Xamarin Studio]. Также можно использовать Xamarin для Visual Studio.

5. В разделе **Скачивание и публикация службы в облаке** выберите **iOS** и щелкните кнопку **Скачать**.

  	При этом загружается решение, содержащее проекты для мобильной службы и примера приложения _To do list_, которое подключается к вашей мобильной службе. Сохраните сжатый файл проекта на локальном компьютере и запомните путь к нему.

6. Скачайте профиль публикации, сохраните скачанный файл на локальный компьютер и запомните путь к нему.

## Тестирование мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Публикация мобильной службы

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Запуск приложения Xamarin iOS

Последний раздел учебника — построение и выполнение нового приложения.

1. Перейдите к проекту клиента в решении мобильной службы в Visual Studio или Xamarin Studio.

	![][8]

	![][9]

2. Нажмите кнопку **Запустить**, чтобы создать проект клиента и запустить приложение в эмуляторе iPhone.

3. В приложении введите осмысленный текст, например _Работа с учебником_, и щелкните значок «плюс» (**+**).

	![][10]

	Отправляет запрос POST в новую мобильную службу, размещенную в Azure. Данные из запроса вставляются в таблицу TodoItem. Элементы, хранящиеся в таблице, возвращаются мобильной службой, а данные отображаются в списке.

>[AZURE.NOTE]Вы можете просмотреть код, обеспечивающий доступ вашей мобильной службе к запросу, и ввести данные в файле C# QSTodoService.cs.


## Дальнейшие действия
Теперь, когда вы закончили быстрый запуск, узнайте, как выполнять дополнительные важные задачи в мобильных службах:

* [Приступая к работе с синхронизацией автономных данных] <br/>Узнайте, как использовать синхронизацию автономных данных, чтобы приложения были более быстрыми и надежными.

* [Приступая к работе с проверкой подлинности ] <br/>Дополнительные сведения о проверке подлинности учетных данных пользователей приложения с помощью поставщика удостоверений.

* [Приступая к работе с push-уведомлениями] <br/>Сведения об отправке в приложение простейших push-уведомлений.

* [Устранение неполадок серверной части в мобильных службах .NET] <br/>Узнайте, как определять и исправлять неполадки, которые могут возникнуть в серверной части мобильных служб для .NET.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Приступая к работе с синхронизацией автономных данных]: mobile-services-xamarin-ios-get-started-offline-data.md
[Приступая к работе с проверкой подлинности ]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Приступая к работе с push-уведомлениями]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[классическом портале Azure]: https://manage.windowsazure.com/
[Серверная версия JavaScript]: mobile-services-ios-get-started.md
[Устранение неполадок серверной части в мобильных службах .NET]: mobile-services-dotnet-backend-how-to-troubleshoot.md


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409

<!---HONumber=AcomDC_0211_2016-->