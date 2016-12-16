---
title: "Учебник. Интеграция Azure Active Directory с Freshdesk | Документация Майкрософт"
description: "Узнайте, как использовать Freshdesk вместе с Azure Active Directory для реализации единого входа, автоматической подготовки и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31177994-7910-4a72-bd76-5fa6260242fb
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7043787c74c138984fdf5370784678d6dfcaa81e


---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Руководство. Интеграция Azure Active Directory с Freshdesk
Цель данного учебника — показать интеграцию Azure и Freshdesk.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Freshdesk

После выполнения действий, описанных в этом руководстве, пользователи Azure AD, которых вы прикрепите к Freshdesk, смогут использовать единый вход в приложение на веб-сайте Freshdesk вашей организации (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Freshdesk
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scenario")

## <a name="enabling-the-application-integration-for-freshdesk"></a>Включение интеграции приложений для Freshdesk
В этом разделе показано, как включить интеграцию приложений для Freshdesk.

### <a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Freshdesk, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Freshdesk**.
   
   ![Коллекция приложений](./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Application gallery")
7. В области результатов выберите **Freshdesk** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Freshdesk](./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Freshdesk со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Freshdesk, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Freshdesk** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Freshdesk?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Freshdesk** введите свой URL-адрес в формате *https://\<имя_клиента\>.Freshdesk.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configure App URL")
4. На странице **Настройка единого входа в Freshdesk** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата локально в формате **c:\\Freshdesk.cer**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configure Single Sign-On")
5. В другом окне браузера войдите на свой корпоративный веб-сайт Freshdesk в качестве администратора.
6. В верхнем меню щелкните **Администратор**.
   
   ![Администратор](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")
7. На вкладке **General Settings** (Общие параметры) щелкните **Security** (Безопасность).
   
   ![Безопасность](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")
8. В разделе **Security** (Безопасность) выполните следующие действия.
   
   ![Единый вход](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")
   
   1. Выберите для параметра **Single Sign On (SSO)** (Единый вход) значение **On** (Включено).
   2. Выберите **Единый вход SAML**.
   3. На странице диалогового окна **Настройка единого входа в Freshdesk** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **SAML Login URL** (URL-адрес входа SAML).
   4. На странице диалогового окна **Настройка единого входа в Freshdesk** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).
   5. Скопируйте значение поля **Thumbprint** (Отпечаток) из экспортированного сертификата и вставьте его в текстовое поле **Security Certificate Fingerprint** (Отпечаток сертификата безопасности).  
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Freshdesk, они должны быть подготовлены для Freshdesk.  
В случае с Freshdesk подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход в клиент **Freshdesk** .
2. В верхнем меню щелкните **Администратор**.
   
   ![Администратор](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")
3. На вкладке **General Settings** (Общие параметры) выберите **Agents** (Агенты).
   
   ![Агенты](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")
4. Нажмите **Создать агента**.
   
   ![Создать агента](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")
5. В диалоговом окне "Сведения об агенте " выполните следующие действия.
   
   ![Сведения об агенте](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   1. В текстовое поле **Полное имя** введите имя учетной записи Azure AD, которую желаете подготовить.
   2. В текстовое поле **Электронная почта** введите адрес электронной почты той учетной записи Azure AD, которую вы хотите подготовить.
   3. В текстовое поле **Название** введите название учетной записи Azure AD, которую желаете подготовить.
   4. Выберите **Agents role** (Роль агента) и нажмите кнопку **Assign** (Назначить).
   5. Щелкните **Сохранить**.
      
      > [!NOTE]
      > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.
      > 
      > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Freshdesk или API, предоставляемые Freshdesk, для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Чтобы назначить пользователей Freshdesk, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Freshdesk** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


