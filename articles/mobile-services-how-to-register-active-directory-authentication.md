<properties 
	pageTitle="Регистрация для проверки подлинности в Azure Active Directory - мобильные службы" 
	description="Узнайте, как зарегистрироваться для использования аутентификации Azure Active Directory в приложении мобильных служб." 
	authors="wesmc7777" 
	services="mobile-services" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Регистрация приложений для входа с использованием учетной записи Azure Active Directory

В этом разделе показано, как зарегистрировать приложения для использования Azure Active Directory в качестве поставщика проверки подлинности для мобильных служб Azure. 


>[AZURE.NOTE] Описанные в этом разделе действия предназначены для применения с учебником [Добавление проверки подлинности в приложение мобильных служб](/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/), если нужно использовать [управляемые службой операции входа](http://msdn.microsoft.com/library/azure/dn283952.aspx) в вашем приложении. А если приложению требуются [управляемые клиентом операции входа](http://msdn.microsoft.com/library/azure/jj710106.aspx) для серверной части Azure Active Directory и .NET, нужно начать с учебника [Проверка подлинности приложения с помощью единого входа библиотеки проверки подлинности Active Directory](/ru-ru/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/).


1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

    ![][1]

2. Откройте вкладку **Удостоверение** своей мобильной службы. 

    ![][2]

3. Прокрутите вниз до раздела поставщика удостоверений **Azure Active Directory** и скопируйте **URL-адрес приложения**, указанный там.

    ![][3]

4. Перейдите к **Active Directory** на портале управления и щелкните свой каталог.

    ![][4] 	

5. Щелкните вкладку **Приложения** наверху, а затем нажмите кнопку, чтобы **Добавить** приложение. 

    ![][10]

6. Щелкните пункт **Добавить приложение, разрабатываемое моей организацией**.

7. В мастере добавления приложения введите **Имя** для приложения и нажмите тип **Веб-приложение и/или веб-API**. Затем щелкните "Далее".

    ![][5]

8. В поле **URL-адрес входа** вставьте идентификатор приложения, скопированный из параметров поставщика удостоверений Active Directory вашей мобильной службы. Также введите уникальный идентификатор ресурса в поле **URI кода приложения**. Затем щелкните "Далее".
 
    ![][6]


9. После добавления приложения щелкните вкладку **Настройка**. Затем щелкните, чтобы скопировать **Код клиента** приложения.

    Если созданная мобильная служба использует серверную часть .Net для вашей мобильной службы, также измените **URL-адрес ответа** в разделе **Единый вход** на URL-адрес мобильной службы с добавлением пути _signin-aad_. Например, `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Вернитесь на вкладку **Удостоверение** мобильной службы. В нижней части вставьте скопированное в поле **Код клиента** поставщика удостоверений Azure Active Directory.

  
11. В списке **Allowed Tenants** (Разрешенные клиенты) нужно добавить домен каталога, в котором зарегистрировано приложение (например, contoso.onmicrosoft.com). Имя домена по умолчанию можно найти, щелкнув вкладку **Домены** в Active Directory.

    ![][11]
 
    Добавьте имя домена в список **Allowed Tenants** (Разрешенные клиенты), а затем нажмите кнопку **Сохранить**.    


    ![][9]



Теперь вы готовы использовать Azure Active Directory для проверки подлинности в приложении. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Портал управления Azure]: https://manage.windowsazure.com/



<!--HONumber=42-->
