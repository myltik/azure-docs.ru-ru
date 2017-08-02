---
title: "Учебник. Интеграция Azure Active Directory с Cezanne HR Software | Документация Майкрософт"
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
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 534cd2f3879937a841b7d1d15c106480e9b45655
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Руководство. Интеграция Azure Active Directory с Cezanne HR Software

В этом руководстве описано, как интегрировать Cezanne HR Software с Azure Active Directory (Azure AD).

Интеграция Cezanne HR Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Cezanne HR Software.
- Вы можете включить автоматический вход пользователей в Cezanne HR Software (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Cezanne HR Software, вам потребуется следующее:

- подписка Azure AD;
- подписка на Cezanne HR Software с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Cezanne HR Software из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Добавление Cezanne HR Software из коллекции.
Чтобы настроить интеграцию Cezanne HR Software с Azure AD, вам потребуется добавить Cezanne HR Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cezanne HR Software из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Cezanne HR Software**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. В области результатов выберите **Cezanne HR Software** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Cezanne HR Software с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Cezanne HR Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cezanne HR Software.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Cezanne HR Software.

Чтобы настроить и проверить единый вход в Azure AD в Cezanne HR Software, вам потребуется выполнить действия в следующих блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Cezanne HR Software](#creating-a-cezanne-hr-software-test-user)** требуется для создания пользователя Britta Simon в Cezanne HR Software, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Cezanne HR Software.

**Чтобы настроить единый вход Azure AD в Cezanne HR Software, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Cezanne HR Software** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Cezanne HR Software** выполните указанные ниже действия.

    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://w3.cezanneondemand.com:443/<tenantid>`.    
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Cezanne HR Software](mailto:info@cezannehr.com).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. В разделе **Cezanne HR Software Configuration** (Конфигурация Cezanne HR Software) щелкните **Configure Cezanne HR Software** (Настройка Cezanne HR Software), чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. В другом окне веб-браузера войдите на веб-сайт Cezanne HR Software своей компании в качестве администратора.

8. В левой панели навигации щелкните **System Setup**(Настройка системы). Выберите пункт **Параметры безопасности**. Далее перейдите к пункту **Конфигурация единого входа**.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. На панели **Allow users to log in using the following Single Sign-On (SSO) Service** (Разрешить пользователям вход с использованием следующей службы единого входа) установите флажок **SAML 2.0** и выберите параметр **Расширенная конфигурация**.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. Нажмите кнопку **Добавить** .

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. В разделе **SAML 2.0 Identity providers** (Поставщики удостоверений SAML) выполните следующие действия.

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    а. Введите имя поставщика удостоверений в поле **Отображаемое имя**.

    b. В текстовое поле **Entity Identifier** (Идентификатор сущности) вставьте значение **Идентификатор сущности SAML**, скопированное на портале Azure. 

    c. В поле **SAML Binding** (Привязка SAML) укажите значение POST.

    в. Вставьте **URL-адрес службы единого входа SAML**, который вы скопировали с портала Azure, в текстовое поле **Security Token Service Endpoint** (Конечная точка службы токенов безопасности). 
    
    д. В текстовом поле "User ID Attribute Name" (Имя атрибута идентификатора пользователя) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Чтобы отправить сертификат, скачанный с Azure AD, нажмите кнопку **Upload** (Отправить).
    
    g. Нажмите кнопку **ОК** . 

12. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-cezanne-hr-software-test-user"></a>Создание тестового пользователя Cezanne HR Software

Чтобы разрешить пользователям Azure AD вход в Cezanne HR Software, их необходимо подготовить для Cezanne HR Software. В случае с Cezanne HR Software подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1.  Войдите на корпоративный сайт Cezanne HR Software в качестве администратора.

2.  В левой панели навигации щелкните **System Setup**(Настройка системы). Перейдите в раздел **Управление пользователями**. Выберите **Добавить нового пользователя**.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Новый пользователь")

3.  В разделе **Person Details** (Личные данные) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Новый пользователь")
    
    а. Установите для параметра **Внутренний пользователь** значение "Выкл.".
    
    b. В текстовом поле **First name** (Имя) введите имя пользователя, например **Britta**.  
 
    c. В текстовом поле **Last name** (Фамилия) введите фамилию, например **Simon**.
    
    г) В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

4.  В разделе **Account Information** (Сведения об учетной записи) выполните следующие действия.

    ![Новый пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Новый пользователь")
    
    а. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Brittasimon@contoso.com.
    
    b. В текстовом поле **Password** (Пароль) введите пароль пользователя.
    
    c. В раскрывающемся списке **Роль безопасности** выберите **Специалист отдела кадров**.
    
    г) Нажмите кнопку **ОК**.

5. Перейдите на вкладку **Единый вход** и нажмите кнопку **Добавить** в области **Идентификаторы SAML 2.0)**.

    ![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Пользователь")

6. Выберите значение в списке **Поставщик удостоверений** и в текстовом поле **Идентификатор пользователя** введите адрес электронной почты учетной записи Britta Simon.

    ![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Пользователь")
    
7. Нажмите кнопку **Сохранить** .

    ![Пользователь](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Пользователь")

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Cezanne HR Software.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в Cezanne HR Software, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Cezanne HR Software**.

    ![Настройка единого входа](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Cezanne HR Software на панели доступа, вы автоматически войдете в приложение Cezanne HR Software.

## <a name="additional-resources"></a>Дополнительные ресурсы

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


