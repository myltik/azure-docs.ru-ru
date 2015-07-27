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
	ms.date="05/26/2015" 
	ms.author="mahender"/>

# Настройка приложения для использования имени для входа Azure Active Directory

В этом разделе показано, как настроить службы приложений Azure для использования Azure Active Directory в качестве поставщика проверки подлинности.

## <a name="register"> </a>Регистрация приложения в Azure Active Directory

1. Войдите на [портал управления предварительной версии Azure] и перейдите к шлюзу служб приложений.

2. В разделе **Параметры** выберите пункт **Удостоверение**, а затем выберите **Azure Active Directory**. Скопируйте **URL-адрес приложения**. Убедитесь, что используете схему HTTPS.

    ![][1]

3. Войдите на [портал управления Azure] и перейдите в **Active Directory**.

    ![][2]

4. Выберите свой каталог, а затем откройте вкладку **Приложения** в верхней части окна. Щелкните **Добавить** в нижней части окна, чтобы зарегистрировать новое приложение.

5. Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

6. В мастере добавления приложения введите **Имя** для приложения и выберите тип **Веб-приложение и/или веб-API**. Затем щелкните "Далее".

7. В поле **URL-адрес входа** вставьте идентификатор приложения, скопированный из параметров поставщика удостоверений Active Directory шлюза. Также введите уникальный идентификатор ресурса в поле **URI кода приложения**. Затем щелкните "Далее".

8. После добавления приложения щелкните вкладку **Настройка**. Измените **URL-адрес ответа** в разделе **Единый вход** на URL-адрес шлюза, дополненный путем _/signin-aad_. Например, `https://contosogateway.azurewebsites.net/signin-aad`. Убедитесь, что используете схему HTTPS.

    ![][3]

9. Щелкните **Сохранить**. Затем скопируйте **идентификатор клиента** для приложения.

## <a name="secrets"> </a>Добавление сведений об Azure Active Directory в мобильное приложение

10. Вернитесь на портал управления предварительной версии, в колонку **Проверка подлинности пользователя** для шлюза. Вставьте параметр **Идентификатор клиента** для поставщика удостоверений Azure Active Directory.
  
11. В списке **Allowed Tenants** (Разрешенные клиенты) нужно добавить домен каталога, в котором зарегистрировано приложение (например, contoso.onmicrosoft.com). Вы можете найти доменное имя по умолчанию, открыв вкладку **Домены** в клиенте Azure Active Directory. Добавьте имя домена в список **Allowed Tenants** (Разрешенные клиенты), а затем щелкните **Сохранить**.

Теперь вы можете использовать Azure Active Directory для проверки подлинности в приложении.

## <a name="related-content"> </a>Связанная информация

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Проверка подлинности пользователей мобильного приложения с помощью единого входа Azure Active Directory: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[портал управления предварительной версии Azure]: https://portal.azure.com/
[портал управления Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=July15_HO3-->