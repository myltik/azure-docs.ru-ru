<properties
	pageTitle="Настройка проверки подлинности Google для приложения служб приложений"
	description="Настройка проверки подлинности Google для приложения служб приложений."
    services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="mahender"/>

# Как настроить приложение службы приложений для использования имени для входа Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

В этом разделе показано, как настроить службу приложений Azure для использования Google в качестве поставщика проверки подлинности.

Чтобы выполнить процедуру, описанную в этом разделе, необходимо иметь учетную запись Google с проверенным адресом электронной почты. Чтобы создать новую учетную запись Google, перейдите по ссылке [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Регистрация приложения с помощью Google

1. Войдите на [портал Azure] и перейдите к своему приложению. Скопируйте **URL-адрес**, который вы будете использовать позже для настройки приложения Google.

2. Перейдите на веб-сайт [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), войдите с помощью учетных данных Google, выберите команду **Create Project** (Создать проект), укажите имя проекта в поле **Project name** (Имя проекта), а затем нажмите кнопку **Create** (Создать).

3. В разделе **Social APIs** (Интерфейсы API для соцсетей) щелкните **Google+ API**, а потом — **Enable** (Включить).

4. В области навигации слева щелкните **Credentials** (Учетные данные) > **OAuth consent screen** (Экран согласия OAuth), выберите свой **адрес электронной почты**, заполните поле **Product Name** (Имя продукта) и щелкните **Save** (Сохранить).

5. На вкладке **Credentials** (Учетные данные) щелкните **Create credentials** (Создать учетные данные) > **OAuth client ID** (Идентификатор клиента OAuth), а затем выберите **Web application** (Веб-приложение).

6. Вставьте скопированный ранее **URL-адрес** службы приложений в поле **Authorized JavaScript Origins** (Разрешенные источники JavaScript) и вставьте универсальный код ресурса (URI) перенаправления в поле **Authorized Redirect URI** (Разрешенный код URI перенаправления). Универсальный код ресурса (URI) перенаправления — это URL-адрес приложения, дополненный путем _/.auth/login/google/callback_. Например, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Убедитесь, что используете схему HTTPS. Затем щелкните **Создать**.

7. На следующем экране запишите идентификатор клиента и секрет клиента.


    > [AZURE.IMPORTANT]
	Секрет клиента — это важные учетные данные безопасности. Не сообщайте этот секрет никому и не раскрывайте его в клиентском приложении.


## <a name="secrets"> </a>Добавление данных Google в приложение

8. Снова вернитесь на [портал Azure] и перейдите к своему приложению. Щелкните **Параметры**, а затем **Аутентификация или авторизация**.

9. Если функция аутентификации или авторизации не включена, установите переключатель в положение **Вкл**.

10. Щелкните **Google**. Вставьте значения идентификатора и секретного кода приложения, полученные ранее, и, при необходимости, включите любые области, которые требуются приложению. Нажмите кнопку **ОК**.

    ![][1]

	По умолчанию служба приложений обеспечивает проверку подлинности, но не ограничивает авторизованный доступ к содержимому сайта и API. Авторизация пользователей должна быть включена в код приложения.

17. (Необязательно) Чтобы предоставить доступ к узлу только для пользователей, прошедших проверку подлинности в Google, установите для параметра **Действие, выполняемое, если запрос не прошел проверку подлинности** значение **Google**. В этом случае все запросы, не прошедшие проверку подлинности, направляются для проверки подлинности с помощью Google.

12. Щелкните **Сохранить**.

Теперь вы готовы использовать Google для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

.<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[портал Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0824_2016-->