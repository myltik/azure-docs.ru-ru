---
title: Руководство. Интеграция Azure Active Directory с Citrix ShareFile | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: 0e860c1f1db77026e775191c76350333aa66dc7f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345883"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Руководство. Интеграция Azure Active Directory с Citrix ShareFile

В этом руководстве описано, как интегрировать Citrix ShareFile с Azure Active Directory (Azure AD).

Интеграция Azure AD с Citrix ShareFile обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Citrix ShareFile.
- Вы можете включить автоматический вход пользователей в Citrix ShareFile (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Citrix ShareFile, вам потребуется:

- подписка Azure AD;
- подписка Citrix ShareFile с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Citrix ShareFile из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="add-citrix-sharefile-from-the-gallery"></a>Добавление Citrix ShareFile из коллекции
Чтобы настроить интеграцию Citrix ShareFile с Azure AD, вам нужно добавить Citrix ShareFile из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Citrix ShareFile из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Citrix ShareFile**, выберите **Citrix ShareFile** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Citrix ShareFile в списке результатов](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Citrix ShareFile с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Citrix ShareFile соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Citrix ShareFile.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Citrix ShareFile.

Чтобы настроить и проверить единый вход Azure AD в Citrix ShareFile, вам нужно выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Citrix ShareFile](#create-a-citrix-sharefile-test-user)** требуется для создания в Citrix ShareFile пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описывается, как включить единый вход Azure AD на портале Azure и настроить его в приложении Citrix ShareFile.

**Чтобы настроить единый вход Azure AD в Citrix ShareFile, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Citrix ShareFile** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Citrix ShareFile** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа приложения Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.sharefile.com/saml/login`

    Б. В текстовом поле **Identifier (Entity ID)** (Идентификатор (ИД сущности)) введите URL-адрес в следующем формате.

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|
    | |

    > [!NOTE]
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Citrix ShareFile](https://www.citrix.co.in/products/sharefile/support.html).

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png)

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. В разделе **конфигурации Citrix ShareFile** щелкните **Настройка Citrix ShareFile**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png)

7. В другом окне веб-браузера войдите на свой корпоративный веб-сайт **Citrix ShareFile** в качестве администратора.

8. На панели инструментов в верхней части экрана нажмите **Администратор**.

9. В левой области навигации нажмите **Настройка единого входа**.
   
    ![Администрирование учетной записи](./media/active-directory-saas-sharefile-tutorial/ic773627.png "Администрирование учетной записи")

10. На странице диалогового окна **Single Sign-On/ SAML 2.0 Configuration** (Настройка единого входа или SAML 2.0) в разделе **Основные параметры** выполните следующие действия:
   
    ![Единый вход](./media/active-directory-saas-sharefile-tutorial/ic773628.png "Единый вход")
   
    a. Выберите команду **Enable SAML**(Включить SAML).
    
    Б. В текстовое поле **Your IDP Issuer/ Entity ID** (Поставщик удостоверений/идентификатор сущности) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    c. Нажмите **Изменить** рядом с полем **X.509 Certificate** (Сертификат X.509) и передайте сертификат, скачанный с портала Azure AD.
    
    d. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
    
    д. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

11. Нажмите **Сохранить** на портале управления Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Создание тестового пользователя Citrix ShareFile

Чтобы пользователи Azure AD могли выполнить вход в Citrix ShareFile, они должны быть подготовлены для Citrix ShareFile. В случае с Citrix ShareFile подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в клиент **Citrix ShareFile** .

2. Последовательно выберите пункты **Manage Users \> Manage Users Home \> + Create Employee** (Управление пользователями > Управление домашним ресурсом пользователей > + Создать сотрудника).
   
   ![Создание сотрудника](./media/active-directory-saas-sharefile-tutorial/IC781050.png "Создание сотрудника")

3. В разделе **Basic Information** (Базовые сведения) выполните следующие действия.
   
   ![Основные сведения](./media/active-directory-saas-sharefile-tutorial/IC799951.png "Основные сведения")
   
   a. В текстовое поле **Email Address** (Электронная почта) введите адрес электронной почты учетной записи Britta Simon**brittasimon@contoso.com**.
   
   Б. В текстовое поле **First Name** (Имя) введите **имя пользователя**, например **Britta**.
   
   c. В текстовое поле **Last Name** (Фамилия) введите **фамилию**, например **Simon**.

4. Нажмите кнопку **Add User**(Добавить пользователя).
  
   >[!NOTE]
   >Владелец учетной записи Azure AD получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие инструменты создания учетной записи пользователя Citrix ShareFile или API, предоставляемые Citrix ShareFile для подготовки учетных записей пользователей AAD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как настроить для пользователя Britta Simon единый вход Azure, предоставив этому пользователю доступ к Citrix ShareFile.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Citrix ShareFile, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Citrix ShareFile**.

    ![Ссылка на ShareFile в списке "Приложения"](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Citrix ShareFile на панели доступа, вы автоматически войдете в приложение Citrix ShareFile.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png
