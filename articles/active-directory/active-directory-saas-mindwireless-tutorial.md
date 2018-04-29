---
title: Руководство. Интеграция Azure Active Directory с mindWireless | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 1c08b0fe24e2545a07e3335cc0e0ae87a2c0540c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Руководство. Интеграция Azure Active Directory с mindWireless

В этом руководстве описано, как интегрировать mindWireless с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением mindWireless обеспечивает следующие преимущества:

- C помощью Azure AD вы можете контролировать доступ к mindWireless.
- Вы можете включить автоматический вход пользователей в mindWireless (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с mindWireless, вам потребуется:

- подписка Azure AD;
- подписка mindWireless с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление mindWireless из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mindwireless-from-the-gallery"></a>Добавление mindWireless из коллекции
Чтобы настроить интеграцию mindWireless с Azure AD, необходимо добавить mindWireless из коллекции в список управляемых приложений SaaS.

**Чтобы добавить mindWireless из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **mindWireless**, выберите **mindWireless** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![mindWireless в списке результатов](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описаны настройка и проверка единого входа Azure AD в mindWireless с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в mindWireless соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в mindWireless.

Чтобы настроить и проверить единый вход Azure AD в mindWireless, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя mindWireless](#create-a-mindwireless-test-user)** требуется для того, чтобы в mindWireless существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении mindWireless.

**Чтобы настроить единый вход Azure AD в mindWireless, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **mindWireless** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

3. В разделе **Домены и URL-адреса приложения mindWireless** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения mindWireless](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.mwsmart.com/`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки mindWireless](mailto:sdulloor@mindwireless.com).

4. Приложение mindWireless ожидает проверочных утверждений SAML в определенном формате, поэтому следует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML.

5. На следующем снимке экрана приведен пример. Утверждение всегда будет носить имя **Employee ID** и иметь значение, сопоставленное с параметром user.employeeid, который содержит идентификатор сотрудника (EmployeeID) для этого пользователя. Здесь выполняется сопоставление пользователя из Azure AD с mindWireless по значению EmployeeID, однако его можно сопоставить с другим значением, учитывая параметры приложения. Вместе со [службой поддержки mindWireless](mailto:sdulloor@mindwireless.com) выполните действия по использованию правильного идентификатора пользователя и его сопоставлению с утверждением **Employee ID**.

    ![Настройка единого входа](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута | Значение пространства имен |
    | -------------- | --------------- | ----------------|
    | Код сотрудника | user.employeeid | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/active-directory-saas-mindwireless-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. В текстовом поле **Пространство имен** введите значение пространства имен, показанное для этой строки.
    
    д. Нажмите кнопку **ОК**.
    
7. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-mindwireless-tutorial/tutorial_general_400.png)

9. В разделе **Настройка mindWireless** щелкните **Настроить mindWireless**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка mindWireless](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_configure.png) 

10. Чтобы настроить единый вход на стороне **mindWireless**, нужно отправить скачанный **сертификат в кодировке Base64, URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** в [службу поддержки mindWireless](mailto:sdulloor@mindwireless.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-mindwireless-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-mindwireless-test-user"></a>Создание тестового пользователя mindWireless

В этом разделе описано, как создать пользователя Britta Simon в приложении mindWireless. Обратитесь в [службу поддержки mindWireless](mailto:sdulloor@mindwireless.com), чтобы добавить пользователей на платформу mindWireless. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к mindWireless.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению mindWireless, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **mindWireless**.

    ![Ссылка на mindWireless в списке приложений](./media/active-directory-saas-mindwireless-tutorial/tutorial_mindwireless_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку mindWireless на панели доступа, вы автоматически войдете в приложение mindWireless.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mindwireless-tutorial/tutorial_general_203.png

