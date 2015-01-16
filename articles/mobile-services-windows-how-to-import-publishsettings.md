<properties urlDisplayName="Import your subscription publish settings file in Visual Studio 2013" pageTitle="Импорт файла параметров публикации подписки в Visual Studio 2013 | Мобильные службы" metaKeywords="Azure import publishsettings, mobile services" description="Узнайте, как импортировать файл параметров публикации подписки для приложения мобильных служб Azure в Visual Studio 2013." title="Import your subscription publish settings file in Visual Studio 2013" documentationCenter="Mobile" services="mobile-services" solutions="" manager="dwrede" editor="" videoId="" scriptId="" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Импорт файла параметров публикации подписки в Visual Studio 2013

Перед созданием мобильной службы необходимо импортировать в Visual Studio файл параметров публикации из подписки Azure. Это позволяет Visual Studio подключиться к Azure от вашего имени.  

>[WACOM.NOTE]Начиная с обновления 2 для Visual Studio 2013, вам больше не нужно использовать файл параметров публикации. Visual Studio может подключиться к Azure напрямую с помощью учетных данных, предоставляемых вами.

1. В Visual Studio 2013 откройте обозреватель решений, щелкните правой кнопкой проект, затем выберите команду **Добавить** и **Подключенная служба...** 

	![add connected service](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. В диалоговом окне диспетчера служб нажмите **Создать службу...**, выберите **Импорт...** из **Подписки** в диалоговом окне "Создание мобильной службы".  

	![create a new mobile service from VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. В подписках Azure для импорта нажмите **Загрузить файл подписки**, войдите в учетную запись Azure (если требуется), нажмите **Сохранить**, когда обозреватель запросит сохранить файл.

	![download subscription file in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>Примечание.</strong> <p>Окно входа в систему отображается в браузере, который может находиться за окном Visual Studio. Запишите себе, где вы сохранили загруженный файл .publishsettings. Этот шаг можно пропустить, если проект уже подключен к вашей подписке Azure.</p></div> 

4. Нажмите **Обзор**, найдите расположение, где вы сохранили PUBLISHSETTINGS-файл, выберите его, а затем нажмите **Открыть** и **Импортировать**. 

	![import subscription in VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio импортирует данные, необходимые для подключения к вашей подписке Azure. Если в вашей подписке уже есть одна или несколько существующих мобильных служб, отображаются имена служб. 

	<div class="dev-callout"><strong>Примечание о безопасности.</strong> <p>После импорта настроек публикации удалите скачанный PUBLISHSETTINGS-файл, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи. Защитите файл, если планируете сохранить его для использования в других подключенных проектах приложений.</p></div>

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on/
[Отправка страницы приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/
[Авторизация пользователей с помощью сценариев]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js/

[Портал управления Azure]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
