---
title: "Руководство по интеграции Azure Active Directory с Tinfoil Security | Документация Майкрософт"
description: "Узнайте, как использовать Tinfoil Security вместе с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 383ecb6978fc975bbee51474c467737720ff927e


---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Учебник. Интеграция Azure Active Directory с Tinfoil Security
Цель данного учебника — показать интеграцию Azure и Tinfoil Security.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка Tinfoil Security с поддержкой единого входа.

После завершения этого руководства пользователи Azure AD, назначенные Tinfoil Security, будут иметь возможность единого входа в приложение на веб-сайте компании Tinfoil Security (вход, инициированный поставщиком удостоверений) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для Tinfoil Security
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Настройка единого входа](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-tinfoil-security"></a>Включение интеграции приложений для Tinfoil Security
В этом разделе показано, как включить интеграцию приложений для Tinfoil Security.

### <a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для Tinfoil Security, выполните следующие действия:
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Tinfoil Security**.
   
   ![Коллекция приложений](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "Application Gallery")
7. В области результатов выберите **Tinfoil Security** и щелкните **Завершить**, чтобы добавить приложение.
   
   ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

## <a name="configuring-single-sign-on"></a>Настройка единого входа
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Tinfoil Security с помощью своей учетной записи Azure AD, используя федерацию на основе протокола SAML.  
Чтобы настроить единый вход для Tinfoil Security, необходимо извлечь значение отпечатка из сертификата.  
Если вы не знакомы с этой процедурой, просмотрите видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Tinfoil Security** классического портала Azure нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "Configure Single Sign-On")
2. На странице **Как пользователи будут входить в Tinfoil Security** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес ответа Tinfoil Security** введите свой URL-адрес службы Assertion Consumer Service (ACS) для Tinfoil Security (например, *https://www.tinfoilsecurity.com/saml/consume*), а затем нажмите кнопку **Далее**.
   
   > [!NOTE]
   > URL-адрес ACS можно получить из метаданных Tinfoil Security (https://www.tinfoilsecurity.com/saml/metadata).
   > 
   > 
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "Configure App URL")
4. На странице **Настройка единого входа в Tinfoil Security** щелкните **Скачать сертификат**, а затем сохраните файл сертификата локально как **c:\\Tinfoil Security.cer**.
   
   ![Настройка единого входа](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Tinfoil Security в качестве администратора.
6. На панели инструментов в верхней части экрана щелкните **Моя учетная запись**.
   
   ![Панель мониторинга](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")
7. Выберите пункт **Безопасность**.
   
   ![Безопасность](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "Security")
8. На странице настроек **Единый вход** выполните следующие действия.
   
   ![Единый вход](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "Single Sign-On")
   
   1. Выберите **Включить SAML**.
   2. Щелкните **Настроить вручную**.
   3. На странице **Настройка единого входа в Tinfoil Security** классического портала Azure скопируйте значение поля **URL-адрес единого входа SAML** и вставьте его в текстовое поле **SAML Post URL** (URL-адрес записи SAML).
   4. Скопируйте значение поля **Отпечаток** из экспортированного сертификата и вставьте его в текстовое поле **SAML Certificate Fingerprint** (Отпечаток сертификата SAML).  
      
      > [!TIP]
      > Дополнительные сведения можно найти в видео [Как извлечь значение отпечатка из сертификата](http://youtu.be/YKQF266SAxI)
      > 
      > 
   5. Скопируйте **идентификатор вашей учетной записи**.
   6. Щелкните **Сохранить**.
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "Configure Single Sign-On")
10. В меню в верхней части экрана выберите пункт **Атрибуты** to open the **SAML Token Атрибуты** .
    
    ![Атрибуты](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "Attributes")
11. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.
    
    ![Атрибуты](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "Attributes")
    
    1. Щелкните **добавить атрибут пользователя**.
    2. В текстовом поле **Имя атрибута** введите **accountid**.
    3. В текстовом поле **Значение атрибута** вставьте идентификатор учетной записи, который вы скопировали в предыдущем разделе.
    4. Нажмите **Завершено**.
12. Нажмите кнопку **Применить изменения**.

## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей
Чтобы пользователи Azure AD могли выполнять вход в систему Tinfoil Security, они должны быть подготовлены для нее.  
В случае с Tinfoil Security подготовка выполняется вручную.

### <a name="to-get-a-user-provisioned-perform-the-following-steps"></a>Для подготовки пользователей выполните следующие действия:
1. Если пользователь является частью корпоративной учетной записи, для получения созданной учетной записи пользователя вам необходимо обратиться в службу поддержки безопасности Tinfoil Security.
2. Если пользователь является обычным пользователем Tinfoil Security SaaS, то он может добавить взаимодействующие объекты к любому из своих сайтов. Это вызывает процесс, который отправляет приглашение по указанному адресу электронной почты, чтобы создать учетную запись пользователя Tinfoil Security.

> [!NOTE]
> Вы можете использовать любые другие средства создания учетной записи пользователя Tinfoil Security или API-интерфейсы, предоставляемые Tinfoil Security для подготовки учетных записей пользователя AAD.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>Чтобы назначить пользователей для Tinfoil Security, выполните следующие действия:
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Tinfoil Security** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


