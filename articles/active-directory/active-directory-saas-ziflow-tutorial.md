---
title: Руководство по интеграции Azure Active Directory с приложением Ziflow | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 5256faab4d1c1dd44cf55f3aa1f6e57f324ccdef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Руководство. Интеграция Azure Active Directory с Ziflow

В этом руководстве описано, как интегрировать Ziflow с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Ziflow обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Ziflow.
- Вы можете включить автоматический вход пользователей в Ziflow (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Ziflow, вам потребуется:

- подписка Azure AD;
- подписка Ziflow с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Ziflow из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-ziflow-from-the-gallery"></a>Добавление Ziflow из коллекции
Чтобы настроить интеграцию Ziflow с Azure AD, необходимо добавить Ziflow из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Ziflow из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Ziflow**, выберите **Ziflow** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Ziflow в списке результатов](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в Ziflow с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Ziflow соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Ziflow.

Чтобы настроить и проверить единый вход Azure AD в Ziflow, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Ziflow](#create-a-ziflow-test-user)** требуется для того, чтобы в Ziflow существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Ziflow.

**Чтобы настроить единый вход Azure AD в Ziflow, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Ziflow** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Ziflow** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`.

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Замените их на уникальные значения идентификатора и URL-адреса входа, которые описываются далее в этом руководстве. Свяжитесь с [группой поддержки Ziflow](mailto:support@ziflow.com), чтобы получить значение дочернего домена в URL-адресе для входа.
    
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-ziflow-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Ziflow** щелкните **Настроить Ziflow**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_configure.png) 

7. В другом окне браузера войдите в приложение Ziflow с правами администратора безопасности.


8. Щелкните аватар в правом верхнем углу, а затем выберите **Manage account** (Управление учетной записью).

    ![Управление конфигурацией Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_manage.png)

9. В верхнем левом углу щелкните **Single Sign-On** (Единый вход).

    ![Конфигурация входа Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_signon.png)

10. На странице **Single Sign-On** (Единый вход) выполните следующие действия:

    ![Одна конфигурация Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_page.png)

    a. Выберите в качестве **типа** значение **SAML2.0**.

    В текстовое поле **Sign-In URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. Отправьте сертификат в кодировке Base64, который был скачан с портала Azure, в поле **X509 Signing Certificate** (Сертификат для подписи X509).

    d. В текстовое поле **Sign Out URL** (URL-адрес выхода) вставьте **URL-адрес выхода**, скопированный на портале Azure.

    д. В разделе **Configuration Settings for your Identifier Provider** (Параметры конфигурации для поставщика удостоверений) скопируйте выделенное значение уникального идентификатора и добавьте его к идентификатору и URL-адрес для входа в разделе **Домены и URL-адреса приложения Ziflow** на портале Azure.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-ziflow-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-ziflow-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-ziflow-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-ziflow-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-ziflow-test-user"></a>Создание тестового пользователя Ziflow

Чтобы пользователи Azure AD могли выполнять вход в Ziflow, они должны быть подготовлены в Ziflow. В Ziflow подготовка выполняется вручную.

Чтобы подготовить учетную запись пользователя, выполните следующие действия.

1. Войдите в Ziflow с правами администратора безопасности.

2. Щелкните **People** (Пользователи) в верхней части страницы.

    ![Конфигурация пользователей Ziflow](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_people.png)

3. Щелкните **Add** (Добавить), а затем — **Add user** (Добавить пользователя).

    ![Настройка Ziflow: добавление пользователей](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_add.png)

4. Во всплывающем меню **Add a user** (Добавление пользователя) сделайте следующее:

    ![Настройка Ziflow: добавление пользователей](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. В текстовое поле **E-mail** (Адрес электронной почты) введите адрес электронной почты пользователя, например brittasimon@contoso.com.

    Б. В текстовое поле **First Name** (Имя) введите имя пользователя, например Britta.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    d. Укажите свою роль Ziflow.

    д. Нажмите кнопку **Add 1 user** (Добавить 1 пользователя).

    > [!NOTE]
    > Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Ziflow.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении Ziflow, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Ziflow**.

    ![Ссылка на Ziflow в списке "Приложения"](./media/active-directory-saas-ziflow-tutorial/tutorial_ziflow_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Ziflow на панели доступа, вы автоматически войдете в приложение Ziflow.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ziflow-tutorial/tutorial_general_203.png

