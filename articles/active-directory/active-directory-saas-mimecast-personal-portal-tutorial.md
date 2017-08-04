---
title: "Руководство по интеграции Azure Active Directory с Mimecast Personal Portal | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Mimecast Personal Portal."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: bf46da35a55608d7e4656c9dd3ad9d5f2253e225
ms.contentlocale: ru-ru
ms.lasthandoff: 07/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Руководство по интеграции Azure Active Directory с Mimecast Personal Portal

В этом руководстве описано, как интегрировать Mimecast Personal Portal с Azure Active Directory (Azure AD).

Интеграция Azure AD с Mimecast Personal Portal обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Mimecast Personal Portal.
- Вы можете включить автоматический вход пользователей в Mimecast Personal Portal (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Mimecast Personal Portal, вам потребуется:

- подписка Azure AD;
- Подписка на личный портал Mimecast с поддержкой единого входа

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Mimecast Personal Portal из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Добавление Mimecast Personal Portal из коллекции
Чтобы настроить интеграцию Mimecast Personal Portal с Azure AD, необходимо добавить Mimecast Personal Portal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mimecast Personal Portal из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Mimecast Personal Portal**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_search.png)

5. На панели результатов выберите **Mimecast Personal Portal** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Mimecast Personal Portal с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Mimecast Personal Portal соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mimecast Personal Portal.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Mimecast Personal Portal.

Чтобы настроить и проверить единый вход Azure AD в Mimecast Personal Portal, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Mimecast Personal Portal](#creating-a-mimecast-personal-portal-test-user)** требуется для того, чтобы в Mimecast Personal Portal существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Mimecast Personal Portal.

**Чтобы настроить единый вход Azure AD в Mimecast Personal Portal, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Mimecast Personal Portal** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Mimecast Personal Portal** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    | |     
    | ----------------------------------------|
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|
    | |
   
    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | |     
    | --- |
    | `https://webmail-us.mimecast.com/sso/<companyname>`|
    | `https://webmail-uk.mimecast.com/sso/<companyname>`|    
    | `https://webmail-za.mimecast.com/sso/<companyname>`|
    | `https://webmail.mimecast-offshore.com/sso/<companyname>`|
    ||                                                 
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Mimecast Personal Portal](https://www.mimecast.com/customer-success/technical-support/). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Mimecast Personal Portal** щелкните **Настроить Mimecast Personal Portal**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. В другом окне веб-браузера войдите в личный портал Mimecast в качестве администратора.

8. Выберите **Services \> Application** ("Службы" > "Приложение").
   
    ![Приложения](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "Приложения")

9. Щелкните **Профили проверки подлинности**.
   
    ![Authentication Profiles](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles") (Профили аутентификации)

10. Щелкните **Новый профиль проверки подлинности**.
   
    ![Создание профиля аутентификации](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "New Authentication Profile")

11. В разделе **Профиль проверки подлинности** сделайте следующее:
   
    ![Authentication Profile](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile") (Профиль аутентификации)
   
    а. В текстовом поле **Описание** введите имя конфигурации.
   
    b. Выберите **Обязательное использование проверки подлинности SAML для личного портала Mimecast**.
   
    c. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   
    г) В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
   
    д. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    f. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    g. Откройте в Блокноте сертификат в кодировке **Base64**, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).

    h. Установите флажок **Разрешить единый вход**.
   
    i. Щелкните **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-mimecast-personal-portal-test-user"></a>Создание тестового пользователя Mimecast Personal Portal

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
   
    а. В текстовое поле **Email Address** (Адрес электронной почты) введите **адрес электронной почты** пользователя, например **BrittaSimon@contoso.com**.
    
    b. В текстовое поле **Global Name** (Глобальное имя) введите **имя пользователя** **BrittaSimon**.

    c. В текстовые поля **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) введите **пароль** пользователя.
   
    b. Щелкните **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Mimecast Personal Portal или API, предоставляемые Mimecast Personal Portal для подготовки учетных записей пользователя Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mimecast Personal Portal.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Mimecast Personal Portal, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Mimecast Personal Portal**.

    ![Настройка единого входа](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Mimecast Personal Portal" на панели доступа, вы автоматически войдете в приложение Mimecast Personal Portal. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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


