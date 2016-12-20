---
title: "Руководство по интеграции Azure Active Directory с Abintegro | Документация Майкрософт"
description: "Узнайте, как использовать Abintegro вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 99287e1f-4189-494a-97c8-e1c03d047fd3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 64a7ff2552d2ccc97b1ec90cf524aed6b62ecb82


---
# <a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Руководство. Интеграция Azure Active Directory с Abintegro
Цель данного руководства — показать интеграцию Azure и Abintegro.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Abintegro

После завершения этого руководства пользователи Azure AD, назначенные Abintegro, будут иметь возможность единого входа в приложение на веб-сайте компании Abintegro (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Abintegro
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scenario")

## <a name="enabling-the-application-integration-for-abintegro"></a>Включение интеграции приложений для Abintegro
В этом разделе показано, как включить интеграцию приложений для Abintegro.

### <a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Abintegro, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-abintegro-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-abintegro-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **abintegro**.
   
   ![Коллекция приложений](./media/active-directory-saas-abintegro-tutorial/IC790077.png "Application Gallery")
7. В области результатов выберите **Abintegro** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Abintegro со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Abintegro** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configure Single SignOn")
2. На странице **Как пользователи должны входить в Abintegro?** выберите **Единый вход Microsoft Azure AD** и щелкните **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configure Single SignOn")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Abintegro** введите URL-адрес, который будет использоваться для входа в Abintegro (например, `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`) и щелкните **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configure App URL")
4. На странице **Настройка единого входа в Abintegro** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configure Single SignOn")
5. Отправьте файл метаданных в службу поддержки Abintegro.
   
   > [!NOTE]
   > Настройка единого входа должна выполняться службой поддержки Abintegro. Сразу же после завершения настройки вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Элемент действия для настройки подготовки пользователей в Abintegro отсутствует.  
Когда назначенный пользователь пытается войти в Abintegro с помощью панели доступа, Abintegro проверяет, существует ли данный пользователь.  
Если учетная запись пользователя отсутствует, Abintegro автоматически создает ее.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Чтобы назначить пользователей Abintegro, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Abintegro** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-abintegro-tutorial/IC790084.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-abintegro-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


