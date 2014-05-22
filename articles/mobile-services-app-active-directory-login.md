<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Регистрация для проверки подлинности Azure Active Directory" pageTitle="Регистрация для проверки подлинности Azure Active Directory — мобильные службы" metaKeywords="Регистрация приложения Azure, проверка подлинности Azure Active Directory, проверка подлинности приложения, проверка подлинности мобильных служб" description="Узнайте, как выполнять регистрацию для проверки подлинности Azure Active Directory в приложении мобильных служб Azure" metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Регистрация приложений для использования входа в учетную запись Azure Active Directory" authors="" />


# Зарегистрируйте свои приложения для входа с использованием учетной записи Azure Active Directory

В этом разделе показано, как зарегистрировать приложения для использования Azure Active Directory в качестве поставщика проверки подлинности для мобильных служб Azure. 

<div class="dev-callout"><b>Примечание.</b>
<p>При необходимо также предоставить проверку подлинности на стороне клиента для единого входа или уведомлений Push в приложении Магазина Windows рассмотрите также возможность регистрации вашего приложения в Магазине Windows. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on">Регистрация приложений Магазина Windows для проверки подлинности Windows Live Connect</a>.</p>
</div>

1.  Выполните вход на [портал управления Azure]. 

2. Перейдите к **Active Directory** на портале управления и щелкните свой каталог.

   ![][1] 

3. Щелкните вкладку **Приложения**, а затем выберите **Добавить приложение**. 

   ![][2]


4. Следуйте инструкциям в мастере создания приложения и выберите **Веб-приложение веб-API** для XXX. Доступ с единым входом. При появлении запроса **URL-адрес приложения** вставьте URL-адрес приложения мобильных служб.


5. *** ДОПОЛНИТЕЛЬНЫЙ МАТЕРИАЛ ***


Теперь вы готовы использовать Azure Active Directory для проверки подлинности в вашем приложении, предоставляя мобильным службам значения кода клиента и секрета клиента.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png

<!-- URLs. -->
[Портал управления Azure]: https://manage.windowsazure.com/

