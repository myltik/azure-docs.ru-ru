---
title: "Учебник. Интеграция Azure Active Directory с Cornerstone OnDemand | Документация Майкрософт"
description: "Узнайте, как использовать Cornerstone OnDemand вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7ab0ffdb9619a488915a7627ac7f50cfaad7019b


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Руководство. Интеграция Azure Active Directory с Cornerstone OnDemand
Цель данного учебника — показать интеграцию Azure и Cornerstone OnDemand.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Cornerstone OnDemand

После завершения этого руководства пользователи Azure AD, назначенные Cornerstone OnDemand, будут иметь возможность единого входа в приложение на веб-сайте компании Cornerstone OnDemand (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Cornerstone OnDemand
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario")

## <a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Включение интеграции приложений для Cornerstone OnDemand
В этом разделе показано, как включить интеграцию приложений для Cornerstone OnDemand.

### <a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Cornerstone OnDemand, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **cornerstone ondemand**.
   
   ![Коллекция приложений](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Application Gallery")
7. В области результатов выберите **Cornerstone OnDemand** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![cornerstone ondemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Cornerstone OnDemand со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Cornerstone OnDemand** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Доступ с единым входом](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Enable Single Sign-On")
2. На странице **Как пользователи должны входить в Cornerstone OnDemand?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Единый вход Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Cornerstone OnDemand** введите свой URL-адрес в формате *http://компания.csod.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configure App URL")
4. На странице **Настройка единого входа в Cornerstone OnDemand** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configure Single Sign-On")
5. Отправьте следующие элементы в службу поддержки Cornerstone OnDemand.
   
   1. Скачанный сертификат
   2. Значение **URL-адрес удаленного входа**
   3. Значение **URL-адрес удаленного выхода**
   
   > [!NOTE]
   > Единый вход должна настроить служба поддержки Cornerstone OnDemand.
   > Сразу же после завершения настройки вы получите уведомление от службы поддержки.
   > 
   > 
6. Выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Cornerstone OnDemand, они должны быть подготовлены для работы с Cornerstone OnDemand.  
В случае с Cornerstone OnDemand подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Отправьте сведения (например, имя, адрес электронной почты) о том пользователе Azure AD, которого необходимо подготовить, в службу поддержки Cornerstone OnDemand.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Cornerstone OnDemand или API, предоставляемые Cornerstone OnDemand, для подготовки учетных записей пользователей AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Чтобы назначить пользователей Cornerstone OnDemand, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Cornerstone OnDemand** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Назначить пользователей](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assign Users")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


