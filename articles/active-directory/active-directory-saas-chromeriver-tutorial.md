---
title: "Учебник. Интеграция Azure Active Directory с Chromeriver | Документация Майкрософт"
description: "Узнайте, как использовать Chromeriver вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 445c5600-e340-4724-a9cb-3cfaf5770b70
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 88b450c61d382c3c3509f85a8015edc58e8d5139


---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Руководство. Интеграция Azure Active Directory с Chromeriver
Цель данного руководства — показать интеграцию Azure и Chromeriver.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Chromeriver

После завершения этого руководства пользователи Azure AD, назначенные Chromeriver, будут иметь возможность единого входа в приложение на веб-сайте компании Chromeriver (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Chromeriver
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scenario")

## <a name="enabling-the-application-integration-for-chromeriver"></a>Включение интеграции приложений для Chromeriver
В этом разделе показано, как включить интеграцию приложений для Chromeriver.

### <a name="to-enable-the-application-integration-for-chromeriver-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Chromeriver, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Chromeriver**.
   
   ![Коллекция приложений](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Application Gallery")
7. В области результатов выберите **Chromeriver** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Chromeriver со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Chromeriver** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Chromeriver?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configure Single Sign-On")
3. На странице **Настройка параметров приложения** выполните следующие действия.
   
   ![Настройка параметров приложения](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configure App Settings")
   
   1. В текстовом поле **URL-адрес ответа** введите **URL-адрес службы AssertionConsumerService** Chromeriver (например, *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).  
      
      > [!NOTE]
      > Это значение можно получить в службе поддержки Chromeriver.
      > 
      > 
   2. Щелкните **Далее**
4. На странице **Настройка единого входа в Chromeriver** нажмите кнопку **Скачать метаданные**, чтобы скачать их, а затем сохраните файл метаданных на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configure Single Sign-On")
5. Отправьте загруженный файл метаданных в службу поддержки Chromeriver.
   
   > [!NOTE]
   > Настройка единого входа должна выполняться службой поддержки Chromeriver.  
   > Как только единый вход для вашей подписки будет включен, вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнить вход в Chromeriver, они должны быть подготовлены для Chromeriver.  
В случае Chromeriver учетные записи пользователей должны быть созданы службой поддержки Chromeriver.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Chromeriver или API, предоставляемые Chromeriver для подготовки учетных записей пользователя Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-chromeriver-perform-the-following-steps"></a>Чтобы назначить пользователей Chromeriver, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Chromeriver** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


