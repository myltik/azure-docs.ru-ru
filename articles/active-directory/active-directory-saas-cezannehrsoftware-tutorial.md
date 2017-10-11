---
title: "Руководство по интеграции Azure Active Directory с Cezanne HR Software | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Cezanne HR Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>Руководство по интеграции Azure Active Directory с Cezanne HR Software

В этом руководстве описано, как интегрировать Cezanne HR Software с Azure Active Directory (Azure AD).

Интеграция Cezanne HR Software с Azure AD обеспечивает следующие преимущества: Вы можете:

- С помощью Azure AD можно контролировать доступ к Cezanne HR Software.
- Вы можете включить автоматический вход пользователей в Cezanne HR Software (единый вход) с использованием учетной записи Azure AD.
- Централизованное управление учетными записями через портал Azure.

Чтобы узнать больше об интеграции приложений SaaS с Azure AD, прочитайте статью [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Cezanne HR Software, вам потребуется следующее:

- подписка Azure AD;
- подписка Cezanne HR Software с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для тестирования действий, выполняемых в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

* Добавление Cezanne HR Software из коллекции.
* Настройка и проверка единого входа Azure AD.

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Добавление Cezanne HR Software из коллекции
Чтобы настроить интеграцию Cezanne HR Software с Azure AD, добавьте Cezanne HR Software из коллекции в список управляемых приложений SaaS.

Чтобы добавить Cezanne HR Software из коллекции, сделайте следующее.

1. На **[портале Azure](https://portal.azure.com)** в области слева нажмите кнопку **Azure Active Directory**. 

    ![Кнопка Azure Active Directory][1]

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Ссылка "Все приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна **Все приложения** нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cezanne HR Software**.

    ![Поле поиска](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. Из списка результатов выберите **Cezanne HR Software** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Список результатов](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описано, как настроить и проверить единый вход Azure AD в Cezanne HR Software с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Cezanne HR Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cezanne HR Software.

Чтобы установить эту связь, присвойте **имени пользователя** в Azure AD значение **имени пользователя** в Cezanne HR Software.

Чтобы настроить и проверить единый вход Azure AD в Cezanne HR Software, выполните действия в следующих стандартных блоках.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

В этом разделе описывается, как включить единый вход Azure AD на портале Azure и настроить единый вход в приложении Cezanne HR Software. Для этого выполните следующее.

1. На портале Azure на странице интеграции с приложением **Cezanne HR Software** щелкните **Единый вход**.

    ![Команда "Единый вход"][4]

2. В диалоговом окне **Единый вход** из списка **Режим** выберите значение **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Поле "Режим"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Cezanne HR Software** выполните следующие действия.

    ![Раздел "Домены и URL-адреса приложения Cezanne HR Software"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    а. В поле **URL-адрес для входа** введите URL-адрес, используя следующий синтаксис: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. В поле **URL-адрес ответа** введите URL-адрес, используя следующий синтаксис: `https://w3.cezanneondemand.com:443/<tenantid>`.    
     
    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Замените их фактическими значениями URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Cezanne HR Software](mailto:info@cezannehr.com).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)** и сохраните файл сертификата на компьютере.

    ![Раздел "Сертификат подписи SAML"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Щелкните **Сохранить**.

    ![Кнопка "Сохранить"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация Cezanne HR Software** щелкните **Настроить Cezanne HR Software**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML** и **URL-адрес службы единого входа SAM**L из раздела **Краткий справочник**.

    ![Раздел "Конфигурация Cezanne HR Software"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. В другом окне веб-браузера войдите в свой клиент Cezanne HR Software в качестве администратора.

8. В левой области щелкните **System Setup** (Настройка системы). Выберите **Security Settings** > **Single Sign-On Configuration** ("Параметры безопасности" > "Конфигурация единого входа").

    ![Ссылка "Single Sign-On Configuration" (Конфигурация единого входа)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. В области **Allow users to log in using the following Single Sign-On (SSO) Service** (Разрешить пользователям вход с использованием следующей службы единого входа) установите флажок **SAML 2.0** и выберите параметр **Advanced Configuration** (Расширенная конфигурация).

    ![Параметры служб единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Нажмите кнопку **Add New** (Добавить).

    ![Кнопка "Add New" (Добавить)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. В разделе **SAML 2.0 Identity Providers** (Поставщики удостоверений SAML 2.0) выполните следующие действия.

    ![Раздел "SAML 2.0 Identity Providers" (Поставщики удостоверений SAML 2.0)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    а. Введите имя поставщика удостоверений в поле **Display Name** (Отображаемое имя).

    b. В поле **Entity Identifier** (Идентификатор сущности) вставьте **идентификатор сущности SAML**, скопированный на портале Azure. 

    c. Из списка **SAML Binding** (Привязка SAML) выберите пункт **POST**.

    d. Вставьте **URL-адрес службы единого входа SAML**, который вы скопировали на портале Azure, в поле **Security Token Service Endpoint** (Конечная точка службы токенов безопасности). 
    
    д. В текстовом поле **User ID Attribute Name** (Имя атрибута идентификатора пользователя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Чтобы передать сертификат, скачанный с Azure AD, нажмите кнопку **Upload** (Передать).
    
    g. Нажмите кнопку **ОК**. 

12. Щелкните **Сохранить**.

    ![Кнопка "Сохранить"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Настроив приложение, вы можете прочитать краткую версию предыдущих инструкций на [портале Azure](https://portal.azure.com). После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход**. Откройте встроенную документацию из раздела **Настройка**. 

Чтобы узнать больше, ознакомьтесь со [встроенной документацией Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon.

![Тестовый пользователь Britta Simon][100]

Чтобы создать тестового пользователя в Azure AD, выполните следующее.

1. На **портале Azure** в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, выберите **Пользователи и группы** > **Все пользователи**.
    
    ![Ссылка "Все пользователи"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    Откроется диалоговое окно **Все пользователи**.

3. Щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. В диалоговом окне **Пользователь** сделайте следующее.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    а. В поле **Имя** введите **BrittaSimon**.

    b. В поле **Имя пользователя** введите **адрес электронной почты** пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, созданное в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Создание тестового пользователя Cezanne HR Software

Чтобы пользователи Azure AD могли входить в Cezanne HR Software, их необходимо подготовить в Cezanne HR Software. В случае Cezanne HR Software подготовка выполняется вручную.

Чтобы подготовить учетную запись пользователя, сделайте следующее.

1.  Войдите на корпоративный сайт Cezanne HR Software своей организации в качестве администратора.

2.  В левой области выберите **System Setup** > **Manage Users** > **Add New User** ("Настройка системы" > "Управление пользователями" > "Добавить пользователя").

    ![Ссылка "Add New User" (Добавить пользователя)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Создание пользователя")

3.  В разделе **Person Details** (Сведения о пользователе) выполните следующее.

    ![Раздел "Person Details" (Сведения о пользователе)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Создание пользователя")
    
    а. Установите для параметра **Internal User** (Внутренний пользователь) значение **OFF** (Выключено).
    
    b. В поле **First Name** (Имя) введите имя пользователя, например **Britta**.  
 
    c. В поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.
    
    d. В поле **E-mail** (Адрес электронной почты) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

4.  В разделе **Account Information** (Сведения об учетной записи) выполните следующее.

    ![Раздел "Account Information" (Сведения об учетной записи)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Создание пользователя")
    
    а. В поле **Username** (Имя пользователя) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.
    
    b. В поле **Password** (Пароль) введите пароль пользователя.
    
    c. В поле **Security Role** (Роль безопасности) выберите **HR Professional** (Специалист отдела кадров).
    
    d. Нажмите кнопку **ОК**.

5. Перейдите на вкладку **Single sign-on** (Единый вход) и в разделе **SAML 2.0 Identifiers** (Идентификаторы SAML 2.0) нажмите кнопку **Add New** (Добавить).

    ![Кнопка "Add New" (Добавить)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Пользователь")

6. Из списка **Identity Provider** (Поставщик удостоверений) выберите поставщик удостоверений. В поле **User Identifier** (Идентификатор пользователя) введите адрес электронной почты учетной записи тестового пользователя Britta Simon.

    ![Поля "Identity Provider" (Поставщик удостоверений) и "User Identifier" (Идентификатор пользователя)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Пользователь")
    
7. Щелкните **Сохранить**.

    ![Кнопка "Save" (Сохранить)](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Пользователь")

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить тестовому пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Cezanne HR Software.

![Проверка доступа пользователей][200] 

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Ссылка "Все приложения"][201] 

2. В списке приложений выберите **Cezanne HR Software**.

    ![Список "Приложения"](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Выберите **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][203]

5. В диалоговом окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** выберите **Назначить**.
    
### <a name="test-sso"></a>Проверка единого входа

В этом разделе вы с помощью панели доступа выполните проверку конфигурации единого входа Azure AD.

Щелкнув элемент "Cezanne HR Software" на панели доступа, вы автоматически войдете в приложение "Cezanne HR Software".

## <a name="next-steps"></a>Дальнейшие действия

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

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

