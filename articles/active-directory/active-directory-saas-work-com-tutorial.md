---
title: "Руководство. Интеграция Azure Active Directory с Work.com | Документация Майкрософт"
description: "Узнайте, как использовать Work.com вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 69d94659f4eff72e1c449fd915616d81fd4712de


---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Руководство. Интеграция Azure Active Directory с Work.com
Цель данного руководства — продемонстрировать интеграцию Azure и Work.com.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка Work.com с поддержкой единого входа.

По завершении работы с этим руководством пользователи AAD, которым будет назначен доступ к Work.com, смогут выполнять единый вход в приложение на веб-сайте Work.com вашей компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Work.com
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

## <a name="enabling-the-application-integration-for-workcom"></a>Включение интеграции приложений для Work.com
В этом разделе показано, как включить интеграцию приложений для Work.com.

### <a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Work.com, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавить приложение](./media/active-directory-saas-work-com-tutorial/IC749321.png "Add application")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавить приложение из коллекции](./media/active-directory-saas-work-com-tutorial/IC749322.png "Add an application from gallerry")

6. В **поле поиска** введите **Work.com**.
   
    ![Коллекция приложений](./media/active-directory-saas-work-com-tutorial/IC794106.png "Application Gallery")

7. В области результатов выберите **Work.com** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Work.com со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется отправить сертификат на сайт Work.com.

> [!NOTE]
> Чтобы настроить единый вход, также необходимо пользовательское имя домена Work.com. Вам требуется определить по крайней мере одно имя домена, а также проверить и развернуть его для всей организации.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. Войдите в клиент Work.com от имени администратора.
2. Перейдите в раздел **Настройка**.
   
    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

3. В области навигации слева в разделе **Administer** (Администрирование) щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, а затем щелкните **My Domain** (Мой домен), чтобы открыть страницу **My Domain** (Мой домен). 
   
    ![Мой домен](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")

4. Чтобы проверить правильность настройки домена, убедитесь, что он находится на этапе **Step 4 Deployed to Users** (Шаг 4. Развернут для пользователей), и просмотрите раздел **My Domain Settings** (Параметры моего домена).
   
    ![Домен развернут для пользователя](./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman Deployed to User")

5. В другом окне веб-браузера войдите на классический портал Azure.

6. На странице интеграции с приложением **Work.com** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")

7. На странице **Как пользователи должны входить в Work.com** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")

8. На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес для входа в Work.com** введите URL-адрес, используемый пользователями для входа в ваше приложение Work.com (например, *http://company.my.salesforce.com*), и нажмите кнопку **Далее**. 
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configure App URL")

9. На странице **Настройка единого входа в Work.com** нажмите кнопку **Скачать сертификат**, чтобы скачать сертификат, а затем сохраните файл сертификата на локальном компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")

10. Войдите в клиент Work.com.

11. Перейдите в раздел **Настройка**.
    
    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

12. Разверните меню **Управление безопасностью**, а затем щелкните **Параметры единого входа**.
    
    ![Параметры единого входа](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")

13. На странице диалогового окна **Параметры единого входа** сделайте следующее:
    
    ![SAML включен](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")
    
    а. Установите флажок **SAML включен**.
    
    b. Нажмите кнопку **Создать**.

14. В разделе **Параметры единого входа SAML** сделайте следующее:
    
    ![Параметры единого входа SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")
    
    а. В текстовом поле **Имя** введите имя конфигурации.  
       
    > [!NOTE]
    > При вводе значения в поле **Имя** текстовое поле **Имя API** заполняется автоматически.
    > 
    > 
    
    b. На странице диалогового окна **Настройка единого входа в Work.com** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Издатель**.
    
    c. Чтобы отправить скачанный сертификат, нажмите кнопку **Обзор**.
    
    d. В текстовом поле **Идентификатор сущности** введите **https://salesforce-work.com**.
    
    д. В поле **SAML Identity Type** (Тип удостоверения SAML) выберите значение **Assertion contains the Federation ID from the User object** (Проверочное утверждение содержит идентификатор федерации из объекта User).
    
    Е. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentfier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).
    
    ж. На странице диалогового окна **Настройка единого входа в Work.com** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений).
    
    h. На странице диалогового окна **Настройка единого входа в Work.com** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений).
    
    i. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициированных поставщиком услуг) выберите значение **HTTP POST**.
    
    j. Щелкните **Сохранить**.

15. В левой области навигации классического портала Work.com щелкните **Domain Management** (Управление доменами), чтобы развернуть соответствующий раздел, и щелкните **My Domain** (Мой домен), чтобы открыть страницу **My Domain** (Мой домен). 
    
    ![Мой домен](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")

16. На странице **My Domain** (Мой домен) в разделе **Login Page Branding** (Фирменная символика страницы входа) нажмите кнопку **Edit** (Изменить).
    
    ![Фирменная символика страницы входа](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")

17. На странице **Login Page Branding** (Фирменная символика страницы входа) в разделе **Authentication Service** (Служба аутентификации) отображается имя **SAML SSO Settings** (Параметры единого входа SAML). Выберите его, а затем нажмите кнопку **Сохранить**.
    
    ![Фирменная символика страницы входа](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")

18. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure Active Directory могли входить систему, их необходимо подготовить для Work.com.  
В случае с Work.com подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на сайт компании Work.com в качестве администратора.

2. Перейдите в раздел **Настройка**.
   
    ![Настройка](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. Выберите **Manage Users \> Users** (Управление пользователями > Пользователи).
   
    ![Управление пользователями](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")

4. Щелкните **Новый пользователь**.
   
    ![Все пользователи](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")

5. В разделе "Изменить пользователя" выполните следующие действия.
   
    ![Изменить пользователя](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")
   
    а. Введите значения атрибутов **Фамилия**, **Псевдоним**, **Электронный адрес**, **Имя пользователя** и **Псевдоним** данными действующей учетной записи Azure Active Directory, которую нужно подготовить.
   
    b. Выберите значения параметров **Роль**, **Лицензия пользователя** и **Профиль**.
   
    c. Щелкните **Сохранить**.  
      
    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
    > 
    > 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Чтобы назначить пользователей Work.com, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.

2. На странице интеграции с приложением Work.com нажмите кнопку **Назначить пользователей**.
   
    ![Назначить пользователей](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assign Users")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")

Подождите 10 минут и убедитесь, что учетная запись синхронизирована с Work.com.

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO1-->


