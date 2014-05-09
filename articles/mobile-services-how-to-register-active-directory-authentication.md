<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Регистрация для проверки подлинности Azure Active Directory" pageTitle="Регистрация для проверки подлинности Azure Active Directory — мобильные службы" metaKeywords="Регистрация приложения Azure, проверка подлинности Azure Active Directory, проверка подлинности приложения, проверка подлинности мобильных служб" description="Регистрация для проверки подлинности Azure Active Directory в приложении мобильных служб" title="Регистрация приложений для использования входа в учетную запись Azure Active Directory" authors="" />

# Зарегистрируйте свои приложения для входа с использованием учетной записи Azure Active Directory

В этом разделе показано, как зарегистрировать приложения для использования Azure Active Directory в качестве поставщика проверки подлинности для мобильных служб Azure. 

> [WACOM.NOTE] Поставщик проверки подлинности Azure Active Directory для мобильных служб в данный момент работает в предварительной версии. Если вы хотите зарегистрироваться в предварительной версии, обратитесь к <a href="mailto:mobileservices@microsoft.com">mobileservices@microsoft.com</a> В противном случае поставщик проверки подлинности будет недоступен из вкладки `Удостоверение` вашей мобильной службы.

> [WACOM.NOTE] При необходимости также предоставить проверку подлинности на стороне клиента для единого входа или push-уведомлений в приложении Магазина Windows, рассмотрите также возможность регистрации вашего приложения в Магазине Windows. Дополнительные сведения см. в разделе <a href="/ru-ru/develop/mobile/how-to-guides/register-for-single-sign-on">Регистрация приложений Магазина Windows для проверки подлинности Windows Live Connect</a>. 

1. Выполните вход на [портал управления Azure], щелкните пункт **Мобильные службы**, а затем щелкните свою мобильную службу.

    ![][1]

2. Нажмите вкладку **Удостоверение** своей мобильной службы. 

    ![][2]

3. Прокрутите вниз до раздела поставщика удостоверений **Azure Active Directory** и скопируйте **URL-адрес приложения**, указанный там.

    ![][3]

4. В портале управления перейдите к **Active Directory** и щелкните свой каталог.

    ![][4] 

5. Щелкните вкладку **Приложения** наверху, а затем нажмите кнопку, чтобы **Добавить** приложение. 

    ![][10]

6. Щелкните пункт **Добавить приложение, разрабатываемое моей организацией**.

7. В мастере добавления приложения введите **Имя** для приложения и нажмите тип **Веб-приложение и/или веб-API**. Затем щелкните "Далее".

    ![][5]

8. В поле **URL-адрес входа** вставьте идентификатор приложения, скопированный из параметров поставщика удостоверений Active Directory вашей мобильной службы. Также введите идентификатор уникального ресурса в поле **URI кода приложения**. Приложение будет использовать URI для отправки запросов единого входа в Azure Active Directory. Затем щелкните "Далее".

    ![][6]

9. Щелкните, чтобы включить для приложения тип доступа **Единый вход**. Нажмите кнопку, чтобы завершить работу мастера добавления приложений.

    ![][7]

10. После добавления приложения прокрутите вниз. Нажмите раздел **Предоставление приложению возможности чтения или записи данных каталога**, чтобы развернуть его. Затем щелкните, чтобы скопировать **Код клиента** приложения.

    ![][8]


11. Вернитесь к вкладке **Удостоверение** мобильной службы. В нижней части вставьте скопированное в поле **Код клиента** поставщика удостоверений Azure Active Directory. Нажмите кнопку **Сохранить**.

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

<!-- URLs. -->
[Портал управления Azure]: https://manage.windowsazure.com/
[Загрузка пакета SDK для предварительной версии мобильных служб]: http://www.windowsazure.com/

