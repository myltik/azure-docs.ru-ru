---
title: "Учебник. Интеграция Azure Active Directory с Mimecast Personal Portal | Документация Майкрософт"
description: "Узнайте, как использовать Mimecast Personal Portal с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a5b24d6897fbc6fdf71e4acc3898d640ec974f51
ms.openlocfilehash: 93544a2b907629c18e8abe6405e85d4623995bd7
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Руководство. Интеграция Azure Active Directory с личным порталом Mimecast
Цель данного учебника — продемонстрировать интеграцию Azure и личного портала Mimecast. 

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

* действующая подписка Azure;
* подписка Mimecast Personal Portal с поддержкой единого входа.

По завершении работы с этим руководством пользователи Azure AD, назначенные на портале Mimecast Personal Portal, смогут выполнять единый вход в приложение Mimecast Personal Portal на веб-сайте компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1. Включение интеграции приложений для личного портала Mimecast
2. Настройка единого входа
3. Настройка подготовки учетных записей пользователей
4. Назначение пользователей

![Сценарий](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Сценарий")

## <a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>Включение интеграции приложений для личного портала Mimecast
В этом разделе показано, как включить интеграцию приложений для личного портала Mimecast.

**Чтобы включить интеграцию с приложением Mimecast Personal Portal, выполните следующее.**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
   ![Приложения](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Приложения")
4. В нижней части страницы нажмите кнопку **Добавить** .
   
   ![Добавление приложения](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Добавление приложения")
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
   ![Добавление приложения из коллекции](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Добавление приложения из коллекции")
6. В **поле поиска** введите **Mimecast Personal Portal**.
   
   ![Коллекция приложений](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Коллекция приложений")
7. В области результатов выберите **Mimecast Personal Portal** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
   ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
   
## <a name="configuring-single-sign-on"></a>Настройка единого входа

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в личном портале Mimecast со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.  

В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64.  
Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

**Чтобы настроить единый вход, выполните следующие действия:**

1. На странице интеграции с приложением **Mimecast Personal Portal** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Настройка единого входа")
2. На странице **Как пользователи должны входить в Mimecast Personal Portal?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Настройка единого входа")
3. На странице **Настроить URL-адрес приложения** в текстовом поле **URL-адрес входа в Mimecast Personal Portal** введите URL-адрес, используемый для входа в приложение Mimecast Personal Portal (например, https://webmail-uk.mimecast.com или https://webmail-us.mimecast.com), и нажмите кнопку **Далее**.
   
   >[!NOTE]
   >URL-адрес для входа зависит от региона. 
   > 
   
   ![Настройка URL-адреса приложения](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Настройка URL-адреса приложения")
4. На странице **Настройка единого входа в Mimecast Personal Portal** нажмите кнопку **Скачать сертификат**, а затем сохраните файл сертификата на локальном компьютере.
   
   ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Настройка единого входа")
5. В другом окне веб-браузера войдите в личный портал Mimecast в качестве администратора.
6. Выберите **Services \> Application** (Службы > Приложение).
   
   ![Приложения](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Приложения")
7. Щелкните **Профили проверки подлинности**.
   
   ![Authentication Profiles](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Authentication Profiles") (Профили аутентификации)
8. Щелкните **Новый профиль проверки подлинности**.
   
   ![New Authentication Profile](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "New Authentication Profile") (Создать профиль аутентификации)
9. В разделе **Профиль проверки подлинности** сделайте следующее:
   
   ![Authentication Profile](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Authentication Profile") (Профиль аутентификации)
   
   1. В текстовом поле **Описание** введите имя конфигурации.
   2. Выберите **Обязательное использование проверки подлинности SAML для личного портала Mimecast**.
   3. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   4. На странице диалогового окна **Настройка единого входа в Mimecast Personal Portal** классического портала Azure скопируйте значение поля **URL-адрес издателя** и вставьте его в текстовое поле **Issuer URL** (URL-адрес издателя).
   5. На странице диалогового окна **Настройка единого входа в Mimecast Personal Portal** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Login URL** (URL-адрес входа).
   6. На странице диалогового окна **Настройка единого входа в Mimecast Personal Portal** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **Logout URL** (URL-адрес для выхода).  
      >[!NOTE]
      >Значения URL-адреса входа в систему и URL-адреса выхода из системы для личного портала Mimecast одинаковы.
      > 
   7. Создайте файл **в кодировке Base-64** из скачанного сертификата.  
      
      >[!TIP]
      >Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).
      >  
   8. Откройте сертификат в кодировке Base-64 в Блокноте, удалите первую строку ("*--*") и последнюю строку ("*--*"), скопируйте остальное содержимое в буфер обмена и вставьте его в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).
   9. Установите флажок **Разрешить единый вход**.
   10. Щелкните **Сохранить**.
10. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.
    
    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Настройка единого входа")
    
## <a name="configuring-user-provisioning"></a>Настройка подготовки учетных записей пользователей

Чтобы разрешить пользователям Azure AD вход в личный портал Mimecast, они должны быть подготовлены для личного портала Mimecast. В случае с личным порталом Mimecast подготовка выполняется вручную.

* Перед созданием пользователей необходимо зарегистрировать домен.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите на **Mimecast Personal Portal** в качестве администратора.
2. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
   ![Directories](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directories") (Каталоги)
3. Щелкните **Зарегистрировать новый домен**.
   
   ![Register New Domain](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Register New Domain") (Зарегистрировать новый домен)
4. После создания нового домена щелкните **Новый адрес**.
   
   ![New Address](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "New Address") (Новый адрес)
5. В окне нового адреса выполните следующие действия.
   
   ![Сохранить](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Сохранить")
   
   1. В соответствующие текстовые поля введите атрибуты **Email Address** (Адрес электронной почты), **Global Name** (Глобальное имя), **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) действующей учетной записи AAD, которую вы хотите подготовить.
   2. Щелкните **Сохранить**.

>[!NOTE]
>Для подготовки учетных записей пользователей AAD можно использовать любые другие средства создания учетных записей личного портала Mimecast или API, предоставляемые личным порталом Mimecast. 
> 

## <a name="assigning-users"></a>Назначение пользователей
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

**Чтобы назначить пользователей в Mimecast Personal Portal, выполните следующее.**

1. На классическом портале Azure создайте тестовую учетную запись.
2. На странице интеграции с приложением **Mimecast Personal Portal** щелкните **Назначить пользователей**.
   
   ![Назначение пользователей](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Назначение пользователей")
3. Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.
   
   ![Да](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Да")

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).


