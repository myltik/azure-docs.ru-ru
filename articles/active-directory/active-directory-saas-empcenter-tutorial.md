---
title: "Учебник. Интеграция Azure Active Directory с EmpCenter | Документация Майкрософт"
description: "Узнайте, как использовать EmpCenter вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a00ecf6e-917a-4284-b998-41506931585e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: 6f217ee0398933cfad713398952a79d39b6020c3
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-empcenter"></a>Руководство. Интеграция Azure Active Directory с EmpCenter
Цель данного руководства — показать интеграцию Azure и EmpCenter.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа EmpCenter

После завершения этого руководства пользователи Azure AD, назначенные EmpCenter, будут иметь возможность единого входа в приложение на веб-сайте компании EmpCenter (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для EmpCenter
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-empcenter-tutorial/IC802916.png "Сценарий")

## <a name="enabling-the-application-integration-for-empcenter"></a>Включение интеграции приложений для EmpCenter
В этом разделе показано, как включить интеграцию приложений для EmpCenter.

### <a name="to-enable-the-application-integration-for-empcenter-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для EmpCenter, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-empcenter-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-empcenter-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-empcenter-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-empcenter-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **EmpCenter**.
   
   ![Коллекция приложений](./media/active-directory-saas-empcenter-tutorial/IC802917.png "Коллекция приложений")
7. В области результатов выберите **EmpCenter** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![EmpCentral](./media/active-directory-saas-empcenter-tutorial/IC802918.png "EmpCentral")
   

## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в EmpCenter с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **EmpCenter** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-empcenter-tutorial/IC802919.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в EmpCenter?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-empcenter-tutorial/IC802920.png "Настройка единого входа")
3. На странице **Настройка параметров приложения** выполните следующие действия.
   
   ![Настройка параметров приложения](./media/active-directory-saas-empcenter-tutorial/IC802921.png "Настройка параметров приложения")
   
   1. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение EmpCenter (например, *https://partner-authenticati.empcenter.com/workforce/SSO.do*).
   2. Щелкните **Далее**
4. На странице **Настройка единого входа в EmpCenter** нажмите кнопку **Скачать метаданные**, чтобы скачать их, а затем сохраните файл метаданных на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-empcenter-tutorial/IC802922.png "Настройка единого входа")
5. Отправьте скачанный файл метаданных в службу поддержки EmpCenter.
   
   > [!NOTE]
   > Настройка единого входа должна выполняться службой поддержки EmpCenter.
   > Как только единый вход для вашей подписки будет включен, вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-empcenter-tutorial/IC802923.png "Настройка единого входа")
   
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в EmpCenter, они должны быть подготовлены для EmpCenter.  
В случае EmpCenter учетные записи пользователей должны быть созданы службой поддержки EmpCenter.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя EmpCenter или API, предоставляемые EmpCenter для подготовки учетных записей пользователя Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-empcenter-perform-the-following-steps"></a>Чтобы назначить пользователей EmpCenter, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **EmpCenter** нажмите кнопку **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-empcenter-tutorial/IC802924.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-empcenter-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


