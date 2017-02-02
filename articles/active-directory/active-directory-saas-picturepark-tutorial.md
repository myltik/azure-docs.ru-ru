---
title: "Руководство по интеграции Azure Active Directory с Picturepark | Документация Майкрософт"
description: "Узнайте, как использовать Picturepark с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 98f2e5596a0af2fc9e633e005642cc3cd21621ce


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Учебник. Интеграция Azure Active Directory с Picturepark
Цель данного учебника — показать интеграцию Azure и Picturepark.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* Клиент Picturepark.

По завершении работы с этим руководством пользователи Azure AD, назначенные в Picturepark, смогут выполнять единый вход в приложение на веб-сайте Picturepark компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Picturepark
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")

## <a name="enabling-the-application-integration-for-picturepark"></a>Включение интеграции приложений для Picturepark
В этом разделе показано, как включить интеграцию приложений для Picturepark.

### <a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Picturepark, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Picturepark**.
   
   ![Коллекция приложений](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Application Gallery")
7. В области результатов выберите **Picturepark** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить аутентификацию в Picturepark со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Picturepark, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)..

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **Picturepark** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Picturepark?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Picturepark** введите свой URL-адрес, используя шаблон *http://company.picturepark.com* , а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configure App URL")
4. На странице **Настройка единого входа в Picturepark** щелкните **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на сайт Picturepark своей компании в качестве администратора.
6. Щелкните **Administrative tools** (Администрирование) на панели инструментов в верхней части страницы и выберите **Management Console** (Консоль управления).
   
   ![Консоль управления](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Management Console")
7. Щелкните **Authentication** (Аутентификация), а затем выберите **Identity providers** (Поставщики удостоверений).
   
   ![Аутентификация](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentication")
8. В разделе **Конфигурация поставщика удостоверений** сделайте следующее:
   
   ![Конфигурация поставщика удостоверений](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Identity provider configuration")
   
   1. Щелкните **Добавить**.
   2. Введите имя конфигурации.
   3. Выберите **По умолчанию**.
   4. На странице диалогового окна **Настройка единого входа в Picturepark** классического портала Azure скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **URI издателя**.
   5. Скопируйте значение **Thumbprint** из экспортированного сертификата и вставьте его в текстовое поле **Trusted Issuer Thumb Print** (Отпечаток надежного издателя).  
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. Щелкните **JoinDefaultUsersGroup**.
   7. Чтобы задать атрибут **Emailaddress**, в текстовом поле **Утверждение** введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
      ![Конфигурация](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
   8. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в Picturepark, они должны быть подготовлены для Picturepark.  
В случае с Picturepark подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход в клиент **Picturepark** .
2. Щелкните **Administrative tools** (Администрирование) на панели инструментов в верхней части страницы и выберите **Users** (Пользователи).
   
   ![Пользователи](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Users")
3. На вкладке **Users overview** (Обзор пользователей) щелкните **New** (Создать).
   
   ![Управление пользователями](./media/active-directory-saas-picturepark-tutorial/IC795068.png "User management")
4. В диалоговом окне **Создание пользователя** сделайте следующее:
   
   ![Создать пользователя](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Create User")
   
   1. Заполните текстовые поля **Email Address** (Адрес электронной почты), **Password** (Пароль), **Confirm Password** (Подтвердите пароль), **First Name** (Имя), **Last Name** (Фамилия), **Company** (Компания), **Country** (Страна), **ZIP** (Почтовый индекс) и **City** (Город) данными действующей учетной записи пользователя Azure Active Directory, которую требуется подготовить.
   2. В поле **Язык**укажите язык.
   3. Щелкните **Создать**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Picturepark или API, предоставляемые Picturepark для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Чтобы назначить пользователей Picturepark, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Picturepark** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


