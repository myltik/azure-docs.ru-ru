---
title: Руководство по интеграции Azure Active Directory с LockPath Keylight | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 5eb3aa008ccfa5711d8282f6d98df44239d236a4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345917"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Руководство по интеграции Azure Active Directory с LockPath Keylight

В этом руководстве описано, как интегрировать LockPath Keylight с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением LockPath Keylight обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LockPath Keylight.
- Вы можете включить автоматический вход пользователей в LockPath Keylight (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с LockPath Keylight, вам потребуется:

- подписка Azure AD;
- подписка LockPath Keylight с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LockPath Keylight из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Добавление LockPath Keylight из коллекции
Чтобы настроить интеграцию LockPath Keylight с Azure AD, необходимо добавить LockPath Keylight из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LockPath Keylight из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **LockPath Keylight**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. На панели результатов выберите **LockPath Keylight** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в LockPath Keylight с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в LockPath Keylight соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LockPath Keylight.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LockPath Keylight.

Чтобы настроить и проверить единый вход Azure AD в LockPath Keylight, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LockPath Keylight](#creating-a-lockpath-keylight-test-user)** требуется для того, чтобы в LockPath Keylight существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LockPath Keylight.

**Чтобы настроить единый вход Azure AD в LockPath Keylight, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **LockPath Keylight** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. В разделе **Домены и URL-адреса приложения LockPath Keylight** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.keylightgrc.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.keylightgrc.com`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.keylightgrc.com/Login.aspx`.
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов LockPath Keylight](https://www.lockpath.com/contact/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютер.

    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. В разделе **Конфигурация LockPath Keylight** щелкните **Настроить LockPath Keylight**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Чтобы включить единый вход в LockPath Keylight, выполните следующие действия.
   
    a. Войдите в учетную запись LockPath Keylight в качестве администратора.
    
    Б. В меню вверху щелкните **Person** (Пользователь) и выберите **Keylight Setup** (Настройка Keylight).
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. В представлении в виде дерева слева щелкните **SAML**.
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. В диалоговом окне **SAML Settings** (Параметры SAML) нажмите кнопку **Edit** (Изменить).
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/404.png) 

8. На странице диалогового окна **Изменение параметров SAML** выполните следующие действия:
   
    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Задайте для параметра **SAML authentication** (Аутентификация SAML) значение **Active** (Активно).

    Б. Вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure, в текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений).

    c. Вставьте значение **URL-адрес службы единого выхода**, скопированное на портале Azure, в текстовое поле **Identity Provider Logout URL** (URL-адрес выхода поставщика удостоверений).

    d. Щелкните **Choose File** (Выбрать файл), чтобы выбрать скачанный сертификат LockPath Keylight, а затем нажмите кнопку **Open** (Открыть), чтобы передать этот сертификат.

    д. В поле **SAML User Id location** (Расположение идентификатора пользователя SAML) выберите значение **NameIdentifier element of the subject statement** (Элемент NameIdentifier оператора Subject).
    
    f. Введите значение **Keylight Service Provider** (Поставщик услуг Keylight), используя следующий формат: **https://&lt;название_компании&gt;.keylightgrc.com**.
    
    ж. Задайте для параметра **Auto-provision users** (Автоматическая подготовка пользователей) значение **Active** (Активно).

    h. Задайте для параметра **Auto-provision account type** (Тип учетной записи для автоматической подготовки) значение **Full User** (С полным доступом).

    i. Задайте для параметра **Auto-provision security role** (Роль безопасности для автоматической подготовки) значение **Standard User with SAML** (Права обычного пользователя с SAML).
    
    j. Задайте для параметра **Auto-provision security config** (Конфигурация безопасности для автоматической подготовки) значение **Standard User Configuration** (Конфигурация обычного пользователя).
     
    k. В текстовое поле **Email Attribute** (Атрибут электронной почты) введите `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. В текстовое поле **First name attribute** (Атрибут имени) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. В текстовое поле **Last name attribute** (Атрибут фамилии) введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Создание тестового пользователя LockPath Keylight

В этом разделе описано, как создать пользователя Britta Simon в приложении LockPath Keylight. LockPath Keylight поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь не существует, то он создается при доступе к LockPath Keylight. 

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться к [группе поддержки клиентов LockPath Keylight](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к LockPath Keylight.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LockPath Keylight, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **LockPath Keylight**.

    ![Настройка единого входа](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "LockPath Keylight" на панели доступа, вы автоматически войдете в приложение LockPath Keylight. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

