<properties pageTitle="Регистрация для проверки подлинности Майкрософт - мобильные службы" metaKeywords="Регистрация приложения Azure, проверка подлинности Майкрософт в Azure, проверка подлинности приложения, проверка подлинности мобильных служб" description="Узнайте, как зарегистрироваться для проверки подлинности Майкрософт в приложении мобильных служб Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Регистрация приложений для использования входа в учетную запись Майкрософт" authors="" />

# Зарегистрируйте свои приложения для входа с использованием учетной записи Майкрософт

В этом разделе показано, как зарегистрировать приложения для использования Live Connect в качестве поставщика проверки подлинности для мобильных служб Azure. 

>[WACOM.NOTE]При необходимости одновременно обеспечить проверку подлинности на стороне клиента для единого входа или push-уведомлений в приложении Магазина Windows рассмотрите также возможность регистрации своего приложения в Магазине Windows. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on">Регистрация приложений Магазина Windows для проверки подлинности Windows Live Connect</a>.


1. Перейдите к странице <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Мои приложения</a> в Центре разработки Live Connect и, если необходимо, выполните вход с использованием учетной записи Майкрософт. 

2. Щелкните **Создать приложение**, введите **Имя приложения** и нажмите кнопку **Принимаю**.

   	![][1] 

   	Эта процедура регистрирует приложение в Live Connect.

10. Щелкните **Страница параметров приложения**, затем **Параметры API** и запишите значения **Идентификатор клиента** и **Секрет клиента**. 

   	![][2]

    <div class="dev-callout"><b>Примечание о безопасности</b>
	<p>Секрет клиента — это важные учетные данные безопасности. Не сообщайте никому этот секрет клиента и не распространяйте его вместе с вашим приложением.</p>
    </div>

11. В поле **Домен перенаправления**введите URL-адрес и нажмите кнопку **Сохранить**.

Теперь вы готовы использовать учетную запись Майкрософт для проверки подлинности в вашем приложении, предоставляя мобильным службам код клиента и секрет клиента приложения.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-live-connect-app-api-settings.png
<!-- URLs. -->
[Единый вход для приложений для магазина Windows с использованием Live Connect]: /ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on/
[Отправить страницу приложения]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Мои приложения]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started/
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-dotnet/
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet/
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-dotnet/
[JavaScript и HTML]: /ru-ru/develop/mobile/tutorials/get-started-with-users-js/

[Портал управления Azure]: https://manage.windowsazure.com/

