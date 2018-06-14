---
title: Руководство по интеграции Azure Active Directory с Mimecast Personal Portal | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 4b976481e60926e6ab95a679e6dcc8aa0818b48b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346886"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Руководство по интеграции Azure Active Directory с Mimecast Personal Portal

В этом руководстве описано, как интегрировать Mimecast Personal Portal с Azure Active Directory (Azure AD).

Интеграция Azure AD с Mimecast Personal Portal обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Mimecast Personal Portal.
- Вы можете включить автоматический вход пользователей в Mimecast Personal Portal (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Mimecast Personal Portal, вам потребуется:

- подписка Azure AD;
- Подписка на личный портал Mimecast с поддержкой единого входа

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Mimecast Personal Portal из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Добавление Mimecast Personal Portal из коллекции
Чтобы настроить интеграцию Mimecast Personal Portal с Azure AD, необходимо добавить Mimecast Personal Portal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mimecast Personal Portal из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Mimecast Personal Portal**, выберите **Mimecast Personal Portal** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Mimecast Personal Portal в списке результатов](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Mimecast Personal Portal с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Mimecast Personal Portal соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mimecast Personal Portal.

Чтобы настроить и проверить единый вход Azure AD в Mimecast Personal Portal, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Mimecast Personal Portal](#create-a-mimecast-personal-portal-test-user)** требуется для того, чтобы в Mimecast Personal Portal существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Mimecast Personal Portal.

**Чтобы настроить единый вход Azure AD в Mimecast Personal Portal, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Mimecast Personal Portal** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Mimecast Personal Portal** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес. 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Регион  |  Значение | 
    | --------------- | --------------- |
    | Европа          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | США   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | ЮАР    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Австралия       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес: 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Значение идентификатора приведено для примера и не является реальным. Вместо него нужно указать фактический идентификатор. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Mimecast Personal Portal](http://www.mimecast.com/customer-success/technical-support/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Mimecast Personal Portal** щелкните **Настроить Mimecast Personal Portal**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. В другом окне веб-браузера войдите в личный портал Mimecast в качестве администратора.

8. Выберите **Services \> Application** ("Службы" > "Приложение").
   
    ![Приложения](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "Приложения")

9. Щелкните **Профили проверки подлинности**.
   
    ![Authentication Profiles](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles") (Профили аутентификации)

10. Щелкните **Новый профиль проверки подлинности**.
   
    ![Создание профиля аутентификации](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "New Authentication Profile")

11. В разделе **Профиль проверки подлинности** сделайте следующее:
   
    ![Authentication Profile](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile") (Профиль аутентификации)
   
    a. В текстовом поле **Описание** введите имя конфигурации.
   
    Б. Выберите **Обязательное использование проверки подлинности SAML для личного портала Mimecast**.
   
    c. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   
    d. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
   
    д. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    f. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    ж. Откройте в Блокноте сертификат в кодировке **Base64**, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).

    h. Установите флажок **Разрешить единый вход**.
   
    i. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Создание тестового пользователя Mimecast Personal Portal

Чтобы разрешить пользователям Azure AD вход в личный портал Mimecast, они должны быть подготовлены для личного портала Mimecast. В случае с личным порталом Mimecast подготовка выполняется вручную.

Перед созданием пользователей необходимо зарегистрировать домен.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите на **Mimecast Personal Portal** в качестве администратора.

2. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
    ![Directories](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "Directories") (Каталоги)

3. Щелкните **Зарегистрировать новый домен**.
   
    ![Register New Domain](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "Register New Domain") (Зарегистрировать новый домен)

4. После создания нового домена щелкните **Новый адрес**.
   
    ![New Address](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "New Address") (Новый адрес)

5. В диалоговом окне "New Address" (Новый адрес) введите соответствующие данные действующей учетной записи Azure AD, которую необходимо подготовить.
   
    ![Сохранить](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "Сохранить")
   
    a. В текстовое поле **Email Address** (Адрес электронной почты) введите **адрес электронной почты** пользователя, например **BrittaSimon@contoso.com**.
    
    Б. В текстовое поле **Global Name** (Глобальное имя) введите **имя пользователя** **BrittaSimon**.

    c. В текстовые поля **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) введите **пароль** пользователя.
   
    Б. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Mimecast Personal Portal или API, предоставляемые Mimecast Personal Portal для подготовки учетных записей пользователя Azure Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mimecast Personal Portal.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Mimecast Personal Portal, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Mimecast Personal Portal**.

    ![Ссылка на Mimecast Personal Portal в списке приложений](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Mimecast Personal Portal" на панели доступа, вы автоматически войдете в приложение Mimecast Personal Portal.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

