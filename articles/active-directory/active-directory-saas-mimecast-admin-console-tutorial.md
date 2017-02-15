---
title: "Учебник. Интеграция Azure Active Directory с Mimecast Admin Console | Документация Майкрософт"
description: "Узнайте, как использовать консоль администрирования Mimecast с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7271ace1c6bd1d56e20106e09ba125ac59145d1d


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Руководство. Интеграция Azure Active Directory с консолью администрирования Mimecast
Цель данного учебника — продемонстрировать интеграцию Azure и консоли администрирования Mimecast.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* Действующая подписка на Azure
* Подписка на консоль администрирования Mimecast с поддержкой единого входа

По завершении работы с этим руководством пользователи Azure AD, назначенные в консоли администрирования Mimecast, смогут выполнять единый вход в приложение консоли администрирования Mimecast на веб-сайте компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для консоли администрирования Mimecast
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Включение интеграции приложений для консоли администрирования Mimecast
В этом разделе показано, как включить интеграцию приложений для консоли администрирования Mimecast.

### <a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>Чтобы включить интеграцию приложений для консоли администрирования Mimecast, выполните следующие действия.
1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавить приложение](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Add application")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавить приложение из коллекции](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Add an application from gallerry")
6. В **поле поиска** введите **Mimecast Admin Console**.
   
   ![Коллекция приложений](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Application Gallery")
7. В области результатов выберите **Mimecast Admin Console** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![консоль администрирования Mimecast](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
   
   ## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в консоли администрирования Mimecast со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  
В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Чтобы настроить единый вход, выполните следующие действия.
1. На странице интеграции с приложением **Mimecast Admin Console** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configure Single Sign-On")
2. На странице **Как пользователи должны входить в Mimecast Admin Console?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configure Single Sign-On")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Mimecast Admin Console** введите URL-адрес, используемый для входа в приложение Mimecast Admin Console (например, https://webmail-uk.mimecast.com или https://webmail-us.mimecast.com), и нажмите кнопку **Далее**.
   
   > [!NOTE]
   > URL-адрес для входа зависит от региона.
   > 
   > 
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configure App URL")
4. На странице **Настройка единого входа в Mimecast Admin Console** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configure Single Sign-On")
5. В другом окне веб-браузера войдите в консоль администрирования Mimecast в качестве администратора.
6. Выберите **Services \> Application** (Службы > Приложение).
   
   ![Службы](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")
7. Щелкните **Профили проверки подлинности**.
   
   ![Профили проверки подлинности](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Authentication Profiles")
8. Щелкните **Новый профиль проверки подлинности**.
   
   ![Новые профили проверки подлинности](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "New Authentication Profiles")
9. В разделе **Профиль проверки подлинности** сделайте следующее:
   
   ![Профиль проверки подлинности](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Authentication Profile")
   
   1. В текстовом поле **Описание** введите имя конфигурации.
   2. Выберите **Обязательное использование проверки подлинности SAML для консоли администрирования Mimecast**.
   3. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   4. На странице диалогового окна **Настройка единого входа в Mimecast Admin Console** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Issuer URL** (URL-адрес издателя).
   5. На странице диалогового окна **Настройка единого входа в Mimecast Admin Console** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login URL** (URL-адрес для входа).
   6. На странице диалогового окна **Настройка единого входа в Mimecast Admin Console** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).  
      
      > [!NOTE]
      > Значения URL-адреса входа в систему и URL-адреса выхода из системы для консоли администрирования Mimecast одинаковы.
      > 
      > 
   7. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      > [!TIP]
      > Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
      > 
      > 
   8. Откройте сертификат в кодировке Base-64 в Блокноте, удалите первую строку ("*--*") и последнюю строку ("*--*"), скопируйте остальное содержимое в буфер обмена и вставьте его в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).
   9. Установите флажок **Разрешить единый вход**.
   10. Щелкните **Сохранить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в консоль администрирования Mimecast, они должны быть подготовлены для консоли администрирования Mimecast.  
В случае с консолью администрирования Mimecast подготовка выполняется вручную.

Перед созданием пользователей необходимо зарегистрировать домен.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Войдите в **консоль администрирования Mimecast** в качестве администратора.
2. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
   ![Каталоги](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directories")
3. Щелкните **Зарегистрировать новый домен**.
   
   ![Зарегистрировать новый домен](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Register New Domain")
4. После создания нового домена щелкните **Новый адрес**.
   
   ![Новый адрес](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "New Address")
5. В окне нового адреса выполните следующие действия.
   
   ![Сохранить](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Save")
   
   1. В соответствующие текстовые поля введите атрибуты **Email Address** (Адрес электронной почты), **Global Name** (Глобальное имя), **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.

> [!NOTE]
> Для подготовки учетных записей пользователей AAD можно использовать любые другие средства создания учетных записей консоли администрирования Mimecast или API, предоставляемое консолью администрирования Mimecast.
> 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

### <a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Чтобы назначить пользователей консоли администрирования Mimecast, выполните следующие действия.
1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Mimecast Admin Console** нажмите кнопку **Назначить пользователей**.
   
   ![Назначить пользователей](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Assign Users")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Yes")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


