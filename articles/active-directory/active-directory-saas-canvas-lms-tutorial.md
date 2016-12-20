---
title: "Руководство. Интеграция Azure Active Directory с Canvas LMS | Документация Майкрософт"
description: "Узнайте, как использовать Canvas LMS вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0031446f8b5202f6627ed02e90c4789f6d434234


---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Руководство. Интеграция Azure Active Directory с Canvas LMS
Цель данного руководства — показать интеграцию Azure и Canvas.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Canvas

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к Canvas, смогут выполнять единый вход в приложение на корпоративном веб-сайте Canvas (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Canvas
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario")

## <a name="enabling-the-application-integration-for-canvas"></a>Включение интеграции приложений для Canvas
В этом разделе показано, как включить интеграцию приложений для Canvas.

### <a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Canvas, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Canvas**.
   
   ![Коллекция приложений](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Application Gallery")
7. В области результатов выберите **Canvas** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Холст](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Canvas со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Canvas, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Canvas** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Canvas?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** введите в текстовом поле **URL-адрес для входа в Canvas** URL-адрес службы в формате `https://<tenant-name>.instructure.com` и нажмите **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configure App URL")
4. На странице **Настройка единого входа в Canvas** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата локально на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Canvas в качестве администратора.
6. Выберите **Курсы \> Управляемые учетные записи \> Microsoft**.
   
   ![Холст](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
7. На панели навигации слева выберите раздел **Проверка подлинности** и нажмите кнопку **Add New SAML Config** (Добавить новую конфигурацию SAML).
   
   ![Аутентификация](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")
8. На странице "Текущая интеграция" выполните следующие действия.
   
   ![Текущая интеграция](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")
   
   1. На классическом портале Azure на диалоговой странице **Настройка единого входа в Canvas** скопируйте значение поля **Идентификатор сущности** и вставьте его в текстовое поле **IdP Entity ID** (Идентификатор сущности IdP).
   2. На диалоговой странице **Настройка единого входа в Canvas** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес для входа**.
   3. На диалоговой странице **Настройка единого входа в Canvas** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес для выхода**.
   4. На диалоговой странице **Настройка единого входа в Canvas** классического портала Azure скопируйте значение поля **Изменить URL-адрес пароля** и вставьте его в текстовое поле **Change Password Link** (Ссылка на изменение пароля).
   5. Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **Отпечаток сертификата**.  
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. В списке **Login Attribute** (Атрибут входа) выберите значение **NameID**.
   7. В списке **Identifier Format** (Формат идентификатора) выберите значение **emailAddress**.
   8. Нажмите кнопку **Сохранить параметры проверки подлинности**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Canvas, они должны быть подготовлены для Canvas.  
В случае с Canvas подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход в клиент **Canvas** .
2. Выберите **Курсы \> Управляемые учетные записи \> Microsoft**.
   
   ![Холст](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
3. Выберите раздел **Пользователи**.
   
   ![Пользователи](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")
4. Нажмите кнопку **Add New User**(Добавить нового пользователя).
   
   ![Пользователи](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")
5. На странице диалогового окна "Добавление пользователя" выполните следующие действия.
   
   ![Add User](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")
   
   1. В тестовом поле **Полное имя** введите имя пользователя.
   2. В текстовое поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя.
   3. В текстовом поле **Имя входа** введите адрес электронной почты пользователя в Azure AD.
   4. Установите флажок **Email the user about this account creation**(Сообщить пользователю о создании этой учетной записи по электронной почте).
   5. Нажмите кнопку **Add User**(Добавить пользователя).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Canvas или API, предоставляемые Canvas для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Чтобы назначить пользователей Canvas, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Canvas** нажмите кнопку **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


