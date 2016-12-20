---
title: "Учебник. Интеграция Azure Active Directory с Coupa | Документация Майкрософт"
description: "Узнайте, как использовать Coupa вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 08331c68378caf7cf3b2bb9bcc14a824a58a249f


---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Руководство. Интеграция Azure Active Directory с Coupa
Цель данного руководства — показать интеграцию Azure и Coupa.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка с поддержкой единого входа Coupa

После завершения этого руководства пользователи Azure AD, назначенные Coupa, будут иметь возможность единого входа в приложение с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Coupa
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")

## <a name="enabling-the-application-integration-for-coupa"></a>Включение интеграции приложений для Coupa
В этом разделе показано, как включить интеграцию приложений для Coupa.

### <a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Coupa, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-coupa-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-coupa-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Coupa**.
   
   ![Коллекция приложений](./media/active-directory-saas-coupa-tutorial/IC791898.png "Application Gallery")
7. В области результатов выберите **Coupa** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Coupa со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Coupa, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. Выполните вход на веб-сайт компании Coupa в качестве администратора.
2. Последовательно выберите **Setup \> Security Control** (Настройка > Управление безопасностью).
   
   ![Средства управления безопасностью](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
3. Чтобы скачать файл метаданных Coupa на свой компьютер, нажмите **Скачать и импортировать метаданные поставщика услуг**.
   
   ![Метаданные поставщика услуг Coupa](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")
4. В другом окне браузера войдите на классический портал Azure.
5. На странице интеграции с приложением **Coupa** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configure Single Sign-On")
6. На странице **Как пользователи должны входить в Coupa?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configure Single Sign-On")
7. На странице **Настройка URL-адреса приложения** выполните следующие действия.
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configure App URL")
   
   1. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение Coupa (например, *http://компания.Coupa.com*).
   2. Откройте скачанный файл метаданных Coupa и скопируйте **AssertionConsumerService index/URL**.
   3. Вставьте значение **AssertionConsumerService index/URL** в текстовое поле **URL-адрес ответа Coupa**.
   4. Нажмите кнопку **Далее**.
8. На странице **Настройка единого входа в Coupa** нажмите кнопку **Скачать метаданные**, чтобы скачать файл метаданных, а затем сохраните его на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configure Single Sign-On")
9. На корпоративном сайте Coupa выберите **Setup \> Security Control** (Настройка > Управление безопасностью).
   
   ![Средства управления безопасностью](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
10. В разделе **Вход с использованием учетных данных Coupa** выполните следующие действия.
    
    ![Вход с использованием учетных данных Coupa](./media/active-directory-saas-coupa-tutorial/IC791906.png "Log in using Coupa credentials")
    
    1. Выберите **Вход с помощью SAML**.
    2. Чтобы отправить загруженный файл метаданных Azure Active, нажмите кнопку **Обзор** .
    3. Щелкните **Сохранить**.
11. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы пользователи Azure AD могли выполнять вход в Coupa, они должны быть подготовлены для Coupa.  
В случае с Coupa подготовка выполняется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт компании **Coupa** в качестве администратора.
2. В меню вверху щелкните **Setup** (Настройка) и выберите **Users** (Пользователи).
   
   ![Пользователи](./media/active-directory-saas-coupa-tutorial/IC791908.png "Users")
3. Щелкните **Создать**.
   
   ![Создание пользователей](./media/active-directory-saas-coupa-tutorial/IC791909.png "Create Users")
4. В разделе **Создание пользователя** выполните следующие действия.
   
   ![Сведения о пользователе](./media/active-directory-saas-coupa-tutorial/IC791910.png "User Details")
   
   1. В соответствующие текстовые поля введите атрибуты **Login** (Имя для входа), **First name** (Имя), **Last Name** (Фамилия), **Single Sign-On ID** (Идентификатор единого входа) и **Email** (Адрес электронной почты) действующей учетной записи Azure Active Directory, которую вы хотите подготовить.
   2. Щелкните **Создать**.
   
   > [!NOTE]
   > Владелец учетной записи Azure Active Directory получит электронное сообщение со ссылкой для подтверждения учетной записи перед ее активацией.
   > 
   > 

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Coupa или API, предоставляемые Coupa для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Чтобы назначить пользователей Coupa, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Coupa** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-coupa-tutorial/IC791911.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-coupa-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


