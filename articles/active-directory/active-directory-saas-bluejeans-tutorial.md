---
title: "Руководство по интеграции Azure Active Directory с BlueJeans | Документация Майкрософт"
description: "Узнайте, как использовать BlueJeans вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 00f00d91e54d35fb9009a3e927dfc8833cd7571e


---
# <a name="tutorial-azure-ad-integration-with-bluejeans"></a>Руководство. Интеграция Azure AD с BlueJeans
Цель данного руководства — показать интеграцию Azure и BlueJeans.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа BlueJeans.

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к BlueJeans, смогут выполнять единый вход в приложение на корпоративном веб-сайте BlueJeans (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для BlueJeans
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")

## <a name="enabling-the-application-integration-for-bluejeans"></a>Включение интеграции приложений для BlueJeans
В этом разделе показано, как включить интеграцию приложений для BlueJeans.

### <a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для BlueJeans, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **BlueJeans**.
   
   ![Коллекция приложений](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")
7. В области результатов выберите **BlueJeans** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в BlueJeans со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **BlueJeans** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в BlueJeans?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в BlueJeans** введите свой URL-адрес в формате *https://company.BlueJeans.com* и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")
4. На странице **Настройка единого входа в BlueJeans** щелкните **Скачать сертификат** и сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на корпоративный веб-сайт **BlueJeans** в качестве администратора.
6. Последовательно щелкните **Admin \> Group Settings \> Security** (Администратор > Параметры группы > Безопасность).
   
   ![Администратор](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")
7. В разделе **Security** (Безопасность) выполните следующие действия.
   
   ![SAML Single Sign On](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")
   
   1. Выберите параметр **SAML Single Sign On**(Единый вход SAML).
   2. Установите флажок **Enable automatic provisioning**(Включить автоматическую подготовку).
8. После этого выполните следующие действия.
   
   ![Путь к сертификату](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")
   
   1. Нажмите кнопку **Choose File**(Выбрать файл) и отправьте скачанный сертификат.
   2. На классическом портале Azure на диалоговой странице **Настройка единого входа в BlueJeans** скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес для входа**.
   3. На классическом портале Azure на диалоговой странице **Настройка единого входа в BlueJeans** скопируйте значение поля **Изменить URL-адрес пароля** и вставьте его в текстовое поле **Password Change URL** (URL-адрес изменения пароля).
   4. На классическом портале Azure на диалоговой странице **Настройка единого входа в BlueJeans** скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода**.
9. После этого выполните следующие действия.
   
   ![Сохранить изменения](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")
   
   1. В поле **Идентификатор пользователя** введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   2. В поле **Электронная почта** введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   3. Нажмите кнопку **Сохранить изменения**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в BlueJeans, они должны быть подготовлены для BlueJeans.  
В случае с BlueJeans подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт компании **BlueJeans** в качестве администратора.
2. Последовательно выберите пункты **Admin \> Manage Users \> Add User** (Администратор > Управление пользователями > Добавить пользователя).
   
   ![Администратор](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   > [!IMPORTANT]
   > Вкладка **Add User** (Добавить пользователя) доступна, только если на вкладке **Security** (Безопасность) снят флажок **Enable automatic provisioning** (Включить автоматическую подготовку).
   > 
   > 
3. В разделе **Add User** (Добавление пользователя) выполните следующие действия.
   
   ![Добавить пользователя](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")
   
   1. Введите в текстовых полях **BlueJeans Username** (Имя пользователя BlueJeans), **Email address** (Адрес электронной почты), **BlueJeans Meeting ID** (Идентификатор собрания BlueJeans), **Moderator Passcode** (Секретный код модератора), **Full Name** (Полное имя) и **Company** (Компания) соответствующие данные действующей учетной записи AAD, которую вы хотите подготовить.
   2. Нажмите кнопку **Add User**(Добавить пользователя).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя BlueJeans или API, предоставляемые BlueJeans для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Чтобы назначить пользователей BlueJeans, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **BlueJeans** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


