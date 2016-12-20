---
title: "Учебник. Интеграция Azure Active Directory с Learningpool | Документация Майкрософт"
description: "Узнайте, как использовать Learningpool вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 52fec971875bb797b5de679918528c5c472e4182


---
# <a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Руководство. Интеграция Azure Active Directory с Learningpool
Цель данного учебника — показать интеграцию Azure и Learningpool.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Learningpool

После завершения этого руководства пользователи Azure AD, назначенные Learningpool, будут иметь возможность единого входа в приложение на веб-сайте компании Learningpool (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Learningpool
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-learningpool"></a>Включение интеграции приложений для Learningpool
В этом разделе показано, как включить интеграцию приложений для Learningpool.

### <a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Learningpool, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Learningpool**.
   
   ![Коллекция приложений](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")
7. В области результатов выберите **Learningpool** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Learningpool со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

Приложение Learningpool ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML** .  
На следующем снимке экрана приведен пример.

![атрибутов токена SAML](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Learningpool** классического портала Azure в меню вверху выберите **Атрибуты**, чтобы открыть диалоговое окно **Атрибуты токена SAML**.
   
   ![Атрибуты](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")
2. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.
   
   ### 
   | Имя атрибута | Значение атрибута |
   | --- | --- |
   |  | |
   
   | urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname |
   | --- | --- |
   |  urn:oid:2.5.4.42 |User.givenname |
   | urn:oid:0.9.2342.19200300.100.1.3 |User.mail |
   | urn:oid:2.5.4.4 |User.surname |
   
   1. Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
   2. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
   3. В списке **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
   4. Нажмите **Завершено**.
3. Нажмите кнопку **Применить изменения**.
4. В браузере нажмите кнопку **Назад**, чтобы снова открыть диалоговое окно **Быстрый запуск**.
5. Щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")
6. На странице **Как пользователи должны входить в Learningpool?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")
7. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Learningpool** введите URL-адрес, используемый для входа в приложение Learningpool (например, https://parliament.preview.learningpool.com/auth/shibboleth/index.php), и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")
8. На странице **Настройка единого входа в Learningpool** нажмите кнопку **Скачать метаданные**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")
9. Передайте этот файл метаданных в группу поддержки Learningpool.
   
   > [!NOTE]
   > Группа поддержки Learningpool должна включить единый вход.
   > 
   > 
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в Learningpool, они должны быть подготовлены для Learningpool.

Элемент действия для настройки подготовки пользователей в Learningpool отсутствует.  
Группа поддержки Learningpool должна создать пользователей.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Learningpool или API, предоставляемые Learningpool для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Чтобы назначить пользователей Learningpool, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Learningpool** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


