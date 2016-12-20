---
title: "Учебник. Интеграция Azure Active Directory с Jobscience | Документация Майкрософт"
description: "Узнайте, как использовать Jobscience вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e4e665ced0fa4e2749c052c2cd087ffb0ee698cb


---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Руководство. Интеграция Azure Active Directory с Jobscience
Цель данного учебника — показать интеграцию Azure и Jobscience.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Jobscience

По завершении работы с этим руководством пользователи Azure AD, назначенные в Jobscience, смогут выполнять единый вход в приложение на веб-сайте Jobscience компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Jobscience
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")

## <a name="enabling-the-application-integration-for-jobscience"></a>Включение интеграции приложений для Jobscience
В этом разделе показано, как включить интеграцию приложений для Jobscience.

### <a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Jobscience, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **jobscience**.
   
   ![Коллекция приложений](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Application Gallery")
7. В области результатов выберите **Jobscience** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Jobscience со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Jobscience, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. Выполните вход на веб-сайт компании Jobscience в качестве администратора.
2. Перейдите в раздел **Настройка**.
   
   ![Настройка](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")
3. В области навигации слева в разделе **Administer** (Администрирование) щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, а затем щелкните **My Domain** (Мой домен), чтобы открыть страницу **My Domain** (Мой домен). 
   
   ![Мой домен](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
4. Чтобы проверить правильность настройки домена, убедитесь, что он находится на этапе **Step 4 Deployed to Users** (Шаг 4. Развернут для пользователей), и просмотрите раздел **My Domain Settings** (Параметры моего домена).
   
   ![Домен развернут для пользователя](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman Deployed to User")
5. В другом окне веб-браузера войдите на классический портал Azure.
6. На странице интеграции с приложением **Jobscience** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configure Single Sign-On")
7. На странице **Как пользователи должны входить в Jobscience?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configure Single Sign-On")
8. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Jobscience** введите свой URL-адрес в формате *http://компания.my.salesforce.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configure App URL")
9. На странице **Настройка единого входа в Jobscience** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configure Single Sign-On")
10. На корпоративном сайте Jobscience щелкните **Security Controls** (Средства управления безопасностью), а затем выберите **Single Sign-On Settings** (Параметры единого входа).
    
    ![Средства управления безопасностью](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")
11. В разделе **Параметры единого входа** сделайте следующее:
    
    ![Параметры единого входа](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")
    
    1. Установите флажок **SAML включен**.
    2. Нажмите кнопку **Создать**.
12. В диалоговом окне **Изменение параметров единого входа SAML** выполните следующие действия.
    
    ![Параметры единого входа SAML](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")
    
    1. В текстовом поле **Имя** введите имя конфигурации.
    2. На странице диалогового окна **Настройка единого входа в Jobscience** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Issuer** (Издатель).
    3. В текстовом поле **Entity Id** (Идентификатор сущности) введите **https://salesforce-jobscience.com**.
    4. Чтобы отправить сертификат Azure AD, нажмите кнопку **Обзор** .
    5. В поле **SAML Identity Type** (Тип удостоверения SAML) выберите значение **Assertion contains the Federation ID from the User object** (Проверочное утверждение содержит идентификатор федерации из объекта User).
    6. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentfier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).
    7. На странице диалогового окна **Настройка единого входа в Jobscience** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений).
    8. На странице диалогового окна **Настройка единого входа в Jobscience** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений).
    9. Щелкните **Сохранить**.
13. В области навигации слева в разделе **Administer** (Администрирование) щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, а затем щелкните **My Domain** (Мой домен), чтобы открыть страницу **My Domain** (Мой домен). 
    
    ![Мой домен](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
14. На странице **My Domain** (Мой домен) в разделе **Login Page Branding** (Фирменная символика страницы входа) нажмите кнопку **Edit** (Изменить).
    
    ![Фирменная символика страницы входа](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")
15. На странице **Login Page Branding** (Фирменная символика страницы входа) в разделе **Authentication Service** (Служба аутентификации) отображается имя **SAML SSO Settings** (Параметры единого входа SAML). Выберите его, а затем нажмите кнопку **Сохранить**.
    
    ![Фирменная символика страницы входа](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")
16. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configure Single Sign-On")

Чтобы получить URL-адрес единого входа, инициированный поставщиком услуг, в разделе меню **Security Controls** (Средства управления безопасностью) щелкните **Single Sign On settings** (Параметры единого входа).

![Средства управления безопасностью](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")

Выберите профиль единого входа, созданный на предыдущем этапе.  
На этой странице отображается URL-адрес единого входа для вашей компании (например, *https://название_компании.my.salesforce.com?so=companyid*).

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы разрешить пользователям Azure AD вход в Jobscience, они должны быть подготовлены для Jobscience.  
В случае с Jobscience подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт **Jobscience** компании в качестве администратора.
2. Выберите пункт «Настройка».
   
   ![Настройка](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")
3. Выберите **Manage Users \> Users** (Управление пользователями > Пользователи).
   
   ![Пользователи](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Users")
4. Щелкните **Новый пользователь**.
   
   ![Все пользователи](./media/active-directory-saas-jobscience-tutorial/IC784370.png "All Users")
5. В диалоговом окне **Изменить пользователя** выполните следующие действия.
   
   ![Изменить пользователя](./media/active-directory-saas-jobscience-tutorial/IC784371.png "User Edit")
   
   1. Введите свойства имени, фамилии, псевдонима, электронной почты, имени пользователя для того пользователя Azure AD, которого необходимо подготовить, в соответствующих текстовых полях.
   2. Щелкните **Сохранить**.
   
   > [!NOTE]
   > Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Jobscience или API, предоставляемые Jobscience для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Чтобы назначить пользователей Jobscience, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Jobscience** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


