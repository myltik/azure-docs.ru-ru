---
title: "Руководство по интеграции Azure Active Directory с Image Relay | Документация Майкрософт"
description: "Сведения о настройке единого входа Azure Active Directory в Image Relay."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 94fdae2992e69f4330d325968a9e66b3f6f38ce0


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Руководство по интеграции Azure Active Directory с приложением Image Relay
Цель этого руководства — показать, как интегрировать Azure Active Directory с приложением Image Relay.  
Интеграция Image Relay с Azure AD обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Image Relay.
* Вы можете включить автоматический вход пользователей в Image Relay (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Image Relay, вам потребуется:

* подписка Azure AD;
* подписка Image Relay с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  
Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Image Relay из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-image-relay-from-the-gallery"></a>Добавление Image Relay из коллекции
Чтобы настроить интеграцию Image Relay с Azure AD, необходимо добавить Image Relay из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Image Relay из коллекции, выполните следующие действия:**

1. На классическом портале Azure в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Image Relay**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. В области результатов выберите **Image Relay** и щелкните **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD.
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в ImageRelay с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход Azure AD, необходима учетная запись пользователя, представляющая соответствующего пользователя в Image Relay.  Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Image Relay.  
Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Image Relay.

Чтобы настроить и проверить единый вход Azure AD в Image Relay, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Image Relay](#creating-a-userecho-test-user)** требуется для создания в Image Relay пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Image Relay.

**Чтобы настроить единый вход Azure AD в Image Relay, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **Image Relay** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
     ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Image Relay?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
     ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    а. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый пользователями для входа в приложение ImageRelay (например, *https://fabrikam.ImageRelay.com/*).
   
    b. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Image Relay** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    а. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   
    b. Нажмите кнопку **Далее**.
5. В другом окне браузера войдите на сайт компании Image Relay с правами администратора.
6. На панели инструментов вверху экрана выберите рабочую нагрузку **Users & Permissions** (Пользователи и разрешения).
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. Щелкните **Создать новое разрешение**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. В рабочей нагрузке **Single Sign On Settings** (Параметры единого входа) установите флажок **This Group can only sign-in via Single Sign On** (Эта группа может входить только через единый вход) и нажмите кнопку **Сохранить**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. Откройте **Параметры учетной записи**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. Выберите рабочую нагрузку **Параметры единого входа** .
    
     ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. В диалоговом окне **SAML Settings** (Параметры SAML) выполните следующие действия.
    
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    а. На классическом портале Azure скопируйте **URL-адрес службы единого входа** и вставьте его в текстовом поле **URL-адрес входа**.

    b. На классическом портале Azure скопируйте **URL-адрес службы единого выхода** и вставьте его в текстовом поле **URL-адрес выхода**.

    c. В поле **Name Id Format** (Формат ИД имени) выберите **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. В разделе **Binding Options for Requests from the Service Provider (Image Relay)** (Обязательные параметры для запросов от поставщика услуг (Image Relay)) выберите **POST Binding** (Привязка POST).

    д. В разделе **Сертификат X.509** щелкните **Обновить сертификат**.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    Е. Откройте скачанный сертификат в блокноте, а затем скопируйте и вставьте его содержимое в текстовое поле X.509 Certificate (Сертификат X.509).

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    ж. В разделе **Just-In-Time User Provisioning** (JIT-подготовка пользователей) выберите **Enable Just-In-Time User Provisioning** (Включить JIT-подготовку пользователей).

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Выберите группу разрешений (например, **SSO Basic**(Базовый единый вход)), которой будет разрешено выполнять вход только через службу единого входа.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Щелкните **Сохранить**.

1. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
2. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
   
    ![единого входа Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    а. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   
    b. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   
    c. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    а. В текстовом поле **Имя** введите **Britta**.  
   
    b. В текстовом поле **Фамилия** введите **Simon**.
   
    c. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   
    d. В списке **Роль** выберите **Пользователь**.
   
    д. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    а. Запишите значение поля **Новый пароль**.
   
    b. Нажмите **Завершено**.   

### <a name="creating-an-image-relay-test-user"></a>Создание тестового пользователя Image Relay
Цель этого раздела — создать пользователя с именем Britta Simon в Image Relay.

**Чтобы создать пользователя с именем Britta Simon в Image Relay, выполните следующие действия:**

1. Войдите на cайт компании Image Relay в качестве администратора.
2. Откройте раздел **Users & Permissions** (Пользователи и разрешения) и выберите параметр **Create SSO User** (Создать пользователя единого входа).
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. Введите **электронную почту**, **имя**, **фамилию** и **компанию** пользователя, которого нужно подготовить, и выберите группу разрешений (например, SSO Basic (Базовый единый вход)), т. е. группу, которая сможет выполнять вход только через службу единого входа.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. Щелкните **Создать**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Image Relay.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Image Relay, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните пункт **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Image Relay**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  
Щелкнув плитку Image Relay на панели доступа, вы автоматически войдете в приложение Image Relay.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


