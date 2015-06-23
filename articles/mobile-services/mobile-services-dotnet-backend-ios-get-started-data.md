<properties
	pageTitle="Добавление мобильных служб в существующее приложение iOS"
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
	ms.date="03/03/2015"
	ms.author="krisragh"/>

# Добавление мобильных служб в существующее приложение

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

В этом учебнике вы будете загружать существующее приложение, которое хранит данные в памяти, и изменять его для работы с мобильной службой Azure.

Прежде чем начинать работу с этим учебником, нужно обязательно завершить курс [Быстрое начало]. Вы будете повторно использовать мобильную службу, созданную в разделе "Быстрое начало".


##<a name="download-app"></a>Загрузка проекта GetStartedWithData

Этот учебник основан на [приложении iOS GetStartedWithData]. Приложение идентично созданному в [Быстром начале], за исключением того, что добавленные элементы хранятся в памяти.

Загрузите [приложение iOS GetStartedWithData]. В Xcode откройте проект и просмотрите файл **TodoService.m**. Восемь комментариев **// TODO** указывают шаги, которые необходимо выполнить, чтобы это приложение заработало.

##<a name="update-app"></a>Обновление приложения для доступа к данным с помощью мобильной службы

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Тестирование приложения

1. В Xcode щелкните кнопку **Запустить** для запуска приложения. Добавляйте элементы в список дел, вводя текст и нажимая **+**.

2. Удостоверьтесь, что изменения сохранены в базе данных Azure. Проверьте базу данных с помощью портала управления Azure или обозревателя объектов SQL Server в Visual Studio.

3. Чтобы проверить базу данных с помощью портала, на странице панели мониторинга для мобильной службы щелкните имя базы данных, нажмите кнопку "Управление" для управления ею и войдите. Выполните следующий запрос, но используйте имя мобильной службы вместо `todolist`:

```
        SELECT * FROM [todolist].[todoitems]
```

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Publish the mobile service to Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->

[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[JavaScript backend version]: /develop/mobile/tutorials/get-started-with-data-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784

[Быстрое начало]: mobile-services-dotnet-backend-ios-get-started.md
[Быстром начале]: mobile-services-dotnet-backend-ios-get-started.md
[приложение iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[приложении iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!--HONumber=54--> 