---
title: "Руководство. Интеграция Azure Active Directory с Workday | Документация Майкрософт"
description: "Узнайте, как использовать Workday вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a9b5410785c0d4a979d04483037069cad6a5dfd7


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Руководство. Интеграция Azure Active Directory с Workday
Цель данного руководства — продемонстрировать интеграцию Azure и Workday. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент в Workday

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Workday
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Настройка подготовки учетных записей пользователей

![Сценарий](./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario")

## <a name="enabling-the-application-integration-for-workday"></a>Включение интеграции приложений для Workday
В этом разделе показано, как включить интеграцию приложений для Salesforce.

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Workday, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4. Чтобы открыть **коллекцию приложений**, щелкните **Добавить приложение**, затем — **Добавить приложение для использования моей организацией**.
   
    ![Что необходимо сделать?](./media/active-directory-saas-workday-tutorial/IC700995.png "What do you want to do?")

5. В **поле поиска** введите **Workday**.
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6. В области результатов выберите **Workday** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Workday со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать сертификат в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Workday** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/IC782920.png "Configure single sign-on")

2. На странице **Как пользователи должны входить в Workday** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/IC782921.png "Configure single sign-on")

3. На странице **Настроить URL-адрес приложения** выполните следующие действия, а затем нажмите кнопку **Далее**.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-workday-tutorial/IC782957.png "Configure App URL")
   
    а. В текстовом поле **URL-адрес входа** введите URL-адрес, с помощью которого пользователи входят в Workday, в следующем формате: `https://impl.workday.com/<tenant>/login-saml2.htmld`.
   
    b.  В текстовом поле **URL-адрес ответа Workday** введите URL-адрес ответа Workday в следующем формате: `https://impl.workday.com/<tenant>/login-saml.htmld`.
   
    > [!NOTE]
    > URL-адрес ответа должен включать поддомен (например, www, wd2, wd3, wd3-impl, wd5, wd5-impl). Можно указать адрес вида *http://www.myworkday.com*, но формат *http://myworkday.com* не поддерживается. 
    > 
    > 

4. На странице **Настройка единого входа в Workday** нажмите кнопку **Скачать сертификат**, чтобы скачать сертификат, а затем сохраните файл сертификата на локальном компьютере.
 
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/IC782922.png "Configure single sign-on")

5. В другом окне браузера войдите на свой корпоративный сайт Workday в качестве администратора.

