---
title: "Руководство. Интеграция Azure Active Directory с Brightspace (разработка Desire2Learn) | Документация Майкрософт"
description: "Узнайте, как использовать Brightspace (разработка Desire2Learn) вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b68e1f772071678cef5380bbcb7c2b057748a6f3


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Руководство. Интеграция Azure Active Directory с Brightspace (разработка Desire2Learn)
Цель данного учебника — показать интеграцию Azure и Brightspace (разработка Desire2Learn).  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Brightspace (разработка Desire2Learn)

После завершения этого руководства пользователи Azure AD, назначенные Brightspace (разработка Desire2Learn), будут иметь возможность единого входа в приложение на веб-сайте компании Brightspace (разработка Desire2Learn; вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Brightspace (разработка Desire2Learn)
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scenario")

## <a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>Включение интеграции приложений для Brightspace (разработка Desire2Learn)
В этом разделе показано, как включить интеграцию приложений для Brightspace (разработка Desire2Learn).

### <a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Brightspace (разработка Desire2Learn), выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)).
   
   ![Коллекция приложений](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication Gallery")
7. В области результатов выберите **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Brightspace (разработка Desire2Learn)](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Brightspace (разработка Desire2Learn) со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configure Single Sign-On")
2. На странице **How would you like users to sign on to Brightspace by Desire2Learn** (Как пользователи должны входить в Brightspace (разработка Desire2Learn)?) выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configure Single Sign-On")
3. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configure App URL")
   
   1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в ваше приложение **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) (например, *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
   2. Щелкните **Далее**
4. На странице **Configure single sign-on at Brightspace by Desire2Learn** (Настройка единого входа в Brightspace (разработка Desire2Learn)) нажмите кнопку **Скачать метаданные**, чтобы скачать их, а затем сохраните файл данных локально на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configure Single Sign-On")
5. Отправьте загруженный файл метаданных в службу поддержки Brightspace (разработка Desire2Learn).
   
   > [!NOTE]
   > Настройка единого входа должна выполняться службой поддержки Brightspace (разработка Desire2Learn).
   > Как только единый вход для вашей подписки будет включен, вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Brightspace (разработка Desire2Learn), они должны быть подготовлены для Brightspace (разработка Desire2Learn).  
В случае Brightspace (разработка Desire2Learn) учетные записи пользователей должны создаваться службой поддержки Brightspace (разработка Desire2Learn).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Brightspace (разработка Desire2Learn) или API, предоставляемые Brightspace (разработка Desire2Learn) для подготовки учетных записей пользователя Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Чтобы назначить пользователей Brightspace (разработка Desire2Learn), выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Brightspace by Desire2Learn** (Brightspace (разработка Desire2Learn)) нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


