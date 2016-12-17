---
title: "Руководство. Интеграция Azure Active Directory с Aha! | Документация Майкрософт"
description: "Узнайте, как использовать Aha! вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f95aecd18d5231413678669d01d16387737e9db2


---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Руководство. Интеграция Azure Active Directory с Aha!
Цель данного руководства — показать интеграцию Azure и Aha!  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Aha!

После изучения этого руководства пользователи Azure AD, назначенные Aha!, смогут выполнять единый вход в приложение на веб-сайте компании Aha! (вход, инициированный поставщиком услуг). Также это можно сделать, используя инструкции из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Aha!
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-aha-tutorial/IC798944.png "Scenario")

## <a name="enabling-the-application-integration-for-aha"></a>Включение интеграции приложений для Aha!
В этом разделе показано, как включить интеграцию приложений для Aha!.

### <a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Aha!, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-aha-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-aha-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Aha!**.
   
   ![Коллекция приложений](./media/active-directory-saas-aha-tutorial/IC798945.png "Application Gallery")
7. В области результатов выберите **Aha!** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Aha! со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Aha!** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-aha-tutorial/IC798946.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Aha!** выберите **Единый вход Microsoft Azure AD**, а затем щелкните **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-aha-tutorial/IC798947.png "Configure Single Sign-On")
3. На странице **Настройка URL-адреса приложения** введите в текстовом поле **URL-адрес входа в Aha!** URL-адрес, используемый для входа в ваше приложение Aha! (например, *https://company.aha.io/session/new*) и нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-aha-tutorial/IC798948.png "Configure App URL")
4. На странице **Настройка единого входа в Aha!** щелкните **Скачать метаданные** для скачивания метаданных, а затем сохраните файл данных локально на компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-aha-tutorial/IC798949.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Aha! в качестве администратора.
6. В верхнем меню щелкните пункт **Параметры**.
   
   ![Параметры](./media/active-directory-saas-aha-tutorial/IC798950.png "Settings")
7. Выберите раздел **Учетная запись**.
   
   ![Профиль](./media/active-directory-saas-aha-tutorial/IC798951.png "Profile")
8. Нажмите **Безопасность и единый вход**.
   
   ![Безопасность и единый вход](./media/active-directory-saas-aha-tutorial/IC798952.png "Security and single sign-on")
9. В разделе **Единый вход** в качестве **поставщика удостоверений** выберите **SAML 2.0**.
   
   ![Безопасность и единый вход](./media/active-directory-saas-aha-tutorial/IC798953.png "Security and single sign-on")
10. На странице настроек **Единый вход** выполните следующие действия.
    
    ![Единый вход](./media/active-directory-saas-aha-tutorial/IC798954.png "Single Sign-On")
    
    1. В текстовом поле **Имя** введите имя конфигурации.
    2. Для параметра **Configure using** (Использовать при настройке) выберите значение **Файл метаданных**.
    3. Чтобы отправить загруженный файл метаданных, нажмите кнопку **Обзор**.
    4. Нажмите кнопку **Обновить**.
11. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-aha-tutorial/IC798955.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Aha!, они должны быть подготовлены для Aha!  
В случае Aha! подготовка выполняется автоматически.  
С вашей стороны никакие действия не требуются.

В случае необходимости пользователи создаются автоматически при первой попытке входа в систему.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Aha! или API, предоставляемые Aha! для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-aha-perform-the-following-steps"></a>Чтобы назначить пользователей Aha!, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Aha!** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-aha-tutorial/IC798956.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-aha-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


