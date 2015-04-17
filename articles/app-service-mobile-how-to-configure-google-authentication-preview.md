<properties 
	pageTitle="Настройка проверки подлинности Google для приложения служб приложений"
	description="Настройка проверки подлинности Google для приложения служб приложений." 
    services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Настройка приложения для использования имени для входа Google

В этом разделе показано, как настроить в службах приложений Azure поставщика проверки подлинности Google. 

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите на сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Регистрация приложения с помощью Google

1. Перейдите на веб-сайт <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google API</a>, войдите по учетным данным учетной записи Google, щелкните **Создать проект**, укажите **Имя проекта**, затем щелкните **Создать**.

2. На левой панели навигации щелкните **API и проверка подлинности**. Затем откройте страницу **Условия использования**. Укажите **Адрес электронной почты** и введите **Имя продукта**. Нажмите кнопку **Сохранить**. 

3. Также в меню **API и проверка подлинности** выберите **API** и включите **Google+ API**.

4. В том же меню **API и проверка подлинности** выберите **Учетные данные**, а затем - **Создать идентификатор клиента**.

5. Выберите **Веб-приложение**. Введите URL-адрес шлюза служб приложений в поле **Авторизованные источники JavaScript**, а затем замените созданный URL-адрес в поле **Авторизованный URI перенаправления** на URL-адрес шлюза с добавлением пути _/signin-google_. Например, `https://contoso.azurewebsites.net/signin-google`. Убедитесь, что используете схему HTTPS. Затем щелкните **Создать идентификатор клиента**.

     ![][0]

6. На следующей странице, в разделе **Идентификатор клиента для веб-приложений** запишите значения полей **Идентификатор клиента** и **Секрет клиента**. 

    > [AZURE.IMPORTANT] Секрет клиента - это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.


## <a name="secrets"> </a>Добавление сведений Google в мобильное приложение

7. Войдите на [портал управления Azure] и перейдите к шлюзу служб приложений.

8. В разделе **Параметры** последовательно выберите пункты **Удостоверение**, **Google**. Вставьте значения идентификатора и секрета клиента, полученные ранее. Нажмите кнопку **Сохранить**.

     ![][1]


Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="related-content"> </a>Похожий контент

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-how-to-configure-google-authentication/app-service-google-redirect.png
[1]: ./media/app-service-how-to-configure-google-authentication/app-service-google-settings.png

<!-- URLs. -->

[API-интерфейсы Google]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Портал управления Azure]: https://portal.azure.com/

<!--HONumber=49-->