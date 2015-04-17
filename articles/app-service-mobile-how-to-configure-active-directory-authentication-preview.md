<properties 
	pageTitle="Настройка проверки подлинности Azure Active Directory для приложения служб приложений" 
	description="Узнайте, как настроить проверку подлинности Azure Active Directory для приложения служб приложений." 
	authors="mattchenderson,wesmc7777" 
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
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Настройка приложения для использования имени для входа Azure Active Directory

В этом разделе показано, как настроить службы приложений Azure для использования Azure Active Directory в качестве поставщика проверки подлинности. 

## <a name="register"> </a>Регистрация приложения в Azure Active Directory

1. Войдите на [портал управления предварительной версии Azure] и перейдите к шлюзу служб приложений.

2. В разделе **Параметры** последовательно выберите пункты **Удостоверение**, **Azure Active Directory**. Скопируйте **URL-адрес приложения**. Убедитесь, что используете схему HTTPS.

    ![][1]

3. Войдите на [портал управления Azure] и перейдите к **Active Directory**.

    ![][2] 

4. Выберите каталог, а затем откройте вкладку **Приложения** в верхней части окна. Щелкните **Добавить** в нижней части окна, чтобы зарегистрировать новое приложение. 

5. Щелкните **Добавить приложение, которое разрабатывает моя организация**.

6. В мастере добавления приложения введите **Имя** приложения и щелкните тип **Веб-приложение или веб-API**. Затем щелкните "Далее".

7. В поле **URL-адрес входа** вставьте идентификатор приложения, скопированный из параметров поставщика удостоверений Active Directory шлюза. Также введите уникальный идентификатор ресурса в поле **URI кода приложения**. Затем щелкните "Далее".

8. После добавления приложения щелкните вкладку **Настройка**. Измените **URL-адрес ответа** под заголовком **Единый вход** на URL-адрес шлюза, добавив путь _/signin-aad_. Например, `https://contosogateway.azurewebsites.net/signin-aad`. Убедитесь, что используете схему HTTPS.

    ![][3]

9. Щелкните **Сохранить**. Затем скопируйте **Идентификатор клиента** приложения.

## <a name="secrets"> </a>Добавление сведений об Azure Active Directory в мобильное приложение

10. Вернитесь на портал управления предварительной версии, к колонке **Проверка подлинности пользователя** шлюза. Вставьте параметр **Идентификатор клиента** для поставщика удостоверений Azure Active Directory.
  
11. В списке **Разрешенные клиенты** нужно добавить домен каталога, в котором зарегистрировано приложение (например, contoso.onmicrosoft.com). Вы можете найти доменное имя по умолчанию, открыв вкладку **Домены** в клиенте Azure Active Directory. Добавьте имя домена в список **Разрешенные клиенты**, а затем нажмите кнопку **Сохранить**.  

Теперь вы можете использовать Azure Active Directory для проверки подлинности в приложении. 

## <a name="related-content"> </a>Похожий контент

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]

Проверка подлинности пользователей мобильного приложения с помощью единого входа Azure Active Directory: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-how-to-configure-active-directory-authentication/app-service-aad-settings.png
[2]: ./media/app-service-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Портал управления предварительной версии Azure]: https://portal.azure.com/
[Портал управления Azure]: https://manage.windowsazure.com/
[ios-adal]: app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md

<!--HONumber=49-->