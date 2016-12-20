---
title: "Руководство по интеграции Azure Active Directory с RightAnswers | Документация Майкрософт"
description: "Узнайте, как использовать RightAnswers с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f09e25a-a716-41e1-8ca3-fd00e3d1b8cc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6c08e0b1349a3eb1bb4876958e36172e87729330


---
# <a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Учебник. Интеграция Azure Active Directory с RightAnswers
Цель данного учебника — показать интеграцию Azure и RightAnswers. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* подписка RightAnswers с поддержкой единого входа.

После завершения этого руководства пользователи Azure AD, назначенные RightAnswers, будут иметь возможность единого входа в приложение с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для RightAnswers
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")

## <a name="enabling-the-application-integration-for-rightanswers"></a>Включение интеграции приложений для RightAnswers
В этом разделе показано, как включить интеграцию приложений для RightAnswers.

### <a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для RightAnswers, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **RightAnswers**.
   
   ![Коллекция приложений](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Application Gallery")
7. В области результатов выберите **RightAnswers** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить аутентификацию в RightAnswers со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На классическом портале Azure на странице интеграции с приложением **RightAnswers** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в RightAnswers?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configure Single Sign-On")
3. На странице **Настроить параметры приложения** в текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение RightAnswers (например, *https://fortify.rightanswers.com/portal/ss/*), и нажмите кнопку **Далее**.
   
   ![Настройка параметров приложения](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configure App Settings")
4. На странице **Настройка единого входа в RightAnswers** щелкните **Скачать метаданные**, чтобы скачать метаданные, а затем сохраните файл метаданных на локальный компьютер.
   
   ![Настройка единого входа](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configure Single Sign-On")
5. Отправьте скачанный файл метаданных в службу поддержки RightAnswers.
   
   > [!NOTE]
   > Настройка единого входа должна выполняться службой поддержки RightAnswers.
   > Как только единый вход для вашей подписки будет включен, вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в RightAnswers, они должны быть подготовлены для RightAnswers.  
В случае RightAnswers подготовка выполняется автоматически.  
С вашей стороны никакие действия не требуются.

В случае необходимости пользователи создаются автоматически при первой попытке входа в систему.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователя RightAnswers или API, предоставляемые RightAnswers для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Чтобы назначить пользователей RightAnswers, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **RightAnswers** щелкните **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


