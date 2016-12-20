---
title: "Руководство. Интеграция Azure Active Directory с Central Desktop | Документация Майкрософт"
description: "Узнайте, как использовать Central Desktop вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5923bf1012c862d500d3c9b0062f0d7662e9f1bc


---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Учебник. Интеграция Azure Active Directory с Central Desktop
Цель данного учебника — показать интеграцию Azure и Central Desktop. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа в Central Desktop/клиент Central Desktop

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Central Desktop
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enabling-the-application-integration-for-central-desktop"></a>Включение интеграции приложений для Central Desktop
В этом разделе показано, как включить интеграцию приложений для Central Desktop.

### <a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Central Desktop, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Central Desktop**.
   
   ![Коллекция приложений](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application gallery")
7. В области результатов выберите **Central Desktop** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Central Desktop со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется передать в клиент Central Desktop сертификат в кодировке Base-64.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Central Desktop** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Central Desktop?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** выполните следующие действия, а затем нажмите кнопку **Далее**. 
   
   * В текстовом поле **URL-адрес входа в Central Desktop** введите URL-адрес своего клиента Central Desktop (например, *http://contoso.centraldesktop.com*).
   * В текстовом поле "URL-адрес ответа Central Desktop" введите URL-адрес службы AssertionConsumerService Central Desktop (например, https://contoso.centraldesktop.com/saml2-assertion.php).
   
   > [!NOTE]
   > Это значение можно найти в метаданных Central Desktop (например, *http://contoso.centraldesktop.com*).
   > 
   > 
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")
4. На странице **Настройка единого входа в Central Desktop** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата локально на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")
5. Выполните вход в клиент **Central Desktop** .
6. Выберите **Параметры**, **Дополнительно** и **Единый вход**.
   
   ![Настройка  — Дополнительно](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")
7. На странице **Параметры единого входа** выполните следующие действия.
   
   ![Параметры единого входа](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
   1. Установите флажок **Разрешить единый вход SAML версии 2**.
   2. На странице **Настройка единого входа в Central Desktop** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **SSO URL** (URL-адрес единого входа).
   3. На странице **Настройка единого входа в Central Desktop** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **SSO Login URL** (URL-адрес единого входа).
   4. На странице **Настройка единого входа в Central Desktop** классического портала Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **SSO Logout URL** (URL-адрес единого выхода).
8. В разделе **Метод проверки подписей в сообщениях** выполните следующие действия.
   
   ![Метод проверки подписей в сообщениях](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
   1. Выберите **Сертификат**.
   2. В списке **SSO Certificate** (Сертификат единого входа) выберите значение **RSH SHA256**.
   3. Создайте текстовый файл из загруженного сертификата, скопируйте содержимое текстового файла и вставьте его в поле **Сертификат единого входа** .  
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Установите флажок **Отображать ссылку на страницу входа SAML версии 2**.
9. Нажмите кнопку **Обновить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи AAD могли входить систему, их необходимо подготовить для Central Desktop. В этом разделе описывается порядок создания учетных записей пользователей AAD в Central Desktop.

### <a name="to-provision-user-accounts-to-central-desktop"></a>Чтобы подготовить учетные записи пользователей для Central Desktop, выполните следующие действия.
1. Выполните вход в клиент Central Desktop.
2. Выберите **Пользователи \> Internal Members** (Внутренние участники).
3. Нажмите кнопку **Добавить внутренних участников**.
   
   ![Люди](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")
4. В текстовом поле **Email Address of New Members** (Адреса электронной почты новых участников) введите учетную запись AAD, которую вы хотите подготовить, и нажмите кнопку **Далее**.
   
   ![Адреса электронной почты новых участников](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. Нажмите кнопку **Добавить внутренних участников**.
   
   ![Добавить внутренних участников](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   > [!NOTE]
   > Добавленные пользователи получат сообщение электронной почты со ссылкой для активации учетной записи.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Central Desktop или API, предоставляемые Central Desktop для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Чтобы назначить пользователей Central Desktop, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Central Desktop** нажмите **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


