---
title: Руководство. Интеграция Azure Active Directory с KnowledgeOwl | Документы Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 1ac63d512645b2eb9627798809ce699129799c94
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348236"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Руководство. Интеграция Azure Active Directory с KnowledgeOwl

В этом руководстве описано, как интегрировать KnowledgeOwl с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением KnowledgeOwl обеспечивает следующие преимущества:

- C помощью Azure AD вы можете контролировать доступ к KnowledgeOwl.
- Вы можете включить автоматический вход пользователей в KnowledgeOwl (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с KnowledgeOwl, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа KnowledgeOwl.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление KnowledgeOwl из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-knowledgeowl-from-the-gallery"></a>Добавление KnowledgeOwl из коллекции
Чтобы настроить интеграцию KnowledgeOwl с Azure AD, нужно добавить KnowledgeOwl из коллекции в список управляемых приложений SaaS.

**Чтобы добавить KnowledgeOwl из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **KnowledgeOwl**, выберите **KnowledgeOwl** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![KnowledgeOwl в списке результатов](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в KnowledgeOwl с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD нужно знать, какой пользователь в KnowledgeOwl соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в KnowledgeOwl.

Чтобы настроить и проверить единый вход Azure AD в KnowledgeOwl, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения KnowledgeOwl](#create-a-knowledgeowl-test-user)** требуется для того, чтобы в KnowledgeOwl существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении KnowledgeOwl.

**Чтобы настроить единый вход Azure AD в KnowledgeOwl, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **KnowledgeOwl** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. Если вы хотите настроить приложение в режиме, **инициированном поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения KnowledgeOwl** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. В текстовом поле **Идентификатор (сущности)** введите URL-адрес в следующем формате:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Эти значения приведены в качестве примера. Потребуется изменить эти значения, указав фактические идентификатор, URL-адрес ответа и URL-адрес для входа. Это описано ниже.

5. Приложение KnowledgeOwl ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения.

    ![Настройка единого входа](./media/active-directory-saas-knowledgeowl-tutorial/attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута | Пространство имен|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.
    
    ![Настройка единого входа](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    г. В списке **Пространство имен** введите значение пространства имен, показанное для этой строки.
    
    д. Нажмите кнопку **ОК**.

7. В разделе **Сертификат подписи SAML** щелкните **Certificate (Raw)** (Сертификат (необработанный)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_400.png)
    
9. В разделе **Настройка KnowledgeOwl** щелкните **Настроить KnowledgeOwl**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. В другом окне веб-браузера войдите на сайт KnowledgeOwl вашей организации в качестве администратора.

11. Выберите **Параметры** и затем **Безопасность**.

    ![Конфигурация KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure1.png)

12. Прокрутите вниз до раздела **SAML SSO Integration** (Интеграция единого входа SAML) и выполните следующие действия:
    
    ![Конфигурация KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure2.png)

    a. Выберите **Enable SAML SSO** (Включить единый вход SAML).

    Б. Скопируйте значение **SP Entity ID** (Идентификатор сущности SP) и вставьте его в поле **Идентификатор (сущности)** в разделе **Домены и URL-адреса приложения KnowledgeOwl** на портале Azure.

    c. Скопируйте значение **SP Login URL** (URL-адрес входа SP) и вставьте его в текстовые поля **"URL-адрес для входа" и "URL-адрес ответа"** в разделе **Домены и URL-адреса приложения KnowledgeOwl** на портале Azure.

    d. В текстовое поле **IdP entityID** (Идентификатор сущности для поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    д. В текстовое поле **IdP Login URL** (URL-адрес входа для поставщика удостоверений) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    f. В текстовое поле **IdP Logout URL** (URL-адрес выхода для поставщика удостоверений) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    ж. Отправьте скачанный сертификат с портала Azure, выбрав **Upload IdP Certificate** (Отправить сертификат поставщика удостоверений).

    h. Выберите **Map SAML Attributes** (Сопоставить атрибуты SAML), чтобы сопоставить атрибуты, и выполните следующие действия:
    
    ![Конфигурация KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure3.png)

    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` в текстовом поле **SSO ID** (Идентификатор единого входа).
    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` в текстовом поле **Username/Email** (Имя пользователя/электронная почта).
    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` в текстовом поле **First Name** (Имя).
    * Введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` в текстовом поле **Last Name** (Фамилия).
    * Нажмите кнопку **Сохранить**

    i. В нижней части страницы нажмите кнопку **Сохранить** .

    ![Конфигурация KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Создание тестового пользователя KnowledgeOwl

В этом разделе описано, как создать пользователя с именем Britta Simon в приложении KnowledgeOwl. Приложение KnowledgeOwl поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь создается при попытке получить доступ к KnowledgeOwl (если он еще не создан).
>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к KnowledgeOwl.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в KnowledgeOwl, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **KnowledgeOwl**.

    ![Ссылка на KnowledgeOwl в списке "Приложения"](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент KnowledgeOwl на панели доступа, вы автоматически войдете в приложение KnowledgeOwl.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_203.png

