---
title: "Учебник. Интеграция Azure Active Directory с Envoy | Документация Майкрософт"
description: "Узнайте, как использовать Envoy вместе с Azure Active Directory для реализации единого входа, автоматической подготовки и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cb3062d8b3ce6640ae07da8887f0e0f14a89d05


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Руководство. Интеграция Azure Active Directory с Envoy
Цель данного руководства — показать интеграцию Azure и Envoy.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Клиент Envoy

После завершения этого руководства пользователи Azure AD, назначенные Envoy, будут иметь возможность единого входа в приложение на корпоративном веб-сайте Envoy (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Envoy
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")

## <a name="enabling-the-application-integration-for-envoy"></a>Включение интеграции приложений для Envoy
В этом разделе показано, как включить интеграцию приложений для Envoy.

### <a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Envoy, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Envoy**.
   
   ![Коллекция приложений](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application gallery")
7. В области результатов выберите **Envoy** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Envoy со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Envoy, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Envoy** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Доступ с единым входом](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")
2. На странице **Как пользователи должны входить в Envoy?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Envoy** введите свой URL-адрес в формате *https://\<имя_клиента\>.Envoy.com*, а затем нажмите кнопку **Далее**.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")
4. На странице **Настройка единого входа в Envoy** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата локально в формате **c:\\Envoy.cer**.
   
   ![Настройка единого входа](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Envoy в качестве администратора.
6. На панели инструментов в верхней части экрана нажмите **Параметры**.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. Нажмите **Компания**.
   
   ![Компания](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")
8. Нажмите кнопку **SAML**.
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. В разделе конфигурации **Проверка подлинности SAML** выполните следующие действия.
   
   ![Проверка подлинности SAML](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")
   
   > [!NOTE]
   > Значение для идентификатора расположения штаб-квартиры автоматически создается приложением.
   > 
   > 
   
   1. Скопируйте значение поля **Thumbprint** (Отпечаток) из экспортированного сертификата и вставьте его в текстовое поле **Fingerprint** (Отпечаток).  
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   2. На странице диалогового окна **Настройка единого входа в Envoy** классического портала Azure скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **Identity Provider HTTP SAML URL** (URL-адрес входа HTTP SAML поставщика удостоверений).
   3. Нажмите кнопку **Сохранить изменения**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Элемент действия для настройки подготовки пользователей в Envoy отсутствует.  
Когда назначенный пользователь пытается войти в Envoy с помощью панели доступа, Envoy проверяет, существует ли данный пользователь.  
Если учетная запись пользователя отсутствует, Envoy автоматически создает ее.

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Чтобы назначить пользователей Envoy, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Envoy** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


