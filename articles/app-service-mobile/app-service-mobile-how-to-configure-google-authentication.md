<properties
	pageTitle="Настройка проверки подлинности Google для приложения служб приложений"
	description="Настройка проверки подлинности Google для приложения служб приложений."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Настройка приложения для использования имени для входа Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как настроить мобильные приложения для использования Google в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Войдите на [портал управления Azure] и перейдите к своему мобильному приложению. Скопируйте свой **URL-адрес**. Позже вы используете его для приложения Google.
 
2. Щелкните **Параметры**, **Проверка подлинности пользователя** и **Google**. Скопируйте **URI перенаправления**. Он будет использован для настройки приложения Google.

3. Перейдите на веб-сайт [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

4. На панели навигации слева щелкните **API и авторизация**, а затем в разделе **API социальных сетей** щелкните **API Google+** > **Включить API**.

5. Щелкните **API и авторизация** > **Учетные данные** > **Экран согласия OAuth**, а затем выберите **электронный адрес**, введите **имя продукта** и нажмите кнопку **Сохранить**.

6. На вкладке **Учетные данные** щелкните **Добавить учетные данные** > **Идентификатор клиента OAuth 2.0**, а затем выберите **Веб-приложение**.

7. Вставьте **URL-адрес** мобильного приложения, скопированный ранее, в поле **Авторизованные источники JavaScript** и вставьте **URI перенаправления**, скопированный ранее, в поле **Авторизованный URI перенаправления**. Этот URI — шлюз мобильного приложения, дополненный путем _/signin-twitter_. Например, `https://contosogateway.azurewebsites.net/signin-google`. Убедитесь, что используете схему HTTPS. Затем щелкните **Создать**.

8. На следующем экране запишите идентификатор клиента и секрет клиента.

    > [AZURE.IMPORTANT]Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.

9. На [портале управления Azure] в колонке настроек Google для вашего мобильного приложения вставьте идентификатор клиента и секрет клиента, значения которых были получены ранее. Нажмите кнопку **Сохранить**.

     ![][1]

Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[портал управления Azure]: https://portal.azure.com/
[портале управления Azure]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO2-->