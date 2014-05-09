<properties linkid="develop-mobile-how-to-guides-import-publish-settings" urlDisplayName="Импорт файла параметров публикации подписки в Visual Studio 2013" pageTitle="Импорт файла параметров публикации подписки в Visual Studio 2013 | Мобильные службы" metaKeywords="импорт параметров публикации Azure, мобильные службы" description="Узнайте, как импортировать файл параметров публикации подписки для приложения мобильных служб Azure в Visual Studio 2013." title="Импорт файла параметров публикации подписки в Visual Studio 2013" documentationCenter="Mobile" services="" solutions="" manager="" editor="" videoId="" scriptId="" authors="" />

# Импорт файла параметров публикации подписки в Visual Studio 2013

Перед созданием мобильной службы необходимо импортировать в Visual Studio файл параметров публикации из подписки Azure. Это позволяет Visual Studio подключиться к Azure от вашего имени.  


1. В Visual Studio 2013 откройте обозреватель решений, щелкните правой кнопкой проект, затем выберите команду **Добавить** и **Подключенная служба...**. 

	![добавление подключенной службы](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2. В диалоговом окне диспетчера служб нажмите **Создать службу...**, выберите **&lt;Импорт...&gt;** из **Подписки** в диалоговом окне "Создание мобильной службы".  

	![создание новой мобильной службы из VS 2013](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. В подписках Azure для импорта нажмите **Загрузить файл подписки**, войдите в учетную запись Azure (если требуется), нажмите **Сохранить**, когда обозреватель запросит сохранить файл.

	![загрузка файла подписки в VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)

	<div class="dev-callout"><strong>Примечание.</strong> <p>Окно входа в систему отображается в браузере, который может находиться позади окна Visual Studio. Запишите себе, где вы сохранили загруженный файл .publishsettings. Этот шаг можно пропустить, если проект уже подключен к вашей подписке Azure.</p></div> 

4. Нажмите **Обзор**, найдите расположение, где вы сохранили файл .publishsettings, выберите его, затем нажмите **Открыть** и **Импортировать**. 

	![импорт подписки в VS](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)

	Visual Studio импортирует данные, необходимые для подключения к вашей подписке Azure. Если в вашей подписке уже есть одна или несколько существующих мобильных служб, отображаются имена служб. 

	<div class="dev-callout"><strong>Примечание о безопасности.</strong> <p>После импорта настроек публикации, удалите загруженный файл .publishsettings, поскольку он содержит информацию, которая может использоваться другими пользователями для доступа к вашей учетной записи. Защитите файл, если планируете сохранить его для использования в других подключенных проектах приложений.</p></div>

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Единый вход для приложений Магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on/
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js/

[Портал управления Azure]: https://manage.windowsazure.com/

