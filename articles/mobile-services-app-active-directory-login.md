<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede"></tags>

# Регистрация приложений для входа с использованием учетной записи Azure Active Directory

В этом разделе показано, как зарегистрировать приложения для использования Azure Active Directory в качестве поставщика проверки подлинности для мобильных служб Azure.

<div class="dev-callout"><b>Примечание.</b>
<p>Если вам нужно одновременно обеспечить аутентификацию на стороне клиента для единого входа или push-уведомлений в приложении для Магазина Windows, то рассмотрите также возможность регистрации своего приложения в Магазине Windows. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on">Регистрация приложений Магазина Windows для проверки подлинности Windows Live Connect</a>.</p>
</div>

1.  Выполните вход на [портал управления Azure][портал управления Azure].

2.  Перейдите к **Active Directory** на портале управления и щелкните свой каталог.

    ![][]

1.  Щелкните вкладку **Приложения**, а затем выберите **Добавить приложение**.

    ![][1]

1.  Следуйте инструкциям в мастере создания приложения и выберите **Веб-приложение и/или веб-API** для XXX. Доступ с единым входом. При появлении запроса **URL-адрес приложения** вставьте URL-адрес приложения мобильных служб.

2.  \*\*\* БУДЕТ ДОПОЛНЕНО \*\*\*

Теперь вы готовы использовать Azure Active Directory для проверки подлинности в вашем приложении, предоставляя мобильным службам значения кода клиента и секрета клиента.



  [Регистрация приложений Магазина Windows для проверки подлинности Windows Live Connect]: /ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on
  [портал управления Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
