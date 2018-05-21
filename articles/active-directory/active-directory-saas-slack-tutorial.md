---
title: Руководство по интеграции Azure Active Directory с приложением Slack | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: jeedes
ms.openlocfilehash: c3fb9eb3e8dd4b8c3add5dcf0436958500aa0189
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>Учебник. Интеграция Azure Active Directory с Slack

В этом руководстве описано, как интегрировать Slack с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Slack обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Slack.
- Вы можете включить автоматический вход пользователей в Slack (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Slack, вам потребуется:

- подписка Azure AD;
- подписка Slack с активированной функцией единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Slack из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-slack-from-the-gallery"></a>Добавление Slack из коллекции.
Чтобы настроить интеграцию Slack с Azure AD, необходимо добавить Slack из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Slack из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Slack**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_search.png)

5. На панели результатов выберите **Slack** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Slack с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Slack соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Slack.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Slack.

Чтобы настроить и проверить единый вход Azure AD в Slack, нужно выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Slack](#creating-a-slack-test-user)** требуется для того, чтобы в Slack существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Slack.

**Чтобы настроить единый вход Azure AD в Slack, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Slack** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Slack** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.slack.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес `https://slack.com`.

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес для входа. Чтобы получить это значение, обратитесь к [группе поддержки Slack](https://slack.com/help/contact).
     
4. Приложение Slack ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute.png)

    > [!NOTE] 
    > Если у вас есть пользователи, которым предоставлен **адрес электронной почты** без использования лицензии Office 365, то утверждение **User.Email** не будет присутствовать в маркере SAML. В таких случаях мы советуем указать **user.userprincipalname** в качестве значения атрибута **User.Email**, чтобы использовать сопоставление с параметром **Уникальный идентификатор**.

5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** выберите значение **user.mail** для параметра **Идентификатор пользователя**, а в каждой строке в таблице ниже выполните следующие действия:
    
    | Имя атрибута | Значение атрибута |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |  
    | User.Username | user.userprincipalname |

    a. Щелкните **Атрибут**, чтобы открыть диалоговое окно **Изменить атрибут**, и выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute1.png)

    a. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    Б. Из списка **Значение** выберите значение атрибута, отображаемое для этой строки.

    c. Оставьте пустым поле **Пространство имен**.
    
    d. Щелкните **ОК**

6. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_certificate.png)

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

8. В разделе **Конфигурация Slack** щелкните **Настроить Slack**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_configure.png) 

9.  В другом окне веб-браузера войдите на свой корпоративный сайт Slack в качестве администратора.

10.  Выберите **Microsoft Azure AD** > **Team Settings** (Параметры команды).

     ![Настройка единого входа на стороне приложения](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

11.  В разделе **Team Settings** (Параметры команды) выберите вкладку **Authentication** (Проверка подлинности) и нажмите кнопку **Change Settings** (Изменить параметры).

     ![Настройка единого входа на стороне приложения](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

12. На странице **Параметры проверки подлинности SAML** сделайте следующее:

    ![Настройка единого входа на стороне приложения](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  В текстовое поле **SAML 2.0 Endpoint (HTTP)** (Конечная точка SAML 2.0 (HTTP)) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    Б.  В текстовое поле **Identity Provider Issuer** (Издатель поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    c.  Откройте скачанный файл сертификата в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Public Certificate** (Общий сертификат).

    d. Настройте три показанных выше параметра для своей группы Slack. Дополнительные сведения о параметрах см. в статье **Guide to single sign-on with Slack** (Руководство по настройке единого входа в Slack). `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    д.  Щелкните **Сохранить конфигурацию**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-slack-test-user"></a>Создание тестового пользователя Slack

Цель этого раздела — создать в Slack пользователя с именем Britta Simon. Приложение Slack поддерживает JIT подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Slack (если он еще не создан).

> [!NOTE]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки Slack](https://slack.com/help/contact).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Slack.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Slack, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Slack**.

    ![Настройка единого входа](./media/active-directory-saas-slack-tutorial/tutorial_slack_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Slack на панели доступа, вы автоматически войдете в приложение Slack.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

