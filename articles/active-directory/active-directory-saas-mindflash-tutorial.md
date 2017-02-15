---
title: "Учебник. Интеграция Azure Active Directory с Mindflash | Документация Майкрософт"
description: "Узнайте, как использовать Mindflash с Azure Active Directory для включения единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 08753aa43d192e2e948b478638fa2868978cb575


---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>Руководство. Интеграция Azure Active Directory с Mindflash
Цель данного руководства — продемонстрировать интеграцию Azure и Mindflash.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка на Mindflash с поддержкой единого входа

После завершения этого руководства пользователи Azure AD, назначенные Mindflash, будут иметь возможность единого входа в приложение на веб-сайте компании Mindflash (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Mindflash
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scenario")

## <a name="enabling-the-application-integration-for-mindflash"></a>Включение интеграции приложений для Mindflash
В этом разделе показано, как включить интеграцию приложений для Mindflash.

### <a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Mindflash, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Mindflash**.
   
   ![Коллекция приложений](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Application Gallery")
7. В области результатов выберите **Mindflash** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Mindflash со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Mindflash** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Mindflash?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес для входа** введите свой URL-адрес в формате *http://компания.mindflash.com* и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configure App URL")
4. На странице **Настройка единого входа в Mindflash** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на своем компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configure Single Sign-On")
5. Отправьте файл метаданных в службу поддержки Mindflash.
   
   > [!NOTE]
   > Настройка единого входа должна выполняться службой поддержки Mindflash. Сразу же после завершения настройки вы получите уведомление.
   > 
   > 
6. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в Mindflash, они должны быть подготовлены для Mindflash.  
В случае с Mindflash подготовка выполняется вручную.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Чтобы подготовить учетные записи пользователей, выполните следующие действия.
1. Выполните вход в **Mindflash** на веб-сайте компании в качестве администратора.
2. Перейдите в раздел **Управление пользователями**.
   
   ![Управление пользователями](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Manage Users")
3. Нажмите кнопку **Add Users** (Добавить пользователей), а затем выберите **New** (Создать).
4. В разделе **Добавление новых пользователей** выполните следующие действия.
   
   ![Добавление новых пользователей](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Add New Users")
   
   1. В соответствующие текстовые поля введите атрибуты **First name** (Имя), **Last name** (Фамилия) и **Email** (Адрес электронной почты) действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Добавить**.

> [!NOTE]
> Для подготовки учетных записей пользователей AAD можно использовать любые другие средства создания учетных записей Mindflash или API, предоставляемое Mindflash.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>Чтобы назначить пользователей Mindflash, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Mindflash** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


