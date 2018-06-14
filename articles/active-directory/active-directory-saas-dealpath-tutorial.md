---
title: Руководство по интеграции Azure Active Directory с Dealpath | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Dealpath.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 51ace608-5a4f-48c0-9446-d9f86ad2e890
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: jeedes
ms.openlocfilehash: 2df638c8c75e07b9df9ace9fc420f0d36920bf8f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341932"
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Руководство по интеграции Azure Active Directory с Dealpath

В этом руководстве описано, как интегрировать Dealpath с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Dealpath обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Dealpath.
- Вы можете включить автоматический вход пользователей в Dealpath (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Dealpath, вам потребуется:

- подписка Azure AD;
- подписка Dealpath с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Dealpath из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-dealpath-from-the-gallery"></a>Добавление Dealpath из коллекции
Чтобы настроить интеграцию Dealpath с Azure AD, необходимо добавить Dealpath из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Dealpath из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Dealpath**, выберите **Dealpath** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Dealpath в списке результатов](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Dealpath с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Dealpath соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Dealpath.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Dealpath.

Чтобы настроить и проверить единый вход Azure AD в Dealpath, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Dealpath](#create-a-dealpath-test-user)** требуется для того, чтобы в Dealpath существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Dealpath.

**Чтобы настроить единый вход Azure AD в Dealpath, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Dealpath** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Dealpath** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://app.dealpath.com/account/login`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE] 
    > Значение идентификатора приведено для примера и не является реальным. Вместо него нужно указать фактический идентификатор. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Dealpath](mailto:kenter@dealpath.com). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-dealpath-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Dealpath** щелкните **Настройка Dealpath**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_configure.png) 

7. В другом окне веб-браузера войдите в приложение Dealpath с правами администратора.

8. В правом верхнем углу щелкните **Admin Tools** (Средства администрирования) и перейдите на страницу **Integrations** (Интеграции), затем в разделе **SAML 2.0 Authentication** (Аутентификация SAML 2.0) щелкните **Update Settings** (Изменить параметры).

    ![Конфигурация Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_admin.png)

9. На странице **Set up SAML 2.0 authentication** (Настройка аутентификации SAML 2.0) выполните указанные ниже действия.

    ![Конфигурация Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. В текстовое поле **SAML SSO URL** (URL-адрес единого входа SAML) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    Б. В текстовое поле **Identity Provider Issuer** (Издатель поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    c. Скопируйте в блокнот содержимое скачанного файла **сертификата в формате Base64**, а затем вставьте его в текстовое поле **Public Certificate** (Общедоступный сертификат).

    d. Щелкните **Update settings** (Обновить параметры).


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-dealpath-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-dealpath-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-dealpath-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-dealpath-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-dealpath-test-user"></a>Создание тестового пользователя Dealpath

В этом разделе описано, как создать пользователя Britta Simon в приложении Dealpath. Обратитесь к [группе поддержки клиентов Dealpath](mailto:kenter@dealpath.com), чтобы добавить пользователей на платформу Dealpath. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Dealpath.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Dealpath, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Dealpath**.

    ![Ссылка на Dealpath в списке "Приложения"](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Dealpath" на панели доступа, вы автоматически войдете в приложение Dealpath.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_203.png

