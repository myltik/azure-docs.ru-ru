<properties
	pageTitle="Добавление мобильных служб в существующее приложение (iOS) | Центр разработчиков для мобильных устройств"
	description="Узнайте, как приступить к работе с мобильными службами, чтобы использовать данные в приложении iOS."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/12/2015"
	ms.author="krisragh"/>

# Добавление мобильных служб к существующему приложению

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

В этом учебнике вы будете загружать существующее приложение, которое хранит данные в памяти, и изменять его для работы с мобильной службой Azure.

Прежде чем начинать работу с этим учебником, нужно обязательно завершить курс [Быстрое начало]. Вы будете повторно использовать мобильную службу, созданную в рамках курса "Быстрое начало".

##<a name="download-app"></a>Скачивание проекта GetStartedWithData

Этот учебник основан на [приложении iOS GetStartedWithData]. Приложение идентично созданному в курсе [Быстрое начало], за исключением того, что добавленные элементы хранятся в памяти.

Скачайте [приложение iOS GetStartedWithData]. В Xcode откройте проект и просмотрите файл **TodoService.m**. Восемь комментариев **// TODO** указывают шаги, которые необходимо выполнить, чтобы это приложение заработало.

##<a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильной службы

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Тестирование приложения

1. В Xcode нажмите кнопку **Выполнить**, чтобы запустить приложение. Добавляйте элементы в список задач, вводя текст и щелкая **+**.

2. Войдите на портал управления Azure, выберите пункт **Мобильные службы**, а затем щелкните свою мобильную службу. Откройте вкладку **Данные**, а затем щелкните **Обзор**. Обратите внимание, что таблица **TodoItem** теперь содержит данные со значениями идентификаторов, которые созданы мобильными службами, и в таблицу были автоматически добавлены столбцы, соответствующие классу TodoItem в приложении.

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png







[8]: ./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784


[Быстрое начало]: ../mobile-services-javascript-backend-ios-get-started.md
[приложение iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[приложении iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!--HONumber=54--> 