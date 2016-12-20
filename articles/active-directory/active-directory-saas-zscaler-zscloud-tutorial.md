---
title: "Руководство по интеграции Azure Active Directory с Zscaler ZSCloud | Документация Майкрософт"
description: "Узнайте, как использовать Zscaler ZSCloud вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cc98bbd4e7febcace445d28f8c5fbc70e5d2d82


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Руководство. Интеграция Azure Active Directory с Zscaler ZSCloud
Цель данного учебника — показать интеграцию Azure и Zscaler ZSCloud.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Zscaler ZSCloud

После прохождения этого учебника пользователи Azure AD, назначенные ZScaler ZSCloud, будут иметь возможность единого входа в приложение на сайте компании ZScaler ZSCloud (вход, инициированный поставщиком услуг) или входа с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md)

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Zscaler ZSCloud
2. Настройка единого входа
3. Настройка параметров прокси-сервера
4. Настройка подготовки учетных записей пользователей
5. Назначение пользователей

![Сценарий](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800275.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler-zscloud"></a>Включение интеграции приложений для Zscaler ZSCloud
В этом разделе показано, как включить интеграцию приложений для Zscaler ZSCloud.

### <a name="to-enable-the-application-integration-for-zscaler-zscloud-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Zscaler ZSCloud, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **ZScaler ZSCloud**.
   
   ![Коллекция приложений](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800276.png "Application Gallery")
7. В области результатов выберите **ZScaler ZSCloud** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![ZScaler ZSCloud](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800277.png "ZScaler ZSCloud")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Zscaler ZSCloud со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат в кодировке Base-64 в клиент Zscaler ZSCloud.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **ZScaler ZSCloud** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800278.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в ZScaler ZSCloud?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800279.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в ZScaler ZSCloud** введите URL-адрес, используемый пользователями для входа в приложение ZScaler ZSCloud, и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800280.png "Configure App URL")
   
   > [!NOTE]
   > Фактическое значение для вашей среды можно получить от службы поддержки Zscaler ZSCloud.
   > 
   > 
4. Чтобы скачать сертификат, на странице **Настройка единого входа в ZScaler ZSCloud** щелкните **Скачать сертификат** и сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800281.png "Configure Single Sign-On")
5. В другом окне браузера войдите на свой корпоративный сайт Zscaler ZSCloud в качестве администратора.
6. В верхнем меню щелкните **Администрирование**.
   
   ![Администрирование](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800206.png "Administration")
7. В разделе **Manage Administrators & Roles** (Управление администраторами и ролями) щелкните **Manage Users & Authentication** (Управление пользователями и проверкой подлинности).
   
   ![Управление пользователями и проверкой подлинности](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800207.png "Manage Users & Authentication")
8. В разделе **Выбор параметров проверки подлинности для организации** выполните следующие действия.
   
   ![Аутентификация](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800208.png "Authentication")
   
   1. Выберите параметр **Проверка подлинности с помощью единого входа SAML**.
   2. Щелкните **Настроить параметры единого входа SAML**.
9. На странице диалогового окна **Configure SAML Single Sign-On Parameters** (Настройка параметров единого входа в SAML) выполните следующие действия и нажмите кнопку **Готово**.
   
   ![Единый вход](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800209.png "Single Sign-On")
   
   1. На странице диалогового окна **Настройка единого входа в ZScaler ZSCloud** классического портала Azure скопируйте значение поля **URL-адрес запроса проверки подлинности** и вставьте его в текстовое поле **URL of the SAML Portal to which users are sent for authentication** (URL-адрес портала SAML, куда пользователи направляются для проверки подлинности).
   2. В текстовом поле **Attribute containing Login Name** (Атрибут, содержащий имя входа) введите **NameID**.
   3. Чтобы передать скачанный сертификат, щелкните **Zscaler pem**.
   4. Выберите параметр **Включить автоматическую подготовку SAML**.
10. На странице **Настройка проверки подлинности пользователей** выполните следующие действия.
    
    ![Администрирование](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800210.png "Administration")
    
    1. Щелкните **Сохранить**.
    2. Щелкните **Активировать сейчас**.
11. На странице диалогового окна **Настройка единого входа в ZScaler ZSCloud** классического портала Azure подтвердите конфигурацию единого входа и нажмите кнопку **Завершить**.
    
    ![Настройка единого входа](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800282.png "Configure Single Sign-On")

## <a name="configuring-proxy-settings"></a>Настройка параметров прокси-сервера
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Настройка параметров прокси-сервера в Internet Explorer
1. Запустите **Internet Explorer**.
2. В меню **Сервис** выберите **Свойства браузера**, чтобы открыть диалоговое окно **Свойства браузера**.
   
   ![Свойства браузера](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769492.png "Internet Options")
3. Щелкните вкладку **Подключения** .
   
   ![Подключения](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769493.png "Connections")
4. Нажмите кнопку **Настройка сети**, чтобы открыть диалоговое окно **Настройка сети**.
5. В разделе "Прокси-сервер" выполните следующие действия.
   
   ![Прокси-сервер](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769494.png "Proxy server")
   
   1. Установите флажок "Использовать прокси-сервер для локальных подключений".
   2. В текстовом поле "Адрес" введите **gateway.zscalerone.net**.
   3. В текстовом поле "Порт" введите **80**.
   4. Установите флаг **Не использовать прокси-сервер для локальных адресов**.
   5. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Настройка параметров локальной сети**.
6. Нажмите кнопку **ОК**, чтобы закрыть диалоговое окно **Свойства браузера**.

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли входить в ZScaler ZSCloud, их необходимо подготовить для ZScaler ZSCloud.  
В случае с ZScaler ZSCloud подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите в клиент **ZScaler** .
2. Щелкните **Администрирование**.
   
   ![Администрирование](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781035.png "Administration")
3. Щелкните **Управление пользователями**.
   
   ![Добавить](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Add")
4. На вкладке **Users** (Пользователи) нажмите кнопку **Add** (Добавить).
   
   ![Добавить](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Add")
5. В разделе "Добавить пользователя" выполните следующие действия.
   
   ![Добавить пользователя](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781038.png "Add User")
   
   1. Заполните текстовые поля **UserID** (Идентификатор пользователя), **User Display Name** (Отображаемое имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля) и выберите **Groups** (Группы) и **Department** (Отдел) действующей учетной записи AAD, которую необходимо подготовить.
   2. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя ZScaler ZSCloud или API, предоставляемые ZScaler ZSCloud для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-zscaler-zscloud-perform-the-following-steps"></a>Чтобы назначить пользователей ZScaler ZSCloud, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **ZScaler ZSCloud** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800283.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-zscaler-zscloud-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


