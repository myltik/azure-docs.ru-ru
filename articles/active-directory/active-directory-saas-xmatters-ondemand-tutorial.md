---
title: "Руководство по интеграции Azure Active Directory с xMatters OnDemand | Документация Майкрософт"
description: "Узнайте, как использовать xMatters OnDemand с Azure Active Directory для реализации единого входа, автоматической подготовки и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 143e4a856a31a44a9ec909d07d5f24710a6e2803


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Руководство. Интеграция Azure Active Directory с xMatters OnDemand
Цель данного учебника — показать интеграцию Azure и xMatters OnDemand. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент xMatters OnDemand

По завершении работы с этим руководством пользователи Azure AD, назначенные в xMatters OnDemand, смогут выполнять единый вход в приложение на веб-сайте xMatters OnDemand компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для xMatters OnDemand
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>Включение интеграции приложений для xMatters OnDemand
В этом разделе показано, как включить интеграцию приложений для xMatters OnDemand.

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для xMatters OnDemand, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6. В **поле поиска** введите **xMatters OnDemand**.
   
    ![Коллекция приложений](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Application gallery")

7. В области результатов выберите **xMatters OnDemand** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в xMatters OnDemand со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **xMatters OnDemand** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2. На странице **Как пользователи должны входить в xMatters OnDemand?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configure app URL")
   
    а. В текстовом поле **URL-адрес входа в xMatters OnDemand** введите URL-адрес в следующем формате: `https://<tenant-name>.XMattersOnDemandapp.com`.
   
    b. Нажмите кнопку **Далее**.

4. На странице **Настройка единого входа в xMatters OnDemand** щелкните **Скачать сертификат**, а затем сохраните файл сертификата локально в папке **c:\\XMatters OnDemand.cer**.
   
    > [!IMPORTANT]
    > Вам необходимо отправить сертификат службе поддержки xMatters. Служба поддержки xMatters должна загрузить сертификат, чтобы вы могли завершить настройку единого входа.
    > 
    > 
   
    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configure single sign on")

5. В другом окне браузера войдите на свой корпоративный сайт XMatters OnDemand в качестве администратора.

6. В верхней части панели инструментов щелкните **Admin** (Администратор) и выберите **Company Details** (Сведения о компании) на панели навигации слева.
   
    ![Администратор](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7. На странице **Настройка SAML** сделайте следующее:
   
    ![Настройка SAML](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML configuration")
   
    а. Выберите **Включить SAML**.
   
    b. На странице диалогового окна **Настройка единого входа в xMatters OnDemand** классического портала Azure скопируйте значение поля **Идентификатор поставщика удостоверений** и вставьте его в текстовое поле **Идентификатор поставщика удостоверений**.
   
    c. На странице диалогового окна **Настройка единого входа в xMatters OnDemand** классического портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес для единого входа**.
   
    d. На странице диалогового окна **Настройка единого входа в xMatters OnDemand** классического портала Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **Single Logout URL** (URL-адрес для единого выхода).
   
    д. В верхней части страницы «Информация о компании» нажмите кнопку **Сохранить изменения**.
    
    ![Сведения о компании](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configure single sign on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в XMatters OnDemand, они должны быть подготовлены для работы с XMatters OnDemand.  
В случае с XMatters OnDemand подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Войдите в клиент **xMatters OnDemand** .

2. Откройте вкладку **Пользователи** .

3. Нажмите кнопку **Add User**(Добавить пользователя).
  
    ![Пользователи](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4. Установите флажок **Активно**.

5. В разделе **Добавление пользователя** сделайте следующее:
   
    ![Добавление пользователя](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")
   
    а. Введите в поля **UserID** (Идентификатор пользователя), **First name** (Имя), **Last name** (Фамилия) и **Site** (Веб-сайт) соответствующие данные действующей учетной записи AAD, которую нужно подготовить.
    
    b. Щелкните **Сохранить**.


## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Чтобы назначить пользователей XMatters OnDemand, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **xMatters OnDemand** нажмите кнопку **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assign users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


