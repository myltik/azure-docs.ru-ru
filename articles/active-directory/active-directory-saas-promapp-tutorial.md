---
title: Руководство по интеграции Azure Active Directory с Promapp | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Promapp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: 02deefa82abc7d776e64de7a5a78c46b971f9ee5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352503"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Руководство по интеграции Azure Active Directory с Promapp

В этом руководстве описано, как интегрировать Promapp с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Promapp обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Promapp.
- Вы можете включить автоматический вход пользователей в Promapp (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Promapp, вам потребуется:

- подписка Azure AD;
- подписка Promapp с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Promapp из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-promapp-from-the-gallery"></a>Добавление Promapp из коллекции
Чтобы настроить интеграцию Promapp с Azure AD, необходимо добавить Promapp из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Promapp из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Promapp**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_search.png)

5. На панели результатов выберите **Promapp** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Promapp с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Promapp соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Promapp.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Promapp.

Чтобы настроить и проверить единый вход Azure AD в Promapp, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Promapp](#creating-a-promapp-test-user)** требуется для того, чтобы в Promapp существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Promapp.

**Чтобы настроить единый вход Azure AD в Promapp, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Promapp** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе**Домены и URL-адреса приложения Promapp** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://go.promapp.com/TENANTNAME/`|
    | `https://au.promapp.com/TENANTNAME/`|
    | `https://us.promapp.com/TENANTNAME/`|
    | `https://eu.promapp.com/TENANTNAME/`|
    | `https://ca.promapp.com/TENANTNAME/`|
    
    > [!NOTE] 
    > Сейчас интеграция Azure AD с Promapp настроена только для аутентификации, инициированной службой. Например, при переходе по URL-адресу Promapp инициируется процесс аутентификации. Но поле "URL-адрес ответа" является обязательным.
    
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Укажите вместо них фактические значения URL-адреса для входа, идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Promapp](https://www.promapp.com/about-us/contact-us/).

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация Promapp** щелкните **Настроить Promapp**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_configure.png) 

8. Войдите на корпоративный сайт Promapp с правами администратора. 

9. В верхнем меню щелкните **Администратор**. 
   
    ![единого входа Azure AD][12]

10. Нажмите **Настроить**. 
   
    ![единого входа Azure AD][13]

11. В диалоговом окне **Security** (Безопасность) сделайте следующее.
   
    ![единого входа Azure AD][14]
    
    a. В текстовое поле **IdP Login URL** (URL-адрес входа IdP) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
    
    Б. В поле **SSO - Single Sign-on Mode** (SSO — режим единого входа) выберите **Optional** (Необязательно), а затем нажмите кнопку **Save** (Сохранить).

    > [!NOTE]
    > Режим **Optional** (Необязательно) предназначен только для тестирования. Настроив конфигурацию, выберите режим **Required** (Обязательно), чтобы все пользователи проходили проверку подлинности с помощью Azure AD.

    c. Откройте скачанный сертификат в блокноте, скопируйте содержимое сертификата без первой строки (-----**BEGIN CERTIFICATE**-----) и последней строки (-----**END CERTIFICATE**-----), вставьте его в текстовое поле **SSO-x.509 Certificate** (Сертификат единого входа x.509) и нажмите кнопку **Save** (Сохранить).
        
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-promapp-test-user"></a>Создание тестового пользователя Promapp

Приложение Promapp поддерживает JIT-подготовку. Это значит, что учетная запись пользователя при необходимости создается автоматически во время попытки доступа к приложению с помощью панели доступа.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Promapp.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Promapp, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Promapp**.

    ![Настройка единого входа](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Чтобы протестировать приложение в режиме, инициированном **поставщиком услуг**, вам потребуется инициировать проверку подлинности на сайте Promapp. Для этого перейдите в режим **Дополнительно** и нажмите кнопку единого входа на странице входа.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png

