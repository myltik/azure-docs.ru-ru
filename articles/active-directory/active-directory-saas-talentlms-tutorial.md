---
title: "Руководство. Интеграция Azure Active Directory с TalentLMS | Документация Майкрософт"
description: "Узнайте, как использовать TalentLMS вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8c03fddb8cabedb9a877cd57ff9aa7c47211016d


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Учебник. Интеграция Azure Active Directory с TalentLMS
Цель данного руководства — показать интеграцию Azure и TalentLMS.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент TalentLMS

По завершении работы с этим руководством пользователи Azure AD, назначенные в TalentLMS, смогут выполнять единый вход в приложение на веб-сайте TalentLMS компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для TalentLMS
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario")

## <a name="enabling-the-application-integration-for-talentlms"></a>Включение интеграции приложений для TalentLMS
В этом разделе показано, как включить интеграцию приложений для TalentLMS.

### <a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для TalentLMS, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **TalentLMS**.
   
   ![Коллекция приложений](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Application gallery")
7. В области результатов выберите **TalentLMS** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
Цель этого раздела — описать, как разрешить пользователям аутентификацию в TalentLMS с помощью своей учетной записи в Azure AD, используя федерацию на основе протокола SAML. .  
Чтобы настроить единый вход для TalentLMS, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **TalentLMS** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в TalentLMS** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configure single sign-on")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес входа в TalentLMS** введите свой URL-адрес, который должен иметь вид *https://\<имя_клиента.\>.TalentLMSapp.com*, а затем нажмите кнопку **Далее**.
   
   ![URL-адрес для входа](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Sign on URL")
4. На странице **Настройка единого входа в TalentLMS** нажмите кнопку **Скачать сертификат**, чтобы скачать сертификат, а затем сохраните файл сертификата локально в папке **c:\\TalentLMS.cer**.
   
   ![Настройка единого входа](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TalentLMS в качестве администратора.
6. В разделе **Учетная запись и параметры** выберите вкладку **Пользователи**.
   
   ![Учетная запись и параметры](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Account & Settings")
7. Щелкните **Единый вход**.
8. В разделе "Единый вход" выполните следующие действия:
   
   ![Единый вход](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")
   
   1. Из списка **Тип интеграции единого входа** выберите **SAML 2.0**.
   2. На странице диалогового окна **Настройка единого входа в TalentLMS** классического портала Azure скопируйте значение поля **Идентификатор поставщика удостоверений** и вставьте его в текстовое поле **Identity provider (IdP)** (Поставщик удостоверений).
   3. Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **Отпечаток сертификата**.
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. На странице диалогового окна **Настройка единого входа в TalentLMS** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Remote sign-in URL** (URL-адрес удаленного входа).
   5. На странице диалогового окна **Настройка единого входа в TalentLMS** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Remote sign-out URL** (URL-адрес удаленного выхода).
   6. В поле **TargetedID** (Целевой идентификатор) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
   7. В текстовом поле **First Name** (Имя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   8. В текстовом поле **Last Name** (Фамилия) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. В текстовом поле **Email** (Электронная почта) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   10. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в систему TalentLMS, они должны быть подготовлены для нее.  
В случае использования TalentLMS подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Войдите в клиент **TalentLMS** .
2. Щелкните **Пользователи**, а затем — **Добавить пользователя**.
3. На странице диалогового окна **Добавление пользователя** сделайте следующее:
   
   ![Добавить пользователя](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Add User")
   
   1. Введите значения атрибутов, связанные с учетной записью Azure AD, в следующие текстовые поля: **First name** (Имя), **Last name** (Фамилия), **Email address** (Адрес электронной почты).
   2. Нажмите кнопку **Add User**(Добавить пользователя).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя TalentLMS или API-интерфейсы, предоставляемые TalentLMS для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Чтобы назначить пользователей TalentLMS, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **TalentLMS** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


