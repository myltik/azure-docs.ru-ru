---
title: "Руководство по интеграции Azure Active Directory с TeamSeer | Документация Майкрософт"
description: "Узнайте, как использовать TeamSeer с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d978744bfecddc43c0bf5e803f1fa6bc281ee79b


---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Учебник. Интеграция Azure Active Directory с TeamSeer
Цель данного руководства — показать интеграцию Azure и TeamSeer.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент TeamSeer

По завершении работы с этим руководством пользователи Azure AD, назначенные в TeamSeer, смогут выполнять единый вход в приложение на веб-сайте TeamSeer вашей компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для TeamSeer
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario")

## <a name="enabling-the-application-integration-for-teamseer"></a>Включение интеграции приложений для TeamSeer
В этом разделе показано, как включить интеграцию приложений для TeamSeer.

### <a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для TeamSeer, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **TeamSeer**.
   
   ![Коллекция приложений](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Application Gallery")
7. В области результатов выберите **TeamSeer** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в TeamSeer с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **TeamSeer** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configure Single Sign-On")
2. На странице **Как пользователи будут входить в TeamSeer** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в TeamSeer** введите свой URL-адрес в формате *http://www.teamseer.com/companyid*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configure App URL")
4. Чтобы скачать сертификат, на странице **Настройка единого входа в TeamSeer** щелкните **Скачать сертификат** и сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TeamSeer в качестве администратора.
6. Перейдите в раздел **Администратор отдела кадров**.
   
   ![Администратор отдела кадров](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")
7. Щелкните **Настройка**.
   
   ![Настройка](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Setup")
8. Щелкните **Задать данные о поставщике SAML**.
   
   ![Настройка SAML](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML Settings")
9. В разделе сведений о поставщике SAML выполните следующие действия:
   
   ![Настройка SAML](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML Settings")
   
   1. На странице диалогового окна **Настройка единого входа в TeamSeer** классического портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **URL-адрес**.
   2. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   3. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Общий сертификат IdP** .
10. Чтобы завершить настройку поставщика SAML, выполните следующие действия:
    
    ![Настройка SAML](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML Settings")
    
    1. В разделе **Тестовые адреса электронной почты**введите адрес электронной почты для тестового пользователя.
    2. В текстовом поле **Издатель** введите URL-адрес издателя поставщика услуг.
    3. Щелкните **Сохранить**.
11. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнить вход в TeamSeer, они должны быть подготовлены в ShiftPlanning.  
В случае с TeamSeer подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Войдите на веб-сайт **TeamSeer** вашей компании в качестве администратора.
2. Выполните следующие действия:
   
   ![Администратор отдела кадров](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")
   
   1. Выберите **HR Admin (Администратор отдела кадров) \> Users (Пользователи)**.
   2. Щелкните **Запустить мастер нового пользователя**.
3. В разделе **Сведения о пользователе** сделайте следующее:
   
   ![Сведения о пользователе](./media/active-directory-saas-teamseer-tutorial/IC789641.png "User Details")
   
   1. Введите в текстовые поля **First Name** (Имя), **Surname** (Фамилия), **User name (Email address)** (Имя пользователя (адрес электронной почты)) соответствующие данные действующей учетной записи AAD, которую нужно подготовить.
   2. Нажмите кнопку **Далее**.
4. Чтобы добавить нового пользователя, следуйте указаниям на экране и нажмите кнопку **Готово**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя TeamSeer или API-интерфейсы, предоставляемые TeamSeer, для подготовки учетных записей пользователей Azure AD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Чтобы назначить пользователей TeamSeer, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **TeamSeer** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


