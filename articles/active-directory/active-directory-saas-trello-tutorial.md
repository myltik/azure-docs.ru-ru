---
title: Руководство по интеграции Azure Active Directory с Trello | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: dfdbef1138c166beca0a470d2e55dd24703d237c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352122"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Руководство. Интеграция Azure Active Directory с Trello

В этом руководстве описано, как интегрировать приложение Trello с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Trello обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Trello.
- Вы можете включить автоматический вход пользователей в Trello (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Trello, вам потребуется:

- подписка Azure AD;
- подписка на Trello с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Trello из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-trello-from-the-gallery"></a>Добавление Trello из коллекции
Чтобы настроить интеграцию Trello с Azure AD, необходимо добавить Trello из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Trello из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Trello**, выберите **Trello** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Trello в списке результатов](./media/active-directory-saas-trello-tutorial/tutorial_trello_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Trello с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Trello соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Trello.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Trello.

Чтобы настроить и проверить единый вход Azure AD в Trello, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Trello](#create-a-trello-test-user)** требуется для того, чтобы в Trello существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Trello.

>[!NOTE]
>Необходимо получить поле динамических данных **\<enterprise\>** из Trello. При отсутствии значения поля динамических данных обратитесь к [группе поддержки Trello](mailto:support@trello.com), чтобы получить его для своего предприятия.
    > 

**Чтобы настроить единый вход Azure AD в Trello, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Trello** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-trello-tutorial/tutorial_trello_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициируемом IdP**, то в разделе **Домены и URL-адреса приложения Trello** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_url.png)
    
    a. В текстовом поле **Идентификатор** введите URL-адрес `https://trello.com/auth/saml/metadata`.
    
    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://trello.com/auth/saml/consume/<enterprise>`.

4. Если вы хотите настроить приложение в **режиме, инициируемом поставщиком услуг**, выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_url1.png)

    a. Установите флажок **Показать дополнительные параметры URL-адресов**,

    Б. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://trello.com/auth/saml/login/<enterprise>` 

5. Приложение Trello ожидает, что утверждения SAML должны содержать определенные атрибуты. Настройте приведенные ниже атрибуты для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Атрибуты пользователя** приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_attribute.png)

6. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:
 
    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_attribute_05.png)

    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки. 

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**. 

7. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-trello-tutorial/tutorial_trello_certificate.png) 

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-trello-tutorial/tutorial_general_400.png)
    
9. В разделе **Конфигурация Trello** щелкните **Настроить Trello**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Trello](./media/active-directory-saas-trello-tutorial/tutorial_trello_configure.png) 

10. Чтобы настроить единый вход на стороне **Trello**, откройте страницу [конфигурации единого входа для Trello Enterprise](https://trello.com/sso-configuration) и отправьте скачанный **сертификат (Base64)**, а также **URL-адрес службы единого входа SAML** в [службу поддержки Trello](mailto:support@trello.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-trello-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-trello-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-trello-test-user"></a>Создание тестового пользователя приложения Trello

В этом разделе в Trello создается пользователь с именем Britta Simon. Приложение Trello поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению Trello создается учетная запись пользователя (если она еще не создана).

>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу поддержки Trello](mailto:support@trello.com).


### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Trello.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Trello, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Trello**.

    ![Ссылка на Trello в списке приложений](./media/active-directory-saas-trello-tutorial/tutorial_trello_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Trello на панели доступа, вы автоматически войдете в приложение Trello.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-trello-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trello-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trello-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trello-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trello-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trello-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trello-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trello-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trello-tutorial/tutorial_general_203.png