6. Выберите **меню \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7. Перейдите в раздел **Администрирование учетной записи**.
   
    ![Администрирование учетной записи](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

8. Выберите **Изменение настройки клиента — безопасность**.
   
    ![Изменение параметров безопасности клиента](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

9. В разделе **URL-адреса перенаправления** выполните следующие действия.
   
    ![URL-адреса перенаправления](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")
   
    а. Нажмите кнопку **Добавить строку**.
   
    b. В текстовых полях **URL-адрес перенаправления входа** и **Mobile Redirect URL** (URL-адрес перенаправления мобильных устройств) введите **URL-адрес клиента Workday**, который был введен на странице **Настроить URL-адрес приложения** на классическом портале Azure.
   
    В. На странице диалогового окна **Настройка единого входа в Workday** классического портала Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **Logout Redirect URL** (URL-адрес перенаправления выхода).
   
    d.  В текстовом поле **Среда** введите имя среды.  

    >[!NOTE]
    > Значение атрибута "Среда" привязано к значению URL-адреса клиента.  
    >-   Если URL-адрес имени домена клиента Workday начинается с Implementation (например: *https://impl.workday.com/\<клиент\>/login-saml2.htmld*), атрибуту **Среда** должно быть присвоено значение Implementation.  
    >-   Если имя домена начинается с другого значения, следует обратиться в Workday для получения соответствующего значения атрибута **Среда** .

1. В разделе **Настройка SAML** выполните следующие действия.
   
    ![Настройка SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")
   
    а.  Установите флажок **Включить проверку подлинности SAML**.
   
    b.  Нажмите кнопку **Добавить строку**.

2. В разделе Поставщики удостоверений SAML" выполните следующие действия.
   
    ![Поставщики удостоверений SAML](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")
   
    а. В текстовом поле «Имя поставщика удостоверений» введите имя поставщика (например, *SPInitiatedSSO*).
   
    b. На странице диалогового окна **Настройка единого входа в Workday** классического портала Azure скопируйте значение поля **Идентификатор поставщика удостоверений** и вставьте его в текстовое поле **Издатель**.
   
    c. Установите флажок **Включить выход, инициируемый Workday**.
   
    d. На странице диалогового окна **Настройка единого входа в Workday** классического портала Azure скопируйте значение поля **URL-адрес службы единого выхода** и вставьте его в текстовое поле **Logout Request URL** (URL-адрес запроса на выход).

    д. Щелкните **Identity Provider Public Key Certificate** (Сертификат открытого ключа поставщика удостоверений), а затем нажмите кнопку **Создать**. 

    ![Создание](./media/active-directory-saas-workday-tutorial/IC782928.png "Create")

    Е. Щелкните **Create x509 Public Key**(Создать открытый ключ x509). 

    ![Создать](./media/active-directory-saas-workday-tutorial/IC782929.png "Create")


1. В разделе **View x509 Public Key** (Просмотр открытого ключа x509) сделайте следующее. 
   
    ![View x509 Public Key](./media/active-directory-saas-workday-tutorial/IC782930.png "View x509 Public Key") 
   
    а. В текстовом поле **Имя** введите имя сертификата (например, *PPE\_SP*).
   
    b. В текстовом поле **Действителен с** введите начальную дату действия сертификата.
   
    c.  В текстовом поле **Действителен до** введите конечную дату действия сертификата.
   
    > [!NOTE]
    > Чтобы получить начальную и конечную даты, дважды щелкните скачанный сертификат.  Даты указаны на вкладке **Подробности** .
    > 
    > 
   
    d. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
   
    > [!TIP]
    > Дополнительные сведения можно узнать из видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    д.  Откройте сертификат в кодировке Base-64 в блокноте и скопируйте его содержимое.
   
    Е.  Вставьте содержимое буфера обмена в текстовое поле **Сертификат** .
   
    g.  Нажмите кнопку **ОК**.

2. Выполните следующие действия: 
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO configuration")
   
    а.  Установите флажок **Пара закрытых ключей x509**.
   
    b.  В текстовом поле **Идентификатор поставщика службы** введите **http://www.workday.com**.
   
    В.  Установите флажок **Включить проверку подлинности SAML, инициированную поставщиком услуг**.
   
    d.  На странице диалогового окна **Настройка единого входа в Workday** классического портала Azure скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовое поле **IdP SSO Service URL** (URL-адрес службы единого входа IdP).
   
    д. Выберите параметр **Не отклонять запрос проверки подлинности, инициированный поставщиком услуг**.
   
    Е. Для параметра **Authentication Request Signature Method** (Метод подписи запроса аутентификации) выберите значение **SHA256**. 
   
    ![Метод подписи запроса проверки подлинности](./media/active-directory-saas-workday-tutorial/IC782932.png "Authentication Request Signature Method") 
   
    g. Нажмите кнопку **ОК**. 
   
    ![ОК](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

3. На странице **Настройка единого входа в Workday** классического портала Azure нажмите кнопку **Далее**. 
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/IC782934.png "Configure single sign-on")

4. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**. 
   
    ![Настройка единого входа](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы подготовить тестового пользователя в Workday, необходимо обратитесь в службу поддержки Workday.  
Служба поддержки создаст пользователя для вас.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-workday-perform-the-following-steps"></a>Чтобы назначить пользователей Workday, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением **Workday** щелкните **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-workday-tutorial/IC782935.png "Assign Users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-workday-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


