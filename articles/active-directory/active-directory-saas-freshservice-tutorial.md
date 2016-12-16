---
title: "Учебник. Интеграция Azure Active Directory с FreshService | Документация Майкрософт"
description: "Узнайте, как использовать FreshService вместе с Azure Active Directory для реализации единого входа, автоматической подготовки и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6fa6a1817bc01e6d9f6b10f7e6479cdc4f60ca2


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Руководство. Интеграция Azure Active Directory с FreshService
Цель данного учебника — показать интеграцию Azure и FreshService.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа FreshService

После выполнения действий, описанных в этом руководстве, пользователи Azure AD, которых вы прикрепите к FreshService, смогут использовать единый вход в приложение через панель доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для FreshService
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")

## <a name="enabling-the-application-integration-for-freshservice"></a>Включение интеграции приложений для FreshService
В этом разделе показано, как включить интеграцию приложений для FreshService.

### <a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для FreshService, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **FreshService**.
   
   ![Коллекция приложений](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Application Gallery")
7. В области результатов выберите **FreshService** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![FreshService](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в FreshService со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для FreshService, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **FreshService** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в FreshService?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в FreshService** введите свой URL-адрес, используемый для входа в приложение Freshdesk (например, *http://компания.freshservice.com/*), и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configure App URL")
4. На странице **Настройка единого входа в FreshService** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт FreshService в качестве администратора.
6. В верхнем меню щелкните **Администратор**.
   
   ![Администратор](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
7. В области **Customer Portal** (Клиентский портал) выберите **Security** (Безопасность).
   
   ![Безопасность](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Security")
8. В разделе **Security** (Безопасность) выполните следующие действия.
   
   ![Единый вход](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")
   
   1. Включите **Единый вход**.
   2. Выберите **Единый вход SAML**.
   3. На странице диалогового окна **Настройка единого входа в FreshService** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **SAML Login URL** (URL-адрес входа SAML).
   4. На странице диалогового окна **Настройка единого входа в FreshService** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).
   5. Скопируйте значение поля **Thumbprint** (Отпечаток) из экспортированного сертификата и вставьте его в текстовое поле **Security Certificate Fingerprint** (Отпечаток сертификата безопасности).
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в FreshService, они должны быть подготовлены для FreshService.  
В случае с FreshService подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход на корпоративный сайт **FreshService** в качестве администратора.
2. В верхнем меню щелкните **Администратор**.
   
   ![Администратор](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
3. В разделе **User Management** (Управление пользователями) выберите **Requesters** (Инициаторы запроса).
   
   ![Инициаторы запроса](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")
4. Нажмите **Новый инициатор запроса**.
   
   ![Новые инициаторы запроса](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. В разделе **Новый инициатор запроса** выполните следующие действия.
   
   ![Новый инициатор запроса](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")
   
   1. В соответствующие текстовые поля введите атрибуты **First name** (Имя) и **Email** (Адрес электронной почты) действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.
   
   > [!NOTE]
   > Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя FreshService или API, предоставляемые FreshService для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Чтобы назначить пользователей FreshService, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **FreshService** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


