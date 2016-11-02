<properties
    pageTitle="Учебник. Как интегрировать Salesforce с Azure Active Directory | Microsoft Azure"
    description="Узнайте, как использовать Salesforce вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>


#<a name="tutorial:-how-to-integrate-salesforce-with-azure-active-directory"></a>Учебник. Как интегрировать Salesforce с Azure Active Directory

В этом учебнике мы покажем, как подключить среду Salesforce к Azure Active Directory. Вы узнаете, как настроить единый вход в Salesforce, включить автоматическую подготовку пользователей и предоставить пользователям доступ к Salesforce.

##<a name="prerequisites"></a>Предварительные требования

1. Для доступа к Azure Active Directory через [классический портал Azure](https://manage.windowsazure.com)необходима действующая подписка Azure.

2. У вас должен быть действительный клиент в [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Если вы используете **пробную** учетную запись Salesforce.com, то не сможете настроить автоматическую подготовку пользователей. У пробных учетных записей нет необходимых прав доступа к API.
> 
> Вы можете обойти это ограничение, используя для выполнения заданий данного учебника [бесплатную учетную запись разработчика](https://developer.salesforce.com/signup) .

Если вы используете изолированную среду Salesforce, ознакомьтесь с [Учебником по интеграции с изолированной средой Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Учебные видеоматериалы

Вы можете выполнять задания из этого учебника, используя следующие видеоролики.

**Видеоучебник (часть 1). Как настроить единый вход**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Видеоучебник (часть 2). Как автоматизировать подготовку пользователей**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1:-add-salesforce-to-your-directory"></a>Шаг 1. Добавление Salesforce в каталог

1. На [классическом портале Azure](https://manage.windowsazure.com)в области навигации слева щелкните **Active Directory**.

    ![Выберите Active Directory в области навигации слева.][0]

2. В списке **Каталог** выберите каталог, в который хотите добавить Salesforce.

3. Щелкните **Приложения** в верхнем меню.

    ![Щелкните "Приложения".][1]

4. В нижней части страницы нажмите кнопку **Добавить** .

    ![Нажмите кнопку "Добавить", чтобы добавить новое приложение.][2]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.

    ![Нажмите кнопку "Добавить приложение из коллекции".][3]

6. В **поле поиска** введите **Salesforce**. Выберите **Salesforce** из списка результатов и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![Добавьте Salesforce.][4]

7. Вы увидите страницу быстрого запуска Salesforce.

    ![Страница быстрого запуска Salesforce в Azure AD][5]

##<a name="step-2:-enable-single-sign-on"></a>Шаг 2. Включение единого входа

1. Перед настройкой единого входа необходимо настроить и развернуть пользовательский домен для вашей среды Salesforce. Инструкции по выполнению этой задачи см. на странице [Настройка имени домена](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. На странице быстрого запуска Salesforce в Azure AD щелкните **Настроить единый вход** .

    ![Кнопка "Настроить единый вход"][6]

3. Откроется диалоговое окно, и вы увидите экран с вопросом "Как пользователи должны входить в Salesforce?". Выберите **Единый вход Azure AD**, а затем нажмите кнопку **Далее**.

    ![Выберите "Единый вход Azure AD".][7]

    > [AZURE.NOTE] Для получения дополнительных сведений о вариантах единого входа [щелкните здесь](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. На странице **Настроить параметры приложения** заполните поле **URL-адрес входа**, указав URL-адрес домена Salesforce в следующем формате.
 - Учетная запись предприятия: `https://<domain>.my.salesforce.com`
 - Учетная запись разработчика: `https://<domain>-dev-ed.my.salesforce.com` 

    ![Введите URL-адрес входа.][8]

5. На странице **Настройка единого входа в Salesforce** щелкните **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.

    ![Загрузить сертификат][9]

6. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce.

7. В области навигации **Administrator** (Администратор) щелкните **Security Controls** (Управление безопасностью), чтобы развернуть соответствующий раздел. Щелкните **Параметры единого входа**.

    ![Щелкните "Параметры единого входа" в разделе "Управление безопасностью".][10]

8. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Нажмите кнопку "Изменить".][11]

    > [AZURE.NOTE] Если вам не удается включить параметры единого входа для своей учетной записи Salesforce, возможно, вам придется обратиться за помощью в службу поддержки Salesforce.

9. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Выберите "SAML включен".][12]

10. Чтобы настроить параметры единого входа SAML, щелкните **Создать**.

    ![Выберите "SAML включен".][13]

11. На странице **Изменение параметров единого входа на основе SAML** выполните следующие действия.

    ![Снимок экрана настроек, которые следует выполнить][14]

 - В поле **Имя** введите понятное имя конфигурации. При вводе значения в поле **Name** (Имя) текстовое поле **API Name** (Имя API) заполняется автоматически.

 - Скопируйте значение из поля **URL-адрес издателя** в Azure AD и вставьте его в поле **Issuer** (Издатель) в Salesforce.

 - Введите в текстовое поле **Идентификатор сущности**имя домена Salesforce в следующем формате.
     - Учетная запись предприятия: `https://<domain>.my.salesforce.com`
     - Учетная запись разработчика: `https://<domain>-dev-ed.my.salesforce.com`

 - Нажмите кнопку **Browse** (Обзор) или **Choose File** (Выбрать файл), чтобы открыть диалоговое окно **Choose File to Upload** (Выбор файла для передачи). Выберите сертификат Salesforce и нажмите кнопку **Open** (Открыть), чтобы передать его.

 - В поле **SAML Identity Type** (Тип удостоверения SAML) выберите значение **Assertion contains User's salesforce.com username** (Утверждение содержит имя пользователя salesforce.com).

 - В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).

 - В Azure AD скопируйте значение из поля **URL-адрес удаленного входа** и вставьте его в поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений) в Salesforce.

 - В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициируемых поставщиком услуг) выберите значение **HTTP Redirect** (Перенаправление HTTP).

 - Наконец, нажмите кнопку **Сохранить** , чтобы применить параметры единого входа SAML.

12. В левой области навигации в Salesforce щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, и нажмите кнопку **My Domain** (Мой домен).

    ![Щелкните "Мой домен".][15]

13. Прокрутите страницу вниз до раздела **Authentication Configuration** (Конфигурация аутентификации) и нажмите кнопку **Edit** (Изменить).

    ![Нажмите кнопку "Изменить".][16]

14. В разделе **Authentication Service** (Служба аутентификации) выберите понятное имя конфигурации единого входа SAML и нажмите кнопку **Save** (Сохранить).

    ![Выберите конфигурацию единого входа.][17]

    > [AZURE.NOTE] Если выбрано несколько служб проверки подлинности, то при попытке инициировать единый вход в среду Salesforce пользователям будет предложено уточнить, с помощью какой службы проверки подлинности нужно выполнить вход. Чтобы этого избежать, **снимите флажки для всех других служб проверки подлинности**.

15. В Azure AD установите флажок подтверждения настройки единого входа, чтобы активировать сертификат, который вы загрузили в Salesforce. Нажмите кнопку **Далее**.

    ![Установите флажок подтверждения.][18]

16. Если вы хотите получать уведомления об ошибках и предупреждения, связанные с обслуживанием конфигурации единого входа, введите адрес электронной почты на последней странице. 

    ![Введите адрес электронной почты.][19]

17. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно. Сведения о проверке конфигурации см. в разделе [Назначение пользователей Salesforce](#step-4-assign-users-to-salesforce) ниже.

##<a name="step-3:-enable-automated-user-provisioning"></a>Шаг 3. Включение автоматической подготовки пользователей

1. На странице быстрого запуска Salesforce в Azure AD нажмите кнопку **Настройка подготовки пользователей** .

    ![Нажмите кнопку "Настройка подготовки пользователей".][20]

2. В диалоговом окне **Настройка подготовки пользователей** введите имя пользователя и пароль администратора Salesforce.

    ![Введите имя пользователя и пароль администратора.][21]

    > [AZURE.NOTE] При настройке рабочей среды рекомендуется создать новую учетную запись администратора Salesforce специально для этого шага. Данной учетной записи в Salesforce должен быть назначен профиль **Системный администратор** .

3. Для получения маркера безопасности Salesforce откройте новую вкладку и выполните вход с этой учетной записью администратора Salesforce. В правом верхнем углу страницы щелкните свое имя и выберите команду **Мои параметры**.

    ![Щелкните свое имя, а затем — "Мои параметры".][22]

4. В области навигации слева щелкните **Personal** (Личное), чтобы развернуть соответствующий раздел, и выберите команду **Reset My Security Token** (Сброс маркера безопасности).

    ![Щелкните свое имя, а затем — "Мои параметры".][23]

5. На странице **Reset My Security Token** (Сброс маркера безопасности) нажмите кнопку **Reset Security Token** (Сбросить маркер безопасности).

    ![Прочитайте предупреждения.][24]

6. Проверьте входящие сообщения в почтовом ящике, связанном с этой учетной записью администратора. Найдите сообщение от Salesforce.com с новым маркером безопасности.

7. Скопируйте маркер, перейдите к окну Azure AD и вставьте его в поле **Маркер безопасности пользователя** . Нажмите кнопку **Далее**.

    ![Вставьте маркер безопасности.][25]

8. На странице подтверждения вы можете указать, хотите ли получать уведомления о сбоях при подготовке на электронную почту. Нажмите кнопку **Завершить** , чтобы закрыть диалоговое окно.

    ![Введите адрес электронной почты для получения уведомлений.][26]

##<a name="step-4:-assign-users-to-salesforce"></a>Шаг 4. Назначение пользователей Salesforce

1. Чтобы проверить конфигурацию, создайте тестовую учетную запись в каталоге.

2. На странице быстрого запуска Salesforce нажмите кнопку **Назначить пользователей** .

    ![Щелкните "Назначить пользователей".][27]

3. Выберите тестового пользователя и нажмите кнопку **Назначить** в нижней части экрана.

 - Если вы еще не включили автоматическую подготовку пользователей, то появится следующее сообщение:

        ![Confirm the assignment.][28]

 - Если автоматическая подготовка пользователей включена, появится запрос на определение профиля, который нужно назначить пользователю в NetSuite. Через несколько минут подготовленные пользователи появятся в вашей среде Salesforce.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] При подготовке для среды **разработчиков** Salesforce количество доступных лицензий для каждого профиля будет крайне ограниченно. Следовательно, лучше подготовить пользователей для профиля **Chatter Free**, который имеет 4999 доступных лицензий.

4. Чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com/), выполните вход с тестовой учетной записью и щелкните **Salesforce**.

##<a name="related-articles"></a>Связанные статьи

- [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
- [Список учебников по интеграции приложений SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png



<!--HONumber=Oct16_HO2-->


