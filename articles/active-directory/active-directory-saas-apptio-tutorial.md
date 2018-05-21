---
title: Руководство по интеграции Azure Active Directory с Apptio | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении Apptio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b23eba91-7698-47e7-ae75-0ceafd739965
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/6/2017
ms.author: jeedes
ms.openlocfilehash: 340b8aa4e99601fd31dccb13a4f69fe4e7adf440
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-apptio"></a>Руководство по интеграции Azure Active Directory с Apptio

В этом руководстве описано, как интегрировать Apptio с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Apptio обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Apptio.
- Вы можете включить автоматический вход пользователей в Apptio (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Apptio, вам потребуется:

- подписка Azure AD;
- подписка Apptio с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Apptio из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-apptio-from-the-gallery"></a>Добавление Apptio из коллекции.
Чтобы настроить интеграцию Apptio с Azure AD, необходимо добавить Apptio из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Apptio из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Apptio**, выберите **Apptio** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Apptio в списке результатов](./media/active-directory-saas-apptio-tutorial/tutorial_apptio_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в приложение Apptio с использованием тестового пользователя Britta Simon.

Чтобы активировать единый вход, Azure AD необходима информация о том, какой пользователь в Apptio соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Apptio.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Apptio.

Чтобы настроить и проверить единый вход Azure AD в Apptio, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Apptio](#create-an-apptio-test-user)** нужно для того, чтобы в Apptio существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в своем приложении Apptio.

**Чтобы настроить единый вход Azure AD в Apptio, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Apptio** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-apptio-tutorial/tutorial_apptio_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе **Домены и URL-адреса приложения Apptio** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Apptio](./media/active-directory-saas-apptio-tutorial/tutorial_apptio_url.png)

    В текстовом поле **Идентификатор** введите значение `urn:federation:apptio`.

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-apptio-tutorial/tutorial_apptio_certificate.png) 

5. Приложение Apptio Software ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. Обратите внимание, что **идентификатор пользователя** сопоставляется с **user.mail**. Вам необходимо настроить еще три других атрибута — **полное имя, электронная почта и роль**. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-apptio-tutorial/tutorial_apptio_attributes.png)       
    
    > [!NOTE]
    > Перейдите по [этой ссылке](http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/), чтобы прочитать о настройке **роли** в Azure AD.
    
6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.

    | Имя атрибута | Значение атрибута |
    | -------------- | -------------------- |    
    | fullname       | user.displayname |
    | mail           | user.mail |
    | role           | user.assignedrole |
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-apptio-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-apptio-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-apptio-tutorial/tutorial_general_400.png)
    
9. Чтобы настроить единый вход на стороне **Apptio**, отправьте скачанный **XML-файл метаданных** в [службу поддержки Apptio](https://www.apptio.com/about/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-apptio-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-apptio-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-apptio-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-apptio-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-apptio-test-user"></a>Создание тестового пользователя в Apptio

В этом разделе описано, как создать пользователя Britta Simon в приложении Apptio. Обратитесь в [службу поддержки Apptio](https://www.apptio.com/about/contact), чтобы добавить пользователей на платформу Apptio. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Apptio, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Apptio, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Apptio**.

    ![Ссылка на Apptio в списке приложений](./media/active-directory-saas-apptio-tutorial/tutorial_apptio_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "Apptio" на панели доступа, вы автоматически войдете в приложение Apptio.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-apptio-tutorial/tutorial_general_203.png

