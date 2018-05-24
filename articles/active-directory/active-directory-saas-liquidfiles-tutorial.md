---
title: Руководство по интеграции Azure Active Directory с LiquidFiles | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: c8c357c4a8d017699d83597766a93cbffeb5e8be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Руководство по интеграции Azure Active Directory с LiquidFiles

В этом руководстве описано, как интегрировать приложение LiquidFiles с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением LiquidFiles обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LiquidFiles.
- Вы можете включить автоматический вход пользователей в LiquidFiles (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с LiquidFiles, вам потребуется:

- подписка Azure AD;
- подписка LiquidFiles с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LiquidFiles из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-liquidfiles-from-the-gallery"></a>Добавление LiquidFiles из коллекции
Чтобы настроить интеграцию LiquidFiles с Azure AD, необходимо добавить LiquidFiles из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LiquidFiles из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **LiquidFiles**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_search.png)

5. На панели результатов выберите **LiquidFiles**, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в LiquidFiles с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в LiquidFiles соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LiquidFiles.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LiquidFiles.

Чтобы настроить и проверить единый вход Azure AD в LiquidFiles, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LiquidFiles](#creating-a-liquidfiles-test-user)** требуется для того, чтобы в LiquidFiles существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LiquidFiles.

**Чтобы настроить единый вход Azure AD в LiquidFiles, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **LiquidFiles** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_samlbase.png)

3. В разделе **Домены и URL-адреса приложения LiquidFiles** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<YOUR_SERVER_URL>/saml/init`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<YOUR_SERVER_URL>`

    c. Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<YOUR_SERVER_URL>/saml/consume`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов LiquidFiles](https://www.liquidfiles.com/support.html). 
 
4. В разделе **Сертификат подписи SAML** скопируйте значение **Отпечаток**.

    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация LiquidFiles** щелкните **Настроить LiquidFiles**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_configure.png)
 
7. Войдите на корпоративный сайт LiquidFiles в качестве администратора.

8. В меню **Admin > Configuration** (Администрирование > Конфигурация) щелкните **Single Sign-On** (Единый вход).

9. На странице **Single Sign-On Configuration** (Конфигурация единого входа) выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_single_01.png)

    a. Для параметра **Single Sign On Method** (Метод единого входа) выберите значение **SAML 2**.

    Б. В текстовое поле **IDP Login URL** (URL-адрес входа IdP) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. В текстовое поле **IDP Logout URL** (URL-адрес выхода IdP) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    d. В текстовое поле **IDP Cert Fingerprint** (Отпечаток сертификата IdP) вставьте значение **Отпечаток**, которое вы скопировали на портале Azure.

    д. В текстовое поле "Name Identifier Format" (Формат идентификатора имени) введите значение **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    f. В текстовое поле "Authn Context" (Контекст аутентификации) введите значение **urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport**.

    ж. Выберите команду **Сохранить**.  

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-liquidfiles-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-liquidfiles-test-user"></a>Создание тестового пользователя LiquidFiles

Цель этого раздела — создать пользователя с именем Britta Simon в LiquidFiles. Обратитесь к администратору сервера LiquidFiles, чтобы он добавил для вас пользователя, после чего выполните вход в приложение LiquidFiles.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к LiquidFiles.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LiquidFiles, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **LiquidFiles**.

    ![Настройка единого входа](./media/active-directory-saas-liquidfiles-tutorial/tutorial_liquidfiles_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "LiquidFiles" на панели доступа, вы автоматически войдете в приложение LiquidFiles.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-liquidfiles-tutorial/tutorial_general_203.png

