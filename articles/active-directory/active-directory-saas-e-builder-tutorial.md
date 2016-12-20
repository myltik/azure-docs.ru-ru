---
title: "Учебник. Интеграция Azure Active Directory с e-Builder | Документация Майкрософт"
description: "Узнайте, как использовать e-Builder вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d5068007-5a54-40ac-9cb8-2ceca89fd8ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c08989aa8dc966fc731be7a53c1eb0f1d57b5053


---
# <a name="tutorial-azure-active-directory-integration-with-e-builder"></a>Руководство. Интеграция Azure Active Directory с e-Builder
Цель данного учебника — показать интеграцию Azure и e-Builder.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент e-Builder

После завершения этого руководства пользователи Azure AD, назначенные е-Builder, смогут выполнять единый вход в приложение на корпоративном веб-сайте е-Builder (вход, инициированный поставщиком услуг) или с использованием инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для e-Builder
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-e-builder-tutorial/IC777378.png "Scenario")

## <a name="enabling-the-application-integration-for-e-builder"></a>Включение интеграции приложений для e-Builder
В этом разделе показано, как включить интеграцию приложений для e-Builder.

### <a name="to-enable-the-application-integration-for-e-builder-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для e-Builder, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-e-builder-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-e-builder-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-e-builder-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-e-builder-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **e-Builder**.
   
   ![Коллекция приложений](./media/active-directory-saas-e-builder-tutorial/IC777379.png "Application gallery")
7. В области результатов выберите **e-Builder** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![e-Builder](./media/active-directory-saas-e-builder-tutorial/IC777380.png "e-Builder")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в e-Builder со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **e-Builder** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-e-builder-tutorial/IC777381.png "Configure single sign-on")
2. На странице **Как пользователи должны входить в e-Builder?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-e-builder-tutorial/IC777382.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в e-Builder** введите свой URL-адрес в формате *https://\<имя_клиента\>.e-Builder.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-e-builder-tutorial/IC777383.png "Configure app URL")
4. На странице **Настройка единого входа в e-Builder** нажмите кнопку **Скачать метаданные**, а затем сохраните файл данных локально в формате **c:\\e-BuilderMetaData.xml**.
   
   ![Настройка единого входа](./media/active-directory-saas-e-builder-tutorial/IC777384.png "Configure single sign-on")
5. Передайте этот файл метаданных в службу поддержки e-Builder. Группа поддержки осуществляет настройку единого входа.
6. Выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-e-builder-tutorial/IC777385.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Элемент действия для настройки подготовки пользователей в e-Builder отсутствует.  
Когда назначенный пользователь пытается войти в e-Builder с помощью панели доступа, e-Builder проверяет, существует ли данный пользователь.  
Если учетная запись пользователя отсутствует, e-Builder автоматически создает ее.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-e-builder-perform-the-following-steps"></a>Чтобы назначить пользователей e-Builder, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **e-Builder** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-e-builder-tutorial/IC777386.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-e-builder-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


