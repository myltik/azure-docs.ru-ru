---
title: Руководство. Интеграция Azure Active Directory с Zendesk | Microsoft Docs
description: Узнайте, как использовать Zendesk вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-zendesk"></a>Руководство. Интеграция Azure Active Directory с Zendesk
Цель данного руководства — показать интеграцию Azure и Zendesk.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Zendesk

По завершении работы с этим руководством пользователи Azure AD, назначенные в Zendesk, смогут выполнять единый вход в приложение на веб-сайте Zendesk компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Zendesk
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

## <a name="enabling-the-application-integration-for-zendesk"></a>Включение интеграции приложений для Zendesk
В этом разделе показано, как включить интеграцию приложений для Zendesk.

### <a name="to-enable-the-application-integration-for-zendesk,-perform-the-following-steps:"></a>Чтобы включить интеграцию приложений для Zendesk, выполните следующие действия.
1. На портале управления Azure в левой области навигации нажмите **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Zendesk**.
   
   ![Коллекция приложений](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")
7. В области результатов выберите **Zendesk** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Zendesk со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Zendesk, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Zendesk** портала Azure AD щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Единый вход](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")
2. На странице **Как пользователи должны входить в Zendesk?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")
3. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")
   
   а. В текстовом поле **URL-адрес входа в Zendesk** введите URL-адрес в следующем формате: `https://<tenant-name>.zendesk.com`.
   
   b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Zendesk** щелкните **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")
5. В другом окне браузера войдите на свой корпоративный сайт Zendesk в качестве администратора.
6. Щелкните **Администратор**.
7. В области навигации слева щелкните **Settings** (Параметры), а затем щелкните **Security** (Безопасность).
   
   ![Безопасность](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")
8. На странице **Security** (Безопасность) щелкните вкладку **Admin & Agents** (Администраторы и агенты).
9. Выберите **Single sign-on (SSO) and SAML** (Единый вход и SAML), а затем щелкните **SAML**.
10. На странице **Настройка единого входа в Zendesk** на портале Azure AD скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **URL-адрес единого входа SAML**.
11. На странице **Настройка единого входа в Zendesk** на портале Azure AD скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес удаленного выхода**.
    
    ![Единый вход](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")
12. Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **Отпечаток сертификата**.
    
    > [!TIP]
    > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
    > 
    > 
13. Щелкните **Сохранить**.
14. На портале Azure AD выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в **Zendesk**, они должны быть подготовлены для **Zendesk**.  
В случае с **Zendesk**подготовка выполняется вручную.

### <a name="to-provision-a-user-account-to-zendesk,-perform-the-following-steps:"></a>Чтобы подготовить учетные записи пользователей для Zendesk, выполните следующие действия.
1. Войдите в клиент **Zendesk** .
2. Откройте вкладку **Список клиентов** .
3. Выберите вкладку **User** (Пользователь) и нажмите кнопку **Add** (Добавить).
   
   ![Добавить пользователя](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")
4. Введите электронный адрес существующей учетной записи Azure AD, которую необходимо подготовить, а затем нажмите кнопку **Сохранить**.
   
   ![Новый пользователь](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Zendesk или API, предоставляемые Zendesk, для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-zendesk,-perform-the-following-steps:"></a>Чтобы назначить пользователей Zendesk, выполните следующие действия.
1. На портале Azure AD создайте тестовую учетную запись.
2. На странице интеграции с приложением **Zendesk** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


