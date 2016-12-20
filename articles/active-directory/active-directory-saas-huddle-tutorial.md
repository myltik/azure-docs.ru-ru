---
title: "Учебник. Интеграция Azure Active Directory с Huddle | Документация Майкрософт"
description: "Узнайте, как использовать Huddle вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85d7789599cfb39ce0de5d52e0fe057482a49039


---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Руководство. Интеграция Azure Active Directory с Huddle
Цель данного руководства — показать интеграцию Azure и Huddle.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Huddle

После завершения этого руководства пользователи Azure AD, назначенные Huddle, будут иметь возможность единого входа в приложение на корпоративном веб-сайте Huddle (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Huddle
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Настройка единого входа](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-huddle"></a>Включение интеграции приложений для Huddle
В этом разделе показано, как включить интеграцию приложений для Huddle.

### <a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Huddle, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-huddle-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-huddle-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Huddle**.
   
   ![Коллекция приложений](./media/active-directory-saas-huddle-tutorial/IC787831.png "Application Gallery")
7. В области результатов выберите **Huddle** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Huddle со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Huddle** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Huddle?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Huddle** введите URL-адрес клиента Huddle, используя шаблон *http://компания.huddle.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configure App URL")
4. На странице **Настройка единого входа в Huddle** выполните следующие действия.
   
   ![Настройка единого входа](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configure Single Sign-On")
   
   1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   2. Скопируйте значения полей **URL-адрес издателя** и **URL-адрес единого входа SAML**, а также скачанный сертификат, и отправьте их в службу поддержки Huddle.
   
   > [!NOTE]
   > Единый вход должна включить служба поддержки Huddle.
   > Сразу же после завершения настройки вы получите уведомление.
   > 
   > 
5. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Huddle, они должны быть подготовлены для Huddle.  
В случае с Huddle подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт компании **Huddle** в качестве администратора.
2. Нажмите **Рабочая область**.
3. Щелкните **People \> Invite People** (Пользователи > Пригласить пользователей).
   
   ![Люди](./media/active-directory-saas-huddle-tutorial/IC787838.png "People")
4. В разделе **Создание нового приглашения** выполните следующие действия.
   
   ![Новое приглашение](./media/active-directory-saas-huddle-tutorial/IC787839.png "New Invitation")
   
   1. В списке **Choose a team to invite people to join** (Выберите группу, в которую следует пригласить пользователей) выберите **группу**.
   2. Введите **Адрес электронной почты** действующей учетной записи AAD, которую вы хотите подготовить, в соответствующем текстовом поле.
   3. Нажмите кнопку **Пригласить**.
   
   > [!NOTE]
   > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Huddle или API, предоставляемые Huddle для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Чтобы назначить пользователей Huddle, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Huddle** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-huddle-tutorial/IC787840.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-huddle-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


