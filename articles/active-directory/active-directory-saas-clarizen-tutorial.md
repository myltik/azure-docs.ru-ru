---
title: "Учебник. Интеграция Azure Active Directory с Clarizen | Документация Майкрософт"
description: "Узнайте, как использовать Clarizen вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f8d2418fe86163a4022960f87803c82487c86fd


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Руководство. Интеграция Azure Active Directory с Clarizen
Цель данного руководства — показать интеграцию Azure и Clarizen.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Clarizen

После завершения этого руководства пользователи Azure AD, назначенные Clarizen, будут иметь возможность единого входа в приложение на веб-сайте компании Clarizen (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Clarizen
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## <a name="enabling-the-application-integration-for-clarizen"></a>Включение интеграции приложений для Clarizen
В этом разделе показано, как включить интеграцию приложений для Clarizen.

### <a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Clarizen, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Clarizen**.
   
   ![Коллекция приложений](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")
7. В области результатов выберите **Clarizen** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Clarizen со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Clarizen** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Clarizen?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")
3. Для скачивания сертификата на странице **Настройка единого входа в Clarizen** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")
4. В другом окне веб-браузера войдите на свой корпоративный сайт **Clarizen** в качестве администратора (например: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).
5. Нажмите свое имя пользователя и выберите пункт **Параметры**.
   
   ![Параметры](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")
6. Откройте вкладку **Global Settings** (Глобальные параметры), а затем рядом с параметром **Federated Authentication** (Федеративная аутентификация) нажмите **edit** (изменить).
   
   ![Глобальные параметры](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. В диалоговом окне **Федеративная проверка подлинности** выполните следующие действия.
   
   ![Федеративная проверка подлинности](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. Чтобы отправить загруженный сертификат, нажмите кнопку **Отправить** .
   2. На странице диалогового окна **Настройка единого входа в Clarizen** классического портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **Sign-in URL** (URL-адрес для входа).
   3. На странице диалогового окна **Configure single sign-out at Clarizen** (Настройка единого выхода для Clarizen) классического портала Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **Sign-out URL** (URL-адрес для выхода).
   4. Установите флаг **Использовать POST**.
   5. Щелкните **Сохранить**.
8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Clarizen, они должны быть подготовлены для Clarizen.  
В случае с Clarizen подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход на свой корпоративный веб-сайт **Clarizen** в качестве администратора.
2. Выберите параметр **Пользователи**.
   
   ![Пользователи](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. Нажмите кнопку **Пригласить пользователя**.
   
   ![Пригласить пользователей](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. На странице диалогового окна "Приглашение пользователей" выполните следующие действия.
   
   ![Приглашение пользователей](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. В текстовом поле **Электронная почта** введите адрес электронной почты действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Нажмите кнопку **Пригласить**.
   
   > [!NOTE]
   > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.
   > 
   > 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Чтобы назначить пользователей Clarizen, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Clarizen** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


