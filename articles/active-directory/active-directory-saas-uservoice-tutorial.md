---
title: "Руководство по интеграции Azure Active Directory с UserVoice | Документация Майкрософт"
description: "Узнайте, как использовать UserVoice вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0bb23a4c2be40c6304d7a09a6ff978db0666ea18


---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Руководство. Интеграция Azure Active Directory с UserVoice
Цель данного учебника — показать интеграцию Azure и UserVoice.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент UserVoice

После прохождения этого учебника пользователи Azure AD, назначенные UserVoice, будут иметь возможность единого входа в приложение на веб-сайте UserVoice вашей компании (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для UserVoice 
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scenario")

## <a name="enabling-the-application-integration-for-uservoice"></a>Включение интеграции приложений для UserVoice 
В этом разделе показано, как включить интеграцию приложений для UserVoice.

### <a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для UserVoice, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **UserVoice**.
   
   ![Коллекция приложений](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Application gallery")
7. В области результатов выберите **UserVoice** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в UserVoice с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для UserVoice, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **UserVoice** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configure single sign-on")
2. На странице **Как пользователи будут входить в UserVoice?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в UserVoice** введите свой URL-адрес в формате *https://\<имя_клиента\>.UserVoice.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configure App URL")
4. На странице **Настройка единого входа в UserVoice** щелкните **Скачать сертификат**, а затем сохраните файл сертификата локально как **c:\\UserVoice.cer**.
   
   ![Настройка единого входа](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configure single sign-on")
5. В другом окне веб-браузера войдите на веб-сайт UserVoice вашей компании в качестве администратора.
6. На панели инструментов в меню в верхней части экрана щелкните "Параметры", а затем — "Веб-портал".
   
   ![Параметры](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Settings")
7. На вкладке **Web portal** (Веб-портал) в разделе **User authentication** (Проверки подлинности пользователя) щелкните **Edit** (Изменить), чтобы открыть диалоговую страницу **Edit User Authentication** (Изменение проверки подлинности пользователя).
   
   ![Веб-портал](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Web portal")
8. На странице **Изменение проверки подлинности пользователя** выполните следующие действия.
   
   ![Изменение проверки подлинности пользователя](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Edit user authentication")
   
   1. Выберите **Единый вход (SSO)**.
   2. На странице **Настройка единого входа в UserVoice** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **SSO Remote Sign-In** (URL-адрес удаленного входа для единого входа).
   3. На странице диалогового окна **Настройка единого входа в UserVoice** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **SSO Remote Sign-Out** (URL-адрес удаленного выхода для единого входа).
   4. Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **Current certificate SHA1 fingerprint** (Отпечаток текущего сертификата SHA1).  
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   5. Нажмите кнопку **Сохранить параметры проверки подлинности**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в UserVoice, они должны быть подготовлены для UserVoice.  
В случае с UserVoice подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Войдите в клиента **UserVoice** .
2. Перейдите в меню **Параметры**.
   
   ![Параметры](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Settings")
3. Выберите пункт **Общие**.
4. Выберите пункт **Агенты и разрешения**.
   
   ![Агенты и разрешения](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agents and permissions")
5. Нажмите кнопку **Добавить администраторов**.
   
   ![Добавить администраторов](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Add admins")
6. В диалоговом окне **Пригласить администраторов** выполните следующие действия.
   
   ![Пригласить администраторов](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invite admins")
   
   1. В текстовом поле "Электронная почта" введите адрес электронной почты учетной записи, которую вы хотите подготовить, а затем нажмите кнопку **Добавить**.
   2. Щелкните **Invite**(Пригласить).

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя UserVoice или API-интерфейсы, предоставляемые UserVoice для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Чтобы назначить пользователей UserVoice, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **UserVoice** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


