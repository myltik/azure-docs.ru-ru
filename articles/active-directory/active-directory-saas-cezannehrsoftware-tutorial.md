---
title: Учебник. Интеграция Azure Active Directory с Cezanne HR Software | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: b6ba41b31df8f950e37724903d3bca31358a598c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343561"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Руководство. Интеграция Azure Active Directory с Cezanne HR Software

В этом руководстве описано, как интегрировать Cezanne HR Software с Azure Active Directory (Azure AD).

Интеграция Cezanne HR Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Cezanne HR Software.
- Вы можете включить автоматический вход пользователей в Cezanne HR Software (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Cezanne HR Software, вам потребуется следующее:

- подписка Azure AD;
- подписка на Cezanne HR Software с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Cezanne HR Software из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Добавление Cezanne HR Software из коллекции.
Чтобы настроить интеграцию Cezanne HR Software с Azure AD, вам потребуется добавить Cezanne HR Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cezanne HR Software из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cezanne HR Software**, выберите **Cezanne HR Software** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Cezanne HR Software в списке результатов](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Cezanne HR Software с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Cezanne HR Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cezanne HR Software.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Cezanne HR Software.

Чтобы настроить и проверить единый вход в Azure AD в Cezanne HR Software, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Cezanne HR Software](#create-a-cezannehrsoftware-test-user)** требуется для создания пользователя Britta Simon в Cezanne HR Software, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Cezanne HR Software.

**Чтобы настроить единый вход Azure AD в Cezanne HR Software, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Cezanne HR Software** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Cezanne HR Software** выполните указанные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Cezanne HR Software](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес `https://w3.cezanneondemand.com/CezanneOnDemand/`.

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`.
    
    > [!NOTE]
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса входа и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Cezanne HR Software](https://cezannehr.com/services/support/).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

6. В разделе **Cezanne HR Software Configuration** (Конфигурация Cezanne HR Software) щелкните **Configure Cezanne HR Software** (Настройка Cezanne HR Software), чтобы открыть окно **Настройка единого входа**.

    ![Раздел "Конфигурация Cezanne HR Software"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Прокрутите вниз до раздела **Краткий справочник**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Раздел "Конфигурация Cezanne HR Software"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. В другом окне веб-браузера войдите на веб-сайт Cezanne HR Software своей компании в качестве администратора.

9. В левой панели навигации щелкните **System Setup**(Настройка системы). Выберите пункт **Параметры безопасности**. Далее перейдите к пункту **Конфигурация единого входа**.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. На панели **Allow users to log in using the following Single Sign-On (SSO) Service** (Разрешить пользователям вход с использованием следующей службы единого входа) установите флажок **SAML 2.0** и выберите параметр **Расширенная конфигурация**.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Нажмите кнопку **Добавить** .

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. В разделе **SAML 2.0 Identity providers** (Поставщики удостоверений SAML) выполните следующие действия.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Введите имя поставщика удостоверений в поле **Отображаемое имя**.

    Б. В текстовое поле **Entity Identifier** (Идентификатор сущности) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure. 

    c. В поле **SAML Binding** (Привязка SAML) укажите значение POST.

    d. В текстовое поле **Security Token Service Endpoint** (Конечная точка службы токенов безопасности) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    д. В текстовом поле "User ID Attribute Name" (Имя атрибута идентификатора пользователя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Чтобы передать сертификат, скачанный с портала Azure, щелкните значок **Upload** (Передать).
    
    ж. Нажмите кнопку **ОК** . 

13. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Создание тестового пользователя в Cezanne HR Software

Чтобы разрешить пользователям Azure AD вход в Cezanne HR Software, их необходимо подготовить для Cezanne HR Software. В случае с Cezanne HR Software подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1.  Войдите на корпоративный сайт Cezanne HR Software в качестве администратора.

2.  В левой панели навигации щелкните **System Setup**(Настройка системы). Перейдите в раздел **Управление пользователями**. Выберите **Добавить нового пользователя**.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Новый пользователь")

3.  В разделе **Person Details** (Личные данные) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Новый пользователь")
    
    a. Установите для параметра **Внутренний пользователь** значение "Выкл.".
    
    Б. В текстовом поле **First name** (Имя) введите имя пользователя, например **Britta**.  
 
    c. В текстовом поле **Last name** (Фамилия) введите фамилию, например **Simon**.
    
    d. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

4.  В разделе **Account Information** (Сведения об учетной записи) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Новый пользователь")
    
    a. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Brittasimon@contoso.com.
    
    Б. В текстовом поле **Password** (Пароль) введите пароль пользователя.
    
    c. В раскрывающемся списке **Роль безопасности** выберите **Специалист отдела кадров**.
    
    d. Последовательно выберите **ОК**.

5. Перейдите на вкладку **Единый вход** и нажмите кнопку **Добавить** в области **Идентификаторы SAML 2.0)**.

    ![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Пользователь")

6. Выберите значение в списке **Поставщик удостоверений** и в текстовом поле **Идентификатор пользователя** введите адрес электронной почты учетной записи Britta Simon.

    ![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Пользователь")
    
7. Нажмите кнопку **Сохранить** .

    ![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Пользователь")

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Cezanne HR Software.

![Назначение роли пользователя][200] 

**Чтобы назначить Britta Simon в Cezanne HR Software, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Cezanne HR Software**.

    ![Ссылка на Cezanne HR Software в списке приложений](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Cezanne HR Software на панели доступа, вы автоматически войдете в приложение Cezanne HR Software.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

