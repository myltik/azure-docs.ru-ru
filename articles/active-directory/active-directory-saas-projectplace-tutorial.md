---
title: "Руководство по интеграции Azure Active Directory с Projectplace | Документация Майкрософт"
description: "Узнайте, как использовать Projectplace с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8eff0394481969839bde974dedaf2ef92c76db73


---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Учебник. Интеграция Azure Active Directory с Projectplace
Цель данного учебника — показать интеграцию Azure и Projectplace.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* подписка Projectplace с поддержкой единого входа.

После завершения этого руководства пользователи Azure AD, назначенные Projectplace, будут иметь возможность единого входа в приложение на веб-сайте компании Projectplace (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Projectplace
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-projectplace-tutorial/IC790217.png "Scenario")

## <a name="enabling-the-application-integration-for-projectplace"></a>Включение интеграции приложений для Projectplace
В этом разделе показано, как включить интеграцию приложений для Projectplace.

### <a name="to-enable-the-application-integration-for-projectplace-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Projectplace, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-projectplace-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-projectplace-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-projectplace-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Projectplace**.
   
   ![Коллекция приложений](./media/active-directory-saas-projectplace-tutorial/IC790218.png "Application Gallery")
7. В области результатов выберите **Projectplace** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Projectplace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в Projectplace со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **Projectplace** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/IC790220.png "Configure Single SignOn")
2. На странице **Как пользователи должны входить в Projectplace?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/IC790221.png "Configure Single SignOn")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Projectplace** введите URL-адрес клиента Projectplace (например, *http://company.projectplace.com*) и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-projectplace-tutorial/IC790222.png "Configure App URL")
4. На странице **Настройка единого входа в Projectplace** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/IC790223.png "Configure Single SignOn")
5. Отправьте файл метаданных в группу поддержки Projectplace.
   
   > [!NOTE]
   > Настройку единого входа должна выполнять группа поддержки Projectplace. Сразу же после завершения настройки вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-projectplace-tutorial/IC790227.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Projectplace, они должны быть подготовлены для Projectplace.  
В случае с Projectplace подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия:
1. Выполните вход на сайт **Projectplace** компании в качестве администратора.
2. Перейдите к разделу **People** (Люди) и щелкните **Members** (Участники).
   
   ![Люди](./media/active-directory-saas-projectplace-tutorial/IC790228.png "People")
3. Щелкните **Добавить участника**.
   
   ![Добавление участников](./media/active-directory-saas-projectplace-tutorial/IC790232.png "Add Members")
4. В разделе **Добавление участника** выполните следующие действия.
   
   ![Новые участники](./media/active-directory-saas-projectplace-tutorial/IC790233.png "New Members")
   
   1. В разделе **Новые участники** введите электронный адрес действующей учетной записи AAD, которую вы хотите подготовить, в соответствующее текстовое поле.
   2. В нижней части страницы нажмите кнопку **Отправить**
      
      > [!NOTE]
      > Владельцу учетной записи Azure Active Directory будет отправлено электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
      > 
      > 

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя Projectplace или API, предоставляемые Projectplace для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-projectplace-perform-the-following-steps"></a>Чтобы назначить пользователей Projectplace, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Projectplace** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-projectplace-tutorial/IC790234.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-projectplace-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


