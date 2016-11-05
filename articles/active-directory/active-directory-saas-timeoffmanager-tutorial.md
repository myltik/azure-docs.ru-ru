---
title: Учебник. Интеграция Azure Active Directory с TimeOffManager | Microsoft Docs
description: Узнайте, как использовать TimeOffManager с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-timeoffmanager"></a>Учебник. Интеграция Azure Active Directory с TimeOffManager
Цель данного учебника — показать интеграцию Azure и TimeOffManager.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа TimeOffManager

По завершении работы с этим руководством пользователи Azure AD, назначенные в TimeOffManager, смогут выполнять единый вход в приложение на веб-сайте TimeOffManager компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для TimeOffManager
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

## <a name="enabling-the-application-integration-for-timeoffmanager"></a>Включение интеграции приложений для TimeOffManager
В этом разделе показано, как включить интеграцию приложений для TimeOffManager.

### <a name="to-enable-the-application-integration-for-timeoffmanager,-perform-the-following-steps:"></a>Чтобы включить интеграцию приложений для TimeOffManager, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **TimeOffManager**.
   
   ![Коллекция приложений](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Application Gallery")
7. В области результатов выберите **TimeOffManager** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в TimeOffManager со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется передать в клиент TimeOffManager сертификат в кодировке Base-64.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **TimeOffManager** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в TimeOffManager?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configure Single Sign-On")
3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес ответа TimeOffManager** введите URL-адрес службы TimeOffManager AssertionConsumerService (например, *https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*), а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configure App URL")
   
   Получить URL-адрес ответа можно на странице параметров единого входа в TimeOffManager.
   
   ![Параметры единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Single Sign-On Settings")
4. Чтобы скачать сертификат, на странице **Настройка единого входа в TimeOffManager** щелкните **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт TimeOffManager в качестве администратора.
6. Выберите **Account \> Account Options \> Single Sign-On Settings** ("Учетная запись" > "Параметры учетной записи" > "Параметры единого входа").
   
   ![Параметры единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Single Sign-On Settings")
7. В разделе **Параметры единого входа** сделайте следующее:
   
   ![Параметры единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Single Sign-On Settings")
   
   а.  Создайте файл **в кодировке Base-64** из скачанного сертификата.  
   
       >[AZURE.TIP] Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
   
   b.  Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена и вставьте весь сертификат в текстовое поле **Сертификат X.509** .
   
   c.  На странице диалогового окна **Настройка единого входа в TimeOffManager** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Idp Issuer** (Издатель Idp).
   
   d.  На странице диалогового окна **Настройка единого входа в TimeOffManager** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **IdP Endpoint URL** (URL-адрес конечной точки IdP).
   
   д.  Для параметра **Enforce SAML** (Применить SAML) выберите значение **No** (Нет).

    Е.  Для параметра **Auto-Create Users** (Автосоздание пользователей) выберите значение **Yes** (Да).

    ж.  На странице диалогового окна **Настройка единого входа в TimeOffManager** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода**.

    h.  Нажмите кнопку **Сохранить изменения**.

1. На странице диалогового окна **Настройка единого входа в TimeOffManager** классического портала Azure выберите подтверждение настройки единого входа и нажмите кнопку **Завершить**.
   
   ![Настройка единого входа](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configure Single Sign-On")
2. В меню в верхней части экрана выберите пункт **Атрибуты** to open the **SAML Token Атрибуты** .
   
   ![Атрибуты](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributes")
3. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.
   
   ![Атрибуты токена SAML](./media/active-directory-saas-timeoffmanager-tutorial/123.png "saml token attributes")
   
   | Имя атрибута | Значение атрибута |
   | --- | --- |
   | Email |User.mail |
   | Firstname |User.givenname |
   | Lastname |User.surname |
   
   а.  Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
   
   b.  В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
   
   c.  В текстовом поле **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
   
   d.  Нажмите **Завершено**.
4. Нажмите кнопку **Применить изменения**.

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли входить в систему TimeOffManager, их необходимо подготовить для нее.  
TimeOffManager поддерживает автоматическую подготовку пользователей. С вашей стороны никакие действия не требуются.  
Пользователи автоматически добавляются при первом входе с помощью единого входа.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя TimeOffManager или API-интерфейсы, предоставляемые TimeOffManager для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-timeoffmanager,-perform-the-following-steps:"></a>Чтобы назначить пользователей для TimeOffManager, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **TimeOffManager** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


