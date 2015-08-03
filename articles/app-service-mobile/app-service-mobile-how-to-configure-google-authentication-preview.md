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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Настройка приложения для использования имени для входа Google

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

В этом разделе показано, как настроить в службах приложений Azure поставщика проверки подлинности Google.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать учетную запись Google, перейдите по ссылке <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Регистрация приложения с помощью Google

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google apis</a>, войдите с учетными данными Google, выберите **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

2. В левой панели навигации щелкните **API и проверка подлинности**. Затем щелкните **экран согласия**. Укажите **адрес электронной почты** и введите **имя продукта**. Нажмите кнопку **Сохранить**.

3. Также в разделе **API и проверка подлинности** выберите **API** и включите **Google+ API**.

4. В том же разделе **API и проверка подлинности** выберите пункт **Учетные данные**, а затем выберите команду **Создать идентификатор клиента**.

5. Выберите **Веб-приложение**. Введите URL-адрес шлюза служб приложений в поле **Авторизованные источники JavaScript**, а затем замените созданный URL-адрес в поле **Авторизованный URI перенаправления** на URL-адрес шлюза с добавлением пути _/signin-google_. Например, `https://contoso.azurewebsites.net/signin-google`. Убедитесь, что используете схему HTTPS. Затем щелкните **Создать идентификатор клиента**.

     ![][0]

6. На следующем экране в разделе **Идентификатор клиента для веб-приложений** запишите значения полей **Идентификатор клиента** и **Секрет клиента**.

    > [AZURE.IMPORTANT]Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.


## <a name="secrets"> </a>Добавление сведений Google в мобильное приложение

7. Войдите на [портал управления Azure] и перейдите к своему шлюзу служб приложений.

8. В разделе **Параметры** последовательно выберите пункты **Удостоверение** и **Google**. Вставьте значения идентификатора и секрета клиента, полученные ранее. Нажмите кнопку **Сохранить**.

     ![][1]


Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/app-service-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[портал управления Azure]: https://portal.azure.com/
 

<!---HONumber=July15_HO4-->