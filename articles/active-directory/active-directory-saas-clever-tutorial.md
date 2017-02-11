---
title: "Учебник. Интеграция Azure Active Directory с Clever | Документация Майкрософт"
description: "Узнайте, как использовать Clever вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fc748c70afede4be52e6c49db72932588be779ce


---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Руководство. Интеграция Azure Active Directory с Clever
Цель данного руководства — показать интеграцию Azure и Clever. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Clever

После завершения этого руководства пользователи Azure AD, назначенные Clever, будут иметь возможность единого входа в приложение на веб-сайте компании Clever (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Clever
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")

## <a name="enabling-the-application-integration-for-clever"></a>Включение интеграции приложений для Clever
В этом разделе показано, как включить интеграцию приложений для Clever.

### <a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Clever, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Clever**.
   
   ![Коллекция приложений](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")
7. В области результатов выберите **Clever** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Clever со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Приложение Clever ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML** .  
На следующем снимке экрана приведен пример.

![Атрибуты](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Clever** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Clever?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Clever** введите URL-адрес, используемый для входа в приложение Clever (например, *https://clever.com/in/azsandbox*), и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")
4. На странице **Настройка единого входа в Clever** нажмите кнопку **Скачать метаданные**, чтобы скачать их, а затем сохраните файл данных на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Clever в качестве администратора.
6. На панели инструментов выберите **Мгновенный вход**.
   
   ![Мгновенный вход](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")
7. На странице **Мгновенный вход** выполните следующие действия.
   
   ![Мгновенный вход](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")
   
   1. Введите **URL-адрес входа**.  
      
      > [!NOTE]
      > **URL-адрес входа** является настраиваемым значением.
      > Фактическое значение можно получить в службе поддержки Clever.
      > 
      > 
   2. Для параметра **Identity System** (Система идентификации) выберите значение **ADFS**.
   3. Щелкните **Сохранить**.
8. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")
9. В меню в верхней части экрана выберите пункт **Атрибуты** to open the **SAML Token Атрибуты** .
   
   ![Атрибуты](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")
10. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.
    
    ![Атрибуты токена SAML](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | clever.student.credentials.district\_username |User.userprincipalname |
    
    1. Для каждой строки данных в приведенной выше таблице нажмите кнопку **добавить атрибут пользователя**.
    2. В текстовом поле **Имя атрибута** введите имя атрибута, отображаемое для этой строки.
    3. В текстовом поле **Значение атрибута** выберите значение атрибута, отображаемое для этой строки.
    4. Нажмите **Завершено**.
11. Нажмите кнопку **Применить изменения**.

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнить вход в Clever, они должны быть подготовлены для Clever.  
В случае Clever подготовка должна быть выполнена вручную службой поддержки Clever.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетной записи пользователя Clever или API, предоставляемые Clever для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-clever-perform-the-following-steps"></a>Чтобы назначить пользователей Clever, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Clever** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


