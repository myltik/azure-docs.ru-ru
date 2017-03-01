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
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 84f909bf26aca70b361a87c0d430b686a068054b
ms.openlocfilehash: 5a6cb773b557b525f8eb0c75a19feb70cab86c6b
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Руководство. Интеграция Azure Active Directory с консолью администрирования Mimecast
Цель данного учебника — продемонстрировать интеграцию Azure и консоли администрирования Mimecast.  
Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* подписка Mimecast Admin Console с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные в консоли администрирования Mimecast, смогут выполнять единый вход в приложение консоли администрирования Mimecast на веб-сайте компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для консоли администрирования Mimecast
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Сценарий")

## <a name="enabling-the-application-integration-for-mimecast-admin-console"></a>Включение интеграции приложений для консоли администрирования Mimecast
В этом разделе показано, как включить интеграцию приложений для консоли администрирования Mimecast.

**Чтобы включить интеграцию с приложением Mimecast Admin Console, выполните следующее.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **Mimecast Admin Console**.
   
   ![Коллекция приложений](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Коллекция приложений")
7. В области результатов выберите **Mimecast Admin Console** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast Admin Console")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в консоли администрирования Mimecast со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **Mimecast Admin Console** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в Mimecast Admin Console?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Mimecast Admin Console** введите URL-адрес, используемый для входа в приложение Mimecast Admin Console (например, https://webmail-uk.mimecast.com или https://webmail-us.mimecast.com), и нажмите кнопку **Далее**.
   
   >[!NOTE]
   >URL-адрес для входа зависит от региона. 
   > 
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Mimecast Admin Console** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Настройка единого входа")
5. В другом окне веб-браузера войдите в консоль администрирования Mimecast в качестве администратора.
6. Выберите **Services \> Application** (Службы > Приложение).
   
   ![Services](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services") (Службы)
7. Щелкните **Профили проверки подлинности**.
   
   ![Authentication Profiles](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Authentication Profiles") (Профили аутентификации)
8. Щелкните **Новый профиль проверки подлинности**.
   
   ![New Authentication Profile](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "New Authentication Profile") (Создать профиль аутентификации)
9. В разделе **Профиль проверки подлинности** сделайте следующее:
   
   ![Authentication Profile](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Authentication Profile") (Профиль аутентификации)
   
   1. В текстовом поле **Описание** введите имя конфигурации.
   2. Выберите **Обязательное использование проверки подлинности SAML для консоли администрирования Mimecast**.
   3. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   4. На странице диалогового окна **Настройка единого входа в Mimecast Admin Console** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Issuer URL** (URL-адрес издателя).
   5. На странице диалогового окна **Настройка единого входа в Mimecast Admin Console** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login URL** (URL-адрес для входа).
   6. На странице диалогового окна **Настройка единого входа в Mimecast Admin Console** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).  
      >[!NOTE]
      >Значения URL-адреса входа в систему и URL-адреса выхода из системы для консоли администрирования Mimecast одинаковы. 
      > 
   7. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      >[!TIP]
      >Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o). 
      > 
   8. Откройте сертификат в кодировке Base-64 в Блокноте, удалите первую строку ("*--*") и последнюю строку ("*--*"), скопируйте остальное содержимое в буфер обмена и вставьте его в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).
   9. Установите флажок **Разрешить единый вход**.
   10. Щелкните **Сохранить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Настройка единого входа")
    
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в консоль администрирования Mimecast, они должны быть подготовлены для консоли администрирования Mimecast. В случае с консолью администрирования Mimecast подготовка выполняется вручную.

* Перед созданием пользователей необходимо зарегистрировать домен.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите в **консоль администрирования Mimecast** в качестве администратора.
2. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
   ![Directories](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Directories") (Каталоги)
3. Щелкните **Зарегистрировать новый домен**.
   
   ![Register New Domain](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Register New Domain") (Зарегистрировать новый домен)
4. После создания нового домена щелкните **Новый адрес**.
   
   ![New Address](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "New Address") (Новый адрес)
5. В окне нового адреса выполните следующие действия.
   
   ![Сохранить](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Сохранить")
   
   1. В соответствующие текстовые поля введите атрибуты **Email Address** (Адрес электронной почты), **Global Name** (Глобальное имя), **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.

>[!NOTE]
>Для подготовки учетных записей пользователей AAD можно использовать любые другие средства создания учетных записей консоли администрирования Mimecast или API, предоставляемое консолью администрирования Mimecast. 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей в Mimecast Admin Console, выполните следующее.**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Mimecast Admin Console** щелкните **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


