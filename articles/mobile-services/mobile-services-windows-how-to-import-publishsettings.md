<properties 
	pageTitle="Импорт файла параметров публикации подписки в Visual Studio 2013 | Мобильные службы" 
	description="Узнайте, как импортировать файл параметров публикации подписки для приложения мобильных служб Azure в Visual Studio 2013." 
	documentationCenter="" 
	services="mobile-services" 
	manager="dwrede" 
	editor="" 
	authors="ggailey777"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Импорт файла параметров публикации подписки в Visual Studio 2013

Перед созданием мобильной службы необходимо импортировать в Visual Studio файл параметров публикации из подписки Azure. Это позволяет Visual Studio подключиться к Azure от вашего имени.

>[AZURE.NOTE]Начиная с обновления 2 для Visual Studio 2013, вам больше не нужно использовать файл параметров публикации. Visual Studio может подключиться к Azure напрямую с помощью учетных данных, предоставляемых вами.

1. В Visual Studio 2013 откройте обозреватель решений, щелкните правой кнопкой проект, затем выберите команду **Добавить** и **Подключенная служба...** 

	![добавление подключенной службы](./media/mobile-services-windows-how-to-import-publishsettings/mobile-add-connected-service.png)

2. В диалоговом окне диспетчера служб щелкните **Создать службу...**, а затем выберите **&lt;Import...&gt;** в разделе **Подписка** в диалоговом окне "Создание мобильной службы".

	![создание новой мобильной службы из VS 2013](./media/mobile-services-windows-how-to-import-publishsettings/mobile-create-service-from-vs2013.png)

3. В подписках Azure для импорта нажмите **Скачать файл подписки**, войдите в учетную запись Azure (если требуется), щелкните кнопку **Сохранить**, когда обозреватель запросит сохранить файл.

	![загрузка файла подписки в VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription.png)

	> [AZURE.NOTE]Окно входа в систему отображается в браузере, который может находиться за окном Visual Studio. Запишите себе, где вы сохранили загруженный файл .publishsettings. Этот шаг можно пропустить, если проект уже подключен к вашей подписке Azure.

4. Нажмите **Обзор**, найдите расположение, где вы сохранили файл .publishsettings, выберите его, затем нажмите **Открыть** и **Импортировать**.

	![импорт подписки в VS](./media/mobile-services-windows-how-to-import-publishsettings/mobile-import-azure-subscription-2.png)

	Visual Studio импортирует данные, необходимые для подключения к вашей подписке Azure. Если в вашей подписке уже есть одна или несколько существующих мобильных служб, отображаются имена служб.

	> [AZURE.NOTE]После импорта настроек публикации удалите скачанный PUBLISHSETTINGS-файл, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи. Защитите файл, если планируете сохранить его для использования в других подключенных проектах приложений.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/how-to-guides/register-for-single-sign-on/
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-users-js/

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54--> 