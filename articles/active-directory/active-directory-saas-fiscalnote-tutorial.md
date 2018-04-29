---
title: Руководство по интеграции Azure Active Directory с FiscalNote | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и FiscalNote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 55274f26-be7e-4514-964c-7186ecb55c4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 2e0d0f2d6fc428dbeb20ce5e056a8d6ed9a0ac97
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fiscalnote"></a>Руководство. Интеграция Azure Active Directory с FiscalNote

В этом руководстве описано, как интегрировать FiscalNote с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением FiscalNote обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к FiscalNote.
- Вы можете включить автоматический вход пользователей в FiscalNote (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с FiscalNote, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа FiscalNote.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление FiscalNote из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-fiscalnote-from-the-gallery"></a>Добавление FiscalNote из коллекции
Чтобы настроить интеграцию FiscalNote с Azure AD, необходимо добавить FiscalNote из коллекции в список управляемых приложений SaaS.

**Чтобы добавить FiscalNote из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **FiscalNote** , выберите **FiscalNote**  на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![FiscalNote в списке результатов](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в FiscalNote с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в FiscalNote соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в FiscalNote.

Чтобы настроить и проверить единый вход Azure AD в FiscalNote, потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения FiscalNote](#create-a-fiscalnote-test-user)** требуется для того, чтобы в FiscalNote существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении FiscalNote.

**Чтобы настроить единый вход Azure AD в FiscalNote, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **FiscalNote** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_samlbase.png)

3. В разделе **Домены и URL-адреса приложения FiscalNote** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения FiscalNote](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_url.png)
    
    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<InstanceName>.fiscalnote.com/login?client=<ClientID>&redirect_uri=https://app.fiscalnote.com/saml-login.html&audience=https://api.fiscalnote.com/&connection=<CONNECTION_NAME>&response_type=id_token%20token`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `urn:auth0:fiscalnote:<CONNECTIONNAME>`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки FiscalNote](mailto:support@fiscalnote.com).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютер.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_certificate.png)

5. Приложение FiscalNote ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения.

    ![Настройка единого входа](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
           
    | Имя атрибута | Значение атрибута |
    | ---------------| ----------------|
    | name | user.userprincipalname|
    | givenName| user.givenname|
    | familyName| user.surname|
    | email| user.mail|
    
    a. Удалите имеющиеся атрибуты и добавьте новые. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/active-directory-saas-fiscalnote-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пространство имен пустым.
    
    д. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_400.png)

8. В разделе **Настройка FiscalNote** щелкните **Настроить FiscalNote**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка FiscalNote](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_configure.png) 

9. Чтобы настроить единый вход на стороне **FiscalNote**, нужно отправить скачанный **сертификат (необработанный), URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [группу поддержки FiscalNote](mailto:support@fiscalnote.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-fiscalnote-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-fiscalnote-test-user"></a>Создание тестового пользователя FiscalNote

Цель этого раздела — создать пользователя с именем Britta Simon в FiscalNote. Приложение FiscalNote поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь создается при попытке получить доступ к приложению FiscalNote (если он еще не существует).
>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки FiscalNote](mailto:support@fiscalnote.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к FiscalNote, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в FiscalNote, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **FiscalNote**.

    ![Ссылка на FiscalNote в списке "Приложения"](./media/active-directory-saas-fiscalnote-tutorial/tutorial_fiscalnote_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку FiscalNote на панели доступа, вы автоматически войдете в приложение FiscalNote.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fiscalnote-tutorial/tutorial_general_203.png

