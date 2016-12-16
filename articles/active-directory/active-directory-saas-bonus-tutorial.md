---
title: "Руководство по интеграции Azure Active Directory с Bonus.ly | Документация Майкрософт"
description: "Узнайте, как использовать Bonus.ly вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 25a4e33ab85139bd3f871f46cef1d12a7b9cd06b


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Руководство. Интеграция Azure Active Directory с Bonus.ly
Цель данного руководства — показать интеграцию Azure и Bonus.ly. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Тестовый клиент в Bonus.ly

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Bonus.ly
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario")

## <a name="enabling-the-application-integration-for-bonusly"></a>Включение интеграции приложений для Bonus.ly
В этом разделе показано, как включить интеграцию приложений для Bonus.ly.

### <a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Bonus.ly, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Включить единый вход](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-bonus-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-bonus-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Bonus.ly**.
   
   ![Коллекция приложений](./media/active-directory-saas-bonus-tutorial/IC773681.png "Application gallery")
7. В области результатов выберите **Bonus.ly** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Bonus.ly со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Bonus.ly, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **Bonus.ly** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Bonus.ly?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в Bonus.ly** введите свой URL-адрес, используя следующий шаблон *https://\<имя-клиента\>.Bonus.ly*, и нажмите кнопку **Далее**. 
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configure app URL")
4. На странице **Настройка единого входа в Bonus.ly** щелкните **Скачать сертификат**, а затем сохраните файл сертификата с именем **c:\\Bonusly.ser** локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")
5. В другом окне браузера войдите в клиент **Bonus.ly** .
6. На панели инструментов в верхней части экрана щелкните **Settings** (Параметры) и выберите **Integrations and apps** (Интеграции и приложения).
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")
7. В разделе **Single Sign-On** (Единый вход) выберите **SAML**.
8. На диалоговой странице **SAML** выполните следующие действия.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")
   
   1. На классическом портале Azure на диалоговой странице **Настройка единого входа в Bonus.ly** скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **IdP SSO target URL** (Целевой URL-адрес единого входа для IdP).
   2. На классическом портале Azure на диалоговой странице **Настройка единого входа в Bonus.ly** скопируйте значение поля **Issuer ID** (Идентификатор издателя) и вставьте его в текстовое поле **IdP Issuer** (Издатель IdP).
   3. На классическом портале Azure на диалоговой странице **Настройка единого входа в Bonus.ly** скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **IdP Login URL** (URL-адрес входа для IdP).
   4. Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **Cert Fingerprint** (Отпечаток сертификата).
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. Щелкните **Сохранить**.
10. На классическом портале Microsoft Azure выберите подтверждение конфигурации, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Bonus.ly, они должны быть подготовлены для Bonus.ly.  
В случае с Bonus.ly подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. В окне браузера войдите в клиент Bonus.ly.
2. В нижней части страницы нажмите кнопку **Settings**
   
   ![Settings](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")
3. Откройте вкладку **Users and bonuses** (Пользователи и бонусы).
   
   ![Users and bonuses](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")
4. Выберите **Manage Users**(Управление пользователями).
   
   ![Manage Users](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")
5. Нажмите кнопку **Add User**(Добавить пользователя).
   
   ![Add User](./media/active-directory-saas-bonus-tutorial/IC781044.png "Add User")
6. На диалоговой странице **Add User** (Добавление пользователя) выполните следующие действия.
   
   ![Добавить пользователя](./media/active-directory-saas-bonus-tutorial/IC781045.png "Add User")
   
   1. Введите **адрес электронной почты**, **имя** и **фамилию** действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.
   
   > [!NOTE]
   > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Bonus.ly или API, предоставляемые Bonus.ly для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Чтобы назначить пользователей Bonus.ly, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением Bonus.ly щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-bonus-tutorial/IC773690.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


