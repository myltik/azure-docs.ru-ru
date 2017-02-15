---
title: "Учебник. Интеграция Azure Active Directory с Cisco Webex | Документация Майкрософт"
description: "Узнайте, как использовать Cisco Webex вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26cc94eeeac96ec4f81a7ba8013487448ae665c8


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Руководство. Интеграция Azure Active Directory с Cisco Webex
Цель данного учебника — показать интеграцию Azure и Cisco Webex.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Cisco Webex

После выполнения действий, описанных в этом руководстве, пользователи Azure AD, которых вы прикрепите к Cisco Webex, смогут использовать единый вход в приложение на веб-сайте Cisco Webex вашей организации (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Cisco Webex
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario")

## <a name="enabling-the-application-integration-for-cisco-webex"></a>Включение интеграции приложений для Cisco Webex
В этом разделе показано, как включить интеграцию приложений для Cisco Webex.

### <a name="to-enable-the-application-integration-for-cisco-webex-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Cisco Webex, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Cisco Webex**.
   
   ![Коллекция приложений](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Application Gallery")
7. В области результатов выберите **Cisco Webex** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Cisco Webex со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать сертификат в кодировке Base-64.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Cisco Webex** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в Cisco Webex?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configure app URL")
   
   1. В текстовом поле **URL-адрес для входа** введите URL-адрес клиента Cisco Webex (например, *http://contoso.webex.com*).
   2. В текстовом поле **URL-адрес ответа Cisco Webex** введите **URL-адрес службы AssertionConsumerService Cisco Webex** (например, *https://компания.webex.com/dispatcher/SAML2AuthService?siteurl=company*).
4. На странице **Настройка единого входа в Cisco Webex** нажмите кнопку **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configure single sign-on")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Cisco Webex в качестве администратора.
6. В верхнем меню нажмите **Администрирование веб-сайта**.
   
   ![Администрирование веб-сайта](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")
7. В разделе **Управление сайтом** выберите **SSO Configuration** (Настройка единого входа).
   
   ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")
8. В разделе "Настройка федеративного единого входа в Интернете" выполните следующие действия.
   
   ![Настройка федеративного единого входа](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")
   
   1. В списке **Federation Protocol** (Протокол федерации) выберите пункт **SAML 2.0**.
   2. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   3. Откройте сертификат в кодировке Base-64 в блокноте и скопируйте его содержимое.
   4. Нажмите **Импорт метаданных SAML**и вставьте сертификат в кодировке Base-64.
   5. На странице диалогового окна **Настройка единого входа в Cisco Webex** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Issuer for SAML (IdP ID)** (Издатель для SAML (идентификатор поставщика удостоверений)).
   6. На странице диалогового окна **Настройка единого входа в Cisco Webex** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Customer SSO Service Login URL** (URL-адрес входа в клиентскую службу единого входа).
   7. В списке **NameID Format** (Формат элемента NameID) выберите пункт **Адрес электронной почты**.
   8. В текстовом поле **AuthnContextClassRef** введите **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.
   9. На странице диалогового окна **Настройка единого входа в Cisco Webex** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Customer SSO Service Logout URL** (URL-адрес выхода для клиентской службы единого входа).
   10. Нажмите кнопку **Обновить**.
9. На странице диалогового окна **Настройка единого входа в Cisco Webex** классического портала Azure выберите подтверждение настройки единого входа и нажмите кнопку **Завершить**.
   
   ![Настройка единого входа](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Cisco Webex, они должны быть подготовлены для Cisco Webex.  
В случае с Cisco Webex подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Войдите в клиент **Cisco Webex** .
2. Последовательно выберите пункты **Manage Users \> Add User** (Управление пользователями > Добавить пользователя).
   
   ![Добавление пользователей](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Add users")
3. В разделе "Добавить пользователя" выполните следующие действия.
   
   ![Добавить пользователя](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Add user")
   
   1. Для параметра **Тип учетной записи** выберите значение **Узел**.
   2. Введите данные действующего пользователя Azure AD в следующие текстовые поля: **Имя, Фамилия**, **Имя пользователя**, **Адрес электронной почты**, **Пароль**, **Подтвердите пароль**.
   3. Щелкните **Добавить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетной записи пользователя Cisco Webex или API, предоставляемые Cisco Webex для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-cisco-webex-perform-the-following-steps"></a>Чтобы назначить пользователей Cisco Webex, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Cisco Webex** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


