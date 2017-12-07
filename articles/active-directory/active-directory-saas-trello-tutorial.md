---
title: "Руководство по интеграции Azure Active Directory с Trello | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Trello."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 598387b6066612c6c4a4c92cba5ba03e03a55203
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Руководство. Интеграция Azure Active Directory с Trello

В этом руководстве описано, как интегрировать приложение Trello с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Trello обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Trello.
- Вы можете включить автоматический вход пользователей в Trello (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Trello, вам потребуется:

- подписка Azure AD;
- подписка Trello с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Trello из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-trello-from-the-gallery"></a>Добавление Trello из коллекции
Чтобы настроить интеграцию Trello с Azure AD, необходимо добавить Trello из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Trello из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Trello**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_search.png)

5. На панели результатов выберите **Trello** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-trello-tutorial/tutorial_trello_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Trello с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Trello соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Trello.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Trello.

Чтобы настроить и проверить единый вход Azure AD в Trello, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Trello](#creating-a-trello-test-user)** требуется для того, чтобы в Trello существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Trello.

>[!NOTE]
    >Необходимо получить поле динамических данных **\<enterprise\>** из Trello. При отсутствии значения поля динамических данных обратитесь к [группе поддержки Trello](mailto:support@trello.com), чтобы получить его для своего предприятия.
    > 

**Чтобы настроить единый вход Azure AD в Trello, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Trello** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициируемом IdP**, то в разделе **Домены и URL-адреса приложения Trello** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://trello.com/auth/saml/consume/<enterprise>`.

4. Если вы хотите настроить приложение в **режиме, инициируемом поставщиком услуг**, выполните следующие действия.

  ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_url1.png)

    а. Установите флажок **Показать дополнительные параметры URL-адресов**.

    b. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://trello.com/auth/saml/login/<enterprise>`.

  c. В текстовом поле **Идентификатор** введите URL-адрес `https://trello.com/auth/saml/metadata`.

5. Приложение Trello ожидает, что утверждения SAML должны содержать определенные атрибуты. Настройте приведенные ниже атрибуты для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Атрибуты пользователя** приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_attribute.png)

6. В диалоговом окне **Атрибуты токена SAML** для каждой строки в таблице ниже выполните следующие действия:
 
    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    а. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_officespace_05.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки. 

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    г) Нажмите кнопку **ОК**. 
 
7. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_certificate.png) 

8. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Trello** щелкните **Настроить Trello**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_configure.png) 

9. Для настойки единого входа для приложения перейдите на страницу [Trello enterprise SSO configuration](https://trello.com/sso-configuration) (Конфигурация единого входа для предприятия Trello), чтобы отправить [группе поддержки Trello](mailto:support@trello.com) значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) и прикрепить скачанный **сертификат в кодировке Base64**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-trello-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-trello-test-user"></a>Создание тестового пользователя приложения Trello

В этом разделе описано, как создать пользователя Britta Simon в приложении Trello. В этом разделе описано, как создать пользователя Britta Simon в приложении Trello. Trello поддерживает JIT-подготовку. При первом входе из Azure AD будет создана учетная запись.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Trello.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Trello, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Trello**.

    ![Настройка единого входа](./media/active-directory-saas-trello-tutorial/tutorial_trello_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Trello на панели доступа, вы автоматически войдете в приложение Trello.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



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

