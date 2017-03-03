---
title: "Руководство по интеграции Azure Active Directory с FM:Systems | Документация Майкрософт"
description: "Сведения об использовании FM:Systems вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: e92f8cb6e980a0552b8ff836ed521e069ba811bb
ms.lasthandoff: 02/14/2017


---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Руководство по интеграции Azure Active Directory с FM:Systems
Цель данного учебника — показать интеграцию Azure и FM:Systems.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа FM:Systems.

После завершения этого руководства пользователи Azure AD, назначенные FM:Systems, будут иметь возможность единого входа в приложение на веб-сайте компании FM:Systems (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для FM:Systems
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Сценарий")

## <a name="enabling-the-application-integration-for-fmsystems"></a>Включение интеграции приложений для FM:Systems
В этом разделе показано, как включить интеграцию приложений для FM:Systems.

### <a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для FM:Systems, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Приложения")

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Добавление приложения](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Добавление приложения")

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Добавление приложения из коллекции](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Добавление приложения из коллекции")

6. В **поле поиска** введите **FM:Systems**.
   
    ![Коллекция приложений](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Коллекция приложений")

7. В области результатов выберите **FM:Systems** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![FM:Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM:Systems")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в FM:Systems со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **FM:Systems** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Настройка единого входа")

2. На странице **Как пользователи должны входить в FM:Systems?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Настройка единого входа")

3. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
    ![Настройка URL-адреса приложения](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Настройка URL-адреса приложения")
   
    а. В текстовом поле **URL-адрес входа в FM:Systems** введите **URL-адрес ответа** FM:Systems (например, *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  
      
    > [!NOTE]
    > Это значение можно получить в службе поддержки FM:Systems.
    > 
    > 
   
    b. В нижней части страницы нажмите кнопку **Далее**

4. На странице **Настройка единого входа в FM:Systems** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на компьютере.
   
    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Настройка единого входа")

5. Отправьте скачанный файл метаданных в службу поддержки FM:Systems.
   
    > [!NOTE]
    > Настройка единого входа должна выполняться службой поддержки FM:Systems.
    > Как только единый вход для вашей подписки будет включен, вы получите уведомление.
    > 
    > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Настройка единого входа")
   
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в FM: Systems, они должны быть подготовлены для FM: Systems.  
В случае с FM: Systems подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. В другом окне веб-браузера войдите на ваш корпоративный веб-сайт FM: Systems в качестве администратора.

2. Последовательно выберите **System Administration \> Manage Security \> Users \> User list** (Системное администрирование > Управление безопасностью > Пользователи > Список пользователей).
   
    ![Администрирование системы](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Администрирование системы")

3. Нажмите **Создать нового пользователя**.
   
    ![Создание пользователя](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Создание пользователя")

4. В разделе **Создание пользователя** выполните следующие действия.
   
    ![Создание пользователя](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Создание пользователя")
   
    а. Введите имя пользователя, пароль, подтверждение пароля, адрес электронной почты и идентификатор сотрудника действующей учетной записи Azure Active Directory, которую вы хотите подготовить, в соответствующих текстовых полях.
   
    b. Нажмите кнопку **Далее**.
 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Чтобы назначить пользователей FM:Systems, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **FM:Systems** нажмите кнопку **Назначить пользователей**.
   
    ![Назначение пользователей](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Назначение пользователей")

3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
    ![Да](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


