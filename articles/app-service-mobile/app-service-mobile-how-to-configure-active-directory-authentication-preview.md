<properties 
	pageTitle="Настройка проверки подлинности Azure Active Directory для приложения служб приложений" 
	description="Узнайте, как настроить проверку подлинности Azure Active Directory для приложения служб приложений." 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/27/2015" 
	ms.author="mahender"/>

# Настройка приложения для использования имени для входа Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

В этом разделе показано, как настроить службы приложений Azure для использования Azure Active Directory в качестве поставщика проверки подлинности.

## <a name="register"> </a>Регистрация приложения в Azure Active Directory

1. Войдите на [портал управления предварительной версии Azure] и перейдите к своему мобильному приложению.

2. В разделе **Параметры** щелкните **Проверка подлинности пользователя**, затем щелкните **Azure Active Directory**. Скопируйте указанные на странице **URL-адрес приложения** и **URL-адрес ответа**. Они понадобятся вам позже. Убедитесь, что в **URL-адресе приложения** и **URL-адресе ответа** используется схема HTTPS.

    ![][1]

3. Войдите на [портал управления Azure] и перейдите в **Active Directory**.

    ![][2]

4. Выберите свой каталог, а затем откройте вкладку **Приложения** в верхней части окна. Щелкните **Добавить** в нижней части окна, чтобы зарегистрировать новое приложение.

5. Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

6. В мастере добавления приложения введите **Имя** для приложения и выберите тип **Веб-приложение и/или веб-API**. Затем щелкните "Далее".

7. В поле **URL-адрес входа** вставьте идентификатор приложения, скопированный из параметров поставщика удостоверений Active Directory вашего мобильного приложения. Введите тот же уникальный идентификатор ресурса в поле **URI кода приложения**. Затем щелкните "Далее".

8. После добавления приложения щелкните вкладку **Настройка**. Измените **URL-адрес ответа** в разделе **Единый вход** на URL-адрес ответа для мобильного приложения, скопированный ранее. Это должен быть шлюз мобильного приложения плюс _/signin-microsoft_. Например, `https://contosogateway.azurewebsites.net/signin-aad`. Убедитесь, что используете схему HTTPS.

    ![][3]

9. Щелкните **Сохранить**. Затем скопируйте **идентификатор клиента** для приложения.

## <a name="secrets"> </a>Добавление сведений об Azure Active Directory в мобильное приложение

1. Вернитесь на портал управления предварительной версии и перейдите к колонке настройки **Azure Active Directory** для мобильного приложения. Вставьте параметр **Идентификатор клиента** для поставщика удостоверений Azure Active Directory.
  
2. В списке **Allowed Tenants** (Разрешенные клиенты) нужно добавить домен каталога, в котором зарегистрировано приложение (например, contoso.onmicrosoft.com). Вы можете найти доменное имя по умолчанию, открыв вкладку **Домены** в клиенте Azure Active Directory. Добавьте имя домена в список **Allowed Tenants** (Разрешенные клиенты), а затем щелкните **Сохранить**.

Теперь вы можете использовать Azure Active Directory для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Проверка подлинности пользователей мобильного приложения с помощью единого входа Azure Active Directory: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/mobile-app-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[портал управления предварительной версии Azure]: https://portal.azure.com/
[портал управления Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=Oct15_HO3-->