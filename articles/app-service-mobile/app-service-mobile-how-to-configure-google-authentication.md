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

# Как настроить приложение службы приложений для использования имени для входа Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Google в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Регистрация приложения с помощью Google

1. Войдите на [портал управления Azure] и перейдите к своему приложению. Скопируйте свой **URL-адрес**. Он будет использован для настройки приложения Google.
 
2. Перейдите на веб-сайт [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

3. На панели навигации слева щелкните **API & Auth** (API и авторизация Auth), а затем в разделе **Social APIs** (API социальных сетей) щелкните **Google+ API** (API Google+) > **Enable API** (Включить API).

4. Щелкните **API & Auth** (API и авторизация) > **Credentials** (Учетные данные) > **OAuth consent screen** (Экран согласия OAuth), а затем выберите **Email address** (Электронный адрес), заполните поле **Product Name** (Имя продукта) и нажмите кнопку **Save** (Сохранить).

5. На вкладке **Credentials** (Учетные данные) щелкните **Add credentials** (Добавить учетные данные) > **OAuth 2.0 client ID** (Идентификатор клиента OAuth 2.0), а затем выберите **Web application** (Веб-приложение).

6. Вставьте **URL-адрес** службы приложений, скопированный ранее, в поле **Authorized JavaScript Origins** (Авторизованные источники JavaScript) и вставьте **универсальный код ресурса (URI) перенаправления**, скопированный ранее, в поле **Authorized Redirect URI** (Авторизованный URI перенаправления). Универсальный код ресурса (URI) перенаправления — это URL-адрес приложения, дополненный путем _/.auth/login/google/callback_. Например, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Убедитесь, что используете схему HTTPS. Затем щелкните **Создать**.


	> [AZURE.NOTE]Если вместо функции аутентификации или авторизации службы приложений используется шлюз службы приложений, то в URL-адресе перенаправления используется URL-адрес шлюза, дополненный путем _/signin-google_.


7. На следующем экране запишите идентификатор клиента и секрет клиента.


    > [AZURE.IMPORTANT]Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.


## <a name="secrets"> </a>Добавление данных Google в приложение


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Google**. Paste in the values you obtained earlier and click **Save**.


8. Вернитесь на [портал управления Azure] и перейдите к своему приложению. Щелкните **Параметры**, а затем **Аутентификация или авторизация**.

9. Если функция аутентификации или авторизации не включена, установите переключатель в положение **Вкл**.

10. Щелкните **Google**. Вставьте значения идентификатора и секретного кода приложения, полученные ранее, и, при необходимости, включите любые области, которые требуются приложению. Нажмите кнопку **ОК**.

    ![][1]
	
11. По умолчанию служба приложений предоставляет вход, но не ограничивает доступ к содержимому сайта и интерфейсов API: эти функции должны осуществляться кодом приложения. Если вы хотите полностью защитить сайт с помощью службы входа Google, выберите из раскрывающегося списка **Действие, выполняемое, если запрос не прошел проверку подлинности** пункт **Google**. Тогда аутентификация всех запросов будет обязательна. Запросы, не прошедшие аутентификацию, будут перенаправляться для входа с помощью Google.

12. Щелкните **Сохранить**.

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
 

<!---HONumber=Nov15_HO4-->