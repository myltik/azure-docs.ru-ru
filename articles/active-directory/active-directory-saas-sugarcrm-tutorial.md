---
title: "Руководство по интеграции Azure Active Directory с SugarCRM | Документация Майкрософт"
description: "Узнайте, как использовать SugarCRM с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75130750d957af6af3bf1ea2e20e75e845aad3da


---
# <a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>Учебник. Интеграция Azure Active Directory с SugarCRM
Цель данного руководства — показать интеграцию Azure и SugarCRM.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка SugarCRM с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные в SugarCRM, смогут выполнять единый вход в приложение на веб-сайте SugarCRM компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для SugarCRM
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenario")

## <a name="enabling-the-application-integration-for-sugar-crm"></a>Включение интеграции приложений для SugarCRM
В этом разделе показано, как включить интеграцию приложений для SugarCRM.

### <a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для SugarCRM, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **SugarCRM**.
   
   ![Коллекция приложений](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Application Gallery")
7. В области результатов выберите **SugarCRM** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![SugarCRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить аутентификацию в SugarCRM со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат в кодировке Base-64 в клиент SugarCRM.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **SugarCRM** классического портала Azure AD нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в SugarCRM** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес для входа в SugarCRM** введите URL-адрес, используемый пользователями для входа в приложение SugarCRM (например, *http://company.sugarondemand.com*) и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configure App URL")
4. Чтобы скачать сертификат, на странице **Настройка единого входа в SugarCRM** щелкните **Скачать сертификат** и сохраните файл сертификата на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на сайт SugarCRM своей компании в качестве администратора.
6. Перейдите в раздел **Администратор**.
   
   ![Администратор](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")
7. В разделе **Administration** (Администрирование) щелкните **Password Management** (Управление паролями).
   
   ![Администрирование](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")
8. Установите флажок **Включить проверку подлинности SAML**.
   
   ![Администрирование](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")
9. В разделе **Проверка подлинности SAML** сделайте следующее:
   
   ![Проверка подлинности SAML](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML Authentication")
   
   1. На странице диалогового окна **Настройка единого входа в SugarCRM** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес входа**.
   2. На странице диалогового окна **Настройка единого входа в SugarCRM** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **SLO URL** (URL-адрес единого выхода).
   3. Создайте файл **в кодировке Base-64** из скачанного сертификата.
      
      > [!TIP]
      > Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена и вставьте весь сертификат в текстовое поле **Сертификат X.509** .
   5. Щелкните **Сохранить**.
10. На странице диалогового окна **Настройка единого входа в SugarCRM** классического портала Azure подтвердите настройку единого входа и щелкните **Завершить**.
    
    ![Настройка единого входа](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли входить в SugarCRM, их необходимо подготовить для SugarCRM.  
В случае SugarCRM подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Войдите на веб-сайт **SugarCRM** компании в качестве администратора.
2. Перейдите в раздел **Администратор**.
   
   ![Администратор](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")
3. В разделе **Administration** (Администрирование) щелкните **User Management** (Управление пользователями).
   
   ![Администрирование](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")
4. Выберите **Users (Пользователи) \> Create New User (Создать нового пользователя)**.
   
   ![Создание нового пользователя](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Create New User")
5. На вкладке **Профиль пользователя** сделайте следующее:
   
   ![Новый пользователь](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "New User")
   
   1. В соответствующие текстовые поля введите имя, фамилию и электронный адрес действующей учетной записи Azure Active Directory.
6. Для параметра **Status** (Состояние) выберите значение **Active** (Активно).
7. На вкладке «Пароль» выполните следующие действия:
   
   ![Новый пользователь](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "New User")
   
   1. Введите пароль в соответствующее текстовое поле.
   2. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей SugarCRM или API, предоставляемые SugarCRM для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>Чтобы назначить пользователей SugarCRM, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **SugarCRM** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


