---
title: "Руководство. Интеграция Azure Active Directory с Zscaler | Документация Майкрософт"
description: "Узнайте, как использовать Zscaler вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: 22f83af7fc19410df01350d40619e6520878bec7


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Руководство. Интеграция Azure Active Directory с Zscaler
Цель данного учебника — показать интеграцию Azure и Zscaler. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент в Zscaler

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Zscaler 
2. Настройка единого входа
3. Настройка параметров прокси-сервера
4. Настройка подготовки учетных записей пользователей
5. Назначение пользователей

![Сценарий](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Включение интеграции приложений для Zscaler 
В этом разделе показано, как включить интеграцию приложений для Zscaler.

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Zscaler, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Add an application from gallerry")

6. В **поле поиска** введите **Zscaler**.
   
    ![Коллекция приложений](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Application gallery")

7. В области результатов выберите **Zscaler** и нажмите кнопку **Завершить**, чтобы добавить это приложение.
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Zscaler со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат на сайт Zscaler.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **Zscaler** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Включить единый вход](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Enable single sign-on")

2. На странице **Как пользователи должны входить в Zscaler** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configure single sign on")

3. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес входа в Zscaler** введите свой URL-адрес, полученный от Zscaler, и нажмите кнопку **Далее**. 
   
    > [!NOTE]
    > Если вы не знаете URL-адрес входа, обратитесь в службу поддержки Zscaler.
    > 
    > 
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configure app URL")

4. На странице **Настройка единого входа в Zscaler** сделайте следующее:
   
    ![Настройка единого входа](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configure single sign-on")
   
    1. Щелкните **Скачать сертификат** и сохраните файл сертификата как **c:\\Zscaler.cer**.
    2. Скопируйте **URL-адрес для проверки подлинности запроса** в буфер обмена.

5. Выполните вход в клиент Zscaler.

6. В верхнем меню щелкните **Администрирование**.
   
    ![Администрирование](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7. В разделе **Manage Administrators & Roles** (Управление администраторами и ролями) щелкните **Mange Users & Authentication** (Управление пользователями и аутентификацией).
   
    ![Управление администраторами и ролями](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Manage Administrators & Roles")

8. В разделе **Выбор параметров проверки подлинности для организации** сделайте следующее:
   
    ![Выбор параметров проверки подлинности](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choose Authentication Options")
   
    1. Выберите параметр **Проверка подлинности с помощью единого входа SAML**.
    2. Щелкните **Настроить параметры единого входа SAML**.

9. На странице диалогового окна **Configure SAML Single Sign-On Parameters** (Настройка параметров единого входа в SAML) выполните следующие действия и нажмите кнопку **Готово**.
   
    ![Upload certificate](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Upload certificate")
   
    1. В поле **URL of the SAML Portal to which users are sent for authentication** (URL-адрес портала SAML, куда пользователи направляются для аутентификации) вставьте значение поля **URL-адрес запроса проверки подлинности** на классическом портале Azure.
   
    2. В текстовом поле **Attribute containing Login Name** (Атрибут, содержащий имя входа) введите **NameID**.
   
    3. В поле **Upload SSL Public Certificate** (Передача открытого сертификата SSL) передайте сертификат, загруженный с классического портала Azure.
   
    4. Выберите параметр **Включить автоматическую подготовку SAML**.

10. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.
    
    ![Настройка проверки подлинности пользователей](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configure User Authentication")
    
    1. Щелкните **Сохранить**.
    2. Щелкните **Активировать сейчас**.

11. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configure single sign-on")


## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer

1. Запустите **Internet Explorer**.

2. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.
   
    ![Свойства браузера](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internet Options")

3. Щелкните вкладку **Подключения** .
   
    ![Подключения](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connections")

4. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.

5. В разделе "Прокси-сервер" выполните следующие действия.
   
    ![Прокси-сервер](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy server")
   
    1. Установите флажок "Использовать прокси-сервер для локальных подключений".

    2. В текстовом поле «Адрес» введите **gateway.zscalertwo.net**.
   
    3. В текстовом поле "Порт" введите **80**.
   
    4. Установите флаг **Не использовать прокси-сервер для локальных адресов**.
   
    5. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.

6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли входить в ZScaler, их необходимо подготовить для ZScaler.  
В случае с ZScaler подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.

1. Войдите в клиент **ZScaler** .

2. Щелкните **Администрирование**.

    ![Администрирование](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3. Щелкните **Управление пользователями**.
   
    ![Управление пользователями](./media/active-directory-saas-zscaler-tutorial/IC781036.png "User Management")

4. На вкладке **Users** (Пользователи) нажмите кнопку **Add** (Добавить).
   
    ![Добавить](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Add")

5. В разделе "Добавить пользователя" выполните следующие действия.
   
    ![Добавить пользователя](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Add User")
   
    1. Заполните текстовые поля **UserID** (Идентификатор пользователя), **User Display Name** (Отображаемое имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля) и выберите **Groups** (Группы) и **Department** (Отдел) действующей учетной записи AAD, которую необходимо подготовить.
   
    2. Щелкните **Сохранить**.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Чтобы назначить пользователей ZScaler, выполните следующие действия.

1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **Zscaler** щелкните **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assign users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


