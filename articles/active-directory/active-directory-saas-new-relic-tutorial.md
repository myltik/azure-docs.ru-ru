---
title: "Руководство по интеграции Azure Active Directory с New Relic | Документация Майкрософт"
description: "Узнайте, как использовать New Relic с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7c7542454bab5e5c2fae88aeefa1a981ee165ff


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Учебник. Интеграция Azure Active Directory с New Relic
Цель данного учебника — продемонстрировать, как настроить единый вход между Active Directory Azure и New Relic.

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа New Relic.

После изучения этого учебника пользователи Azure Active Directory, которые были назначены в New Relic, смогут выполнять единый вход с помощью панели доступа AAD.

1. Включение интеграции приложений для New Relic
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")

## <a name="enabling-the-application-integration-for-new-relic"></a>Включение интеграции приложений для New Relic
В этом разделе показано, как включить интеграцию приложений для New Relic.

### <a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для New Relic, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **New Relic**.
   
   ![Коллекция приложений](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")
7. В области результатов выберите **New Relic** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в New Relic с помощью своей учетной записи Azure Active Directory, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **New Relic** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в New Relic** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в New Relic** введите URL-адрес, используемый пользователями для входа в приложение New Relic, и нажмите кнопку **Далее**. 
   
   URL-адрес приложения — это URL-адрес клиента New Relic (например, *https://rpm.newrelic.com*).
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")
4. На странице **Настройка единого входа в New Relic** щелкните **Загрузить сертификат**, а затем сохраните файл сертификата локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на веб-сайт **New Relic** компании в качестве администратора.
6. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
   ![Параметры учетной записи](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")
7. Щелкните вкладку **Security and authentication** (Безопасность и аутентификация), а затем выберите вкладку **Single sign on** (Единый вход).
   
   ![Единый вход](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")
8. На диалоговой странице «Настройка SAML» выполните следующие действия:
   
   ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")
   
   1. Щелкните **Выбор файла** , чтобы передать скачанный сертификат Azure Active Directory.
   2. На странице **Настройка единого входа в New Relic** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес удаленного входа**.
   3. На странице **Настройка единого входа в New Relic** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout landing URL** (URL-адрес целевой страницы выхода).
   4. Щелкните **Сохранить изменения**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure Active Directory могли входить New Relic, они должны быть предоставлены в New Relic.  
В случае New Relic подготовка пользователей осуществляется вручную.

### <a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей для New Relic, выполните следующие действия:
1. Выполните вход на веб-сайт **New Relic** компании в качестве администратора.
2. В меню в верхней части страницы щелкните **Параметры учетной записи**.
   
   ![Параметры учетной записи](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")
3. В области **Account** (Учетная запись) слева щелкните **Summary** (Сводка) и нажмите кнопку **Add user** (Добавить пользователя).
   
   ![Параметры учетной записи](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")
4. В диалоговом окне **Активные пользователи** сделайте следующее:
   
   ![Активные пользователи](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")
   
   1. В текстовом поле **Электронная почта** введите электронный адрес действующего пользователя Azure Active Directory, которого вы хотите подготовить.
   2. Для параметра **Role** (Роль) выберите значение **User** (Пользователь).
   3. Щелкните **Добавить этого пользователя**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя New Relic или API, предоставляемые New Relic для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, необходимо предоставить выбранным пользователям Azure AD доступ к приложению, назначив их.

### <a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Чтобы назначить пользователей New Relic, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **New Relic** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


