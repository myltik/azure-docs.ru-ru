<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Регистрация приложений для входа с использованием учетной записи Azure Active Directory

В этом разделе показано, как зарегистрировать приложения для использования Azure Active Directory в качестве поставщика проверки подлинности для мобильных служб Azure.

> [WACOM.NOTE] Если необходимо обеспечить клиентскую аутентификацию для единого входа в Azure Active Directory, см. учебник [Аутентификация приложения с помощью единого входа библиотеки аутентификации Active Directory][Аутентификация приложения с помощью единого входа библиотеки аутентификации Active Directory].

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свою мобильную службу.

    ![][0]

2.  Нажмите вкладку **Удостоверение** своей мобильной службы.

    ![][1]

3.  Прокрутите страницу вниз до раздела поставщика удостоверений **Azure Active Directory** и скопируйте **URL-адрес приложения**, указанный там.

    ![][2]

4.  Перейдите к **Active Directory** на портале управления и щелкните свой каталог.

    ![][3]

5.  Щелкните вкладку **Приложения** наверху, затем нажмите кнопку **ДОБАВИТЬ**, чтобы добавить приложение.

    ![][4]

6.  Выберите команду **Добавить приложение, разрабатываемое моей организацией**.

7.  В мастере добавления приложения введите **Имя** для приложения и выберите тип **Веб-приложение и/или веб-API**. Затем щелкните "Далее".

    ![][5]

8.  В поле **URL-адрес входа** вставьте идентификатор приложения, скопированный из параметров поставщика удостоверений Active Directory вашей мобильной службы. Также введите уникальный идентификатор ресурса в поле **URI кода приложения**. Затем щелкните "Далее".

    ![][6]

9.  После добавления приложения щелкните вкладку **Настройка**. Затем щелкните, чтобы скопировать **Код клиента** приложения.

    Если созданная мобильная служба использует серверную часть .Net для вашей мобильной службы, также измените **URL-адрес ответа** в разделе **Единый вход** на URL-адрес мобильной службы с добавлением пути *signin-aad*. Например, `https://todolist.azure-mobile.net/signin-aad`

    ![][7]

10. Вернитесь к вкладке **Удостоверение** мобильной службы. В нижней части вставьте скопированное в поле **Код клиента** поставщика удостоверений Azure Active Directory.

11. В списке **Allowed Tenants** (Разрешенные клиенты) нужно добавить домен каталога, в котором зарегистрировано приложение (например, contoso.onmicrosoft.com). Имя домена по умолчанию можно найти, щелкнув вкладку **Домены** в Active Directory.

    ![][8]

    Добавьте имя домена в список **Allowed Tenants** (Разрешенные клиенты), а затем щелкните **Сохранить**.

    ![][9]

Теперь вы готовы использовать Azure Active Directory для проверки подлинности в приложении.



  [Аутентификация приложения с помощью единого входа библиотеки аутентификации Active Directory]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
