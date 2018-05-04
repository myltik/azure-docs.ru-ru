---
title: Руководство по интеграции Azure Active Directory с Fluxx Labs | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 0bba820c14c5eddc6db99923e3fb1de58c110f4c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Руководство по интеграции Azure Active Directory с Fluxx Labs

В этом руководстве описано, как интегрировать приложение Fluxx Labs с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Fluxx Labs обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Fluxx Labs.
- Вы можете включить автоматический вход пользователей в Fluxx Labs (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Fluxx Labs, вам потребуется:

- подписка Azure AD;
- подписка Fluxx Labs с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Fluxx Labs из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-fluxx-labs-from-the-gallery"></a>Добавление Fluxx Labs из коллекции
Чтобы настроить интеграцию Fluxx Labs с Azure AD, необходимо добавить Fluxx Labs из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Fluxx Labs из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Fluxx Labs**, выберите **Fluxx Labs** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Приложение Fluxx Labs в списке результатов](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Fluxx Labs с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Fluxx Labs соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Fluxx Labs.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Fluxx Labs.

Чтобы настроить и проверить единый вход Azure AD в Fluxx Labs, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Fluxx Labs](#create-a-fluxx-labs-test-user)** требуется для того, чтобы во Fluxx Labs существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Fluxx Labs.

**Чтобы настроить единый вход Azure AD в Fluxx Labs, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Fluxx Labs** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Fluxx Labs** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Среда | Шаблон URL-адреса|
    |-------------|------------|
    | Производство | `https://<subdomain>.fluxx.io` |
    | Предварительный этап | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    | Среда | Шаблон URL-адреса|
    |-------------|------------|
    | Производство | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Предварительный этап | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки Fluxx Labs](mailto:travis@fluxxlabs.com).

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Fluxx Labs** щелкните **Настроить Fluxx Labs**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. В другом окне веб-браузера войдите на веб-сайт Fluxx Labs компании в качестве администратора.

8. Выберите **Admin** (Администратор) в разделе **Settings** (Параметры).

    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. На панели администрирования выберите **Plug-ins** > **Integrations** и **SAML SSO-(Disabled)** (Подключаемые модули > Интеграция > Единый вход SAML (отключен)).

    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. В разделе атрибутов выполните следующие действия.
    
    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Установите флажок **SAML SSO** (Единый вход SAML).

    Б. В текстовом поле **Request Path** (Путь запроса) введите **/auth/saml**.

    c. В текстовом поле **Callback Path** (Путь обратного вызова) введите **/auth/saml/callback**.

    d. В текстовое поле **Assertion Consumer Service Url (Single Sign-On URL)** (URL-адрес службы обработчика утверждений (URL-адрес службы единого входа)) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    д. В текстовое поле **Audience (SP Entity ID)** (Аудитория (идентификатор сущности SP)) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    f. Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений**.

    ж. В текстовое поле **Name identifier Format** (Формат идентификатора имени) введите значение `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Выберите команду **Сохранить**.

    > [!NOTE]
    > После сохранения содержимого поле будет отображаться пустым в целях безопасности, но его значение сохранится в конфигурации.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Создание тестового пользователя Fluxx Labs

Чтобы пользователи Azure AD могли выполнить вход в Fluxx Labs, они должны быть подготовлены в Fluxx Labs. В случае с Fluxx Labs подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на веб-сайт компании Fluxx Labs в качестве администратора.

2. Щелкните показанный ниже **значок**.

    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config6.png)

3. На панели мониторинга щелкните показанный ниже значок, чтобы открыть карту **New PEOPLE** (Новые пользователи).

    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

4. В разделе **NEW PEOPLE** (Новые пользователи) выполните следующие действия:
    
    ![Конфигурация Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. Во Fluxx Labs в качестве уникального идентификатора для единого входа используется адрес электронной почты. Укажите в поле **SSO UID** (ИД пользователя для единого входа) адрес электронной почты пользователя, который используется в качестве имени для входа для единого входа.

    Б. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Fluxx Labs.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Fluxx Labs, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Fluxx Labs**.

    ![Ссылка на Fluxx Labs в списке приложений](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Fluxx Labs" на панели доступа, вы автоматически войдете в приложение Fluxx Labs.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png
