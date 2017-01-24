---
title: "Руководство. Интеграция Azure Active Directory с AirWatch | Документация Майкрософт"
description: "Узнайте, как использовать AirWatch вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5f2030abf8b6c2d5884f4c5c2cb29d89ff329227


---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Руководство. Интеграция Azure Active Directory с AirWatch
Цель данного руководства — показать интеграцию Azure и AirWatch.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа AirWatch

После выполнения инструкций, приведенных в этом руководстве, пользователи Azure AD, добавленные к AirWatch, смогут выполнять единый вход в приложение на корпоративный веб-сайт AirWatch (вход, инициированный поставщиком услуг) или на панели доступа, как описано в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для AirWatch
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")

## <a name="enabling-the-application-integration-for-airwatch"></a>Включение интеграции приложений для AirWatch
В этом разделе показано, как включить интеграцию приложений для AirWatch.

### <a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для AirWatch, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-airwatch-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-airwatch-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-airwatch-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **AirWatch**.
   
   ![Коллекция приложений](./media/active-directory-saas-airwatch-tutorial/IC791914.png "Коллекция приложений")
7. В области результатов выберите **AirWatch** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в AirWatch со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **AirWatch** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в AirWatch?** выберите параметр **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Настройка единого входа")
3. На странице **Configure App URL** (Настройка URL-адреса приложения) введите в текстовом поле **AirWatch Sign On URL** (URL-адрес входа в AirWatch) URL-адрес, используемый для входа в приложение AirWatch (например, "*https://companycode.awmdm.com/AirWatch/Login?gid=companycode*"), и щелкните **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-airwatch-tutorial/IC791918.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в AirWatch** щелкните **Скачать сертификат** и сохраните файл сертификата на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Настройка единого входа")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт AirWatch в качестве администратора.
6. На панели навигации слева щелкните **Accounts** (Учетные записи), а затем — **Administrators** (Администраторы).
   
   ![Администраторы](./media/active-directory-saas-airwatch-tutorial/IC791920.png "Администраторы")
7. Разверните меню **Settings** (Параметры) и выберите пункт **Directory Services** (Службы каталогов).
   
   ![Параметры](./media/active-directory-saas-airwatch-tutorial/IC791921.png "Параметры")
8. Откройте вкладку **User** (Пользователь), введите в текстовом поле **Base DN** (Базовое доменное имя) свое доменное имя и щелкните **Save** (Сохранить).
   
   ![Пользователь](./media/active-directory-saas-airwatch-tutorial/IC791922.png "Пользователь")
9. Откройте вкладку **Server** (Сервер).
   
   ![Сервер](./media/active-directory-saas-airwatch-tutorial/IC791923.png "Сервер")
10. Выполните следующие действия:
    
    ![Передача](./media/active-directory-saas-airwatch-tutorial/IC791924.png "Передача")
    
    1. Для параметра **Directory Type** (Тип каталога) выберите значение **None** (Нет).
    2. Установите флажок **Use SAML For Authentication**(Использовать SAML для проверки подлинности).
    3. Чтобы отправить скачанный сертификат, нажмите кнопку **Upload**(Отправить).
11. В разделе **Request** (Запрос) выполните следующие действия.
    
    ![Запрос](./media/active-directory-saas-airwatch-tutorial/IC791925.png "Запрос")
    
    1. Для параметра **Request Binding Type** (Тип привязки запроса) выберите значение **POST**.
    2. На классическом портале Azure на диалоговой странице **Настройка единого входа в AirWatch** скопируйте значение поля **URL-адрес службы единого входа** и вставьте его в текстовом поле **Identity Provider Single Sign On URL** (URL-адрес единого входа для поставщика удостоверений).
    3. Для параметра **NameID Format** (Формат идентификатора имени) выберите значение **Email Address** (Адрес электронной почты).
    4. Щелкните **Сохранить**.
12. Снова откройте вкладку **Пользователь** .
    
    ![Пользователь](./media/active-directory-saas-airwatch-tutorial/IC791926.png "Пользователь")
13. В разделе **Атрибут** выполните следующие действия.
    
    ![Атрибут](./media/active-directory-saas-airwatch-tutorial/IC791927.png "Атрибут")
    
    1. В текстовом поле **Object Identifier** (Идентификатор объекта) введите **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2. В текстовом поле **Username** (Имя пользователя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3. В текстовом поле **Display Name** (Отображаемое имя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4. В текстовом поле **First Name** (Имя) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5. В текстовом поле **Last Name** (Фамилия) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6. В текстовом поле **Email** (Электронная почта) введите **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7. Щелкните **Сохранить**.
14. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Настройка единого входа")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в AirWatch, они должны быть подготовлены для AirWatch.  
В случае с AirWatch подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход на корпоративном веб-сайте **AirWatch** в качестве администратора.
2. На панели навигации слева щелкните **Accounts** (Учетные записи), а затем —**Users** (Пользователи).
   
   ![Пользователи](./media/active-directory-saas-airwatch-tutorial/IC791929.png "Пользователи")
3. В меню **Users** (Пользователи) выберите пункт **List View** (Представление списка), а затем щелкните **Add \> Add User** (Добавить > Добавить пользователя).
   
   ![Добавление пользователя](./media/active-directory-saas-airwatch-tutorial/IC791930.png "Добавление пользователя")
4. В диалоговом окне **Add / Edit User** (Добавление или изменение пользователя) выполните следующие действия.
   
   ![Добавление пользователя](./media/active-directory-saas-airwatch-tutorial/IC791931.png "Добавление пользователя")
   
   1. В текстовых полях **Username** (Имя пользователя), **Password** (Пароль), **Confirm Password** (Подтверждение пароля), **First Name** (Имя), **Last Name** (Фамилия) и **Email Address** (Адрес электронной почты) введите соответствующие данные действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя AirWatch или API, предоставляемые AirWatch для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Чтобы назначить пользователей AirWatch, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **AirWatch ** нажмите кнопку **Assign users** (Назначить пользователей).
   
   ![Назначение пользователей](./media/active-directory-saas-airwatch-tutorial/IC791932.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-airwatch-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO4-->


