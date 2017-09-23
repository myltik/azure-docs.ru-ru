---
title: "Руководство. Интеграция Azure Active Directory с WORKS MOBILE | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в WORKS MOBILE."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 139a1968a59424eae278de3e7fa227ad340a1eb8
ms.contentlocale: ru-ru
ms.lasthandoff: 06/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-works-mobile"></a>Руководство. Интеграция Azure Active Directory с WORKS MOBILE

В этом руководстве описано, как интегрировать WORKS MOBILE с Azure Active Directory (Azure AD).

Интеграция WORKS MOBILE с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к WORKS MOBILE.
- Вы можете настроить автоматический вход пользователей в WORKS MOBILE (единый вход) под учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с WORKS MOBILE, вам потребуются:

- подписка Azure AD;
- подписка WORKS MOBILE с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление WORKS MOBILE из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-works-mobile-from-the-gallery"></a>Добавление WORKS MOBILE из коллекции
Чтобы настроить интеграцию WORKS MOBILE с Azure AD, необходимо добавить WORKS MOBILE из коллекции в список управляемых приложений SaaS.

**Чтобы добавить WORKS MOBILE из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **WORKS MOBILE**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_search.png)

5. На панели результатов выберите **WORKS MOBILE** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описывается настройка и проверка единого входа Azure AD в WORKS MOBILE с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход, Azure AD необходимо знать, какой пользователь в WORKS MOBILE соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в WORKS MOBILE.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения параметра **Имя пользователя** в WORKS MOBILE.

Чтобы настроить и проверить единый вход Azure AD в WORKS MOBILE, потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя WORKS MOBILE](#creating-a-works-mobile-test-user)** — требуется для создания в WORKS MOBILE копии пользователя Britta Simon, связанной с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описывается, как включить единый вход Azure AD на портале Azure и настроить единый вход в приложении WORKS MOBILE.

**Чтобы настроить единый вход Azure AD в WORKS MOBILE, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **WORKS MOBILE** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_samlbase.png)

3. В разделе **Домены и URL-адреса приложения WORKS MOBILE** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_url.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.worksmobile.com/jp/myservice`

    b. В текстовом поле **Идентификатор** введите значение `worksmobile.com`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес для входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютер.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_general_400.png)

6. В разделе **Настройка WORKS MOBILE** щелкните **Настроить WORKS MOBILE**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_configure.png) 

7. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com) и предоставьте следующие сведения: 

    • скачанный **файл сертификата**;

    • **URL-адрес службы единого входа SAML**;

    • **идентификатор сущности SAML**;

    • **URL-адрес выхода**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-worksmobile-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-works-mobile-test-user"></a>Создание тестового пользователя WORKS MOBILE

 В этом разделе описано, как создать пользователя Britta Simon в приложении WORKS MOBILE. Обратитесь в [службу поддержки WORKS MOBILE](mailto:dl_ssoinfo@worksmobile.com), чтобы добавить пользователей на платформу WORKS MOBILE.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к WORKS MOBILE.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в WORKS MOBILE, выполните следующие действия**.

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **WORKS MOBILE**.

    ![Настройка единого входа](./media/active-directory-saas-worksmobile-tutorial/tutorial_worksmobile_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент WORKS MOBILE на панели доступа, вы автоматически войдете в приложение WORKS MOBILE.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-worksmobile-tutorial/tutorial_general_203.png


